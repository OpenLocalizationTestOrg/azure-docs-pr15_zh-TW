<properties
    pageTitle="如何使用從 Java 佇列中的儲存 |Microsoft Azure"
    description="瞭解如何使用 Azure 佇列服務來建立及刪除佇列，並插入取得，與刪除的郵件。 Java 撰寫的範例。"
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-queue-storage-from-java"></a>如何使用從 Java 佇列中的儲存空間

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南會顯示如何執行常見的案例使用 Azure 佇列中儲存服務。 範例 Java 撰寫，並使用[Azure 儲存體 SDK java][]。 涵蓋的案例包含**插入**、**查看**、**快速**，及**刪除**佇列中的郵件，以及**建立**及**刪除**佇列。 如需有關佇列的詳細資訊，請參閱[下一步](#Next-Steps)] 區段。

附註︰ SDK 適用於開發人員在 Android 裝置上使用 Azure 儲存體。 如需詳細資訊，請參閱[Azure 儲存體 SDK Android 版][]。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

本指南，您會使用本機，或在網頁角色或工作者角色 Azure 中的執行的程式碼中可以執行 Java 應用程式中儲存功能。

若要這麼做，您需要安裝 Java 開發套件 (JDK) 和 Azure 訂閱中建立 Azure 儲存體帳戶。 一旦您這麼做，則您必須以驗證您的開發系統符合的最低需求及上 GitHub [Azure 儲存體 SDK java][]存放庫中所列的相依性。 如果您的系統符合這些需求，您可以遵循下載並安裝 Java Azure 儲存文件庫，從該存放庫系統上的指示進行。 當您完成工作之後時，您可以建立 Java 應用程式會使用本文中的範例。

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式，來存取佇列中的儲存空間

移至您要使用 Azure 儲存體 Api 存取佇列 Java 檔案頂端新增下列匯入陳述式︰

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="setup-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串

Azure 儲存用戶端使用儲存空間的連接字串儲存端點和的認證以存取資料管理服務。 在用戶端應用程式執行時，您必須提供下列格式的儲存空間連接字串的*AccountName*和*AccountKey*值[Azure 入口網站](https://portal.azure.com)中所列的儲存空間帳戶使用您儲存的帳戶和主要便捷鍵的名稱。 此範例會示範如何宣告靜態欄位，按住連線字串︰

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

應用程式中的角色 Microsoft Azure 中執行，這個字串可以儲存在服務設定檔中， *ServiceConfiguration.cscfg*，並可以使用**RoleEnvironment.getConfigurationSettings**方法呼叫來存取。 以下是範例稱為*StorageConnectionString*服務設定檔中的**設定**項目從取得連線字串︰

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例會假設您使用下列兩種方法之一來取得儲存連線字串。

## <a name="how-to-create-a-queue"></a>如何︰ 建立佇列

**CloudQueueClient**物件可讓您取得佇列中的參照物件。 下列程式碼會建立**CloudQueueClient**物件。 (附註︰ 還有其他方法來建立**CloudStorageAccount**物件; 如需詳細資訊，請參閱[Azure 儲存用戶端 SDK 參照]中的**CloudStorageAccount** 。)

使用**CloudQueueClient**物件取得佇列中您想要使用的參考。 如果不存在，您可以建立佇列。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the queue client.
       CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

       // Retrieve a reference to a queue.
       CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Create the queue if it doesn't already exist.
       queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-a-message-to-a-queue"></a>如何︰ 佇列中新增訊息

若要插入的現有佇列中的郵件，請先建立新的**CloudQueueMessage**。 接下來，呼叫**addMessage**方法。 從格式的字串 （utf-8） 或位元組陣列可以建立**CloudQueueMessage** 。 以下是 （如果不存在） 建立佇列程式碼，插入"Hello，全球 」 的訊息。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Create the queue if it doesn't already exist.
        queue.createIfNotExists();

        // Create a message and add it to the queue.
        CloudQueueMessage message = new CloudQueueMessage("Hello, World");
        queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-peek-at-the-next-message"></a>如何︰ 查看下一封郵件

您可以查看前面佇列訊息，而不移除，則可電話**peekMessage**佇列。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Peek at the next message.
        CloudQueueMessage peekedMessage = queue.peekMessage();

        // Output the message value.
        if (peekedMessage != null)
        {
          System.out.println(peekedMessage.getMessageContentAsString());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何︰ 變更佇列訊息的內容

您可以變更訊息就地佇列中的內容。 如果郵件是代表工時的任務，您可以使用這項功能更新的工時的任務狀態。 下列程式碼會更新佇列中郵件與新的內容，並設定延伸另一個 60 秒的可見度逾時。 這儲存的郵件時，相關聯的工時狀態，並提供用戶端繼續處理郵件的其他幾分鐘。 您可以使用此技巧來追蹤佇列中的郵件，而不必處理步驟失敗，因為軟硬體失敗時，從頭開始從頭多重步驟的工作流程。 一般而言，您想要保留重試次數，及郵件重試一次超過*n*次，如果您想要刪除。 這可以防止處理程序每次觸發的應用程式錯誤訊息。

下列程式碼範例搜尋佇列中的郵件，透過會找出符合"Hello，全球 」 的內容，然後修改訊息內容，離開第一封郵件。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32.
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields =
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

或者，下列程式碼範例更新只的第一個顯示封郵件佇列。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage message = queue.retrieveMessage();

        if (message != null)
        {
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-get-the-queue-length"></a>如何︰ 取得佇列長度

您可以在佇列中取得的郵件數的估計值。 **DownloadAttributes**方法要求數個目前的值，包括多少郵件是在佇列中的計數佇列服務。 因為郵件可以新增或移除佇列服務回應您的要求之後，才大約計數。 **GetApproximateMessageCount**方法會傳回不呼叫佇列服務擷取**downloadAttributes**，通話的最後一個值。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

       // Download the approximate message count from the server.
        queue.downloadAttributes();

        // Retrieve the newly cached approximate message count.
        long cachedMessageCount = queue.getApproximateMessageCount();

        // Display the queue length.
        System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-dequeue-the-next-message"></a>如何︰ 取消佇列下一封郵件

您的程式碼取消來自兩個步驟佇列中的訊息。 當您呼叫**retrieveMessage**時，您會取得佇列中的下一封郵件。 傳回從**retrieveMessage**訊息會變成看不到任何其他佇列中讀取郵件的程式碼。 根據預設，此訊息皆會保持不可見 30 秒的時間。 若要完成移除佇列中的郵件，您也必須呼叫**deleteMessage**。 移除郵件的兩個步驟程序可確保如果您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 處理郵件後，程式碼呼叫**deleteMessage**權限。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve the first visible message in the queue.
        CloudQueueMessage retrievedMessage = queue.retrieveMessage();

        if (retrievedMessage != null)
        {
            // Process the message in less than 30 seconds, and then delete the message.
            queue.deleteMessage(retrievedMessage);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## <a name="additional-options-for-dequeuing-messages"></a>移除佇列郵件的其他選項

有兩種方法您可以自訂佇列中的郵件擷取。 首先，您可以取得郵件 （最多 32) 以批的次。 第二，您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。

下列範例會使用一個通話取得 20 郵件**retrieveMessages**方法。 然後，它會處理使用**的**循環播放每一封郵件。 它也 5 分鐘 （300 秒） 的每一封郵件中設定隱形術逾時。 5 分鐘啟動所有郵件的同時，請注意，因此當 5 分鐘有自從**retrieveMessages**通話，不要刪除任何訊息一次會顯示。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes,
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-queues"></a>如何︰ 清單佇列

若要取得目前佇列的清單，請連絡**CloudQueueClient.listQueues()**方法，將會傳回**CloudQueue**物件的集合。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-queue"></a>如何︰ 刪除佇列

若要刪除佇列中所包含的所有郵件，呼叫**deleteIfExists**方法**CloudQueue**物件上。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>後續步驟

現在，您學到佇列中儲存的基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作。

- [Azure 儲存體 java SDK][]
- [Azure 儲存用戶端 SDK 參考][]
- [Azure 儲存服務 REST API][]
- [Azure 儲存體小組部落格][]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure 儲存體 java SDK]: https://github.com/azure/azure-storage-java
[Android 版 SDK azure 儲存空間]: https://github.com/azure/azure-storage-android
[Azure 儲存用戶端 SDK 參考]: http://dl.windowsazure.com/storage/javadoc/
[Azure 儲存服務 REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
