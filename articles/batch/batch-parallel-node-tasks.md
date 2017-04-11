<properties
    pageTitle="最大化批次節點搭配平行工作 |Microsoft Azure"
    description="增加 Azure 批次資料庫中的每個節點上使用較少的運算節點與執行同時工作效率，並降低成本"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="maximize-azure-batch-compute-resource-usage-with-concurrent-node-tasks"></a>最大化 Azure 批次計算資源使用狀況，同時節點工作

在您 Azure 批次集區中每個運算節點同時執行多個任務，您可以最大化較小的數字集區中的節點上的 [資源使用狀況。 某些工作負載，這可能導致短的工作時間和較低的成本。

同時指定所有的節點的資源專用到單一任務因某些情況下，幾種情況會受益允許多個共用這些資源的任務︰

 - **最小化的資料傳輸**工作時可以共用資料。 在此案例中，您可以大幅降低資料傳輸費用共用的資料複製到較小的數字的節點並平行每個節點上執行的工作。 這特別是適用於必須傳送地理區域之間的資料複製到每個節點。

 - **Maximizing 記憶體使用量**工作所需的大量的記憶體，但只在短時間量，然後在變數執行期間的時間。 您可以採用更多記憶體有效率地處理這類特殊圖文集的較少，但較大，運算的節點。 這些節點必須在每個節點，同時執行多個工作，但每項工作會利用節點的大量記憶體在不同的時間。

 - 集區中需要節點間通訊時，請**減少節點的數字限制**。 目前資料庫設定節點間通訊限於 50 運算節點。 如果這類資料庫中的每個節點可以同時執行工作，則可以同時執行更多的工作。

 - **複製內部部署運算叢集**，例如當您第一次將在計算環境至 Azure。 如果您目前的內部部署解決方案執行每個運算節點的多個工作，您可以增加深入分析鏡像該設定節點任務的最大數目。

## <a name="example-scenario"></a>範例案例

以描繪出執行平行工作的優點舉例來說，假設您任務的應用程式具有 CPU 和記憶體需求，例如[標準\_D1](../cloud-services/cloud-services-sizes-specs.md#general-purpose-d)節點已足夠。 不過，才能完成工作所需的時間，需要這些節點 1000。

而不是使用標準\_有 1 CPU 核心 D1 節點，您可以使用[標準\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d)節點 16 核心每個，並啟用執行平行工作。 因此，可用於*16 時間較少的節點*，而不是 1000 的節點，只 63 必須進行。 此外，如果大型的應用程式的檔案或參考資料所需的每個節點，工作持續時間] 及 [效率是一次改良由於資料複製到只 16 的節點。

## <a name="enable-parallel-task-execution"></a>啟用執行平行工作

您設定平行工作執行運算節點層級的資料庫。 與批次.NET 文件庫中，設定[CloudPool.MaxTasksPerComputeNode] [maxtasks_net]時建立資料庫的屬性。 如果您使用批次 REST API，設定[maxTasksPerNode] [rest_addpool]資料庫建立期間邀請內文中的項目。

Azure 批次可讓您設定每個節點的最大工作多達四個時間 (4 x) 節點核心數目。 例如，如果集區設定節點的調整大小 「 大 」 （四個核心），然後`maxTasksPerNode`可能會設定為 16。 每個節點大小的核心數目的詳細資訊，請參閱[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)。 如需有關服務限制的詳細資訊，請參閱[配額和 Azure 批次服務的限制](batch-quota-limit.md)。

> [AZURE.TIP] 請務必考慮`maxTasksPerNode`建構[自動調整大小的公式]時的值[enable_autoscaling]集區。 例如，公式的評估`$RunningTasks`可能大幅影響增加每個節點的基本工作。 如需詳細資訊，請參閱[自動小數位數計算 Azure 批次資料庫中的節點](batch-automatic-scaling.md)。

## <a name="distribution-of-tasks"></a>通訊群組的工作

時運算節點資料庫中的可同時執行工作，請務必指定分佈集區中的節點的工作方式。

使用[CloudPool.TaskSchedulingPolicy] [task_schedule]屬性，您可以指定的工作的指派平均 （「 分配 」） 的資料庫中的所有節點。 或者您可以指定的任何數量的工作，盡可能應該要指派給每個節點之前工作指派給另一個節點 （「 壓縮 」） 的資料庫。

此功能寶貴方式的範例，請考慮的集區[標準\_D14](../cloud-services/cloud-services-sizes-specs.md#memory-intensive-d)節點 （在上述範例） 設定[CloudPool.MaxTasksPerComputeNode] [ maxtasks_net] 16 的值。 如果[CloudPool.TaskSchedulingPolicy] [ task_schedule] [ComputeNodeFillType]設定[fill_type]的*套件*，它會最大化的每個節點的所有 16 核心的使用方式，並允許[自動縮放資料庫](batch-automatic-scaling.md)中刪除資料庫 （不含任何指定的工作節點） 中未使用過的節點。 如此會最小化 [資源使用狀況，並將儲存金錢。

## <a name="batch-net-example"></a>批次.NET 範例

此[批次.NET] [ api_net] API 程式碼片段顯示建立包含與每個節點的四個任務的最大值的四個大型節點的集區的邀請。 它會指定工作排程原則，會將每個節點填入之前指派工作給另一個節點集區中的工作。 如需有關如何使用批次.NET API 新增資料庫的詳細資訊，請參閱[BatchClient.PoolOperations.CreatePool][poolcreate_net]。

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicated: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>批次的其餘部分範例

此[批次其餘][ api_rest] API 程式碼片段顯示建立資料庫包含與每個節點的四個任務的最大值的兩個大節點的要求。 如需有關使用 REST API 新增資料庫的詳細資訊，請參閱[新增集區帳戶][rest_addpool]。

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicated":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [AZURE.NOTE] 您可以設定`maxTasksPerNode`項目和[MaxTasksPerComputeNode] [maxtasks_net]只能在資料庫建立時間的屬性。 建立資料庫後無法修改它們。

## <a name="code-sample"></a>程式碼範例

[ParallelNodeTasks] [ parallel_tasks_sample] GitHub 上的專案會說明如何使用[CloudPool.MaxTasksPerComputeNode] [maxtasks_net]屬性。

此 C# 主控台應用程式使用[批次.NET] [api_net]使用一或多個運算節點建立資料庫的文件庫。 模擬變數載入這些節點上執行工作可設定數的字。 從應用程式的輸出指定哪些節點執行每項工作。 應用程式也會提供的摘要工作參數和持續時間。 從範例應用程式的兩個不同的執行輸出的摘要部分出現的下方。

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

範例應用程式的第一次執行會顯示資源的資料庫及節點，每一項任務的預設設定中的單一節點與工作持續時間超過 30 分鐘。

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

第二個執行的範例顯示大幅降低的狀況工作持續時間。 這是時間的因為設定集區有四個工作，每個節點，可讓平行工作執行幾乎季完成工作。

> [AZURE.NOTE] 在上方的摘要工作期間不包含資料庫建立時間。 每個以上的工作提交到其運算節點*閒置*狀態已送出次先前建立的資料庫。

## <a name="next-steps"></a>後續步驟

### <a name="batch-explorer-heat-map"></a>檔案總管熱力圖批次

[Azure 批次檔案總管][batch_explorer]，其中一個 Azure 批次[範例應用程式][github_samples]，包含提供執行工作的視覺效果的*熱力圖*功能。 當您執行[ParallelTasks] [parallel_tasks_sample]範例應用程式，您可以使用熱力圖功能，輕鬆地在每個節點的平行工作的執行以視覺化方式呈現。

![檔案總管熱力圖批次][1]

*批次檔案總管顯示熱力圖的四個節點，與目前正在執行四個任務每個節點集區*

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

[1]: ./media/batch-parallel-node-tasks\heat_map.png
