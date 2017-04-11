<properties 
    pageTitle="AMQP 1.0 支援服務匯流排分割佇列 」 和 「 主題 |Microsoft Azure" 
    description="瞭解如何使用進階訊息佇列通訊協定 (AMQP) 1.0 與服務匯流排分割佇列 」 和 「 主題。" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>AMQP 1.0 支援服務匯流排分割佇列 」 和 「 主題 

Azure 服務匯流排現在支援的進階訊息佇列通訊協定 (**AMQP**) 1.0 版服務匯流排**分割佇列 」 和 「 主題。**

**AMQP**是開啟標準訊息佇列通訊協定，可讓您開發跨平台應用程式使用不同的程式設計語言。 一般資訊 AMQP 支援服務匯流排，請參閱[支援服務匯流排 AMQP 1.0](service-bus-amqp-overview.md)。

**分割佇列 」 和 「 主題**，也就是*分割的實體*，提供更高的可用性、 可靠性和處理量比傳統的非分割佇列 」 和 「 主題。 如需有關分割的實體的詳細資訊，請參閱[分割訊息的項目](service-bus-partitioning.md)。

為與分割的佇列 」 和 「 主題通訊的通訊協定的 AMQP 1.0 新增可讓您建立互通性應用程式，可以利用更高的可用性可靠性，整個所提供的服務匯流排分割項目。

詳細的線層級 AMQP 1.0 通訊協定指南，其中說明如何實作服務匯流排，並根據 OASIS AMQP 技術規格，請參閱[Azure 服務匯流排和事件集線器 AMQP 1.0 通訊協定指南](service-bus-amqp-protocol-guide.md)。    

## <a name="use-amqp-with-partitioned-queues"></a>使用分割佇列 AMQP

佇列非常有用需要暫時耦合載入資源撫平、 負載平衡與具備彈性的案例。 佇列中，發行者會傳送郵件給佇列中與使用者皆會從佇列中，在哪裡郵件僅能接收一次的情況下接收郵件。 使用例子這是庫存系統中的銷售點終端機，請發佈至佇列而不是直接庫存管理系統的資料。 然後，庫存管理系統會使用資料管理股票補充隨時。 以下有幾項優點，包括不需要在線上，始終庫存管理系統。 如需服務匯流排佇列的詳細資訊，請參閱[建立應用程式的使用服務匯流排佇列](service-bus-create-queues.md)。 

分割佇列中進一步隨著可用性、 可靠性和處理量的應用程式，這些佇列分割跨多個訊息經紀人和訊息存放區。     

### <a name="create-partitioned-queues"></a>建立分割的佇列

您可以建立使用[傳統的 Azure 入口網站][]和服務匯流排 SDK 分割佇列。 若要建立分割佇列中， [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx)屬性設定為**true** [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)執行個體中。 下列程式碼會顯示如何建立使用服務匯流排 SDK 分割佇列。 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>傳送及接收郵件使用 AMQP

您可以傳送郵件，並從[TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx)屬性設定為 [ [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) ，如下圖所示，在下列程式碼中，為通訊協定，使用 AMQP 分割佇列接收訊息。  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>使用分割主題 AMQP

主題和佇列的概念類似，但主題可以將相同的郵件複本傳送到多個*訂閱*。 在主題中，發行者傳送郵件的主題及消費者接收郵件從訂閱。 在庫存系統銷售點案例中，終端機發佈資料的主題。 庫存管理系統會從訂閱，然後接收郵件。 此外，監控系統也可接收相同的郵件，從不同的訂閱。 如需詳細瞭解服務匯流排主題和訂閱的詳細資訊，請參閱[建立應用程式的使用服務匯流排主題和訂閱](service-bus-create-topics-subscriptions.md)。 

就跟佇列，進一步分割的主題增加可用性、 可靠性和處理量的應用程式，如跨多個訊息經紀人與訊息儲存分割這些主題及訂閱。 

### <a name="create-partitioned-topics"></a>建立分割的主題

您可以建立使用[傳統的 Azure 入口網站][]和服務匯流排 SDK 分割的主題。 若要建立分割的主題， [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx)屬性設定為**true** [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)執行個體中。 下列程式碼會顯示如何建立使用服務匯流排 SDK 分割的主題。
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>傳送及接收郵件使用 AMQP

您可以傳送郵件，並從分割的主題訂閱[TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx)， [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx)屬性設為下列所示，做為通訊協定，使用 AMQP 接收郵件。  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>後續步驟

請參閱若要進一步瞭解分割訊息的項目，以及 AMQP 下列其他資訊。

*    [分割訊息的項目](service-bus-partitioning.md)
*    [OASIS 進階訊息佇列通訊協定 (AMQP) 1.0 版](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [服務匯流排 AMQP 1.0 支援](service-bus-amqp-overview.md)
*    [Azure 服務匯流排和事件集線器通訊協定指南中 AMQP 1.0](service-bus-amqp-protocol-guide.md)
*    [如何使用服務匯流排和 AMQP 1.0 Java 郵件服務 (JMS) API](service-bus-java-how-to-use-jms-api-amqp.md)
*    [如何使用服務匯流排.NET API AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md)

[Azure 傳統入口網站]: http://manage.windowsazure.com
