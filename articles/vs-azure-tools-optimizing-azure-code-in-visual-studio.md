<properties
   pageTitle="最佳化您 Azure 的程式碼，在 Visual Studio |Microsoft Azure"
   description="了解如何 Azure 的程式碼的相關協助讓您的程式碼，更強大且效能最佳化工具在 Visual Studio 中。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>最佳化您 Azure 的程式碼

您設計程式時使用 Microsoft Azure 的應用程式，有一些程式碼撰寫的方式，應該先按照可協助避免發生應用程式延展性、 行為與雲端環境中的效能問題。 Microsoft 提供 Azure 程式碼分析工具，可辨識識別數個經常遇到問題，並可協助您解決。 您可以下載透過 NuGet Visual Studio 中的工具。

## <a name="azure-code-analysis-rules"></a>Azure 的程式碼分析規則

Azure 程式碼分析工具會自動標示 Azure 程式碼，它發現效能影響的已知的問題時使用下列規則。 偵測到問題顯示為警告或編譯器錯誤。 通常透過燈泡圖示提供程式碼修正或解決警告或錯誤的建議。

## <a name="avoid-using-default-in-process-session-state-mode"></a>避免使用預設值 （在 [程序） 工作階段狀態模式

### <a name="id"></a>識別碼

AP0000

### <a name="description"></a>描述

