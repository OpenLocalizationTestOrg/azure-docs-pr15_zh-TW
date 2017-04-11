<properties 
    pageTitle="如何使用 WebJobs SDK Azure 服務匯流排" 
    description="瞭解如何使用 WebJobs SDK 的 Azure 服務匯流排佇列 」 和 「 主題。" 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>如何使用 WebJobs SDK Azure 服務匯流排

## <a name="overview"></a>概觀

本指南可提供 C# 程式碼範例，顯示觸發程序時收到 Azure 服務匯流排訊息的方式。 程式碼範例使用[WebJobs SDK](websites-dotnet-webjobs-sdk.md)版本 1.x。

本指南假設您已經知道[如何建立 WebJob 專案在 Visual Studio 中，指向您儲存的帳戶的連線字串](websites-dotnet-webjobs-sdk-get-started.md)。

僅顯示的程式碼片段的函數，不會建立的程式碼`JobHost`物件範例如下︰

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

[完成的服務匯流排程式碼範例](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs)是在 GitHub.com azure webjobs-sdk 範例存放庫中。

## <a id="prerequisites"></a>必要條件

若要使用服務匯流排必須安裝的其他 WebJobs SDK 套件除了[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet 套件。 

您也可以設定 AzureWebJobsServiceBus 連接字串，除了儲存空間的連接字串。  您可以`connectionStrings`區段 App.config 檔案中，如下列範例所示︰

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

範例專案 App.config 檔案中包含服務匯流排連接字串設定，請參閱[服務匯流排範例](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus)。 

也可以在 Azure 執行階段環境中，然後會覆寫 App.config WebJob 執行 Azure; 中時設定的連線字串如需詳細資訊，請參閱[開始使用 WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account)。

## <a id="trigger"></a>如何觸發程序時收到的服務匯流排佇列中郵件的函數

若要撰寫 WebJobs SDK 通話時收到的佇列中郵件的函數，請使用`ServiceBusTrigger`屬性。 屬性建構函式的參數，指定以獲得佇列中的名稱。

### <a name="how-servicebustrigger-works"></a>ServiceBusTrigger 的運作方式

SDK 接收訊息中的`PeekLock`模式和通話`Complete`函數成功，完成時，當訊息或來電`Abandon`如果功能將會失敗。 如果函數執行超過`PeekLock`逾時，鎖定就會自動更新。

服務匯流排會無法控制，或由 WebJobs SDK 設定自己有害佇列中處理。 

### <a name="string-queue-message"></a>字串佇列中的訊息

下列程式碼範例讀取佇列中訊息，包含字串與字串寫入 WebJobs SDK 儀表板。

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**附註︰**如果您不是使用 WebJobs SDK 的應用程式中建立佇列中的郵件，請確定[BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx)設定為 [」 文字/純文字]。

### <a name="poco-queue-message"></a>POCO 佇列中的郵件

SDK 會自動還原序列化佇列中訊息，其中包含 POCO JSON [(純文字舊的 CLR 物件](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 類型。 下列程式碼範例讀取佇列中郵件包含`BlobInformation`物件`BlobName`屬性︰

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

顯示如何使用內容 POCO 以使用二進位大型物件和在同一個函數中的表格的程式碼範例，請參閱[本文的儲存空間佇列版本](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs)。

如果您建立佇列中郵件的程式碼不是使用 WebJobs SDK，使用類似以下的範例︰

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>類型 ServiceBusTrigger 可搭配使用

除了`string`POCO 類型] 中，您可以使用`ServiceBusTrigger`位元組陣列的屬性或`BrokeredMessage`物件。

## <a id="create"></a>如何建立服務匯流排佇列中的郵件

若要撰寫函數來建立新的佇列中郵件使用`ServiceBus`屬性和傳入的佇列名稱屬性建構函式。 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>建立非非同步函數中的單一佇列郵件

下列程式碼範例使用輸出參數佇列中名為 「 outputqueue 」 具有相同名稱為 「 inputqueue 」 佇列中收到的郵件為內容建立新郵件。

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

建立單一佇列訊息的輸出參數可以是下列類型︰

* `string`
* `byte[]`
* `BrokeredMessage`
* 序列化 POCO 輸入您所定義。 自動序列化為 JSON。

POCO 輸入參數，佇列中郵件則一律會建立函數結束;參數是空值，如果 SDK 就會建立佇列中訊息時收到郵件，並還原序列化會傳回 null。 針對其他類型，如果參數是空值會建立不佇列中的訊息。

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>建立多個佇列中的郵件或非同步函數

若要建立多封郵件，請使用`ServiceBus`屬性與`ICollector<T>`或`IAsyncCollector<T>`下列程式碼範例所示︰

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

每個佇列中的訊息會立即建立時`Add`呼叫方法。

## <a id="topics"></a>如何使用服務匯流排主題

若要撰寫 SDK 通話時收到的郵件服務匯流排主題的函數，請使用`ServiceBusTrigger`與使用主題名稱，以及訂閱名稱，如以下的程式碼範例所示建構函式的屬性︰

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

若要建立主題的郵件，請使用`ServiceBus`主題名稱相同的方式使用佇列名稱的屬性。

## <a name="features-added-in-release-11"></a>在 [版本 1.1 （英文） 中新增功能

版本 1.1 （英文） 中加入下列功能︰

* 允許透過處理郵件的深自訂`ServiceBusConfiguration.MessagingProvider`。
* `MessagingProvider`支援的服務匯流排自訂`MessagingFactory`和`NamespaceManager`。
* A`MessageProcessor`策略模式 」 可讓您指定的處理器每佇列/主題。
* 郵件處理並行預設的支援。 
* 輕鬆自訂的`OnMessageOptions`透過`ServiceBusConfiguration.MessageOptions`。
* 允許在指定的[AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) `ServiceBusTriggerAttribute` / `ServiceBusAttribute` （的情況，您可能沒有管理權限）。 

## <a id="queues"></a>相關的主題覆蓋儲存佇列使用方法文章

如 WebJobs SDK 案例服務匯流排不是特定的相關資訊，請參閱[如何使用與 WebJobs SDK Azure 佇列中的儲存空間](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。 

下列文件中涵蓋下列主題︰

* 非同步函數
* 多個執行個體
* 正常的關閉
* 使用函數的內文中的 WebJobs SDK 屬性
* 在 [程式碼中設定 SDK 連線字串
* 設定值 WebJobs SDK 建構函式參數程式碼
* 手動觸發函數
* 寫入記錄檔

## <a id="nextsteps"></a>後續步驟

本指南已提供程式碼範例，顯示如何處理 Azure 服務匯流排所使用的常見的案例。 如需有關如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。
 
