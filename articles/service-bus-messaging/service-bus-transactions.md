<properties 
    pageTitle="服務匯流排交易 |Microsoft Azure" 
    description="Azure 服務匯流排最小的異動及透過傳送的概觀" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>服務匯流排交易處理的概觀

本文將討論 Azure 服務匯流排交易的功能。 [最小的異動服務匯流排範例](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)說明的討論。 本文是限於交易處理和服務匯流排的*傳送透過*功能的概觀時最小的異動範例狀況提出更廣義且更複雜的範圍。

## <a name="transactions-in-service-bus"></a>服務匯流排交易

[交易](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions)群組在一起變成*執行範圍*的兩個或更多作業。 特性，例如交易必須確保所有作業屬於指定群組的 [作業成功或失敗共同。 在這方面交易會作為單一單位，通常稱為*完整性*。 

服務匯流排是交易訊息代理人，並確保所有的內部作業對其郵件存放區的交易完整性。 所有傳送的郵件內服務匯流排，例如之間實體，移動至[信件佇列](service-bus-dead-letter-queues.md)或郵件[自動轉寄](service-bus-auto-forwarding.md)的郵件都為可交易。 因此，如果服務匯流排接受訊息時，它已儲存及標示為 [序列數字。 之後，請於服務匯流排內的任何郵件轉送協同的作業各項目，而不會影響 （成功來源和目標失敗） 或重複 （無法來源和目標成功） 的郵件。

服務匯流排支援單一訊息實體佇列、 主題 （訂閱） 範圍內的交易分組作業。 例如，您可以傳送幾個訊息給一個佇列從交易在範圍內，及郵件時，才會認可佇列中的記錄檔交易順利完成時。

## <a name="operations-within-a-transaction-scope"></a>交易範圍內的作業 

交易範圍內，才能執行的作業如下所示︰

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx)、 [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**︰ 傳送，SendAsync，SendBatch，SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**︰ 完成，CompleteAsync 別抱任何、 AbandonAsync、 Deadletter、 DeadletterAsync，延後，DeferAsync、 RenewLock RenewLockAsync 

接收作業不包含在內，請因為假定它與應用程式取得郵件[ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)模式內部分接收循環播放或與[OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx)回撥，然後只開啟交易範圍以處理郵件。

處理的郵件 （完成，別抱任何信件，延），則會發生上交易的整體的結果，以及相依範圍內。

## <a name="transfers-and-send-via"></a>傳輸和 「 傳送透過 」

若要啟用的佇列中的資料的處理器，然後至另一個佇列交易移，服務匯流排支援*傳輸*。 在傳輸作業，寄件者先將郵件傳送至 「 傳輸佇列 」，並傳送佇列中立即將郵件移至預定的目的地佇列中使用相同的強大傳輸實作所使用的自動轉寄功能的。 為未認可傳輸佇列中的記錄檔的方式，它會變成可見傳輸佇列中的消費者用的訊息。

此異動的功能的顯而易見的寄件者輸入郵件的來源傳輸佇列中本身時。 換句話說，服務匯流排可以傳送郵件至目的地佇列 」 透過 「 傳輸佇列中，執行完成 (或延後，或信件) 輸入訊息，所有在一個作業的操作。 

### <a name="see-it-in-code"></a>在 [程式碼] 中檢視

若要設定這種轉移，您可以建立目標目的地佇列中透過傳輸佇列中的郵件寄件者。 您也必須提取來自該相同的佇列中的郵件的收件者。 例如︰

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

簡單交易再使用這些項目，如下列範例所示︰

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>後續步驟

請參閱以下文章中的服務匯流排佇列的詳細資訊︰

- [使用自動轉寄鏈結服務匯流排項目](service-bus-auto-forwarding.md)
- [自動轉寄範例](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [最小的異動服務匯流排範例](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure 佇列和服務匯流排佇列比較](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)