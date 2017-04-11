<properties
    pageTitle="如何使用服務匯流排主題 java |Microsoft Azure"
    description="瞭解如何使用中 Azure 服務匯流排主題和訂閱。 Java 應用程式的撰寫程式碼範例。"
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>如何使用服務匯流排主題和訂閱

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本指南將說明如何使用服務匯流排主題和訂閱。 範例 Java 撰寫，並使用[Java Azure SDK][]。 涵蓋的案例包括**建立主題和訂閱**、**建立訂閱篩選**、**傳送主題的郵件**、**接收來自訂閱**，與**刪除主題和訂閱**。

## <a name="what-are-service-bus-topics-and-subscriptions"></a>什麼是服務匯流排主題和訂閱？

服務匯流排主題及訂閱支援*發佈/訂閱*訊息通訊模型。 使用主題和訂閱時，分散式應用程式的元件不彼此直接;請改為交換透過主題，做為中間的訊息。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

與服務匯流排佇列，每一封郵件處理單一使用者的主題和訂閱提供通訊，使用發佈/訂閱模式 」 一對多 「 的表單。 請可註冊主題的多個訂閱。 當郵件傳送主題時，它就可供每個訂閱大小控點/流程圖。

主題訂閱類似虛擬佇列接收主題已傳送的郵件的複本。 您可以選擇性地註冊篩選規則，以允許您篩選器/限制主題的郵件都會收到哪些主題訂閱的每個訂閱基準主題。

服務匯流排主題和訂閱可讓您不按比例縮放過大量的使用者和應用程式處理大量郵件。

## <a name="create-a-service-namespace"></a>建立服務命名空間

若要開始使用中 Azure 服務匯流排主題和訂閱，您必須先建立服務命名空間。 命名空間提供範圍容器來處理您的應用程式中的服務匯流排資源。

若要建立命名空間︰

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>設定您的應用程式使用服務匯流排

請確定您有安裝[Java Azure SDK][]建置這個範例之前。 如果您使用的蝕，您可以安裝包含 java Azure SDK[的蝕 Azure 工具組][]。 您可以新增**Microsoft Azure java 的文件庫**至您的專案︰

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Java 檔案頂端新增下列匯入陳述式︰

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

新增 java Azure 文件庫至您建立路徑並將其加入您的專案部署組件中。

## <a name="create-a-topic"></a>建立主題

透過**ServiceBusContract**類別，才能執行服務匯流排主題管理作業。 使用適當的設定封裝權限管理，為 SA 權杖建構**ServiceBusContract**物件和**ServiceBusContract**類別是唯一的通訊與 Azure 點。

