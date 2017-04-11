<properties
    pageTitle="Azure 批次的有效清單查詢 |Microsoft Azure"
    description="增加效能篩選查詢時要求批次資源集區、 工作、 工作等的詳細資訊，並且計算節點。"
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

# <a name="query-the-azure-batch-service-efficiently"></a>有效率地查詢 Azure 批次服務

這裡，您將學習如何減少查詢工作的工作，然後計算以[批次.NET]節點服務會傳回的資料量增加 Azure 批次應用程式的效能[api_net]文件庫。

幾乎所有批次應用程式需要執行某些類型的查詢批次服務，通常定期間隔的監控或其他作業。 例如，若要判斷是否有任何工作的剩餘的佇列的工作，您必須取得資料工作中的每個任務。 若要判斷您的資料庫中的節點的狀態，您必須取得資料的資料庫中的每個節點。 本文說明如何執行這類查詢中的最有效的方式。

## <a name="meet-the-detaillevel"></a>符合 DetailLevel

在產生批次應用程式，可以以千為單位的數字工作、 工作等運算節點的項目。 當您要求這些資源的詳細資訊時，大量資料必須 「 交叉線 「 批次服務應用程式在每個查詢。 藉由限制的項目數和查詢所傳回的資訊類型，您可以增加速度的查詢，因此您的應用程式的效能。

此[批次.NET] [ api_net] API 程式碼片段清單與工作，以及*所有*內容的每個任務相關聯的*所有*工作︰

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

您可以將 「 詳細資料層級 」 套用至您的查詢，不過，執行更有效率的清單查詢。 您執行這個動作提供[ODATADetailLevel] [odata]物件[JobOperations.ListTasks] [net_list_tasks]方法。 此程式碼片段傳回只識別碼、 命令列和計算節點資訊屬性已完成的工作︰

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

在此範例案例中，如果有數以千計的任務，在工作中，從第二個查詢的結果通常會傳回許多比第一個更快。 深入瞭解使用清單項目以批次.NET API ODATADetailLevel 是包含[下方](#efficient-querying-in-batch-net)。

> [AZURE.IMPORTANT]
> 我們強烈建議，您*隨時都*提供 ODATADetailLevel 物件在.NET API 清單通話，以確保最高效率和應用程式的效能。 藉由指定詳細資料層級，您可以協助降低批次服務回應時間，改善網路使用率減少記憶體使用量用戶端應用程式。

## <a name="filter-select-and-expand"></a>篩選，請選取，然後展開

[批次.NET] [api_net]和[批次其餘][ api_rest] Api 提供的功能，以減少傳回在清單中的項目數目及每個所傳回的資訊量。 您這麼做時執行查詢清單指定**篩選**、**選取**和**展開的字串**。

### <a name="filter"></a>篩選
篩選字串的運算式，減少傳回的項目。 例如，清單的執行工作的工作，或清單的準備好要執行的工作運算節點。

- 篩選字串包含一或多個運算式組成的屬性名稱、 運算子和值的運算式。 您可以指定的屬性是特定的查詢，每個實體類型支援的每個屬性的運算子。
- 使用邏輯運算子也可以合併多個運算式`and`和`or`。
- 此範例中篩選字串清單，只執行的 「 轉譯 」 任務︰ `(state eq 'running') and startswith(id, 'renderTask')`。

### <a name="select"></a>選取
選取的字串限制的屬性值所傳回的每個項目。 您指定的屬性名稱清單，並僅這些屬性的值會傳回查詢結果中的項目。

- 選取字串包含逗點分隔的屬性名稱清單。 您可以指定您要查詢的實體類型的屬性。
- 此範例選取字串指定只有三個屬性值應該會傳回每項工作︰ `id, state, stateTransitionTime`。

### <a name="expand"></a>展開
展開字串減少的 API 呼叫所需取得特定資訊。 當您使用的展開字串時，您可以取得深入瞭解每個項目與單一 API 通話。 而不是第一份取得的項目，然後要求資訊在清單中，每個項目清單中，您可以使用展開字串來取得單一的 API 呼叫相同的資訊。 小於 API 呼叫表示較佳的效能。

- 與選取的字串，展開字串控制特定的資料是否包含在清單中的查詢結果中。
- 使用中列出的工作、 工作排程、 工作和集區時，才支援展開字串。 目前，它只支援統計資訊。
- 當所需的所有內容，並沒有選取指定字串時，展開字串，*必須*使用統計資訊。 如果要將選取的字串可用來取得子集屬性]，然後`stats`可以指定在選取的字串，並展開字串不需要加以指定。
- 此範例中展開字串指定每個項目清單中，應傳回統計資訊︰ `stats`。