如果您使用的預設 （在 [程序） 工作階段狀態模式雲端應用程式時，您可能會遺失工作階段狀態。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

根據預設，在 [程序是 web.config 檔案中所指定的工作階段狀態模式。 此外，如果沒有設定檔中指定的項目，階段模式預設的程序。 在 [程序模式會在網頁伺服器上的記憶體儲存工作階段狀態。 重新啟動執行個體，或使用新的執行個體負載平衡或容錯移轉支援，不會儲存在網頁伺服器上的記憶體儲存的工作階段狀態。 這種情況下會防止應用程式可調整在雲端上。

ASP.NET 工作階段狀態支援數種不同的儲存工作階段狀態資料︰ InProc、 「 狀態伺服器 」、 SQLServer 自訂]，然後關閉。 建議您在外部工作階段狀態存放區，例如[意指 Azure 工作階段狀態提供者](http://go.microsoft.com/fwlink/?LinkId=401521)使用自訂主機資料的模式。

### <a name="solution"></a>解決方案

其中一個建議的解決方案是儲存在受管理的快取服務上的工作階段狀態。 瞭解如何使用[意指 Azure 工作階段狀態提供者](http://go.microsoft.com/fwlink/?LinkId=401521)儲存您的工作階段狀態。 您也可以在其他位置，以確保您的應用程式會在雲端上可調整儲存工作階段狀態。 若要深入瞭解替代方案，請閱讀[工作階段狀態模式](https://msdn.microsoft.com/library/ms178586)。

## <a name="run-method-should-not-be-async"></a>執行的方法不應該非同步

### <a name="id"></a>識別碼

AP1000

### <a name="description"></a>描述

建立外部[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法的非同步方法 （例如[等](https://msdn.microsoft.com/library/hh156528.aspx)），然後非同步方法從呼叫[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)。 宣告為非同步[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法會使輸入重新啟動迴圈工作者角色。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

呼叫[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法內非同步方法會使雲端服務執行階段資源回收工作者角色。 工作者角色啟動時，所有執行程式都會在[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法。 結束[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法會導致重新啟動的工作者角色。 當工作者角色執行階段非同步方法，將其分派非同步方法之後的所有作業，然後傳回。 這會使從[[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法結束並重新啟動的工作者角色。 在執行下一個反覆運算工作者角色重新叫用非同步方法，並重新啟動，導致重新回收的工作者角色。

### <a name="solution"></a>解決方案

將[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法以外的所有非同步作業。 然後呼叫重整的非同步方法從[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)方法，例如 RunAsync （).wait 內。 Azure 程式碼分析工具可協助您修正這個問題。

下列程式碼片段示範程式碼修正這個問題︰

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>使用服務匯流排共用存取簽章驗證

### <a name="id"></a>識別碼

AP2000

### <a name="description"></a>描述

使用共用 Access 簽章 (SA) 進行驗證。 Access 控制項服務 (ACS) 已被取代服務匯流排驗證。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

增強安全性的 Azure Active Directory 取代 ACS 驗證 SA 驗證。 切換方案的資訊，請參閱[Azure Active Directory 是未來的 ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) 。

### <a name="solution"></a>解決方案

在您的應用程式中使用 SA 驗證。 下列範例會示範如何使用現有的 SA 權杖存取服務匯流排命名空間或實體。

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

請參閱下列主題的詳細資訊。

- 概略資訊，請參閱[共用 Access 簽章驗證與服務匯流排](https://msdn.microsoft.com/library/dn170477.aspx)

- [如何使用共用的 Access 簽章驗證服務匯流排](https://msdn.microsoft.com/library/dn205161.aspx)

- 範例專案，請參閱[使用共用 Access 簽章 (SA) 驗證服務匯流排訂閱](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>請考慮使用 OnMessage 方法若要避免 」 會收到循環播放]

### <a name="id"></a>識別碼

AP2002

### <a name="description"></a>描述

若要避免進入 」 會收到循環播放 」，呼叫**OnMessage**方法是較佳的方案接收訊息呼叫**接收**方法。 不過，如果您必須使用**接收**的方法，且您指定的非預設伺服器等待的時間，請確定伺服器等待的時間超過一分鐘。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

當呼叫**OnMessage**，用戶端便會開始內部訊息幫浦，持續輪詢佇列中或訂閱。 此訊息幫浦包含無限迴圈的問題打電話將接收訊息。 如果通話逾時，它會發出新電話號碼。 取決於逾時間隔[MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)所使用的[OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx)屬性的值。

使用**OnMessage**比較**接收**的優點是，使用者不必手動投票的郵件、 處理的例外狀況、 處理多個訊息平行，並完成的郵件。

如果您不使用預設值呼叫**接收**，請務必*ServerWaitTime*該值超過一分鐘。 設定*ServerWaitTime*超過一分鐘防止伺服器逾時，才能完全接收訊息。

### <a name="solution"></a>解決方案

請參閱下列程式碼範例的建議的使用方式。 如需詳細資訊，請參閱[QueueClient.OnMessage 方法 (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)和[QueueClient.Receive 方法 (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx)。

若要改善效能 Azure 訊息基礎結構，請參閱設計模式[非同步訊息入門](https://msdn.microsoft.com/library/dn589781.aspx)。

以下是使用**OnMessage**將接收訊息的範例。

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

以下是使用預設伺服器等待的時間**接收**的範例。

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

以下是使用非預設的伺服器等待的時間**接收**的範例。

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>請考慮使用非同步服務匯流排方法

### <a name="id"></a>識別碼

AP2003

### <a name="description"></a>描述

使用 [非同步服務匯流排方法以提升效能與仲介訊息。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

使用 [非同步方法可讓應用程式並行，因為執行每個呼叫不會封鎖主要執行緒。 使用服務匯流排通訊方法，執行的作業時 （傳送、 接收、 刪除、 等） 花的時間。 這次服務匯流排服務要求和回覆的延遲除了包含作業的處理。 若要增加每次運算的數目，必須同時執行作業。 如需詳細資訊，請參閱[最佳作法效能的改良功能使用服務匯流排代理訊息](https://msdn.microsoft.com/library/azure/hh528527.aspx)。

### <a name="solution"></a>解決方案

如需如何使用建議的非同步方法的資訊，請參閱[QueueClient 類別 (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) 。

若要改善效能 Azure 訊息基礎結構，請參閱設計模式[非同步訊息入門](https://msdn.microsoft.com/library/dn589781.aspx)。

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>請考慮分割服務匯流排佇列及主題

### <a name="id"></a>識別碼

AP2004

### <a name="description"></a>描述

分割服務匯流排佇列，較佳的效能與服務匯流排訊息的主題。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

分割服務匯流排佇列 」 和 「 主題會增加效能處理量，以及服務的狀態，因為分割佇列中或主題的整體處理量不受限於單一郵件代理人或訊息存放區的效能。 此外，訊息存放區的暫時性的問題不讓分割佇列中或主題無法使用。 如需詳細資訊，請參閱[分割訊息的項目](https://msdn.microsoft.com/library/azure/dn520246.aspx)。

### <a name="solution"></a>解決方案

下列程式碼片段會顯示如何分割訊息的項目。

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

如需詳細資訊，請參閱[分割服務匯流排佇列 」 和 「 主題 |Microsoft Azure 部落格](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/)請參閱[Microsoft Azure 服務匯流排分割的佇列](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f)範例。

## <a name="do-not-set-sharedaccessstarttime"></a>未設定 SharedAccessStartTime

### <a name="id"></a>識別碼

AP3001

### <a name="description"></a>描述

您應避免使用立即啟動的共用存取原則 SharedAccessStartTimeset 目前的時間。 您只需要設定此屬性，如果您想要在稍後啟動的共用存取原則。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

時鐘同步處理會導致之間的資料中心稍微時間差異。 例如，您想要以邏輯方式認為與目前的時間設定啟動時間的儲存空間 SA 原則，使用 DateTime.Now 或類似的方法會導致 SA 原則立即生效。 不過，資料中心的稍微時間差異可能會導致這個問題，因為某些資料中心時間可能稍有晚於開始時間，而其他人在它。 如此一來，SA 原則可以過期快速 （或甚至立即） 如果原則生命週期設定太短。

取得更多在 Azure 儲存體中使用共用的 Access 簽章的詳細指示，請參閱[介紹表格 SA （共用存取簽章），佇列中 SA 及更新 Blob SA-Microsoft Azure 儲存體小組部落格-網站首頁-MSDN 部落格](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)。

### <a name="solution"></a>解決方案

移除設定共用的 access 原則的開始時間的陳述式。 Azure 程式碼分析工具提供的修正這個問題。 如需有關安全性管理的詳細資訊，請參閱設計模式[Valet 鍵圖樣](https://msdn.microsoft.com/library/dn568102.aspx)。

下列程式碼片段示範程式碼修正這個問題。

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>共用存取原則必須超過五分鐘到期時間。

### <a name="id"></a>識別碼

AP3002

### <a name="description"></a>描述

可以有很多為 5 分鐘中時鐘在不同的位置，因為稱為 「 時鐘 skew。 」 條件的資料中心之間的差異 若要防止 SA 從到期原則權杖計劃之前設定的到期時間超過五分鐘。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

世界各地的不同位置的資料中心時鐘訊號同步處理。 因為需要花時間時鐘訊號接收到不同的位置，可以有時間之間的差異不同地理位置的資料中心雖然應該同步處理所有項目。 此時間差異可能會影響共用存取原則開始時間和到期日的間隔。 因此，若要確保共用存取原則會立即生效，不指定的開始時間。 此外，請確定的到期時間超過 5 分鐘，避免提早逾時。

如需有關如何在 Azure 儲存體中使用共用的 Access 簽章的詳細資訊，請參閱[介紹表格 SA （共用 Access 簽章），佇列中 SA 及更新 Blob SA-Microsoft Azure 儲存體小組部落格-網站首頁-MSDN 部落格](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)。

### <a name="solution"></a>解決方案

如需有關安全性管理的詳細資訊，請參閱設計模式[Valet 鍵圖樣](https://msdn.microsoft.com/library/dn568102.aspx)。

以下是範例不指定共用存取原則開始時間。

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

以下是範例使用原則到期工期超過五分鐘指定共用存取原則開始時間。

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

如需詳細資訊，請參閱[建立及使用共用的 Access 簽章](https://msdn.microsoft.com/library/azure/jj721951.aspx)。

## <a name="use-cloudconfigurationmanager"></a>使用 CloudConfigurationManager

### <a name="id"></a>識別碼

AP4000

### <a name="description"></a>描述

使用[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx)類別，例如 Azure 網站和 Azure 行動服務的專案，就不會導致執行階段問題。 最佳作法是，不過，則建議使用雲端[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx)整合的管理所有 Azure 雲端應用程式的設定。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

CloudConfigurationManager 會讀取適當的應用程式環境的設定檔。

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>解決方案

重構[CloudConfigurationManager 課程](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)使用程式碼。 Azure 程式碼分析工具會提供程式碼修正這個問題。

下列程式碼片段示範程式碼修正這個問題。 取代

`var settings = ConfigurationManager.AppSettings["mySettings"];`

使用

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

以下是如何儲存設定的設定 App.config 或 Web.config 檔案中的範例。 新增設定的設定檔和 appSettings 區段。 以下是先前的程式碼範例的 Web.config 檔案。

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>避免使用硬式編碼連接字串

### <a name="id"></a>識別碼

AP4001

### <a name="description"></a>描述

如果您使用的硬式編碼的連接字串，您需要更新之後，您必須變更您的程式碼，然後重新編譯應用程式。 不過，如果您儲存設定檔中的連接字串，您可以變更其稍後只要更新設定檔。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

硬式編碼連接字串是不正確的做法，因為它提出問題時需要快速變更的連接字串。 此外，如果需要來源控制存回專案，硬式編碼連接字串會造成安全性漏洞由於字串可以檢視程式碼。

### <a name="solution"></a>解決方案

儲存在設定檔或 Azure 環境中的連接字串。

- 獨立的應用程式，使用 app.config 儲存連接字串設定。

- IIS 裝載 web 應用程式，使用 [web.config 儲存連接字串。

- ASP.NET vNext 應用程式，使用 [configuration.json 儲存連接字串。

使用 web.config 或 app.config 等的設定檔的資訊，請參閱[ASP.NET Web 設定指導方針](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx)。 如需如何 Azure 環境變數工作，請參閱[Azure 網站︰ 如何應用程式字串與連接字串運作](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)。 連接字串儲存在來源控制項的詳細資訊，請參閱[避免將機密資訊，例如在來源程式碼存放庫中儲存的檔案中的連接字串](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)。

## <a name="use-diagnostics-configuration-file"></a>使用 [診斷設定檔

### <a name="id"></a>識別碼

AP5000

### <a name="description"></a>描述

而不是使用程式設計 API Microsoft.WindowsAzure.Diagnostics，在您的程式碼，例如設定診斷程式設定，您應該 diagnostics.wadcfg 檔案中設定診斷程式設定。 （或者，如果您是使用 Azure SDK 2.5 diagnostics.wadcfgx）。 這麼一來，您可以變更診斷程式設定，而不需要重新編譯程式碼。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

使用數種不同的方法，則可以設定 Azure SDK 2.5 （即使用 Azure 診斷 1.3）、 Azure 診斷 (WAD) 之前︰ 加中儲存空間，設定 blob 中使用必要的程式碼、 宣告式設定或預設的設定。 不過，若要設定診斷慣用的方式是使用 XML 設定檔案 （diagnostics.wadcfg 或 diagnositcs.wadcfgx SDK 2.5 或更新版本） 應用程式在 project 中。 在這種方式，diagnostics.wadcfg 檔案完全定義設定可以會更新，並將部署。 混合 diagnostics.wadcfg 設定檔的使用與設定的程式設計的方式，使用[DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)或[RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)課程會造成混淆。 如需詳細資訊，請參閱[初始化或變更 Azure 診斷程式設定](https://msdn.microsoft.com/library/azure/hh411537.aspx)。

開頭 WAD 1.3 （隨附於 Azure SDK 2.5），則不會再可能使用程式碼設定診斷。 如此一來，您可以只提供設定時套用或更新診斷副檔名。

### <a name="solution"></a>解決方案

使用診斷設定設計工具，將診斷設定移至 [診斷設定檔案 （diagnositcs.wadcfg 或 diagnositcs.wadcfgx SDK 2.5 或更新版本）。 建議您安裝[Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) ，並使用最新的 [診斷] 功能。

1. 在您想要設定的角色的快顯功能表，選擇 [內容]，然後選擇 [設定] 索引標籤。

1. 在 [**診斷**] 區段中，請確定已選取 [**啟用診斷**] 核取方塊。

1. 選擇 [**設定**] 按鈕。

  ![存取 [啟用診斷] 選項](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  如需詳細資訊，請參閱[設定診斷 Azure 雲端服務與虛擬機器](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>避免宣告為靜態 DbContext 物件

### <a name="id"></a>識別碼

AP6000

### <a name="description"></a>描述

若要儲存的記憶體，避免宣告為靜態 DBContext 物件。

請共用您的想法和[Azure 程式碼分析意見反應](http://go.microsoft.com/fwlink/?LinkId=403771)的意見反應。

### <a name="reason"></a>原因

DBContext 物件保留每個呼叫的查詢結果。 卸載應用程式網域之前，不會處置靜態 DBContext 物件。 因此，靜態 DBContext 物件可以使用大量的記憶體。

### <a name="solution"></a>解決方案

宣告 DBContext 為區域變數或非靜態執行個體欄位，用於任務，然後將它會丟棄後使用。

下列範例 MVC 控制器類別顯示如何使用 DBContext 物件。

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>後續步驟

若要瞭解 optimzing 和疑難排解 Azure 應用程式的詳細資訊，請參閱[疑難排解 Azure 應用程式服務中的 web 應用程式使用 Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)。
