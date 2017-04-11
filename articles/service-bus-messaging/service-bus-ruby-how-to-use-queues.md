<properties
    pageTitle="如何使用 [注音標示的服務匯流排佇列 |Microsoft Azure"
    description="瞭解如何使用服務匯流排佇列 Azure 中。 並列文字以撰寫程式碼範例。"
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本指南將說明如何使用服務匯流排佇列。 範例撰寫的 [注音標示，並使用 Azure 健身。 涵蓋的案例包含**建立佇列、 傳送及接收訊息**，然後**刪除佇列**。 如需有關服務匯流排佇列的詳細資訊，請參閱[下一步](#next-steps)] 區段。

## <a name="what-are-service-bus-queues"></a>服務匯流排佇列是什麼？

服務匯流排佇列支援*仲介訊息*的通訊模型。 佇列中，使用的分散式應用程式的元件不彼此直接;請改為交換透過佇列中，做為中間的訊息。 郵件產生者 （寄件者） 交出佇列中的訊息，然後再繼續處理。
非同步，訊息消費者 （接收器） 會提取佇列中的訊息，並加以處理。 產生者沒有等待消費者回覆，才能繼續處理程序，並進一步傳送訊息。 佇列提供**第一個中，第一個查看 (FIFO)**的郵件傳送給一或多個競爭消費者。 也就是說，郵件會通常收到，且由順序的佇列中，加入與每一封郵件接收，並處理只有一封郵件消費者接收器處理。

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

服務匯流排佇列是用途技術，可以用於各種情況︰

-   在[多層 Azure 應用程式](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)中的網頁和背景工作角色之間的通訊。
-   內部部署的應用程式和 Azure 之間的通訊裝載在[混合式解決方案](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)的應用程式。
-   執行在不同的組織或組織的部門的內部部署的分散式應用程式的元件之間的通訊。

使用佇列可以啟用您將應用程式更好的並啟用多個恢復到您的結構。

## <a name="create-a-namespace"></a>建立命名空間

若要開始使用服務匯流排佇列 Azure 中，您必須先建立命名空間。 命名空間提供範圍容器來處理您的應用程式中的服務匯流排資源。 因為 Azure 入口網站不會建立命名空間 ACS 連線，您必須建立透過命令列介面的命名空間。

若要建立命名空間︰

1. 開啟的 Powershell 的 Azure 主控台。

2. 輸入下列命令以建立服務匯流排命名空間。 提供您自己的命名空間值，並為您的應用程式中指定的相同的地區。

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>取得管理認證的命名空間

若要執行管理作業，例如佇列中建立新的命名空間，您必須取得管理認證的命名空間。

您執行建立 Azure 服務匯流排命名空間的 PowerShell 指令程式會顯示可用來管理命名空間的鍵。 複製 [ **DefaultKey**值。 稍後在本教學課程，您會在您的程式碼中使用此值。

![複製索引鍵](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] 如果您登入[Azure 入口網站](https://portal.azure.com/)，瀏覽至 [連線資訊，您的服務匯流排命名空間，您也可以找到此按鍵。

## <a name="create-a-ruby-application"></a>建立拼音應用程式

建立並列文字的應用程式。 如需相關指示，請參閱[建立並列文字上的應用程式 Azure](/develop/ruby/tutorials/web-app-with-linux-vm/)。

## <a name="configure-your-application-to-use-service-bus"></a>設定您的應用程式使用服務匯流排

若要使用 Azure 服務匯流排，請下載並使用 [注音標示 Azure 套件，其中包含一組方便時觀看文件庫與儲存其他服務。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 取得套件

1. 使用**PowerShell** (Windows)、**終端機**(Mac)，或**被**(Unix) 等命令列介面。

2. 安裝健身和相依性的 [命令] 視窗中，輸入 「 健身安裝 azure 」。

### <a name="import-the-package"></a>匯入套件

使用您偏好使用的文字編輯器，移至您想要使用儲存的位置並列文字檔案頂端新增下列動作︰

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>設定 Azure 服務匯流排連線

Azure 模組讀取環境變數**AZURE\_SERVICEBUS\_命名空間**和**AZURE\_SERVICEBUS\_ACCESS_KEY**連線到您的服務匯流排命名空間所需的資訊。 如果沒有設定這些環境變數，您必須指定之前**Azure::ServiceBusService**使用下列程式碼的命名空間資訊︰

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

命名空間值設為您建立了，而不是整個 URL 的值。 例如，使用**「 yourexamplenamespace 」**，不是 「 yourexamplenamespace.servicebus.windows.net 」。

## <a name="how-to-create-a-queue"></a>如何建立佇列

**Azure::ServiceBusService**物件可讓您使用佇列。 若要建立佇列中，使用**create_queue()**方法。 下列範例會建立佇列或印出的任何錯誤。

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

您也可以將傳遞**Azure::ServiceBus::Queue**物件其他選項，這可讓您覆寫預設佇列中的設定，例如郵件存留時間或佇列最大值。 下列範例會示範如何設定佇列最大值為 5 GB 和時間至即時 1 分鐘︰

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>如何傳送郵件給佇列中

若要將訊息傳送給服務匯流排佇列中，您的應用程式呼叫**傳送\_佇列中\_message()** **Azure::ServiceBusService**物件上的方法。 郵件已傳送至 （和收到從） 服務匯流排佇列**Azure::ServiceBus::BrokeredMessage**物件，以及有一組標準的屬性 (例如**標籤**和**時間\_至\_live**)，用來保留自訂的應用程式的特定屬性，字典和本文任意應用程式的資料。 應用程式，可以為郵件傳遞的字串值來設定郵件的本文，任何必要的標準屬性會填入預設值。

下列範例會示範如何將測試訊息傳送給佇列中名為 「 測試佇列中 「 使用**傳送\_佇列中\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

服務匯流排佇列中[進階版層](service-bus-premium-messaging.md)支援的郵件大小上限 256 KB[標準層](service-bus-premium-messaging.md)和 1 MB。 頁首，其中包含標準和自訂應用程式屬性，可以有 64 KB 的大小上限。 沒有限制的郵件保留在佇列數但還有首字放大的總大小由佇列中的郵件。 在建立時，使用 5 GB 的上限定義此佇列大小。

## <a name="how-to-receive-messages-from-a-queue"></a>如何從佇列中接收郵件

從佇列中，使用接收訊息**接收\_佇列中\_message()** **Azure::ServiceBusService**物件上的方法。 根據預設，郵件會閱讀，並鎖定不從佇列中刪除。 不過，您可以刪除的郵件佇列中讀取設定時**: peek_lock**為 [ **false**] 選項。

讀取和刪除的兩個階段作業，也能夠在支援的不允許遺失的郵件應用程式，可讓您的預設行為。 服務匯流排收到要求時，它會找出要使用下一封郵件、 鎖定，防止其他消費者接收，然後傳回應用程式。 應用程式完成處理郵件 （或供未來處理可靠的方式將它儲存之後），完成接收程序的第二個階段，則可電話**刪除\_佇列中\_message()**方法，並提供要做為參數刪除訊息。 **刪除\_佇列中\_message()**方法將訊息標示為所使用，並從佇列中移除。

如果**︰ 預覽\_鎖定**參數設定為**false**，閱讀並刪除訊息會變成最簡單的模型，最適合用於案例的應用程式可容許不處理失敗訊息。 若要了解，請考慮消費者接收邀請中發生的問題，然後當機處理之前，先情況。 因為服務匯流排會有將郵件標示為所使用，重新啟動應用程式，並開始再次使用訊息時，它會遺失的使用狀況之前損毀的訊息。

下列範例會示範如何接收和處理訊息使用**接收\_佇列中\_message()**。 範例第一次接收，並使用刪除的郵件**︰ 預覽\_鎖定**設為**false**，它會收到另一封郵件，並刪除訊息使用**刪除\_佇列中\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機和無法讀取的郵件

服務匯流排提供的功能，可協助您順利復原錯誤，在您的應用程式或處理郵件的問題。 如果基於某種原因，處理郵件的收件者的應用程式無法再它可呼叫**解除鎖定\_佇列中\_message()** **Azure::ServiceBusService**物件上的方法。 這會使服務匯流排，若要解除鎖定訊息佇列中的，使其可接收一次，相同的使用應用程式或另一個使用應用程式。

也有佇列中，在鎖定訊息相關聯的逾時，如果應用程式無法處理之前訊息鎖定逾 （例如，如果應用程式當機），然後服務匯流排會自動解除鎖定訊息，並可接收一次。

應用程式當機之後但之前處理郵件**刪除\_佇列中\_message()**呼叫方法，然後重新啟動時，郵件會重新應用程式的傳遞。 這通常稱為**至少一次處理**;也就是說，每一封郵件處理至少一次，但在某些情況下可能會被重新傳遞相同的郵件。 如果此案例不允許重複處理，應用程式開發人員應該將其他邏輯新增至其應用程式來處理重複的郵件傳遞。 這通常用來達成**訊息\_識別碼**屬性的郵件中，在嘗試傳遞維持不變。

## <a name="next-steps"></a>後續步驟

現在，您學到的服務匯流排佇列基本概念，請遵循這些連結，瞭解更多。

-   [佇列、 主題和訂閱](service-bus-queues-topics-subscriptions.md)的概觀。
-   瀏覽 GitHub[注音 Azure SDK](https://github.com/Azure/azure-sdk-for-ruby)存放庫。

在此文件和[如何使用從 [注音標示的佇列中儲存](../storage/storage-ruby-how-to-use-queue-storage.md)文件中所討論的 Azure 佇列中討論 Azure 服務匯流排佇列之間的比較，請參閱[Azure 佇列和 Azure 服務匯流排佇列-比較 Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