> [AZURE.NOTE] 建構任何三個查詢字串類型時 （篩選、 選取，並展開），您必須確定的屬性名稱和大小寫，使其符合其 REST API 項目有何差異。 例如，使用.NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask)類別工作時，您必須先指定**狀態**，而不是**狀態**，，即使.NET 屬性是[CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state)。 請參閱.NET 和 REST Api 之間的屬性對應] 下方的資料表。

### <a name="rules-for-filter-select-and-expand-strings"></a>規則的篩選]，選取，然後展開字串

- 屬性名稱，在篩選中，選取，然後展開字串應該會出現在[批次的其餘部分]中一樣[api_rest]API，即使您使用[批次.NET] [api_net]或其他批次 Sdk 的其中一個。
- 所有屬性的名稱都會區分大小寫，但是屬性值不區分大小寫。
- 日期/時間字串可以有兩種格式，而且必須有`DateTime`。

  - W3C DTF 格式範例︰`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC 1123 格式範例︰`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- 布林值的字串是`true`或`false`。
- 如果指定無效的屬性或運算子，`400 (Bad Request)`會產生錯誤。

## <a name="efficient-querying-in-batch-net"></a>有效以批次.NET 查詢

[批次.NET]內[api_net]API、 [ODATADetailLevel] [odata]類別用於提供篩選、 選取和展開清單的作業的字串。 ODataDetailLevel 類別有三個可指定建構函式，或直接在物件上設定公用字串屬性。 然後您傳遞 ODataDetailLevel 物件做為參數至不同的清單作業，例如[ListPools][net_list_pools]， [ListJobs][net_list_jobs]，及[ListTasks][net_list_tasks]。

- [ODATADetailLevel][odata]。[FilterClause][ odata_filter]︰ 限制傳回的項目數目。
- [ODATADetailLevel][odata]。[SelectClause][ odata_select]︰ 指定哪些屬性值會傳回與每個項目。
- [ODATADetailLevel][odata]。[ExpandClause][ odata_expand]︰ 在單一 API 的所有項目擷取資料，而不是個別的來電撥打的每個項目。

下列程式碼片段使用批次.NET API 有效率地查詢批次服務的一組特定的集區的統計資料。 在這個案例中，批次使用者會有測試和實際的資料庫。 測試資料庫識別碼會加上 「 測試 」，然後生產資料庫識別碼會加上 「 產品 」。 在 [程式碼片段， *myBatchClient*是[BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient)類別正確初始化執行個體。

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] 執行個體的[ODATADetailLevel] [odata]選取的設定，並展開子句可以也傳遞到適當的取得方法，例如[PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx)，以限制傳回的資料量。

## <a name="batch-rest-to-net-api-mappings"></a>批次 REST API 對應

