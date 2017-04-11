<properties
   pageTitle="可測試性動作 |Microsoft Azure"
   description="本文說話關於 Microsoft Azure 服務布料的轉印圖樣中找到可測試性動作。"
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>可測試性動作
若要模擬不可靠的基礎結構，Azure 服務布料的轉印圖樣可讓您，開發人員]，以模擬各種實際的錯誤和狀態轉換的方式。 這些被公開為可測試性動作。 動作有特定的錯誤插入、 狀態轉場效果，或驗證低功率 Api。 藉由組合這些動作，您可以撰寫全面涵蓋所有內容的測試方案服務。

服務架構提供一些常見的測試案例組成這些動作。 我們強烈建議您，利用這些內建的情況下，謹慎選擇測試一般狀態轉換及錯誤情況。 不過，動作可用來建立自訂的測試方案，當您要新增的未涵蓋的內建的案例尚未或自訂應用程式的量身打造的案例的範圍。

System.Fabric.dll 組件中，找到 C# 實作的動作。 在 [組件 Microsoft.ServiceFabric.Powershell.dll 中找到系統布料的轉印圖樣 PowerShell 模組。 執行階段安裝的一部分，以便於使用允許安裝 ServiceFabric PowerShell 模組。

## <a name="graceful-vs-ungraceful-fault-actions"></a>正常與優雅故障動作
主要兩者分成可測試性動作︰

* 優雅錯誤︰ 這些錯誤模擬像電腦重新啟動失敗，以及處理當機。 在的失敗次數這種情況下，執行內容的程序突然停止。 這表示沒有清理狀態的應用程式再次啟動之前執行。

* 正常錯誤︰ 這些錯誤模擬正常動作等複本移動和觸發負載平衡的距離。 在這種情況下，服務收到通知，關閉，並且可以清除之前結束的狀態。

更好的品質驗證執行服務和公司工作負載同時查各種正常和優雅錯誤。 優雅的錯誤一位置的服務程序突然結束中間某些工作流程的案例。 這是測試復原路徑後服務複本還原依服務布料的轉印圖樣。 這將可協助測試資料的一致性及服務狀態] 失敗之後的正確維護是否。 其他設定的失敗次數 （正常失敗） 測試服務正確地反應服務布料的轉印圖樣正在移動的複本。 這是測試取消之處理方式在 RunAsync 方法。 檢查取消權杖所設定，正確地儲存的狀態，並結束 RunAsync 方法需要服務。

## <a name="testability-actions-list"></a>可測試性動作清單

| 巨集指令 | 描述 | 受管理的 API | PowerShell cmdlet | 正常/優雅的錯誤 |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| 若不正確的測試驅動程式關機叢集會移除所有測試狀態。 | CleanTestStateAsync | 移除 ServiceFabricTestState | 不適用 |
| InvokeDataLoss | 引發服務磁碟分割的資料遺失。 | InvokeDataLossAsync | 呼叫 ServiceFabricPartitionDataLoss | 正常 |
| InvokeQuorumLoss | 指定狀態服務分割放仲裁遺失。 | InvokeQuorumLossAsync | 呼叫 ServiceFabricQuorumLoss | 正常 |
| 移動主要 | 將指定的主要複本的狀態服務移至指定的叢集節點。 | MovePrimaryAsync | 移動 ServiceFabricPrimaryReplica | 正常 |
| 移動次要 | 將目前的次要複本狀態服務的移到不同的叢集節點。 | MoveSecondaryAsync | 移動 ServiceFabricSecondaryReplica | 正常 |
| RemoveReplica | 模擬複本失敗移除叢集的複本。 這將會關閉複本，並會轉換為角色 [無]，移除所有其狀態叢集。 | RemoveReplicaAsync | 移除 ServiceFabricReplica | 正常 |
| RestartDeployedCodePackage | 重新啟動的程式碼套件部署叢集節點上模擬程式碼套件程序失敗。 這會中止程式碼套件程序，將該程序在重新啟動裝載的所有使用者服務複本。 | RestartDeployedCodePackageAsync | 重新啟動 ServiceFabricDeployedCodePackage | 優雅 |
| RestartNode | 重新啟動節點，模擬服務布料的轉印圖樣叢集節點失敗。 | RestartNodeAsync | 重新啟動 ServiceFabricNode | 優雅 |
| RestartPartition | 模擬案例的資料中心轉黑或叢集轉黑重新啟動部分或所有磁碟分割的複本。 | RestartPartitionAsync | 重新啟動 ServiceFabricPartition | 正常 |
| RestartReplica | 模擬複本失敗叢集在重新啟動保存的複本，關閉複本，然後再重新開啟。 | RestartReplicaAsync | 重新啟動 ServiceFabricReplica | 正常 |
| StartNode | 已停止叢集開始節點。 | StartNodeAsync | 開始 ServiceFabricNode | 不適用 |
| StopNode | 停止叢集節點，模擬節點失敗。 節點會保留向下，直到 StartNode 稱為。 | StopNodeAsync | 停止 ServiceFabricNode | 優雅 |
| ValidateApplication | 驗證可用性及的應用程式，通常之後查一些故障系統中的所有服務布料的轉印圖樣服務健康狀況。 | ValidateApplicationAsync | 測試 ServiceFabricApplication | 不適用 |
| ValidateService | 驗證可用性和健康情況的服務布料的轉印圖樣服務，通常之後查系統中的某些錯誤。 | ValidateServiceAsync | 測試 ServiceFabricService | 不適用 |

