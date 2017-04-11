<properties 
    pageTitle="如何使用 Python 服務匯流排主題 |Microsoft Azure" 
    description="瞭解如何使用 Azure 服務匯流排主題和從 Python 訂閱。" 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>如何使用服務匯流排主題和訂閱

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文將說明如何使用服務匯流排主題和訂閱。 範例 Python 撰寫，並使用[Python Azure 套件][]。 涵蓋的案例包括**建立主題和訂閱**、**建立訂閱篩選**、**傳送主題的郵件**、**接收來自訂閱**，與**刪除主題和訂閱**。 如需主題和訂閱的詳細資訊，請參閱[下一步](#next-steps)] 區段。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**附註︰**如果您需要安裝 Python 或[Python Azure 套件][]，請參閱[Python 安裝指南](../python-how-to-install.md)。

## <a name="create-a-topic"></a>建立主題

**ServiceBusService**物件可讓您使用的主題。 新增您要以程式控制方式存取服務匯流排任何 Python 檔案頂端附近下列動作︰

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

下列程式碼會建立**ServiceBusService**物件。 取代`mynamespace`， `sharedaccesskeyname`，及`sharedaccesskey`具有您實際的命名空間，共用 Access 簽章 (SA) 按鍵名稱和關鍵值。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

從[Azure 入口網站][]，您可以取得 SA 機碼名稱中的值和值。

```
bus_service.create_topic('mytopic')
```

**建立\_主題**也支援其他選項，讓您將會覆寫預設主題設定，例如郵件存留時間] 或 [最大的主題大小。 下列範例會設定至 5 GB，並一次 live (TTL) 值 1 分鐘的最大的主題大小︰

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>建立訂閱

主題的訂閱，也會建立**ServiceBusService**物件。 訂閱名稱，並且可以有限制的郵件傳遞到訂閱的虛擬佇列中一組選擇性篩選。

> [AZURE.NOTE] 訂閱會持續性，並繼續存在於直到他們，或讓它們的訂閱，會刪除的主題。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>使用預設 (MatchAll) 篩選器建立訂閱

**MatchAll**是如果建立新的訂閱時指定沒有篩選條件，則會使用預設篩選器。 使用**MatchAll**篩選時，發佈至主題中的所有郵件都會都放在訂閱的虛擬佇列中。 下列範例會建立名為 「 AllMessages 」 的訂閱，並使用預設**MatchAll**篩選器。

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>建立篩選的訂閱

您也可以定義篩選可讓您指定主題的郵件應該顯示特定主題訂閱中。

最有彈性的訂閱支援類型是篩選的**SqlFilter**，來實作 SQL92 子集。 SQL 篩選運作內容的發佈至主題的郵件。 如需有關可與 SQL 篩選運算式的詳細資訊，請參閱[SqlFilter.SqlExpression][]語法。

您也可以使用的訂閱新增篩選**建立\_規則** **ServiceBusService**物件的方法。 這個方法可讓您新增至現有訂閱的篩選器。

> [AZURE.NOTE] 因為預設篩選器會自動套用到所有的新訂閱，您必須先移除預設篩選器或**MatchAll**會覆寫任何其他您可能會指定的篩選。 您可以移除預設規則使用**刪除\_規則** **ServiceBusService**物件的方法。

下列範例會建立名為訂閱`HighMessages`與**SqlFilter**只選取 [自訂**messagenumber**屬性大於 3 的訊息︰

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

同樣地，下列範例會建立名為訂閱`LowMessages`與**SqlFilter**只選取**messagenumber**屬性小於或等於 3 的訊息︰

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

現在，當訊息會傳送至`mytopic`它永遠傳送至接收器訂閱**AllMessages**主題訂閱]，然後選擇性地傳遞到接收器訂閱**HighMessages**和**LowMessages**主題訂閱 （根據郵件內容）。

## <a name="send-messages-to-a-topic"></a>傳送郵件給主題

若要將訊息傳送給服務匯流排主題，您的應用程式必須使用**傳送\_主題\_訊息** **ServiceBusService**物件的方法。

下列範例會示範如何傳送五個測試郵件`mytopic`。 請注意，每一封郵件的**messagenumber**屬性值不盡相同上的循環播放反覆運算 （此決定哪些訂閱收到其）︰

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

服務匯流排主題支援[進階版層](service-bus-premium-messaging.md)中的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的主題中的郵件數但還有首字放大的總大小由主題的郵件。 此主題檔案大小定義在建立時，使用 5 GB 的上限。 如需有關配額的詳細資訊，請參閱[服務匯流排配額][]。

## <a name="receive-messages-from-a-subscription"></a>從訂閱接收郵件

從訂閱使用接收訊息**接收\_訂閱\_訊息** **ServiceBusService**物件上的方法︰

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

讀取的時機時，郵件會從訂閱刪除參數**預覽\_鎖定**設為**False**。 您可以讀取 （預覽） 及鎖定訊息，而不刪除佇列中，將參數設定**預覽\_鎖定**為**True**。

讀取和刪除郵件接收作業的一部分是最簡單的模型，以及最適合的應用程式可容許不處理失敗訊息的案例的行為。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，然後重新啟動應用程式，然後開始一次使用訊息，它會有未接來電之前損毀的使用狀況的訊息。

如果**預覽\_鎖定**參數設為**True**，接收變成兩個階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），完成接收程序的第二個階段呼叫**訊息**物件上的 [**刪除**的方法。 **刪除**方法標示該郵件時所使用，並將它從訂閱移除。

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果無法基於某種原因處理郵件的收件者的應用程式，它可以在**訊息**物件上呼叫**解除鎖定**的方法。 這會導致服務匯流排，若要解除鎖定訂閱中的訊息，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有訂閱，請在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，並可接收一次。

應用程式當機之後處理郵件，但之前稱為**刪除**的方法，然後郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為**至少後處理**，也就是將至少一次處理每一封郵件，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應該將其他邏輯新增至其應用程式來處理重複的郵件傳遞。 這是通常用來達成會保持不變跨嘗試傳遞訊息的**訊息識別碼**] 屬性。

## <a name="delete-topics-and-subscriptions"></a>刪除主題及訂閱

主題和訂閱常設，而必須在 [透過[Azure 入口網站][]或以程式設計方式明確刪除。 下列範例會示範如何刪除本主題`mytopic`:

```
bus_service.delete_topic('mytopic')
```

刪除主題時，也會刪除任何訂閱註冊的主題。 您也可以個別刪除訂閱。 下列程式碼會顯示如何刪除命名訂閱`HighMessages`從`mytopic`主題︰

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>後續步驟

現在，您學到服務匯流排主題的基本概念，請遵循這些連結，瞭解更多。

-   請參閱[佇列主題，以及訂閱][]。
-   [SqlFilter.SqlExpression][]參考。

[Azure 入口網站]: https://portal.azure.com
[Python Azure 套件]: https://pypi.python.org/pypi/azure  
[佇列、 主題及訂閱]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[服務匯流排配額]: service-bus-quotas.md 
