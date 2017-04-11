<properties 
    pageTitle="分割佇列 」 和 「 主題 |Microsoft Azure"
    description="說明如何使用多個訊息經紀人分割服務匯流排佇列 」 和 「 主題。"
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
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>分割的佇列 」 和 「 主題

Azure 服務匯流排採用處理郵件的多個訊息經紀人和儲存郵件的多個訊息存放區。 傳統佇列或主題由單一郵件代理人，而儲存在一個訊息存放區。 佇列] 或 [分割跨多個訊息經紀人和訊息存放區 」 主題，同時也會啟用服務匯流排。 這表示分割佇列中或主題的整體處理量不限於單一郵件代理人或訊息存放區的效能。 此外，訊息存放區的暫時性的問題不會呈現的分割的佇列或主題無法使用。 分割的佇列 」 和 「 主題可以包含所有進階的服務匯流排功能，例如支援交易和工作階段。

如需內部服務匯流排資訊的詳細資訊，請參閱[服務匯流排架構][]主題。

## <a name="how-it-works"></a>運作方式

每個分割佇列中或主題包含多個片段。 每個片段會儲存在不同的訊息存放區，並由不同的訊息代理人。 當郵件傳送至分割佇列中或主題時，服務匯流排會指派到其中一個完整的郵件。 選取範圍是隨機服務匯流排或使用 [寄件者可以指定資料分割索引鍵。

當用戶端想要收到一則訊息分割佇列中，從或分割主題服務匯流排查詢的訂閱郵件的所有片段時，則會傳回可以從任何訊息存放區取得接收者的第一封郵件。 其他郵件，並傳回其在收到時額外的快取服務匯流排收到要求。 接收的用戶端並不知道分割;分割的佇列或主題的用戶端具行為 (例如讀取、 完成及延遲，deadletter，預先) 完全相同的一般的實體行為。

有任何其他成本時傳送郵件，或從分割的佇列或主題接收訊息。

## <a name="enable-partitioning"></a>啟用分割

若要使用 Azure 服務匯流排分割的佇列 」 和 「 主題，請使用 Azure SDK 版本 2.2 或更新版本，或指定`api-version=2013-10`您 http 要求。

您可以建立服務匯流排佇列 」 和 「 主題 1、 2、 3、 4 或 5 GB （預設值為 1 GB） 的大小。 使用分割啟用，服務匯流排建立 16 的磁碟分割區您指定每 GB。 因此，如果您建立的大小是 5 GB 佇列中，使用 16 的磁碟分割區佇列最大值成為 (5 \* 16) = 80 GB。 您可以查看 [ [Azure 入口網站][]上的 [項目，請參閱分割佇列中或主題的大小上限。

有數種方式可建立分割佇列中或主題。 當您從應用程式建立的佇列中或主題時，您可以啟用分別[QueueDescription.EnablePartitioning][]或[TopicDescription.EnablePartitioning][]屬性設定為**true**分割佇列中的主題。 這些屬性必須設定時的佇列中，或建立主題。 無法變更現有的佇列或主題這些內容。 例如︰

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

或者，您可以建立的分割的佇列或主題 Visual Studio 中或在[Azure 入口網站][]。 當您建立的新佇列或主題入口網站中時，將 [**啟用分割**選項**一般設定**刀佇列中的主題**設定**為 [ **true**] 視窗中。 在 Visual Studio 中，按一下 [**新的佇列**或**新的主題**] 對話方塊的 [**啟用分割**核取方塊。

## <a name="use-of-partition-keys"></a>使用磁碟分割索引鍵

至分割佇列中或主題佇列郵件後，請檢查有服務匯流排分割索引鍵的目前狀態。 如果找到，它會選取該金鑰為基礎的片段。 如果找不到資料分割索引鍵，它會選取根據內部演算法的片段。

### <a name="using-a-partition-key"></a>使用資料分割索引鍵

某些情況下，例如工作階段或交易，需要郵件儲存在特定的片段。 所有這些案例需要使用的資料分割索引鍵。 使用相同的磁碟分割金鑰的所有郵件都指派給相同的片段。 如果片段暫時無法使用，服務匯流排會傳回錯誤。

根據此案例中，不同的郵件內容會作為分割索引鍵︰

**工作階段識別碼**︰ 郵件的[BrokeredMessage.SessionId][] ] 屬性設定，則服務匯流排使用此屬性的磁碟分割索引鍵。 如此一來，所有屬於相同的工作階段的郵件會處理相同的郵件代理人。 這可讓服務匯流排，以確保訊息以及工作階段狀態的一致性順序。

**PartitionKey**︰ 如果郵件有[BrokeredMessage.PartitionKey][]屬性，但不是[BrokeredMessage.SessionId][] ] 屬性設定，然後服務匯流排分割索引鍵使用[PartitionKey][]屬性。 如果郵件有[工作階段識別碼][]和[PartitionKey][]屬性設定，這兩個屬性必須相同。 如果 [ [PartitionKey][] ] 屬性設定為不同的[工作階段識別碼][]] 屬性的值，服務匯流排會傳回**InvalidOperationException**例外狀況。 如果寄件者傳送非工作階段注意交易訊息，應[PartitionKey][]屬性。 分割索引鍵可確保交易內所傳送的所有郵件都由相同的訊息代理人。

**訊息**︰ 如果佇列或主題具有[QueueDescription.RequiresDuplicateDetection][]屬性設為**true** [BrokeredMessage.SessionId][]或[BrokeredMessage.PartitionKey][]屬性並未設定，然後[BrokeredMessage.MessageId][]屬性當做分割索引鍵。 （請注意是否不會傳送的應用程式的 Microsoft.NET 及 AMQP 文件庫時自動指派訊息識別碼）。如此一來，所有相同的郵件的複本會處理相同的郵件代理人。 這個選項可讓服務匯流排偵測並避免重複的訊息。 如果未設定[QueueDescription.RequiresDuplicateDetection][]屬性則為**true**，服務匯流排不會將 [[訊息][]] 屬性的磁碟分割鍵。

### <a name="not-using-a-partition-key"></a>不使用分割索引鍵

如果沒有分割索引鍵，服務匯流排分配分割佇列中或主題所有的片段的循環方式的郵件。 如果找不到所選的片段，服務匯流排會將郵件到不同的片段。 如此一來，傳送作業成功還是訊息存放區的暫時無法使用。

授與服務匯流排足夠的時間，在佇列中不同的片段，將郵件傳送訊息的客戶所指定的[MessagingFactorySettings.OperationTimeout][]值必須是大於 15 秒。 建議您[OperationTimeout][]屬性設定為預設值的 60 秒。

請注意，資料分割索引鍵 「 固定 」 訊息以特定的片段。 如果無法使用保留這個片段的訊息存放區，服務匯流排會傳回錯誤。 如果沒有分割索引鍵，服務匯流排可以選擇不同的片段和作業成功。 因此，建議您沒有提供資料分割索引鍵，除非需要。

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>進階主題︰ 交易使用分割的項目

交易傳送的郵件，必須指定資料分割索引鍵。 這可以是下列屬性︰ [BrokeredMessage.SessionId][]、 [BrokeredMessage.PartitionKey][]或[BrokeredMessage.MessageId][]。 屬於相同的交易傳送的所有郵件，必須都指定相同的分割索引鍵。 如果您嘗試要傳送的訊息不交易內的資料分割索引鍵，服務匯流排會傳回**InvalidOperationException**例外狀況。 如果您嘗試傳送相同的交易內有不同的磁碟分割索引鍵的多封郵件，服務匯流排會傳回**InvalidOperationException**例外狀況。 例如︰

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

如果設定的任何內容，可做為資料分割索引鍵，服務匯流排固定到特定片段的訊息。 不論使用交易，就會發生這個問題。 建議您沒有指定資料分割索引鍵，如果您不需要。

## <a name="using-sessions-with-partitioned-entities"></a>使用分割的項目中的工作階段

若要傳送交易訊息工作階段注意的主題或佇列中，在郵件必須有[BrokeredMessage.SessionId][]屬性設定。 如果以及指定[BrokeredMessage.PartitionKey][]屬性，則必須相同，[[工作階段識別碼][]] 屬性。 如果它們之間的差異，服務匯流排會傳回**InvalidOperationException**例外狀況。

一般 （非分割） 佇列與或不同的主題，並不使用單一交易多封郵件傳送到不同的工作階段。 如果嘗試服務匯流排會傳回**InvalidOperationException**例外狀況。 例如︰

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>分割實體自動郵件轉寄功能

Azure 服務匯流排支援從，或分割的項目之間的自動郵件轉寄。 若要啟用自動轉寄的郵件，請將[QueueDescription.ForwardTo][]屬性來源佇列或訂閱。 如果郵件是指定資料分割索引鍵 （[工作階段識別碼][]、 [PartitionKey][]或[訊息識別碼][]），該分割索引鍵用於目的地實體。

## <a name="considerations-and-guidelines"></a>考量和準則

- **高的一致性功能**︰ 如果實體使用功能，例如階段、 重複偵測或明確控制分割鍵，然後訊息作業永遠路由至特定的片段。 如果任一片段體驗高或基礎存放區不佳，這些作業失敗，且可用性會減少。 整體來說，一致性大於仍以上的項目。流量的子集合發生問題，而不是所有流量。
- **管理**︰ 例如建立、 更新及刪除作業必須執行的實體的所有片段。 如果任何片段是不佳，可能會導致這些作業失敗。 取得作業，資訊例如郵件計算必須彙總從所有片段。 如果任何片段不佳，實體可用性狀態報告為限制。
- **低大量郵件案例**︰ 這種情況下，尤其是使用 HTTP 通訊協定，您可能必須執行多個以取得所有郵件接收作業。 接收要求的前端上所有的片段執行接收，並快取所收到的回應。 在相同的連接的後續接收要求想受益此快取及接收延遲將會較低。 不過，如果您有多個連線，或使用 HTTP 的會建立新的連接，每個要求。 因此，有它會位於相同的節點登陸不保證。 如果是鎖定所有現有的訊息，而且在另一個前端快取，請接收作業會傳回**null**。 郵件最後會到期且您再次接收。 建議您保持 HTTP。
- **瀏覽/預覽郵件**︰ PeekBatch 永遠也不會傳回[MessageCount 屬性](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx)中指定的訊息數。 有兩個常見的原因。 其中一個原因是集合的郵件的彙總的大小超過 256 KB 的大小上限。 另一個原因是，如果佇列或主題有[EnablePartitioning 屬性](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx)設為**true**，磁碟分割可能沒有足夠的郵件，完成所要求的訊息的數目。 一般而言，如果應用程式，想要接收郵件的特定數字，它應致電[PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx)鍵，直到其取得的郵件，該數字或沒有檢視更多的郵件。 如需詳細資訊，包括程式碼範例，請參閱[QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx)或[SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx)。

## <a name="latest-added-features"></a>最新版本的新增的功能

- 新增或移除規則現已支援分割的實體。 非分割實體不同，這些作業不支援交易。 
- 傳送及接收訊息分割的實體現在支援 AMQP。
- AMQP 現已支援為下列作業︰[[批次傳送](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx)、[批次接收](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx)、[順序電話號碼接收](https://msdn.microsoft.com/library/azure/hh330765.aspx)、[鑽](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx)、[更新鎖定](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx)、[排程郵件](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx)、[取消排程的郵件](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx)、[新增規則](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx)、[移除規則](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx)、[工作階段更新鎖定](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx)、 設定工作階段狀態](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx)、[取得工作階段狀態](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)，[查看工作階段郵件](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)，以及[列舉工作階段](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx)。

## <a name="partitioned-entities-limitations"></a>分割的項目限制

目前服務匯流排會在分割的佇列 」 和 「 主題以下的限制︰

-   分割的佇列 」 和 「 主題不支援屬於不同的工作階段，在單一的交易的傳送訊息。
-   服務匯流排目前允許最多 100 個資料分割的佇列] 或 [每個命名空間的主題。 每個分割佇列中或主題計算的每個命名空間 （不適用於進階版層） 10000 實體配額根據。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解分割訊息的實體的[AMQP 1.0 支援服務匯流排分割佇列 」 和 「 主題][]的討論，請參閱。 

  [服務匯流排架構]: service-bus-architecture.md
  [Azure 入口網站]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [工作階段識別碼]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [訊息]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0 支援服務匯流排分割佇列 」 和 「 主題]: service-bus-partitioned-queues-and-topics-amqp-overview.md
