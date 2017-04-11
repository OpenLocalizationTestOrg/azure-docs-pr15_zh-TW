<properties
    pageTitle="如何使用佇列中的儲存空間 （c + +） |Microsoft Azure"
    description="瞭解如何使用佇列中儲存服務 Azure 中。 C + + 撰寫範例。"
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-queue-storage-from-c"></a>如何使用 c + + 佇列中儲存空間  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀
本指南會顯示如何執行常見的案例使用 Azure 佇列中儲存服務。 範例 c + + 撰寫，並使用[Azure 儲存用戶端文件庫 c + +](http://github.com/Azure/azure-storage-cpp/blob/master/README.md)。 涵蓋的案例包含**插入**、**查看**、**快速**，及**刪除**佇列中的郵件，以及**建立及刪除佇列**。

>[AZURE.NOTE] 本指南為 c + + 版本 1.0.0 和上方目標 Azure 儲存用戶端的文件庫。 建議的版本是儲存用戶端程式庫 2.2.0，可透過[NuGet](http://www.nuget.org/packages/wastorage)或[GitHub](http://github.com/Azure/azure-storage-cpp/)。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>建立 c + + 應用程式  
本指南，您會使用它們可以執行 c + + 應用程式中儲存功能。

若要這麼做，您將需要 c + + 安裝 Azure 儲存用戶端的文件庫和 Azure 訂閱中建立 Azure 儲存體帳戶。

若要 c + + 安裝 Azure 儲存用戶端的文件庫，您可以使用以下兩種方法︰

-   **Linux:**請依照 [ [Azure c + + 讀我檔案的儲存空間用戶端程式庫](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)] 頁面中的指示。
-   **Windows:**在 Visual Studio 中，按一下 [**工具 > NuGet 封裝管理員 > 封裝管理員主控台**。 [NuGet 封裝管理員主控台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)中輸入下列命令，然後按**ENTER**。

        Install-Package wastorage

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式，來存取佇列中的儲存空間
新增下列包含您要使用 Azure 儲存體 Api 存取佇列 c + + 檔案頂端的陳述式︰  

    #include "was/storage_account.h"
    #include "was/queue.h"

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串

Azure 儲存用戶端使用儲存空間的連接字串儲存端點和的認證以存取資料管理服務。 在用戶端應用程式執行時，您必須提供下列格式的儲存空間連接字串的*AccountName*和*AccountKey*值[Azure 入口網站](https://portal.azure.com)中所列的儲存空間帳戶使用您儲存的帳戶和儲存空間便捷鍵的名稱。 儲存帳戶及便捷鍵的資訊，請參閱[關於 Azure 儲存體帳戶](storage-create-storage-account.md)。 此範例會示範如何宣告靜態欄位，按住連線字串︰  

    // Define the connection-string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

若要測試您的應用程式在本機的 Windows 電腦中，您可以使用與[Azure SDK](https://azure.microsoft.com/downloads/)已安裝 Microsoft Azure[儲存模擬器](storage-use-emulator.md)。 儲存模擬器是公用程式模擬用於您的本機開發上 Azure Blob 與佇列中，表格服務。 下列範例會顯示如何宣告靜態欄位以保留您的本機存放區模擬器連線字串︰  

    // Define the connection-string with Azure Storage Emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

若要啟動 Azure 儲存模擬器，請選取 [**開始**] 按鈕或按下**Windows**鍵。 開始輸入**Azure 儲存模擬器**，然後從應用程式清單中選取 [ **Microsoft Azure 儲存模擬器**。

下列範例會假設您使用下列兩種方法之一來取得儲存連線字串。

## <a name="retrieve-your-connection-string"></a>擷取連接字串
您可以使用**cloud_storage_account**類別，代表您儲存帳戶的資訊。 若要從儲存連線字串中擷取您儲存的帳戶資訊，您可以使用**剖析**的方法。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-queue"></a>如何︰ 建立佇列
**Cloud_queue_client**物件可讓您取得佇列中的參照物件。 下列程式碼會建立**cloud_queue_client**物件。

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create a queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

使用**cloud_queue_client**物件取得佇列中您想要使用的參考。 如果不存在，您可以建立佇列。

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();  

## <a name="how-to-insert-a-message-into-a-queue"></a>如何︰ 插入佇列中的郵件
若要插入的現有佇列中的郵件，請先建立新的**cloud_queue_message**。 接下來，呼叫**add_message**方法。 從字串或**位元組**陣列可以建立**cloud_queue_message** 。 以下是 （如果不存在） 建立佇列程式碼，插入 「 Hello，全球 」 的訊息︰

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Create the queue if it doesn't already exist.
    queue.create_if_not_exists();

    // Create a message and add it to the queue.
    azure::storage::cloud_queue_message message1(U("Hello, World"));
    queue.add_message(message1);  

## <a name="how-to-peek-at-the-next-message"></a>如何︰ 查看下一封郵件
您可以查看前面佇列訊息，而不移除呼叫**peek_message**方法佇列。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Peek at the next message.
    azure::storage::cloud_queue_message peeked_message = queue.peek_message();

    // Output the message content.
    std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何︰ 變更佇列訊息的內容
您可以變更訊息就地佇列中的內容。 如果郵件是代表工時的任務，您可以使用這項功能更新的工時的任務狀態。 下列程式碼會更新佇列中郵件與新的內容，並設定延伸另一個 60 秒的可見度逾時。 這儲存的郵件時，相關聯的工時狀態，並提供用戶端繼續處理郵件的其他幾分鐘。 您可以使用此技巧來追蹤佇列中的郵件，而不必處理步驟失敗，因為軟硬體失敗時，從頭開始從頭多重步驟的工作流程。 一般而言，您想要保留重試次數，及郵件重試一次以上 n 次，如果您想要刪除。 這可以防止處理程序每次觸發的應用程式錯誤訊息。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the message from the queue and update the message contents.
    // The visibility timeout "0" means make it visible immediately.
    // The visibility timeout "60" means the client can get another minute to continue
    // working on the message.
    azure::storage::cloud_queue_message changed_message = queue.get_message();

    changed_message.set_content(U("Changed message"));
    queue.update_message(changed_message, std::chrono::seconds(60), true);

    // Output the message content.
    std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## <a name="how-to-de-queue-the-next-message"></a>如何︰ 清除佇列 [下一封郵件
您的程式碼就能取消佇列中兩個步驟的佇列中的訊息。 當您呼叫**get_message**時，您會取得佇列中的下一封郵件。 傳回從**get_message**訊息會變成看不到任何其他佇列中讀取郵件的程式碼。 若要完成移除佇列中的郵件，您也必須呼叫**delete_message**。 移除郵件的兩個步驟程序可確保如果您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 處理郵件後，程式碼呼叫**delete_message**權限。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Get the next message.
    azure::storage::cloud_queue_message dequeued_message = queue.get_message();
    std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

    // Delete the message.
    queue.delete_message(dequeued_message);

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>如何︰ 運用就能取消佇列郵件的其他選項
有兩種方法您可以自訂佇列中的郵件擷取。 首先，您可以取得郵件 （最多 32) 以批的次。 第二，您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。 下列範例會使用一個通話取得 20 郵件**get_messages**方法。 然後，它會處理使用**的**循環播放每一封郵件。 它也會針對每一封郵件的 5 分鐘設定隱形術逾時。 請注意，5 分鐘啟動所有郵件的同時，因此後有 5 分鐘**get_messages**，任何未被刪除的郵件通話一次會顯示自從。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
    // 5 minutes (300 seconds).
    azure::storage::queue_request_options options;
    azure::storage::operation_context context;

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

    for (auto it = messages.cbegin(); it != messages.cend(); ++it)
    {
        // Display the contents of the message.
        std::wcout << U("Get: ") << it->content_as_string() << std::endl;
    }

## <a name="how-to-get-the-queue-length"></a>如何︰ 取得佇列長度
您可以在佇列中取得的郵件數的估計值。 **Download_attributes**方法要求擷取佇列中的屬性，包括郵件計數佇列服務。 **Approximate_message_count**方法佇列中取得大約訊息數。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // Fetch the queue attributes.
    queue.download_attributes();

    // Retrieve the cached approximate message count.
    int cachedMessageCount = queue.approximate_message_count();

    // Display number of messages.
    std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## <a name="how-to-delete-a-queue"></a>如何︰ 刪除佇列
若要刪除佇列中所包含的所有郵件，呼叫**delete_queue_if_exists**方法佇列中物件上。

    // Retrieve storage account from connection-string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the queue client.
    azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

    // Retrieve a reference to a queue.
    azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

    // If the queue exists and delete it.
    queue.delete_queue_if_exists();  

## <a name="next-steps"></a>後續步驟

現在，您學到佇列中儲存的基本概念，請遵循這些連結，深入瞭解 Azure 儲存體。

-   [如何使用 c + + Blob 儲存體](storage-c-plus-plus-how-to-use-blobs.md)
-   [如何使用 c + + 資料表儲存體](storage-c-plus-plus-how-to-use-tables.md)
-   [C + + 清單 Azure 儲存體資源](storage-c-plus-plus-enumeration.md)
-   [儲存空間 c + + 參考的用戶端文件庫](http://azure.github.io/azure-storage-cpp)
-   [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)

