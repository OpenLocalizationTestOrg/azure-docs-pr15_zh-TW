<properties
    pageTitle="使用.NET 服務匯流排主題 |Microsoft Azure"
    description="瞭解如何使用.NET Azure 中的服務匯流排主題和訂閱。 程式碼範例會寫入.NET 應用程式。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>如何使用服務匯流排主題和訂閱

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文將說明如何使用服務匯流排主題和訂閱。 範例以 C# 撰寫，並使用.NET Api。 涵蓋的案例，包括建立主題和訂閱]，建立訂閱篩選、 傳送訊息給主題、 從訂閱，接收訊息及刪除主題和訂閱。 如需主題和訂閱的詳細資訊，請參閱[下一步](#next-steps)] 區段。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>設定應用程式使用服務匯流排

使用服務匯流排的應用程式建立後，您必須新增服務匯流排組件的參考，及包含對應的命名空間。 若要執行此動作的最簡單方法是下載適當的[NuGet](https://www.nuget.org)套件。

## <a name="get-the-service-bus-nuget-package"></a>取得服務匯流排 NuGet 套件

[服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus)是最簡單的方法，取得服務匯流排 API，並設定您的應用程式的所有必要的服務匯流排相依性。 若要安裝的服務匯流排 NuGet 套件您專案中，執行下列動作︰

1.  在方案總管中，以滑鼠右鍵按一下 [**參考資料**，然後按一下 [**管理 NuGet 套件**。
2.  搜尋 「 服務匯流排 」，並選取**Microsoft Azure 服務匯流排**項目。 按一下 [**安裝**] 以完成安裝，然後關閉 [動作] 對話方塊︰

    ![][7]

您已準備好要撰寫程式碼的服務。

## <a name="create-a-service-bus-connection-string"></a>建立服務匯流排連線字串

服務匯流排儲存結束點和認證使用連接字串。 您可以在設定檔，而不是硬式編碼，將您的連線字串︰

- 使用 Azure 服務時，建議您儲存在您使用 Azure 服務設定系統 （.csdef 和.cscfg 檔案） 的連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您儲存在您使用.NET 設定系統 （例如 Web.config 檔案） 的連接字串。

在這兩種情況下，您可以擷取您的 [連線字串使用`CloudConfigurationManager.GetSetting`方法，本文稍後所示。

### <a name="configure-your-connection-string"></a>設定您的連線字串

服務設定機制可讓您以動態方式從[Azure 入口網站][]變更設定的設定，而不重新部署您的應用程式。 例如，新增`Setting`標籤到您的服務定義 (**.csdef**) 檔案，在下一個範例所示。

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

然後，您指定的值在服務設定 (.cscfg) 檔案。

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

使用 [共用 Access 簽章 (SA) 機碼名稱及擷取從入口網站，先前所述的關鍵值。

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>使用 Azure 網站或 Azure 虛擬機器時，設定您的連線字串

使用網站或虛擬機器時，建議您使用.NET 設定系統 (例如 Web.config)。 您儲存連線字串使用`<appSettings>`項目。

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

使用 [SA 名稱] 和 [從[Azure 入口網站][]，而擷取的關鍵值，先前所述。

## <a name="create-a-topic"></a>建立主題

您可以執行服務匯流排主題和使用[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)類別訂閱管理作業。 本課程提供方法來建立、 列舉和刪除主題。

下列範例會建構`NamespaceManager`物件使用 Azure`CloudConfigurationManager`類別連線字串，其中包含的服務匯流排命名空間和適當 SA 的基底地址的認證與權限管理。 此連線字串為下列格式︰

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

使用下列範例中，指定上一節中的設定。

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

有多載的[CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx)方法可讓您可以設定屬性的主題。例如，若要設定預設存留時間 (TTL) 值套用至主題的郵件。 使用[TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)類別，就會套用這些設定。 下列範例會示範如何建立使用大小上限為 5 GB 與預設訊息 1 分鐘 TTL 命名**TestTopic**主題。

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] 若要檢查的主題，以指定名稱是否已有命名空間中，您可以[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)物件上使用[TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx)方法。

## <a name="create-a-subscription"></a>建立的訂閱

您也可以建立使用[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)類別的主題訂閱。 訂閱名稱，並且可以有限制郵件傳遞給訂閱的虛擬佇列中的設定選擇性篩選。

> [AZURE.IMPORTANT] 郵件接收所訂閱的順序，您必須建立該訂閱，然後再傳送至主題的任何郵件。 如果有任何訂閱主題，請主題捨棄這些郵件。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>使用預設 (MatchAll) 篩選器建立訂閱

如果沒有篩選指定當您建立新的訂閱時， **MatchAll**篩選是預設的篩選器所使用。 當您使用**MatchAll**篩選時，發佈至主題中的所有郵件都會都放在訂閱的虛擬佇列中。 下列範例會建立名為 「 AllMessages 」 的訂閱，並使用預設**MatchAll**篩選器。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>建立篩選的訂閱

您也可以設定篩選可讓您可以指定哪些主題的郵件應該出現在特定主題訂閱。

最有彈性的訂閱支援類型是篩選的[SqlFilter][]類別，來實作 SQL92 子集。 SQL 篩選運作內容的發佈至主題的郵件。 如需有關可與 SQL 篩選運算式的詳細資訊，請參閱[SqlFilter.SqlExpression][]語法。

下列範例會建立一個以只選取 [自訂**MessageNumber**屬性大於 3 的訊息[SqlFilter][]物件命名**HighMessages**的訂閱。

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

同樣地，下列範例會建立一個名為**LowMessages**與只會選取郵件的**MessageNumber**屬性小於或等於 3 [SqlFilter][]的訂閱。

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

現在當訊息會傳送至`TestTopic`，它永遠傳送至接收器訂閱**AllMessages**主題訂閱]，然後選擇性地傳遞到接收器訂閱**HighMessages**和**LowMessages**主題訂閱 （根據郵件內容）。

## <a name="send-messages-to-a-topic"></a>傳送郵件給主題

若要將訊息傳送給服務匯流排主題，您的應用程式會建立[TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)物件使用連接字串。

下列程式碼將示範如何建立較舊版本使用建立**TestTopic**主題[TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)物件[`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx)API 通話。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

傳送給服務匯流排主題的郵件是[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)類別的執行個體。 用來保留自訂的應用程式的特定屬性，字典和本文任意應用程式的資料， [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)物件都有一組標準的屬性 （例如[標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)和[TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)）。 應用程式，可以設定郵件的本文任何序列化物件傳遞給建構函式的[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)物件，然後適當**DataContractSerializer**然後用於序列化物件。 或者，您可以提供**System.IO.Stream** 。

下列範例會示範如何將五個測試訊息傳送給**TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)物件，取得在先前的程式碼範例。 請注意，每一封郵件的[MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx)屬性值異的循環播放反覆運算 （此決定哪些訂閱收到其）。

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

服務匯流排主題支援[進階版層](service-bus-premium-messaging.md)中的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的主題中的郵件數但還有首字放大的總大小由主題的郵件。 此主題檔案大小定義在建立時，使用 5 GB 的上限。 如果啟用分割，上限為較高。 如需詳細資訊，請參閱[分割訊息的實體](service-bus-partitioning.md)。

## <a name="how-to-receive-messages-from-a-subscription"></a>如何從訂閱接收郵件

從訂閱接收郵件的建議的方式是使用[SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx)物件。 在兩個不同的模式中工作， [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx)物件︰ [ *ReceiveAndDelete*和*PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)。

使用 「 **ReceiveAndDelete**模式時，收到時是單一擷取的作業。也就是說，當服務匯流排訂閱中收到郵件的讀取的要求，並標示該郵件時所使用且傳回應用程式。 **ReceiveAndDelete**模式最簡單的模型，最適合的案例的應用程式可容許不處理失敗訊息。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排已標示郵件，為 consumed，重新啟動應用程式，並開始再次使用訊息時，它會遺失的使用狀況之前損毀的訊息。

在**PeekLock**模式 （這是預設的模式），接收程序會變成兩階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），完成接收程序的第二個階段，則可[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)電話上收到的郵件。 當服務匯流排看到**完成**通話時，它會標示該郵件時所使用，並將它從訂閱移除。

下列範例會示範如何能接收郵件，並使用預設**PeekLock**模式處理。 若要指定不同的[ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)值，您可以使用另一個超量的狀況的[CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx)。 轉換**HighMessages**訂閱到達時，此範例使用[OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx)回撥處理郵件。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

此範例中設定使用[OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx)物件[OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx)回撥。 若要啟用手動控制何時要收到的郵件上呼叫[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)的[自動完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx)設定為**false** 。 [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx)設定為 [1 分鐘，會造成等候一分鐘之前結束自動續約功能的用戶端，並在用戶端提出新的電話號碼，以檢查郵件。 此屬性值會減少用戶端呼叫收費的並不會擷取郵件的次數。

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果基於某種原因處理訊息無法接收的應用程式，它可以在收到的郵件 （而非 [[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)] 方法） 呼叫[放棄](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx)的方法。 這會使服務匯流排，若要解除鎖定訂閱中的訊息，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有訂閱，請在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾時到期 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，並可接收一次。

應用程式當機之後處理郵件，但發行[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)要求之前，郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為*至少一次處理*;也就是說，每一封郵件處理至少一次，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應該將其他邏輯新增至其應用程式來處理重複的郵件傳遞。 這通常被達成使用 [[訊息](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)] 屬性的郵件中，在嘗試傳遞維持不變。

## <a name="delete-topics-and-subscriptions"></a>刪除主題及訂閱

下列範例會示範如何主題**TestTopic**刪除**HowToSample**服務命名空間。

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

刪除主題時，也會刪除任何訂閱註冊的主題。 您也可以個別刪除訂閱。 下列程式碼將示範如何刪除命名**HighMessages**從**TestTopic**主題的訂閱。

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>後續步驟

現在，您學到服務匯流排主題和訂閱的基本概念，請遵循這些連結，瞭解更多。

-   [佇列主題，以及訂閱][]。
-   [主題篩選範例][]
-   [SqlFilter][]API 參考。
-   建立可用的應用程式的傳送和接收服務匯流排佇列中的郵件︰[服務匯流排代理訊息.NET 教學課程][]。
-   服務匯流排範例︰ 從[Azure 範例][]下載或請參閱[概觀](service-bus-samples.md)。

  [Azure 入口網站]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [佇列、 主題及訂閱]: service-bus-queues-topics-subscriptions.md
  [主題篩選範例]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [服務匯流排代理訊息.NET 教學課程]: service-bus-brokered-tutorial-dotnet.md
  [Azure 範例]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
