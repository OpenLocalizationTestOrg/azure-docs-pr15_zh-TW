<properties 
    pageTitle="如何使用從 [注音標示的佇列中儲存 |Microsoft Azure" 
    description="瞭解如何使用 Azure 佇列服務來建立及刪除佇列，並插入取得，與刪除的郵件。 撰寫並列文字的範例。" 
    services="storage" 
    documentationCenter="ruby" 
    authors="robinsh" 
    manager="carmonm" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-ruby"></a>如何使用佇列中的儲存空間，從 [注音標示

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南為您示範如何執行常見的案例使用 Microsoft Azure 佇列中儲存體服務。 使用並列文字 Azure API 寫入範例。
涵蓋的案例包含**插入**、**查看**、**快速**，及**刪除**佇列中的郵件，以及**建立及刪除佇列**。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>建立並列文字應用程式

建立並列文字的應用程式。 如需相關指示，請參閱[Azure VM 滑軌 Web 應用程式上的 [注音標示](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式，以存取儲存空間

若要使用 Azure 儲存空間，您需要下載並使用並列文字 azure 套件，其中包含一組方便時觀看文件庫與儲存其他服務。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 取得套件

1. 使用**PowerShell** (Windows)、**終端機**(Mac)，或**被**(Unix) 等命令列介面。

2. 安裝健身和相依性的 [命令] 視窗中，輸入 「 健身安裝 azure 」。

### <a name="import-the-package"></a>匯入套件

使用您偏好使用的文字編輯器，請移至您想要使用儲存的位置並列文字檔案頂端新增下列動作︰

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

Azure 模組會讀取環境變數**AZURE\_儲存\_帳戶**和**AZURE\_儲存\_ACCESS_KEY**連線至 Azure 儲存體帳戶所需的資訊。 如果沒有設定這些環境變數，您必須指定下列程式碼中使用**Azure::QueueService**之前的帳戶資訊︰

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

 
若要從傳統或 Azure 入口網站中的資源管理員儲存帳戶中取得這些值︰

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 瀏覽至您想要使用的儲存空間帳戶。
3. 在右側設定刀，按一下 [**便捷鍵**。
4. 在 Access 鍵刀出現，您會看到便捷鍵 1 和 2 的便捷鍵。 您可以使用下列其中一項。 
5. 按一下 [複製] 圖示，以將金鑰複製到剪貼簿。 

若要取得這些值傳統的儲存空間帳戶在傳統 Azure 入口網站︰

1. [傳統 Azure 入口網站](https://manage.windowsazure.com)登入。
2. 瀏覽至您想要使用的儲存空間帳戶。
3. 按一下 [**管理便捷鍵**底部的功能窗格]。
4. 在 [pop 設定] 對話方塊中，您會看到儲存體帳戶名稱、 存取主索引鍵和次要便捷鍵。 存取鍵，您可以使用的主索引項目或第二個項目。 
5. 按一下 [複製] 圖示，以將金鑰複製到剪貼簿。

## <a name="how-to-create-a-queue"></a>如何︰ 建立佇列

下列程式碼會建立**Azure::QueueService**物件，這可讓您使用佇列。

    azure_queue_service = Azure::QueueService.new

使用**create_queue()**方法來建立佇列中指定的名稱。

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## <a name="how-to-insert-a-message-into-a-queue"></a>如何︰ 插入佇列中的郵件

若要插入佇列中的郵件，請使用**create_message()**方法建立新郵件，並將其新增至佇列中。

    azure_queue_service.create_message("test-queue", "test message")

## <a name="how-to-peek-at-the-next-message"></a>如何︰ 查看下一封郵件

您可以查看前面佇列訊息而不移除，則可電話佇列中**預覽\_messages()**方法。 根據預設，**預覽\_messages()**窺視在單一郵件。 您也可以指定您想要查看的訊息數目。

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## <a name="how-to-dequeue-the-next-message"></a>如何︰ 取消佇列下一封郵件

您可以移除郵件從佇列中兩個步驟。

1. 當您呼叫**清單\_messages()**，預設會取得下一封郵件佇列中。 您也可以指定您希望收到的郵件數。 傳回的訊息**清單\_messages()**變成看不到任何其他佇列中讀取郵件的程式碼。 在 [可見度逾時傳遞做為參數的秒數。

2. 若要完成移除佇列中的郵件，您也必須呼叫**delete_message()**。

移除郵件的兩個步驟程序可確保當您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 程式碼來電**刪除\_message()**處理郵件後，以滑鼠右鍵。

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何︰ 變更佇列訊息的內容

您可以變更訊息就地佇列中的內容。 下列程式碼會使用**update_message()**方法來更新郵件。 方法會傳回包含佇列訊息和 UTC 日期時間值，表示在當郵件會顯示在佇列中的 pop 回條，表示 tuple。

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## <a name="how-to-additional-options-for-dequeuing-messages"></a>如何︰ 取消佇列的其他選項的郵件

有兩種方法您可以自訂佇列中的郵件擷取。

1. 您可以取得以批次的訊息。

2. 您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。

下列程式碼範例用途**清單\_messages()**一個通話中取得 15 訊息的方式。 然後列印並刪除每一封郵件。 它也會針對每一封郵件的 5 分鐘設定隱形術逾時。

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## <a name="how-to-get-the-queue-length"></a>如何︰ 取得佇列長度

您可以取得的郵件數的估計佇列中。 **取得\_佇列中\_metadata()**方法要求佇列服務傳回大約郵件計數和佇列中的中繼資料。

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## <a name="how-to-delete-a-queue"></a>如何︰ 刪除佇列

若要刪除佇列和其包含的所有郵件，請連絡**刪除\_queue()**佇列中物件上的方法。

    azure_queue_service.delete_queue("test-queue")

## <a name="next-steps"></a>後續步驟

現在，您學到佇列中儲存的基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作。

- 請造訪[Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- 瀏覽 GitHub 以[[注音標示的 Azure SDK](https://github.com/WindowsAzure/azure-sdk-for-ruby)存放庫

本文及[如何使用服務匯流排佇列](/develop/ruby/how-to-guides/service-bus-queues/)文件中所討論的 Azure 服務匯流排佇列中所討論的佇列中 Azure 服務之間的比較，請參閱[Azure 佇列和服務匯流排佇列-比較 Contrasted](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
