<properties 
    pageTitle="服務匯流排配對命名空間 |Microsoft Azure"
    description="成對的命名空間實作詳細資料] 與 [成本"
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

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>配對命名空間實作詳細資料，並顯示成本的影響

[PairNamespaceAsync][]方法，使用[SendAvailabilityPairedNamespaceOptions][]執行個體，代表您執行可見的工作。 使用功能時，會成本考量，因為很有用，好讓您預期的行為發生時，了解這些工作。 API 出現下列代表您自動行為︰

-   建立積存佇列。
-   建立交談佇列] 或 [主題[MessageSender][]物件。
-   訊息的實體無法使用時，傳送偵測 （ping) 的實體的郵件中偵測到的實體有空時再嘗試。
-   您也可以建立一組 」 的訊息幫浦 」，將郵件從積存佇列移到主佇列。
-   協調右/發生錯誤的主要和次要[MessagingFactory][]執行個體。

在高的層級，此功能的運作，如下所示︰ 沒有行為變更主要的實體健全時，會發生。 當[FailoverInterval][]工期經過，與主要的實體會看到不成功傳送之後非暫時性[MessagingException][]或[「 逾時][]，會發生下列情形︰

1.  傳送作業的主要的實體被停用，系統會偵測主要的實體，直到 ping 可以順利傳送。

2.  隨機積存佇列中已選取。

3.  以所選的積存佇列傳閱[BrokeredMessage][]物件。

1.  如果所選的積存佇列傳送作業失敗，請從旋轉提取該佇列中，並選取新的佇列。 [MessagingFactory][]執行個體上的所有寄件者瞭解失敗。

下圖說明順序。 首先，寄件者傳送郵件。

![成對的命名空間][0]

在傳送給主要佇列中失敗，寄件者會開始傳送訊息給隨機選擇的積存佇列中。 同時，同時也會偵測 （ping） 工作。

![成對的命名空間][1]

此時郵件仍在次要佇列中，且無法傳遞給主要佇列中。 一旦主要佇列中健全一次，至少有一個程序應該執行虹吸。 虹吸會將郵件從各種不同的積存佇列傳遞至適當的目的實體 （「 佇列 」 和 「 主題 」）。

![成對的命名空間][2]

本主題的其餘部分說明下列設備組件的運作方式的詳細的資料。

## <a name="creation-of-backlog-queues"></a>建立的積存佇列

傳遞給[PairNamespaceAsync][]方法[SendAvailabilityPairedNamespaceOptions][]物件會指出您想要使用的積存佇列數目。 每個積存佇列然後會明確建立包含下列屬性設定 （所有其他的值會設定為[QueueDescription][]預設值）︰

| 路徑                                   | [主要命名空間] / x servicebus 傳輸 / [索引] 其中的 [索引] 是 [0，BacklogQueueCount) 中的值 |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | int。MaxValue                                                                                         |
| DefaultMessageTimeToLive               | TimeSpan.MaxValue                                                                                    |
| AutoDeleteOnIdle                       | TimeSpan.MaxValue                                                                                    |
| LockDuration                           | 1 分鐘                                                                                             |
| EnableDeadLetteringOnMessageExpiration | true                                                                                                 |
| EnableBatchedOperations                | true                                                                                                 |

例如，第一個積存佇列中建立的命名空間**contoso**名為`contoso/x-servicebus-transfer/0`。

在建立佇列時，程式碼會先檢查佇列中是否存在。 如果佇列中不存在，會建立佇列。 程式碼並不會清理 」 額外 」 積存佇列。 明確地說，如果主索引的命名空間**contoso**應用程式要求五個 backlog 但路徑的積存佇列佇列`contoso/x-servicebus-transfer/7`存在，該額外的積存佇列仍然存在，但不是會使用。 系統明確允許不想使用的額外積存佇列存在於。 命名空間擁有者，您負責清除任何未使用過/不想要的積存佇列。 此決策的原因是服務匯流排不知道您的命名空間中的所有佇列存都在於目的。 此外，如果佇列中存在指定的名稱，但不是符合假定的[QueueDescription][]，然後您原因是您自己的變更預設行為。 不提供任何保證所做修改積存佇列中，您的程式碼。 請確定徹底測試您的變更。

