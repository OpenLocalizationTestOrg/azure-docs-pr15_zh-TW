<properties 
    pageTitle="如何使用 Python 服務匯流排佇列 |Microsoft Azure" 
    description="瞭解如何使用 Python 從 Azure 服務匯流排佇列。" 
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
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文將說明如何使用服務匯流排佇列。 範例 Python 撰寫，並使用[Python Azure 服務匯流排套件][]。 涵蓋的案例包含**建立佇列、 傳送及接收訊息**，然後**刪除佇列**。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] 若要安裝 Python 或[Python Azure 服務匯流排套件][]，請參閱[Python 安裝指南](../python-how-to-install.md)。

## <a name="create-a-queue"></a>建立佇列

**ServiceBusService**物件可讓您使用佇列。 新增您要在其中任何 Python 檔案以程式控制方式存取服務匯流排上方附近的下列程式碼︰

```
from azure.servicebus import ServiceBusService, Message, Queue
```

下列程式碼會建立**ServiceBusService**物件。 取代`mynamespace`， `sharedaccesskeyname`，及`sharedaccesskey`與您的命名空間、 共用的 access 簽章 (SA) 機碼名稱值。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

SA 機碼名稱的值和值可以找到[Azure 傳統入口網站][]的連線資訊，或在 [Visual Studio**屬性**] 窗格中選取伺服器總管] 中的服務匯流排命名空間 （如前一節中所示）。

```
bus_service.create_queue('taskqueue')
```

**create_queue**也支援其他選項，可讓您覆寫預設佇列中設定，例如訊息 live (TTL) 的時間或佇列最大值。 下列範例會設定 5 GB，及 [TTL] 值為 1 分鐘佇列最大值︰

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>傳送郵件給佇列中

若要將訊息傳送給服務匯流排佇列中，您的應用程式呼叫**傳送\_佇列中\_訊息** **ServiceBusService**物件上的方法。

下列範例會示範如何將測試訊息傳送給佇列中名為*taskqueue 使用***傳送\_佇列中\_訊息**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

服務匯流排佇列中[進階版層](service-bus-premium-messaging.md)支援的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的郵件保留在佇列數但還有首字放大的總大小由佇列中的郵件。 在建立時，使用 5 GB 的上限定義此佇列大小。 如需有關配額的詳細資訊，請參閱[服務匯流排配額][]。

## <a name="receive-messages-from-a-queue"></a>從佇列中接收郵件

從佇列中，使用接收訊息**接收\_佇列中\_訊息** **ServiceBusService**物件上的方法︰

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

讀取的時機時，郵件會從佇列中刪除參數**預覽\_鎖定**設為**False**。 您可以讀取 （預覽） 及鎖定訊息，而不刪除佇列中，將參數設定**預覽\_鎖定**為**True**。

讀取和刪除郵件接收作業一部分是最簡單的模型，以及最適合的應用程式可容許不處理失敗訊息的案例的行為。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，然後重新啟動應用程式，然後開始一次使用訊息，它會有未接來電之前損毀的使用狀況的訊息。

如果**預覽\_鎖定**參數設為**True**，接收變成兩個階段作業，讓它可能不允許遺失郵件的支援應用程式。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），完成接收程序的第二個階段呼叫**訊息**物件上的 [**刪除**的方法。 [**刪除**] 方法會將時所使用的郵件標示，並從佇列中移除。

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果無法基於某種原因處理郵件的收件者的應用程式，它可以在**訊息**物件上呼叫**解除鎖定**的方法。 這會導致服務匯流排，若要解除鎖定訊息佇列中的，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有佇列中，在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，使其可接收一次。

應用程式當機之後處理郵件，但之前稱為**刪除**的方法，然後郵件會被重新傳遞至應用程式時，重新啟動。 這通常稱為**至少後處理**，也就是將至少一次處理每一封郵件，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應該將其他邏輯新增至其應用程式來處理重複的郵件傳遞。 這是通常用來達成會保持不變跨嘗試傳遞訊息的**訊息識別碼**] 屬性。

## <a name="next-steps"></a>後續步驟

現在，您已經學會服務匯流排佇列中的基本概念，請遵循這些連結，瞭解更多。

-   請參閱[佇列主題，以及訂閱][]。

[Azure 傳統入口網站]: https://manage.windowsazure.com
[Python Azure 服務匯流排套件]: https://pypi.python.org/pypi/azure-servicebus  
[佇列、 主題及訂閱]: service-bus-queues-topics-subscriptions.md
[服務匯流排配額]: service-bus-quotas.md
 
