<properties
   pageTitle="Azure 診斷中使用效能計數器 |Microsoft Azure"
   description="使用 Azure 雲端服務或虛擬機器中的效能計數器找出瓶頸，並調整效能。"
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>建立及使用效能計數器 Azure 應用程式中

本文將說明的好處，以及如何將效能計數器放入 Azure 應用程式。 您可以用來收集資料，尋找瓶頸和調整系統和應用程式的效能。

效能計數器適用於 Windows Server、 IIS 和 ASP.NET 也會收集並用來決定 Azure web 角色、 工作者角色和虛擬機器的狀況。 您也可以建立並使用自訂的效能計數器。  

您可以檢查效能計數器資料
1. 直接在使用遠端桌面存取效能監視器工具的應用程式主機上
2. 與使用 Azure Management Pack 系統管理中心 Operations Manager
3. 存取診斷資料的其他監控工具轉移至 Azure 儲存體。 如需詳細資訊，請參閱[儲存及檢視診斷資料 Azure 儲存體](https://msdn.microsoft.com/library/azure/hh411534.aspx)。  

如需有關如何監控效能的應用程式中[Azure 傳統入口網站](http://manage.azure.com/)的詳細資訊，請參閱[如何監控 Cloud Services](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/)。

其他您採取進階建立記錄並追蹤策略和使用診斷和其他技術來解決問題，以及最佳化 Azure 應用程式，請參閱[疑難排解的開發 Azure 應用程式的最佳作法](https://msdn.microsoft.com/library/azure/hh771389.aspx)。


## <a name="enable-performance-counter-monitoring"></a>啟用 [計數器監視效能

預設不會啟用效能計數器。 您的應用程式或啟動工作必須修改包含您想要監視的每一個角色執行個體特定的效能計數器預設診斷代理程式的設定。

### <a name="performance-counters-available-for-microsoft-azure"></a>適用於 Microsoft Azure 效能計數器

Azure 提供 Windows Server 與 IIS ASP.NET 堆疊的可用的效能計數器子集。 下表列出一些 Azure 應用程式的特定感興趣的效能計數器。

|[計數器類別︰ 物件 （執行個體）|[計數器名稱      |參照|
|---|---|---|
|.NET CLR 例外狀況 （_全域_）|# Exceps 擲回 / 秒   |例外狀況效能計數器|
|.NET CLR 記憶體 （_全域_）    |GC %時間            |記憶體效能計數器|
|ASP.NET                      |應用程式重新啟動    |ASP.NET 的效能計數器|
|ASP.NET                      |要求的執行時間  |ASP.NET 的效能計數器|
|ASP.NET                      |要求中斷連線   |ASP.NET 的效能計數器|
|ASP.NET                      |背景工作程序重新啟動 |ASP.NET 的效能計數器|
|ASP.NET 應用程式 (__總計__)|要求總計        |ASP.NET 的效能計數器|
|ASP.NET 應用程式 (__總計__)|每秒要求          |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319           |要求的執行時間  |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319           |要求等待的時間       |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319           |要求目前        |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319           |要求佇列         |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319           |被拒絕的要求       |ASP.NET 的效能計數器|
|記憶體                       |可用 Mb        |記憶體效能計數器|
|記憶體                       |已確認的位元組         |記憶體效能計數器|
|Processor(_Total)            |%處理器時間        |ASP.NET 的效能計數器|
|TCPv4                        |連線失敗     |TCP 物件|
|TCPv4                        |建立連線 |TCP 物件|
|TCPv4                        |連線重設       |TCP 物件|
|TCPv4                        |區段秒傳送       |TCP 物件|
|網路 Interface(*)         |接收位元組/秒      |網路介面物件|
|網路 Interface(*)         |秒傳送位元組          |網路介面物件|
|網路介面 （Microsoft 虛擬機器匯流排網路介面卡 _2）|接收位元組/秒|網路介面物件|
|網路介面 （Microsoft 虛擬機器匯流排網路介面卡 _2）|秒傳送位元組|網路介面物件|
|網路介面 （Microsoft 虛擬機器匯流排網路介面卡 _2）|每秒位元組總計|網路介面物件|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>建立並新增自訂的效能計數器應用程式

Azure 有支援，以建立及修改自訂效能計數器 web 角色和工作者角色。 計數器可能會用來追蹤和監視特定應用程式的行為。 您可以建立及自訂效能計數器分類和規範刪除啟動任務、 網頁角色或背景工作以提高權限的角色。

>[AZURE.NOTE] 必須有權限來執行更高程式碼自訂效能計數器做的變更。 如果程式碼是在 web 角色或工作者角色，角色必須包含標籤<Runtime executionContext="elevated" />正確初始化角色 ServiceDefinition.csdef 檔案中。

您可以傳送自訂效能計數器資料使用的診斷代理程式的 Azure 儲存體。

Azure 程序會產生的標準效能計數器資料。 必須由您的 web 角色或背景工作角色應用程式中建立自訂的效能計數器資料。 可以在 [自訂效能計數器儲存的資料類型的詳細資訊，請參閱[效能計數器類型](https://msdn.microsoft.com/library/z573042h.aspx)。 建立並設定自訂效能計數器資料 web 角色中的範例，請參閱[PerformanceCounters 範例](http://code.msdn.microsoft.com/azure/)。

## <a name="store-and-view-performance-counter-data"></a>儲存和檢視的效能計數器資料

Azure 快取效能計數器資料與其他的診斷資訊。 使用遠端監視角色執行個體執行時若要檢視工具，例如效能監視器使用遠端桌面存取此資料。 若要保存以外的角色執行個體的資料，診斷代理程式必須傳送資料至 Azure 儲存體。 診斷代理程式，您可以設定的快取的效能計數器資料的大小限制，或可能會將它設定為共用診斷的所有資料限制的部分。 如需關於設定緩衝大小的詳細資訊，請參閱[OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx)和[DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx)。 傳送資料到儲存的帳戶設定診斷代理程式的概觀，請參閱[儲存和檢視診斷資料 Azure 儲存體](https://msdn.microsoft.com/library/azure/hh411534.aspx)。

每個設定的效能計數器執行個體記錄指定的取樣速率，並將取樣的資料轉移至儲存帳戶的排程的傳輸要求或視傳輸要求。 排程自動傳送可能會經常為每分鐘一次。 傳送診斷代理程式的效能計數器資料會儲存於資料表，WADPerformanceCountersTable，在儲存帳戶。 此資料表可能會存取和使用標準 Azure 儲存 API 方法查詢。 請參閱[Microsoft Azure PerformanceCounters 範例](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9)的查詢，並顯示 WADPerformanceCountersTable 資料表中的效能計數器資料的範例。

>[AZURE.NOTE] 根據診斷代理程式傳輸頻率和佇列中延遲，最新的效能計數器資料儲存帳戶中可能需要幾分鐘的時間過期。

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>啟用效能計數器使用診斷設定檔

若要啟用效能計數器 Azure 應用程式中的使用下列程序。

## <a name="prerequisites"></a>必要條件

本節假設您有診斷監視器匯入您的應用程式和診斷設定檔新增至您的 Visual Studio 方案 （diagnostics.wadcfg 中 SDK 2.4 和以下或 diagnostics.wadcfgx 中 SDK 2.5 與上方）。 請參閱步驟 1 和 2 中[Azure 雲端服務與虛擬機器中的 [啟用診斷](./cloud-services-dotnet-diagnostics.md)） 的詳細資訊。

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>步驟 1︰ 收集及儲存資料的效能計數器

診斷檔案新增至您的 Visual Studio 方案之後您可以設定 Azure 應用程式中的集合，效能計數器資料的儲存空間。 這是新增效能計數器診斷檔案。 診斷資料，包括效能計數器第一次會收集的執行個體。 資料然後保存 WADPerformanceCountersTable 表格中的 Azure 資料表服務，讓您也必須在應用程式中指定的儲存空間的帳戶。 如果您在計算模擬器中的本機測試您的應用程式，您也可以儲存診斷資料在本機上儲存模擬器中。 儲存診斷資料之前，必須先前往[Azure 傳統入口網站](http://manage.windowsazure.com/)，然後建立儲存帳戶。 最佳作法是在同一個地理位置與 Azure 應用程式中找出您儲存的帳戶，以避免支付外部頻寬成本，並減少延遲。

### <a name="add-performance-counters-to-the-diagnostics-file"></a>新增效能計數器診斷檔案

有許多計數器，您可以使用。 下列範例會顯示數個效能計數器建議 web 及工作者角色監控。

開啟診斷檔案 （diagnostics.wadcfg 中 SDK 2.4 和以下或 diagnostics.wadcfgx 在 SDK 2.5），並將下列文字新增至 DiagnosticMonitorConfiguration 的項目︰

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

BufferQuotaInMB 屬性，指定的最大資料集合類型 （Azure 記錄、 IIS 記錄） 的檔案系統儲存空間量。 預設值為 0。 達到配額時，新增新的資料時，會刪除舊的資料。 加總的所有 bufferQuotaInMB 屬性必須大於 OverallQuotaInMB 屬性的值。 如決定多少儲存空間，都必須收集診斷資料的詳細說明，請參閱[疑難排解的最佳作法開發 Azure 應用程式](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx)的設定 WAD 一節。

ScheduledTransferPeriod 屬性，指定排定傳送資料之間的間隔，無條件進位至最接近的分鐘。 在下列範例中，會設定為 PT30M （30 分鐘）。 設定傳輸期間小的值，例如 1 分鐘，以利於生產環境中的應用程式的效能，但可以用來查看您正在測試快速使用的診斷。 排程的傳輸期間應該縮小，以確保診斷資料不會覆寫的執行個體，但大型的並不會影響應用程式的效能。

CounterSpecifier 屬性會指定要收集的效能計數器。SampleRate 屬性會指定用以效能計數器應該要取樣，在此情況下 30 秒的頻率。

新增您要收集的效能計數器後, 請診斷檔案以儲存變更。 接下來，您要指定儲存帳戶的診斷資料會保留。

### <a name="specify-the-storage-account"></a>指定儲存帳戶

若要保存您診斷資訊 」 來 Azure 儲存體帳戶，您必須在服務設定 (ServiceConfiguration.cscfg) 檔案中指定連線字串。

Azure SDK 2.5 儲存帳戶可以指定 diagnostics.wadcfgx 檔案中。

>[AZURE.NOTE] 這些指示只會套用至 Azure SDK 2.4 及下方。 Azure SDK 2.5 儲存帳戶可以指定 diagnostics.wadcfgx 檔案中。

若要設定的連線字串︰

1. 開啟使用您偏好使用的文字編輯器 ServiceConfiguration.Cloud.cscfg 檔案，並設定您的儲存空間的連接字串。 儲存帳戶儀表板，管理機碼之下的 Azure 傳統入口網站中，找到 [ *AccountName* ] 和 [ *AccountKey*值。

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. 儲存 ServiceConfiguration.Cloud.cscfg 檔案。

3. 開啟 ServiceConfiguration.Local.cscfg 檔案，並確認 UseDevelopmentStorage 已設為 true。

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
現在，設定連線字串，您的應用程式會在部署您的應用程式時保存診斷資料，您儲存的帳戶。
4. 儲存並建立您的專案，然後將應用程式部署]。

## <a name="step-2-optional-create-custom-performance-counters"></a>步驟 2: （選擇性） 建立自訂的效能計數器

除了預先定義的效能計數器，您可以新增您自己的自訂效能計數器監控網頁或背景工作的角色。 自訂效能計數器可能會用來追蹤和監視特定應用程式的行為和可以建立或刪除啟動工作、 網頁角色或背景工作以提高權限的角色。

Azure 診斷代理程式會重新整理的效能計數器組態從.wadcfg 檔案在啟動後一分鐘。  如果您建立自訂效能計數器 OnStart 方法，並啟動工作時間超過一分鐘執行，您的自訂效能計數器會尚未建立當 Azure 診斷代理程式嘗試載入它們。  在這個案例中，您會看到 Azure 診斷正確擷取您的自訂效能計數器以外的所有診斷資料。  若要解決此問題，建立效能計數器啟動任務或移動啟動工作的一些公司建立的效能計數器之後 OnStart 方法。

若要建立簡單的自訂效能計數器名為 「 \MyCustomCounterCategory\MyButton1Counter 」 下列步驟執行︰

1. 開啟您的應用程式的服務定義檔案 (CSDEF)。
2. 新增 WebRole 執行階段項目或 WorkerRole 項目，以允許執行提高權限的權限︰

    ```
    <runtime executioncontext="elevated"/>
    ```
3. 儲存檔案。
4. 開啟診斷檔案 （diagnostics.wadcfg 中 SDK 2.4 和以下或 diagnostics.wadcfgx 在 SDK 2.5），並將以下 DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. 儲存檔案。
6. 在您的角色的 OnStart 方法中建立自訂效能計數器分類，叫用基底之前。OnStart。 如果不存在，下列 C# 範例會建立自訂類別:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. 更新您的應用程式中的計數器。 下列範例會更新自訂效能計數器 Button1_Click 事件︰

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. 儲存檔案。  

自訂效能計數器資料現在會收集 Azure 診斷監視器。

## <a name="step-3-query-performance-counter-data"></a>步驟 3︰ 查詢效能計數器資料

一旦部署應用程式，並執行診斷監視器，就會開始收集效能計數器和保存 Azure 儲存的資料。 您可以使用工具，例如在 Visual Studio、 [Azure 儲存檔案總管](http://azurestorageexplorer.codeplex.com/)] 中或依 Cerebrata [Azure 診斷管理員](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx)伺服器檔案總管來檢視 WADPerformanceCountersTable 資料表中的效能計數器資料。 您可以也以程式設計方式查詢[C#](../storage/storage-dotnet-how-to-use-tables.d)、 [Java](../storage/storage-java-how-to-use-table-storage.md)、 [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md)、 [Python](../storage/storage-python-how-to-use-table-storage.md)、[並列文字](../storage/storage-ruby-how-to-use-table-storage.md)或[PHP](../storage/storage-php-how-to-use-table-storage.md)表格服務。

下列 C# 範例顯示簡單查詢 WADPerformanceCountersTable 資料表，並將診斷資料儲存為 CSV 檔案。 效能計數器會儲存為 CSV 檔案之後您可以使用 Microsoft Excel 中或其他工具的圖形功能，以視覺化方式呈現資料。 請務必新增參照 Microsoft.WindowsAzure.Storage.dll，這是 Azure SDK.net 2012 年 10 月中包含及更新版本。 組件已安裝的 %程式 Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ 目錄。

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

實體對應至 C# 使用衍生自**TableEntity**自訂類別的物件。 下列程式碼定義代表效能計數器**WADPerformanceCountersTable**資料表中的實體類別。


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>後續步驟
[檢視其他文件上 Azure 診斷](../ azure diagnostics.md）