## <a name="custom-messagesender"></a>自訂 MessageSender

傳送時，所有郵件都移到內部[MessageSender][]物件的所有項目時運作，以及項目 」 中斷。 」 時，重新導向至積存佇列正常運作 在收到非暫時性失敗時，會啟動計時器。 之後，[時段][] [FailoverInterval][]屬性值不成功的訊息會傳送期間所組成，可供容錯移轉。 此時，會發生下列事件，每個實體︰

- 工作偵測 （ping） 執行每個[PingPrimaryInterval][]檢查實體是否有空。 一旦順利完成這項工作，會啟動立即使用實體的所有用戶端程式碼，傳送新郵件的主要命名空間。

- 若要傳送給其他寄相同的實體會導致[BrokeredMessage][]傳送到修改坐在積存佇列未來的要求。 修改[BrokeredMessage][]物件中移除某些屬性，然後將其儲存到其他位置。 清除並在 [新的別名，讓服務匯流排和 SDK 一致地處理的郵件] 下新增下列屬性︰

| 舊的屬性名稱       | 新屬性名稱 |
|-------------------------|-------------------|
| 工作階段識別碼               | 工作階段 ms x 識別碼    |
| TimeToLive              | x-ms-timetolive   |
| ScheduledEnqueueTimeUtc | x ms 路徑         |

為名為 x ms 路徑的屬性，原始的目的路徑也會儲存在郵件中。 此設計可讓許多實體並存單一積存佇列中的郵件。 傳回由虹吸轉譯屬性。

郵件處理 256 KB 限制，並可供容錯移轉時，自訂[MessageSender][]物件可以發生問題。 自訂[MessageSender][]物件積存佇列中，請以一起儲存所有佇列與主題的郵件。 這個物件混合來自多個主運算積存佇列內的郵件。 若要處理負載平衡許多不知道彼此的用戶端之間，SDK 隨機挑選一個積存佇列中每個[QueueClient][]或[TopicClient][]您建立的程式碼。

## <a name="pings"></a>Ping

偵測 （ping） 訊息是[ContentType][]屬性設定為應用程式/vnd.ms-servicebus-偵測 （ping）、 [TimeToLive][]值 1 的第二個空白[BrokeredMessage][] 。 此偵測 （ping） 服務匯流排中有一個特殊的特色︰ 伺服器永不傳送偵測 （ping） 任何來電者要求[BrokeredMessage][]時。 因此，您永遠不必自行若要瞭解如何接收並略過這些訊息。 當視為無法使用時，會 ping 每個[MessagingFactory][]執行個體，每個用戶端每個實體 （唯一佇列或主題）。 根據預設，這是一次每分鐘。 偵測 （ping） 訊息會被視為一般的服務匯流排訊息，而且可能會導致頻寬和訊息的費用。 當用戶端偵測到系統有，郵件會停止。

## <a name="the-syphon"></a>虹吸

應用程式中的至少有一個可執行程式應該正在執行虹吸。 虹吸執行 long 投票收到的持續時間 15 分鐘。 可在所有項目，您有 10 積存佇列會裝載虹吸的應用程式呼叫接收作業 40 時間每小時，960 時間每日和 28800 時間 30 天。 虹吸積極移動郵件時從積存至主要佇列中，每一封郵件體驗 （在所有套用的郵件大小及頻寬標準費用） 的下列費用︰

1.  傳送到 [待處理事項。

2.  從 [待處理事項接收。

3.  傳送給主要。

4.  從主要接收。

## <a name="closefault-behavior"></a>關閉錯誤行為

主控虹吸，一次主要或次要[MessagingFactory][]錯誤，或其合作夥伴不關閉的應用程式中同時又錯誤/關閉後再虹吸偵測到這個狀態，虹吸動作。 如果沒有其他[MessagingFactory][]關閉 5 數秒內，虹吸會錯誤仍開啟[MessagingFactory][]。

## <a name="next-steps"></a>後續步驟

如服務匯流排非同步訊息的詳細說明，請參閱[非同步訊息圖樣及可用性][]。 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [「 逾時]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [時段]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [非同步訊息的圖樣與可用性]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
