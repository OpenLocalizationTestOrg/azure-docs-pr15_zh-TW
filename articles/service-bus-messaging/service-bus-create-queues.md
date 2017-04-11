<properties 
    pageTitle="撰寫使用服務匯流排佇列的應用程式 |Microsoft Azure"
    description="如何撰寫簡單佇列中的應用程式，使用服務匯流排。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-queues"></a>建立使用服務匯流排佇列應用程式

本主題說明服務匯流排佇列，並說明如何撰寫簡單佇列中的應用程式，使用服務匯流排。

請考慮從零售必須使用的資料時都 replenished 股票圖，決定庫存管理系統傳送的個別 Point-of-Sale （位置） 標誌的銷售資料的情況。 此方案使用服務匯流排訊息的標誌和庫存管理系統之間的通訊如下圖所示︰

![服務匯流排佇列圖像 1](./media/service-bus-create-queues/IC657161.gif)

每個位置終端機傳送訊息給**DataCollectionQueue**報表的銷售資料。 庫存管理系統取出，直到，這些郵件會留在此佇列中。 這個模式通常，稱為 「*非同步訊息*，因為位置終端機沒有等待從繼續處理庫存管理系統的回覆。

## <a name="why-queuing"></a>為什麼佇列嗎？

我們看看程式碼，才能將此應用程式設定之前，請考慮使用佇列中，在這個案例中而不是由位置標誌的優點與直接 （同步） 庫存管理系統。

### <a name="temporal-decoupling"></a>暫時聯繫

