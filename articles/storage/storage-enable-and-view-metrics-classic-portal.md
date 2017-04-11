<properties 
    pageTitle="啟用 Azure 入口網站中的 [儲存指標 |Microsoft Azure" 
    description="如何啟用儲存指標 Blob、 佇列、 表格和檔案的服務" 
    services="storage" 
    documentationCenter="" 
    authors="robinsh" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/03/2016" 
    ms.author="robinsh"/>

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>啟用 [儲存指標與檢視指標資料

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>概觀

根據預設，儲存指標無法進行儲存服務。 您可以監視使用任一[Azure 傳統入口網站](https://manage.windowsazure.com)，Windows PowerShell 來啟用或以程式設計方式透過 API 儲存空間。

當您啟用 [儲存指標時，您必須選擇資料保留期限︰ 這個期間會決定多久儲存空間的服務會保留的指標及您的空間，將它們儲存所需的費用。 一般而言，您應該使用 minute 眼比下限指標的短的保留期限，由於所需的 minute 指標嚴重額外的空間。 例如，您有足夠的時間來分析資料，並下載任何您想要保持離線的分析或報告用途的度量，請選擇保留期限。 請記住，您也計費指標之資料的下載從您儲存的帳戶。

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>如何啟用儲存指標使用 Azure 傳統入口網站

在[Azure 傳統入口網站](https://manage.windowsazure.com)，您可以使用 [設定] 頁面來控制儲存指標儲存帳戶。 監視，您可以設定一個層級和保留期限在每個二進位大型物件、 表格和佇列天。 每個層級是下列其中一項︰

- 關閉 — 不收集任何指標。

- 最小 — 儲存指標會收集一組基本的度量，例如輸入/出口、 可用性、 延遲和成功百分比，Blob、 表格和佇列中服務彙總。

- 詳細資訊，儲存指標會收集一組完整的度量，其中包含每個 API 作業，除了服務層級指標的儲存空間相同的指標。 詳細資訊的指標啟用更深入的分析的應用程式作業期間發生的問題。

請注意，Azure 傳統入口網站無法目前讓您以在您儲存帳戶; 設定 minute 指標您必須先啟用 minute 指標使用 PowerShell 或以程式設計方式。


## <a name="how-to-enable-storage-metrics-using-powershell"></a>如何啟用使用 PowerShell 儲存指標

若要變更的目前設定使用 PowerShell 的 Azure cmdlet 取得-AzureStorageServiceMetricsProperty 擷取目前的設定，以及 cmdlet 設定 AzureStorageServiceMetricsProperty 您儲存的帳戶設定儲存指標，您可以在您的本機電腦上使用 PowerShell。

控制儲存指標的指令程式會使用下列參數︰

- MetricsType 可能值是小時和分鐘。

- ServiceType 可能值是 Blob 與佇列中，表格。

- MetricsLevel 可能值是無 （相當於 Azure 傳統入口網站中為關閉）、 服務 （相當於 Azure 傳統入口網站中最小），並 ServiceAndApi （相當於 Azure 傳統入口網站中的詳細資訊）。

例如，下列命令 minute 指標預設儲存帳戶的 blob 服務上切換期間設定為 5 天保留與︰

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

下列命令會擷取目前每小時指標層級和保留天的預設儲存帳戶 blob 服務︰

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

如需如何設定的資訊 Azure PowerShell cmdlet 來操作您 Azure 的訂閱及如何選取預設儲存帳戶使用，請參閱︰[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

## <a name="how-to-enable-storage-metrics-programmatically"></a>如何以程式設計方式啟用儲存指標

下列 C# 程式碼片段示範如何啟用指標和使用的儲存空間用戶端文件庫的.NET Blob 服務的記錄︰

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);
    
## <a name="viewing-storage-metrics"></a>檢視儲存指標

當您設定儲存指標監控您儲存的帳戶時，它會記錄一組中的已知資料表儲存體帳戶中的指標。 若要檢視每小時指標正在使用在圖表上，您可以儲存帳戶 Azure 傳統入口網站中使用 [監視器] 頁面。 在此頁面中 Azure 傳統入口網站中，您可以︰

- 選取要繪製的圖表 （使用指標的選項取決於您選擇 [設定] 頁面上的服務的詳細資訊，或最小監控） 上的指標。


- 選取顯示在圖表上的度量資訊的時間範圍。


- 選擇要繪製計量使用絕對或相對小數位數。


- 設定特定的公制達到追加為特定的值時，請通知您的電子郵件通知。


如果您要下載的長期儲存指標，或在本機分析，您必須使用工具或撰寫程式碼來朗讀表格。 您必須下載分析 minute 的指標。 如果您的所有資料表中您儲存的帳戶，但您可以直接依名稱存取這些不會顯示表格。 許多協力廠商儲存瀏覽工具知道這些表格，讓您直接檢視 （請參閱的部落格文章[Microsoft Azure 儲存瀏覽器](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)可用工具的清單）。

### <a name="hourly-metrics"></a>每小時的指標
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Minute 指標
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>容量
- $MetricsCapacityBlob

您可以找到[儲存分析指標表格的結構描述](https://msdn.microsoft.com/library/azure/hh343264.aspx)，這些表格的結構描述的完整詳細資料。 下列範例資料列會顯示可用的資料行的子集，但說明的方式儲存指標會儲存這些計量一些重要功能︰

| PartitionKey  |       RowKey       |                    時間戳記 | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | 顯示狀態 | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      使用者資訊。所有      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | 使用者資訊。QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7.8                  | 100            |
| 20140522T1100 |  使用者資訊。QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | 使用者資訊。UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

在此範例 minute 指標資料分割索引鍵使用 minute 解析度的時間。 資料列識別碼識別的儲存在資料列的資訊類型，這由的資訊、 的存取類型，並要求輸入的兩個部分組成︰

- Access 類型為 [使用者] 或 [系統] 位置使用者所參照的儲存空間服務，所有使用者要求，而且系統都代表所做的儲存空間分析的要求。

- 要求類型為所有則它會摘要線條，或用來識別特定 API QueryEntity 或 UpdateEntity 等。


上述範例資料 （在 11:00 AM 開始） 為單一分鐘顯示所有記錄、 UpdateEntity QueryEntities 要求數加上 QueryEntity 數要求加號的數目，要求加入最七個，也就是總顯示在使用者︰ 所有資料列。 同樣地，您可以藉由計算衍生的使用者︰ 所有資料列的平均端對端延遲 104.4286 ((143.8 * 5) + 3 + 9) / 7。

您應該考慮設定 Azure 傳統入口網站上的 [監視器] 頁面中的通知，以便儲存指標自動通知您的儲存空間服務的行為，任何重要變更。如果您使用的儲存空間的檔案總管] 工具下載此指標中的資料分隔格式時，您可以使用 Microsoft Excel 分析的資料。 請參閱的部落格文章[Microsoft Azure 儲存瀏覽器](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)可用儲存空間檔案總管] 工具的清單。



## <a name="accessing-metrics-data-programmatically"></a>以程式控制方式存取指標資料

下列清單顯示範例 C# 程式碼存取 minute 計量分鐘的時間範圍，並將結果顯示在主控台視窗。 它會使用 Azure 儲存文件庫版本 4，其中包含可簡化存取指標資料表儲存體中的 CloudAnalyticsClient 類別。

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>當您啟用 [儲存指標，造成哪些費用？

撰寫要求建立表格實體眼薪資適用於所有 Azure 儲存體作業的標準工資率。

讀取] 和 [刪除的要求，用戶端指標資料，也會在 [標準工資率計費。 如果您已設定資料保留原則，您不會時 Azure 儲存體刪除舊的指標資料。 不過，如果您刪除分析資料時，您的帳戶被費用刪除作業。

使用標準表格的容量也是計費︰ 您可以使用下列來估計的容量用來儲存指標資料︰

- 如果每小時服務使用每個 API 的每個服務，大約 148 KB 的資料將儲存指標交易資料表中每個小時，如果您已啟用 API 層級摘要和服務。

- 如果每小時服務使用每個 API 的每個服務，大約 12 KB 的資料將儲存指標交易資料表中每個小時，如果您已啟用摘要只服務等級。

- 二進位大型物件的容量資料表有兩個資料列加入每日 （如果使用者必須已加入記錄）︰ 這表示的每日這個表格的大小，藉此增加最約 300 個位元組。

## <a name="next-steps"></a>後續步驟︰
[啟用記錄，以及存取記錄資料的儲存空間狀況分析](https://msdn.microsoft.com/library/dn782840.aspx)
 