屬性名稱，在篩選中，選取，然後展開字串*必須*反映 REST API 對應，同時在名稱] 和 [大小寫。 下表提供.NET 和 REST API 對應項目之間的對應。

### <a name="mappings-for-filter-strings"></a>篩選字串的對應

- **.NET 清單的方法**︰ 每個.NET API 方法，在 [此欄會接受[ODATADetailLevel] [odata]物件做為參數。
- **其他清單的要求**︰ 連結至在 [此欄的每個 REST API 頁面包含指定*篩選*字串中的摘要資訊與作業所允許的資料表。 您在建構[ODATADetailLevel.FilterClause]時會使用這些屬性名稱及作業[odata_filter]字串。

| .NET 清單的方法 | 其他清單邀請 |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [清單中的帳戶的憑證][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [清單與任務相關的檔案][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [列出工作準備及工作的工作發行任務的狀態][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [在帳戶中的工作] 清單][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [清單上節點的檔案][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [清單與工作相關的工作][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [列出在帳戶中的工作排程][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [清單與工作排程相關聯的工作][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [集區中的計算節點] 清單][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [清單的集區帳戶][rest_list_pools]

### <a name="mappings-for-select-strings"></a>選取字串的對應

- **批次.NET 類型**︰ 批次.NET API 類型。
- **REST API 項目**︰ 每個頁面，在 [此欄包含一或多個類型的 REST API 屬性名稱] 清單中的資料表。 建構*選取*字串時，會使用這些屬性名稱。 當您建構[ODATADetailLevel.SelectClause]時，您會使用這些相同的屬性名稱[odata_select]字串。

| 批次.NET 類型 | REST API 項目 |
|---|---|
| [憑證][net_cert] | [取得憑證的相關資訊][rest_get_cert] |
| [CloudJob][net_job] | [取得工作的相關資訊][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [取得工作排程的相關資訊][rest_get_schedule] |
| [ComputeNode][net_node] | [取得節點的相關資訊][rest_get_node] |
| [CloudPool][net_pool] | [取得資料庫的相關資訊][rest_get_pool] |
| [CloudTask][net_task] | [取得有關任務的資訊][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>範例︰ 建構篩選字串

當您建構篩選字串[ODATADetailLevel.FilterClause][odata_filter]，對應到您想要執行的清單作業的 REST API 尋找文件頁面，請參閱在 [「 對應篩選字串 「 上列表格。 在頁面上的第一個多列的資料表中，您會發現可篩選的摘要資訊與支援的運算子。 如果您想要擷取所有任務的結束程式碼是零，例如，此[與工作相關聯的工作清單]的列[rest_list_tasks]指定適用的屬性字串和允許運算子︰

| 屬性 | 允許的作業 | 類型 |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

因此，就會列出所有工作零的結束程式碼篩選字串︰

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>範例︰ 建構選取字串

若要建構[ODATADetailLevel.SelectClause][odata_select]，在 [「 對應選取字串出現的 [上方表格，請參閱瀏覽至對應的會列出的實體類型 REST API 頁面。 在頁面上的第一個多列的資料表中，您會發現可選取的摘要資訊與支援的運算子。 如果您想要擷取只識別碼和每個任務清單中的命令列，例如，您會發現這些資料列[取得工作的相關資訊]之相關資料表中[rest_get_task]:

| 屬性 | 類型 | 備忘稿 |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

選取的字串，包括只 ID 和命令與每個所列的任務將會是︰

`id, commandLine`

## <a name="code-samples"></a>程式碼範例

### <a name="efficient-list-queries-code-sample"></a>有效的清單查詢程式碼範例

請查看[EfficientListQueries] [ efficient_query_sample] GitHub 若要查看如何有效查詢的清單上的範例專案會影響應用程式中的效能。 此 C# 主控台應用程式建立，並將工作的大量的任務。 然後，其可讓您多次呼叫[JobOperations.ListTasks] [net_list_tasks]方法和傳遞[ODATADetailLevel] [odata]設定為使用不同的屬性值，來變更要傳回的資料量的物件。 它會產生類似以下的輸出︰

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

經過的時間所示，可以大幅降低查詢回應時間限制屬性和所傳回的項目數。 您可以尋找[azure-批次的範例]這和其他範例專案[github_samples]GitHub 上的儲存機制。

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics 文件庫和程式碼範例

除了上面的 EfficientListQueries 程式碼範例，您可以找到[BatchMetrics] [ batch_metrics] [azure-批次的範例]中的專案[github_samples]GitHub 存放庫。 BatchMetrics 範例專案示範如何以有效率地監控使用批次 API 的 Azure 批次工作進度。

[BatchMetrics] [batch_metrics]範例包括.NET 類別文件庫專案可以加入到您的專案和簡單的命令列程式練習，並示範如何使用文件庫。

在專案中的範例應用程式示範下列作業︰

1. 選取的特定屬性，才能下載您需要的屬性
2. 篩選狀態切換時間才能下載自上次查詢的只會變更

例如，下列方法會出現在 BatchMetrics 文件庫。 會傳回指定的只 ODATADetailLevel`id`和`state`屬性應該取得查詢的實體。 它也會指定的實體其狀態已變更自指定`DateTime`應傳回參數。

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>後續步驟

### <a name="parallel-node-tasks"></a>平行節點工作

[最大化 Azure 批次計算資源使用狀況，同時節點任務](batch-parallel-node-tasks.md)是另一個批次應用程式的效能與相關的文件。 某些類型的工作負載的效益禁止在上執行平行工作較大，但更少，計算節點。 查看如這種狀況的詳細資訊，請參閱此[範例案例](batch-parallel-node-tasks.md#example-scenario)。

### <a name="batch-forum"></a>批次論壇

[Azure 批次論壇][forum]是 MSDN 上的 [討論批次並提出問題有關服務的絕佳起點。 標題上透過實用的 「 自黏 」 文章，並張貼您的問題，您建立批次方案時，所出現。


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
