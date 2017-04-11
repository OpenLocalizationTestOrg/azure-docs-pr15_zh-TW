<properties 
    pageTitle="自動轉寄訊息的實體服務匯流排 |Microsoft Azure"
    description="如何鏈佇列中或另一個佇列中或主題的訂閱。"
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>使用自動轉寄鏈結服務匯流排項目

*自動轉寄*功能可讓您鏈結佇列中或另一個佇列中或主題屬於相同的命名空間的訂閱。 啟用自動轉寄時，服務匯流排自動移除會放在第一個佇列或訂閱 （來源） 的郵件，並將其放在第二個佇列中或主題 （目的地）。 請注意，仍然可以直接傳送郵件的目的地實體。 此外，不可能鍊子，例如 deadletter 佇列中，另一個佇列中或主題。

## <a name="using-auto-forwarding"></a>使用自動轉寄

您可以藉由設定[QueueDescription.ForwardTo][]或[SubscriptionDescription.ForwardTo][]內容來源，如下列範例所示的[QueueDescription][]或[SubscriptionDescription][]物件上啟用自動轉寄。

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

目的地實體必須同時建立來源實體存在。 如果目的地實體不存在，服務匯流排會傳回時要求您建立來源實體例外狀況。

您可以使用自動轉寄至不按比例縮放的個別主題。 服務匯流排限制 2000[數字的指定主題的訂閱](service-bus-quotas.md)。 您可以藉由建立第二層級主題配合其他訂閱。 請注意，即使您不受限於服務匯流排限制訂閱數目，新增第二層主題可以改善主題的整體處理能力。

![自動轉寄案例][0]

您也可以使用自動轉寄減少從接收器郵件寄件者。 例如，請考慮 ERP 系統的三個模組所組成︰ 順序處理、 庫存管理和客戶關係管理。 每一個這些模組會產生佇列將對應的主題的郵件。 Alice 和百勝是有興趣與客戶相關的所有郵件的業務。 若要接收的郵件，Alice 和百勝每個建立個人佇列中和訂閱 ERP 主題的自動轉寄所有郵件到其佇列中的每個。

![自動轉寄案例][1]

如果 Alice 休假、 其個人的佇列中，而不是 ERP 主題，填滿。 在此案例中，代表有不會收到任何訊息，因為沒有 ERP 主題曾連絡配額。

## <a name="auto-forwarding-considerations"></a>自動轉寄考量

如果目的地實體已累積有多郵件，而且超過配額] 中，或目的地實體已停用，來源實體會將郵件的[信件佇列中](service-bus-dead-letter-queues.md)的目的地有空間 （或實體已重新啟用），直到。 這些郵件會繼續 live 信件佇列中，讓您必須明確接收，並從信件佇列中處理它們。

當鏈結以取得複合主題有多個訂閱的個別主題，建議您有少量的第一層級主題訂閱並在第二層級主題上的多個訂閱。 例如 20 訂閱，每個鏈結 200 訂閱，第二層級主題的第一層級主題可讓為較高的處理量，比第一層級主題 200 訂閱，每個鏈結至第二層級主題 20 訂閱。

服務匯流排帳單的每個轉寄郵件的一項作業。 比方說，傳送郵件給 20 訂閱，每個設定為自動轉寄郵件到其他佇列中或主題主題被付費 21 作業如果第一層級的所有訂閱都收到郵件的複本。

若要建立鏈結至另一個佇列或主題的訂閱，訂閱的建立者必須來源與目的地實體上有**管理權**限。 將郵件傳送至來源主題只需要來源主題**傳送**的權限。

## <a name="next-steps"></a>後續步驟

如需自動轉接的詳細資訊，請參閱下列主題︰

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

若要深入瞭解服務匯流排改善效能，請參閱[分割訊息的實體][]。

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [分割訊息的項目]: service-bus-partitioning.md