<properties 
    pageTitle="建立使用服務匯流排主題和訂閱的應用程式 |Microsoft Azure"
    description="發佈簡介-訂閱服務匯流排主題和訂閱所提供的功能。"
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
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>建立使用服務匯流排主題和訂閱的應用程式

Azure 服務匯流排支援一組雲端、 訊息導向介軟體技術，包括可靠的訊息和長期發佈/訂閱訊息。 本文是根據[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)中所提供的資訊，並提供發佈/訂閱所提供的功能服務匯流排主題的簡介。

## <a name="evolving-retail-scenario"></a>發展零售案例

這份文件會繼續[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)中使用此零售案例。 回收的銷售資料的個別點的銷售 （位置） 終端機必須路由庫存管理系統會使用該資料時都 replenished 股票圖，決定。 每個位置終端機報表的銷售資料傳送訊息給**DataCollectionQueue**佇列中，在他們保持鍵，直到已接收庫存管理系統，如下所示︰

![服務匯流排 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

若要發展這種情況下，新的需求已新增至系統︰ 存放區擁有人想要可以監視存放區中進行即時的執行方式。

若要解決此需求，系統必須 「 點選 「 銷售資料流。 我們仍想要的位置終端機傳送到庫存管理系統之前，傳送每封郵件，但我們希望我們可以用來呈現存放區的擁有者的儀表板檢視的每封郵件的另一份複本。

在任何情況下，例如此，您需要每一封郵件来使用多方，您可以使用服務匯流排*主題*。 主題提供發佈/訂閱的圖樣的每個已發佈的訊息會提供一或多個訂閱註冊的主題。 相反地，與佇列每一封郵件是單一使用者收到。

郵件傳送主題相同的方式傳送至佇列。 不過，沒有收到郵件從主題直接;已接收從訂閱。 您可以將收到的郵件傳送給該主題的副本虛擬佇列中為主題的訂閱。 郵件都會收到從訂閱的相同方式這些接收到佇列中。

回到零售案例，佇列中會由主題，並新增訂閱，可以使用庫存管理系統元件。 系統現在會出現，如下所示︰

![服務匯流排 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

設定以下執行相同的上一個佇列中為基礎的設計。 也就是將主題傳送的郵件路由到**庫存**訂閱，請從**庫存管理系統**會使用它們。

才支援管理儀表板，我們建立第二個訂閱的主題，如下所示︰

![服務匯流排 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

使用此設定時，從位置終端機每一封郵件是提供 [**儀表板**] 和 [**庫存**訂閱。

## <a name="show-me-the-code"></a>顯示我的程式碼

[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)本文會說明如何註冊 Azure 帳戶及建立服務命名空間。 若要使用的服務匯流排命名空間，應用程式必須參考服務匯流排組件，特別是 Microsoft.ServiceBus.dll。 參照服務匯流排相依性的最簡單方法是安裝服務匯流排[Nuget 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)。 您也可以找到 Azure SDK 的組件的組件。 下載的[Azure SDK 下載頁面](https://azure.microsoft.com/downloads/)。

### <a name="create-the-topic-and-subscriptions"></a>建立主題和訂閱

管理服務匯流排訊息實體 （佇列及發佈/訂閱主題） 被作業透過[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)類別。 適當認證，才能建立特定的命名空間[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)執行個體。 服務匯流排使用[共用 Access 簽章 (SA)](service-bus-sas-overview.md)為基礎的安全性模型。 [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)類別代表安全性權杖提供者的內建的工廠方法傳回一些已知的權杖提供者。 如要保留的 SA 認證，我們將使用[CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx)方法。 然後公式[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)執行個體的結構與服務匯流排命名空間和權杖提供者的基底地址。

[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)課程提供方法來建立、 列舉及刪除訊息的實體。 如何建立以及用來建立**DataCollectionTopic**主題[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)執行個體，則會顯示的程式碼，如下所示。

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

請注意，有多載的[CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx)方法可讓您設定屬性的主題。 例如，您可以設定預設存留時間 (TTL) 值的主題傳送的郵件。 接下來，新增**庫存**和**儀表板**的訂閱。

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>傳送郵件給主題

執行階段作業服務匯流排實體;例如，傳送及接收訊息，應用程式，必須先建立[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)物件。 類似於[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)類別， [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)執行個體是從建立服務命名空間和權杖提供者的基底地址。

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

若要傳送的訊息，收到來自服務匯流排主題， [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)類別的執行個體。 此課程組成的一組標準的屬性 （例如[標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)和[TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)），用來保留應用程式屬性和本文任意應用程式資料的字典。 應用程式，可以設定將會用於[DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx)序列化物件透過傳入 （傳遞表示的銷售資料的位置終端機**SalesData**物件中的下列範例） 任何序列化的物件，本文。 或者，您可以提供[資料流](https://msdn.microsoft.com/library/azure/system.io.stream.aspx)物件。

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

傳送郵件給主題最簡單的方法是使用[CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx)直接從[MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)執行個體建立[MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx)物件。

```
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>從訂閱接收郵件

與使用類似佇列中，將接收訊息，您可以使用您直接建立使用[CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)物件將訂閱。 您可以使用兩個不同的其中一個接收模式 （**ReceiveAndDelete**及**PeekLock**），[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)中所述。

請注意，當您建立的訂閱[MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) ， *entityPath*參數是表單的`topicPath/subscriptions/subscriptionName`。 因此，若要建立的**DataCollectionTopic**主題**庫存**訂閱[MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) ， *entityPath*必須設定為 [ `DataCollectionTopic/subscriptions/Inventory`。 程式碼如下所示︰

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
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

## <a name="subscription-filters"></a>訂閱篩選

到目前為止，在這個案例中傳送至主題中的所有郵件，都可用於所有已註冊的訂閱。 索引鍵的片語是 「 供。 」 雖然服務匯流排訂閱，請參閱所有傳送至主題的訊息，您可以將這些郵件的子集複製虛擬訂閱佇列中。 這是使用訂閱*篩選*所執行的。 當您建立訂閱時，您可以提供 SQL92 樣式述詞的郵件中，同時系統內容 （例如，[標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)） 和應用程式屬性，例如在先前範例**storename] 拖曳**屬性上運作的表單中的篩選運算式。

第二個存放區發展以描繪出此案例，是新增至零售案例。 從兩個儲存的位置終端機的全部的銷售資料仍有路由傳送至集中的庫存管理系統，但使用儀表板工具經理僅有興趣的存放區的效能。 您可以使用篩選，以達到此目標訂閱。 請注意，當位置終端機發佈郵件時，他們設定**storename] 拖曳**應用程式屬性訊息。 指定兩個存放區，例如**雷蒙市**和**西雅圖**，雷蒙市中的位置終端機存放的戳記**西雅圖**等於**storename] 拖曳**而西雅圖存放位置終端機使用**storename] 拖曳**等於**雷蒙市**，與他們銷售資料的郵件。 雷蒙市存放區的市集管理員只想要查看其位置終端機的資料。 系統會顯示如下︰

![服務 Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

若要設定此路由，建立**儀表板**訂閱，如下所示︰

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

使用此訂閱篩選]，僅有 [ **storename] 拖曳**] 屬性設定為 [**雷蒙市**的郵件會複製到虛擬佇列中的**儀表板**訂閱。 還有更多訂閱篩選，不過。 應用程式可以有多個篩選規則，每個訂閱，除了訂閱的虛擬佇列經過修改的郵件內容的能力。

## <a name="summary"></a>摘要

所有使用佇列[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)所述的原因也適用於主題，特別是︰

- 暫時耦合 – 訊息產生者和消費者不需要同時在線上。

- 載入資源撫平 – 平滑的載入啟用使用的應用程式，而不是最大使用量載入平均負載提供的主題。

- 負載平衡 – 類似於佇列中，您可以讓多個競爭的消費者聆聽單一的訂閱，使用其中一個消費者，藉此平衡載入交出每一封郵件。

- 具備彈性 – 您可以發展訊息的網路，而不會影響現有的端點。例如，新增訂閱，或變更允許新的消費者用主題的篩選。

## <a name="next-steps"></a>後續步驟

如需如何使用佇列中的位置零售案例的資訊，請參閱[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)。