**ServiceBusService**課程提供方法來建立、 列舉和刪除主題。 下列範例會示範如何**ServiceBusService**物件可用於建立名為主題`TestTopic`，與一部分的命名空間`HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

在**TopicInfo**上啟用的主題設定屬性的方法有 (例如︰ 若要設定預設存留時間 (TTL) 值，可套用至主題的郵件)。 下列範例會示範如何建立名為主題`TestTopic`的 5 GB 的最大︰

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

請注意，您可以使用**listTopics**方法**ServiceBusContract**物件上檢查服務命名空間中是否已經存在的主題，以指定的名稱。

## <a name="create-subscriptions"></a>建立訂閱

主題的訂閱，也會建立與**ServiceBusService**類別。 訂閱名稱，並且可以有限制郵件傳遞給訂閱的虛擬佇列中的設定選擇性篩選。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll**是如果建立新的訂閱時指定沒有篩選條件，則會使用預設篩選器。 使用**MatchAll**篩選時，發佈至主題中的所有郵件都會都放在訂閱的虛擬佇列中。 下列範例會建立名為 「 AllMessages 」 的訂閱，並使用預設**MatchAll**篩選器。

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>建立篩選的訂閱

您也可以建立這主題傳送的郵件應會顯示特定主題訂閱中的範圍可讓您的篩選。

最有彈性的訂閱支援類型是篩選的[SqlFilter][]，來實作 SQL92 子集。 SQL 篩選運作內容的發佈至主題的郵件。 如需可供使用 SQL 篩選運算式的詳細資訊，檢閱[SqlFilter.SqlExpression][]語法。

下列範例會建立名為訂閱`HighMessages`與[SqlFilter][]物件，只選取 [自訂**MessageNumber**屬性大於 3 的訊息︰

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

同樣地，下列範例會建立名為訂閱`LowMessages`與[SqlFilter][]物件，只選取**MessageNumber**屬性小於或等於 3 的訊息︰

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

郵件時立即傳送到`TestTopic`，它會永遠傳遞給接收器訂閱`AllMessages`訂閱，然後選擇性地傳送至接收器訂閱`HighMessages`和`LowMessages`訂閱 （視郵件內容）。

## <a name="send-messages-to-a-topic"></a>傳送郵件給主題

若要將訊息傳送給服務匯流排主題，您的應用程式會取得**ServiceBusContract**物件。 下列程式碼示範如何傳送郵件`TestTopic`內部先前建立的主題`HowToSample`命名空間︰

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

傳送給服務匯流排主題的郵件是[BrokeredMessage][]類別的執行個體。 [BrokeredMessage][]*物件都有一組標準方法 (例如* *setLabel* *和* *TimeToLive**)，用來保留自訂的應用程式的特定屬性，字典和本文任意應用程式的資料。應用程式，可以設定郵件的本文傳遞序列化的任何物件的建構函式[BrokeredMessage][]，並適當將*然後會使用*DataContractSerializer* *序列化物件。或者， * *java.io.InputStream** 可提供。

下列範例會示範如何傳送五個測試郵件`TestTopic` **MessageSender**我們取得上述的程式碼片段。
請注意，每一封郵件的**MessageNumber**屬性值不盡相同上的循環播放反覆運算 （這會決定哪些訂閱收到）︰

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

服務匯流排主題支援[進階版層](service-bus-premium-messaging.md)中的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的主題中的郵件數但還有首字放大的總大小由主題的郵件。 此主題檔案大小定義在建立時，使用 5 GB 的上限。

## <a name="how-to-receive-messages-from-a-subscription"></a>如何從訂閱接收郵件

若要從訂閱中收到的郵件，請使用**ServiceBusContract**物件。 收到的郵件可使用兩種不同的模式︰ **ReceiveAndDelete**和**PeekLock**。

使用 「 **ReceiveAndDelete**模式時，收到時是單一擷取作業-也就是說，當服務匯流排收到郵件的讀取的要求時，它標示該郵件為所使用，傳回應用程式。 **ReceiveAndDelete**模式最簡單的模型，最適合的案例的應用程式可容許不處理失敗訊息。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，然後重新啟動應用程式，然後開始一次使用訊息，它會有未接來電之前損毀的使用狀況的訊息。

在**PeekLock**模式中，會收到變成兩個階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），藉由呼叫 [收到的郵件上的 [**刪除**完成接收程序的第二個階段。 當服務匯流排看到**刪除**通話時，它會將時所使用的郵件標示，並移除主題。

下列範例會示範如何能接收的郵件，並處理使用**PeekLock**模式 （非預設模式）。 下面這個範例是循環執行和處理 「 HighMessages 」 訂閱中的郵件，然後離開 [沒有任何訊息時 （或者，它可能會設定為新郵件，請等候）。

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果無法基於某種原因處理郵件的收件者的應用程式，就可以收到的郵件 （而非**deleteMessage**方法） 上呼叫**unlockMessage**方法。 這會導致服務匯流排，若要解除鎖定訊息中的主題中，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有內主題鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，使其可接收一次。

應用程式當機之後處理郵件，但發行**deleteMessage**要求之前，然後郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為**至少後處理**，也就是將至少一次處理每一封郵件，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應該將其他邏輯新增至其應用程式來處理重複的郵件傳遞。 這通常被達成使用該郵件，仍在嘗試傳遞常數**getMessageId**方法。

## <a name="delete-topics-and-subscriptions"></a>刪除主題及訂閱

若要刪除的主題和訂閱的主要方式是使用**ServiceBusContract**物件。 刪除主題也會刪除任何訂閱註冊的主題。 您也可以個別刪除訂閱。

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>後續步驟

現在，您學到的服務匯流排佇列基本概念，請參閱[服務匯流排佇列主題，以及訂閱][]，如需詳細資訊。

  [Java azure SDK]: http://azure.microsoft.com/develop/java/
  [針對蝕 azure 工具組]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [服務匯流排佇列主題，以及訂閱]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