## <a name="running-a-testability-action-using-powershell"></a>執行使用 PowerShell 可測試性巨集指令

本教學課程教您如何使用 PowerShell 來執行可測試性動作。 您將學習如何執行本機的 （一個方塊） 叢集或 Azure 叢集可測試性動作。 Microsoft.Fabric.Powershell.dll-當您安裝 Microsoft 服務布料的轉印圖樣 MSI 服務布料的轉印圖樣 PowerShell 模組，會自動安裝。 當您開啟 PowerShell 提示時，會自動載入模組。

教學課程區段︰

- [針對為一個方塊叢集執行動作](#run-an-action-against-a-one-box-cluster)
- [針對 Azure 叢集執行動作](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>針對為一個方塊叢集執行動作

若要針對本機叢集執行可測試性動作，請先連線到叢集，然後以系統管理員模式開啟 PowerShell 提示。 讓我們看看**重新啟動 ServiceFabricNode**動作。

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

以下是名為 「 Node1 」 的節點上執行動作**重新啟動 ServiceFabricNode** 。 完成模式指定，它不應該確認是否重新啟動節點動作實際成功。 指定 「 驗證 」 會導致驗證是否重新啟動動作實際成功完成模式。 而不是直接指定節點依其名稱，您可以指定分割索引鍵和種類的複本，請透過，如下所示︰

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**重新啟動 ServiceFabricNode**應在叢集重新啟動的服務布料的轉印圖樣節點。 這會停止 Fabric.exe 程序，將會重新啟動所有系統服務和使用者服務複本裝載的節點。 若要測試您的服務中使用此 API，可協助發掘沿著容錯移轉復原路徑的錯誤。 有助於模擬叢集節點失敗。

以下螢幕擷取畫面顯示作用中的 [**重新啟動 ServiceFabricNode**可測試性] 命令。

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

輸出的第一個**取得 ServiceFabricNode** (從服務布料的轉印圖樣 PowerShell 模組的 cmdlet) 會顯示的本機叢集都有五個節點︰ 以 Node.5 Node.1。 執行的節點上，名為 Node.4，可測試性動作 (cmdlet)**重新啟動 ServiceFabricNode**後我們看到節點的執行時間已重設。

### <a name="run-an-action-against-an-azure-cluster"></a>針對 Azure 叢集執行動作

執行可測試性動作 （藉由使用 PowerShell） Azure 叢集就像對本機叢集執行的動作。 唯一的差別之前您可以執行的動作，而不是連線到本機叢集，您需要先連線到 Azure 叢集。

## <a name="running-a-testability-action-using-c35"></a>執行使用 C# 35; 可測試性巨集指令

若要使用 C# 執行可測試性動作，首先您必須使用 FabricClient 連線到叢集。 然後，取得執行此動作所需的參數。 不同的參數可用來執行相同的動作。
查看 RestartServiceFabricNode 動作，以執行的其中一個方法，是使用叢集節點資訊 （節點名稱節點執行個體識別碼）。

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

參數說明︰

- **CompleteMode**指定模式應該確認是否重新啟動動作實際成功。 指定 「 驗證 」 會導致驗證是否重新啟動動作實際成功完成模式。  
- **OperationTimeout**設定 「 逾時例外狀況先完成作業的時間量。
- **CancellationToken**可讓使用者可以取消擱置通話。

而不是直接指定節點依其名稱，您可以將其指定透過分割索引鍵和複本的類型。

如需進一步資訊，請參閱[PartitionSelector 和 ReplicaSelector](#partition_replica_selector)。


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector 和 ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector 協助程式中可測試性公開，而用來選取特定的磁碟分割在其上執行任何可測試性的動作。 可以用於選取特定的磁碟分割，如果事先已知的磁碟分割識別碼。 或者，您可以提供分割索引鍵，然後作業解決分割識別碼的內部。 您也可以選取 [隨機的磁碟分割的選項。

若要使用這個協助程式，建立 PartitionSelector 物件，然後使用選取 * 方法的其中一個選取的資料分割。 然後傳遞 PartitionSelector 物件對 API 的需要。 如果沒有選項時，預設會隨機的磁碟分割。

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector 協助程式中可測試性公開，而用來協助選取要執行任何可測試性動作的複本。 可以用於選取特定的複本，如果事先已知的複本識別碼。 此外，您可以選取 [主要複本或隨機次要的選擇。 ReplicaSelector 衍生 PartitionSelector，因此您必須選取的複本，因此您要執行可測試性作業的磁碟分割。

若要使用這個協助程式，建立 ReplicaSelector 物件並設定您想要選取的複本，分割的方式。 然後您可以將它，將需要 API。 如果沒有選項時，其預設隨機複本和隨機分割。

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>後續步驟

- [可測試性案例](service-fabric-testability-scenarios.md)
- 如何測試您的服務
   - [服務工作負載期間模擬失敗](service-fabric-testability-workload-tests.md)
   - [若要服務通訊失敗](service-fabric-testability-scenarios-service-communication.md)