使用 [非同步訊息模式，產生者和消費者沒有同時在線上。 訊息基礎結構可靠儲存郵件，直到耗用的廠商會收到通知。 這表示的分散式應用程式的元件可以中斷連線，請主動;例如，在進行的維修作業，或因為元件當機，而不影響整個系統。 此外，使用應用程式可能只需要為特定的一天的時間。 例如，這個零售案例中，庫存管理系統可能只有上線後的工作日結束。

### <a name="load-leveling"></a>載入資源撫平

在許多應用程式系統載入不盡相同一段時間，而針對每個單位的工作所需的時間通常是常數。 Intermediating 訊息產生者和佇列中使用的消費者表示使用應用程式 （工作） 只提供服務的平均負載，而不是最大使用量載入。 佇列中的深度會變大和收合為內送的載入不盡相同。 直接儲存金錢與基礎結構服務應用程式載入所需的量。

![服務匯流排佇列圖像 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>負載平衡

載入增加時，可以新增更多背景工作處理程序讀取工作者佇列。 每一封郵件處理僅有一個背景工作處理程序。 此外，此擷取負載平衡可讓最佳使用量的工作者電腦即使工作者電腦不同上處理能力，並與他們將會提取郵件在自己的最大速度。 這個模式通常稱為競爭消費者圖樣。

![服務匯流排佇列圖像 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>具備彈性

使用郵件佇列中繼訊息生產者與客戶之間提供元件之間的內建具備彈性。 因為產生者和消費者並不知道彼此，就可以升級消費者而不需產生器在任何效果。 此外，可發展訊息拓撲，而不會影響現有的結束點。 我們將討論此更時討論發佈/訂閱。

## <a name="show-me-the-code"></a>顯示我的程式碼

[動作] 區段會顯示如何使用服務匯流排建置此應用程式。

### <a name="sign-up-for-an-azure-account"></a>註冊 Azure 帳戶

若要開始使用的服務匯流排必須 Azure 帳戶。 如果您還沒有其中一個，您可以註冊免費的帳戶[以下](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF)。

### <a name="create-a-namespace"></a>建立命名空間

一旦您有訂閱，您可以[建立新的命名空間](service-bus-create-namespace-portal.md)。 每個命名空間做為一組服務匯流排實體範圍容器。 所有服務匯流排帳號都為新的命名空間唯一的名稱。 

### <a name="install-the-nuget-package"></a>安裝 NuGet 套件

若要使用的服務匯流排命名空間，應用程式必須參考服務匯流排組件，特別是 Microsoft.ServiceBus.dll。 您可以找到 Microsoft Azure sdk，您可以屬於此組件，下載[Azure SDK 下載頁面](https://azure.microsoft.com/downloads/)。 不過，[服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus)是最簡單的方法，取得服務匯流排 API，並設定您的應用程式與服務匯流排相依性的所有。

### <a name="create-the-queue"></a>建立佇列中

管理服務匯流排訊息實體 （佇列及發佈/訂閱主題） 被作業透過[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)類別。 服務匯流排使用[共用 Access 簽章 (SA)](service-bus-sas-overview.md)為基礎的安全性模型。 [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)類別代表安全性權杖提供者的內建的工廠方法傳回一些已知的權杖提供者。 如要保留的 SA 認證，我們將使用[CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx)方法。 然後公式[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)執行個體的結構與服務匯流排命名空間和權杖提供者的基底地址。

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)課程提供方法來建立、 列舉及刪除訊息的實體。 如何建立以及用來建立**DataCollectionQueue**佇列中的[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)執行個體，則會顯示的程式碼，如下所示。

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

請注意，有多載的[CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx)方法可讓要微調佇列中的屬性。 例如，您可以設定預設存留時間 (TTL) 值傳送到佇列中的郵件。

### <a name="send-messages-to-the-queue"></a>傳送郵件給佇列中

執行階段作業服務匯流排實體;例如，傳送及接收訊息，應用程式，必須先建立[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)物件。 類似於[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)類別， [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)執行個體是從建立服務命名空間和權杖提供者的基底地址。

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

郵件傳送到，，從服務匯流排收到佇列是[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)類別的執行個體。 此課程組成的一組標準的屬性 （例如[標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)和[TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)），用來保留應用程式屬性和本文任意應用程式資料的字典。 應用程式，可以設定將會用於[DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx)序列化物件透過傳入 （傳遞表示的銷售資料的位置終端機**SalesData**物件中的下列範例） 任何序列化的物件，本文。 或者，您可以提供[資料流](https://msdn.microsoft.com/library/azure/system.io.stream.aspx)物件。

若要將訊息傳送至指定的佇列中，在此例中**DataCollectionQueue**中，最簡單的方法是使用[CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx)直接從[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)執行個體建立[MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx)物件。

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>接收來自佇列中的訊息

若要將接收訊息從佇列中，您可以使用您直接建立使用[CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)物件。 在兩個不同的模式中工作，郵件接收器︰ **ReceiveAndDelete**和**PeekLock**。 建立郵件收件者，做為參數， [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx)通話時，會設定[ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 。


接收使用**ReceiveAndDelete**模式時，是單一擷取作業。就是服務匯流排收到要求時，它標示為所使用的訊息並傳回應用程式。 **ReceiveAndDelete**模式最簡單的模型，最適合的應用程式可容許不處理郵件，如果發生錯誤的情況。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 由於服務匯流排將郵件標示為所使用，當應用程式重新啟動和使用訊息一次啟動，它會有未接來電之前損毀的使用狀況的訊息。

**PeekLock**模式中接收會變成兩階段作業，可讓支援不允許遺失的郵件應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），完成接收程序的第二個階段，則可[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)電話上收到的郵件。 當服務匯流排看到[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)通話時，它會標示該郵件為所使用。

可能會出現兩個其他結果。 首先，如果應用程式無法處理郵件基於某種原因，它可呼叫[放棄](https://msdn.microsoft.com/library/azure/hh181837.aspx)上收到的郵件 （而非[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)）。 這會使解除鎖定訊息，並提供相同的消費者或另一個完成的消費者再次收到服務匯流排。 第二，逾時鎖定相關聯，而且如果應用程式無法處理郵件之前鎖定逾時到期 （例如，如果應用程式當機），然後服務匯流排會解除鎖定訊息，使其可接收一次 （基本上執行預設[放棄](https://msdn.microsoft.com/library/azure/hh181837.aspx)作業）。

請注意，是否應用程式當機處理郵件，但在[完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)之前發出要求之後，郵件會被重新傳遞至應用程式重新啟動時。 這通常稱為 「*至少一次*處理。 這表示將至少一次處理每一封郵件，但在某些情況下可能會被重新傳遞相同的郵件。 如果案例不允許重複處理，在應用程式中偵測到重複項目所需的額外邏輯。 這可以根據郵件的 [[訊息](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)] 屬性來達成。 此屬性的值過嘗試傳遞保持不變。 這被稱為*一次*處理。

如下所示的程式碼會接收，並處理使用**PeekLock**模式，這是預設值，如果沒有[ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)值明確所提供的訊息。

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### <a name="use-the-queue-client"></a>使用佇列中的用戶端

之前在本節中的範例會建立直接從傳送及接收郵件佇列中，分別[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx)和[MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)物件。 另一個方法是使用[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)類別中的支援傳送與接收除了更多進階功能，例如工作階段的作業。

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## <a name="next-steps"></a>後續步驟

現在，您學到的佇列基本概念，請參閱[建立應用程式的使用服務匯流排主題和訂閱](service-bus-create-topics-subscriptions.md)，以繼續使用服務匯流排主題和訂閱的發佈/訂閱功能此討論區。