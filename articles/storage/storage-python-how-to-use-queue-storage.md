<properties
    pageTitle="如何使用從 Python 佇列中的儲存 |Microsoft Azure"
    description="瞭解如何使用 Python 從 Azure 佇列服務來建立及刪除佇列，並插入取得，與刪除的郵件。"
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-python"></a>如何使用從 Python 佇列中的儲存空間

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南為您示範如何執行常見的案例使用 Azure 佇列中儲存服務。 範例 Python 撰寫，並使用[Microsoft Azure 儲存體 SDK python]。 涵蓋的案例包含**插入**、**查看**、**快速**，及**刪除**佇列中的郵件，以及**建立及刪除佇列**。 如需有關佇列的詳細資訊，請參閱 [下一步] 區段。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="how-to-create-a-queue"></a>如何︰ 建立佇列

**QueueService**物件可讓您使用佇列。 下列程式碼會建立**QueueService**物件。 新增您要以程式控制方式存取 Azure 儲存體任何 Python 檔案頂端附近下列動作︰

    from azure.storage.queue import QueueService

下列程式碼會建立使用儲存體帳戶名稱和帳戶金鑰**QueueService**物件。 使用您的帳戶名稱和鍵取代 「 我的帳戶 」 和 「 mykey 」。

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')


## <a name="how-to-insert-a-message-into-a-queue"></a>如何︰ 插入佇列中的郵件

若要插入佇列中的郵件，請使用**放置\_訊息**方法來建立新郵件，並將其新增至佇列中。

    queue_service.put_message('taskqueue', u'Hello World')


## <a name="how-to-peek-at-the-next-message"></a>如何︰ 查看下一封郵件

您可以查看前面佇列訊息而不移除，則可電話佇列中**預覽\_郵件**方法。 根據預設，**預覽\_郵件**窺視在單一郵件。

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.content)


## <a name="how-to-dequeue-messages"></a>如何︰ 取消佇列郵件

您的程式碼從兩個步驟佇列中移除一則訊息。 當您呼叫**取得\_郵件**，預設會取得下一封郵件佇列中。 傳回從郵件**取得\_郵件**變成看不到任何其他佇列中讀取郵件的程式碼。 根據預設，此訊息皆會保持不可見 30 秒的時間。 若要完成移除佇列中的郵件，您必須也呼叫**刪除\_訊息**。 移除郵件的兩個步驟程序可確保當您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 程式碼來電**刪除\_訊息**處理郵件後，以滑鼠右鍵。

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

有兩種方法您可以自訂佇列中的郵件擷取。
首先，您可以取得郵件 （最多 32) 以批的次。 第二，您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。 下列程式碼範例用途**取得\_郵件**一個通話中取得 16 訊息的方式。 然後處理每個訊息使用的循環播放。 它也會針對每一封郵件的 5 分鐘設定隱形術逾時。

    messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
    for message in messages:
        print(message.content)
        queue_service.delete_message('taskqueue', message.id, message.pop_receipt)      


## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何︰ 變更佇列訊息的內容

您可以變更訊息就地佇列中的內容。 如果郵件是代表工時的任務，您可以使用這項功能更新的工時的任務狀態。 使用下列程式碼**更新\_訊息**更新郵件的方法。 可見度逾時設定為 0，也就是說，會立即出現訊息，以及的內容會更新。

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## <a name="how-to-get-the-queue-length"></a>如何︰ 取得佇列長度

您可以在佇列中取得的郵件數的估計值。 **取得\_佇列中\_中繼資料**方法要求佇列中傳回服務的佇列中，與**approximate_message_count**的中繼資料。 因為郵件可以新增或移除佇列服務回應您的要求之後，則結果為僅大約。

    metadata = queue_service.get_queue_metadata('taskqueue')
    count = metadata.approximate_message_count

## <a name="how-to-delete-a-queue"></a>如何︰ 刪除佇列

若要刪除佇列和其包含的所有郵件，請連絡**刪除\_佇列中**方法。

    queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>後續步驟

現在，您學到佇列中儲存的基本概念，請遵循這些連結，瞭解更多。

- [Python 開發人員中心](/develop/python/)
- [Azure 儲存服務 REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure 儲存體小組部落格]
- [Microsoft Azure 儲存體 python SDK]

[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure 儲存體 python SDK]: https://github.com/Azure/azure-storage-python
