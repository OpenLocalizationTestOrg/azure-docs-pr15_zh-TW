<properties
    pageTitle="快速入門使用.NET Azure 佇列中儲存 |Microsoft Azure"
    description="Azure 佇列提供可靠的非同步訊息應用程式元件之間。 雲端訊息可讓您的應用程式元件，以調整大小。"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="robinsh"/>

# <a name="get-started-with-azure-queue-storage-using-net"></a>使用.NET Azure 佇列中儲存快速入門

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

Azure 佇列中的儲存空間提供訊息應用程式元件間的雲端。 在設計應用程式的小數位數，應用程式元件是通常分離，讓他們可以不按比例縮放獨立。 佇列中的儲存空間提供非同步訊息應用程式元件之間的通訊是否在雲端，在桌面上，在內部部署伺服器上，或在行動裝置上執行。 佇列中的儲存空間也支援管理非同步工作，以及建立程序的工作流程。

### <a name="about-this-tutorial"></a>關於本教學課程

本教學課程中會顯示如何撰寫.NET 程式碼的一些常見的案例使用 Azure 佇列中的儲存空間。 案例涵蓋包含建立及刪除佇列及新增、 讀取及刪除佇列中的郵件。

**估計完成時間︰** 45 分鐘

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure 儲存體.NET 的用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [.Net azure 組態管理員](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- [Azure 儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>新增命名空間宣告

新增下列`using`陳述式以頂端`program.cs`檔案︰

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### <a name="parse-the-connection-string"></a>剖析的連接字串

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>建立佇列服務用戶端

**CloudQueueClient**類別可讓您擷取佇列儲存在佇列中的儲存空間。 以下是建立服務用戶端的其中一個方法︰

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

現在您已經準備好要撰寫程式碼的讀取和寫入佇列中儲存的資料。

## <a name="create-a-queue"></a>建立佇列

此範例會示範如何建立佇列中，如果不存在︰

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-a-message-into-a-queue"></a>插入佇列中的郵件

若要插入的現有佇列中的郵件，請先建立新的**CloudQueueMessage**。 接下來，呼叫**AddMessage**方法。 從格式的字串 （utf-8） 或**位元組**陣列可以建立**CloudQueueMessage** 。 以下是 （如果不存在） 建立佇列程式碼，插入 「 Hello，全球 」 的訊息︰

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-at-the-next-message"></a>查看 [下一封郵件

您可以查看前面佇列訊息，而不移除呼叫**PeekMessage**方法佇列。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-the-contents-of-a-queued-message"></a>變更佇列訊息的內容

您可以變更訊息就地佇列中的內容。 如果郵件是代表工時的任務，您可以使用這項功能更新的工時的任務狀態。 下列程式碼會更新佇列中郵件與新的內容，並設定延伸另一個 60 秒的可見度逾時。 這儲存的郵件時，相關聯的工時狀態，並提供用戶端繼續處理郵件的其他幾分鐘。 您可以使用此技巧來追蹤佇列中的郵件，而不必處理步驟失敗，因為軟硬體失敗時，從頭開始從頭多重步驟的工作流程。 一般而言，您想要保留重試次數，及郵件重試一次超過*n*次，如果您想要刪除。 這可以防止處理程序每次觸發的應用程式錯誤訊息。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="de-queue-the-next-message"></a>清除佇列 [下一封郵件

您的程式碼就能取消佇列中兩個步驟的佇列中的訊息。 當您呼叫**GetMessage**時，您會取得佇列中的下一封郵件。 傳回從**GetMessage**訊息會變成看不到任何其他佇列中讀取郵件的程式碼。 根據預設，此訊息皆會保持不可見 30 秒的時間。 若要完成移除佇列中的郵件，您也必須呼叫**DeleteMessage**。 移除郵件的兩個步驟程序可確保如果您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 處理郵件後，程式碼呼叫**DeleteMessage**權限。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>使用含常見佇列中儲存的 Api 非同步等圖樣

此範例會示範如何使用含常見佇列中儲存的 Api 非同步等模式。 樣本通話每個指定的方法，非同步的版本，以*非同步*稱謂的每一種方法。 使用非同步方法時，非同步-等圖樣暫停本機執行，直到呼叫完成。 這個問題，可讓目前執行緒執行其他工作，可協助您避免效能瓶頸並改善您的應用程式的整體回應。 如需詳細資訊.NET 中使用非同步 Await 圖樣，請參閱[非同步和 Await （C# 和 Visual Basic）](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="leverage-additional-options-for-de-queuing-messages"></a>運用就能取消佇列郵件的其他選項

有兩種方法您可以自訂佇列中的郵件擷取。
首先，您可以取得郵件 （最多 32) 以批的次。 第二，您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。 下列範例會使用一個通話取得 20 郵件**GetMessages**方法。 然後，它會處理使用**foreach**迴圈每一封郵件。 它也會針對每一封郵件的 5 分鐘設定隱形術逾時。 請注意，5 分鐘啟動所有郵件的同時，因此後有 5 分鐘**GetMessages**，任何未被刪除的郵件通話一次會顯示自從。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>取得佇列長度

您可以在佇列中取得的郵件數的估計值。 **FetchAttributes**方法要求擷取佇列中的屬性，包括郵件計數佇列服務。 **ApproximateMessageCount**屬性會傳回不呼叫佇列服務擷取**FetchAttributes**的方法，最後一個值。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-a-queue"></a>刪除佇列

若要刪除佇列中所包含的所有郵件，呼叫**都刪除**方法佇列中物件上。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>後續步驟

現在，您學到佇列中儲存的基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作。

- 檢視可用的 Api 的完整詳細的佇列中服務參考文件︰
    - [儲存.NET 參照的用戶端文件庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST API 參照](http://msdn.microsoft.com/library/azure/dd179355)
- 瞭解如何將簡化您撰寫與 Azure 儲存體搭配使用[Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md)的程式碼。
- 檢視更多的功能輔助線，若要瞭解如何將資料儲存在 Azure 中的其他選項。
    - [使用.NET Azure 資料表儲存體快速入門](storage-dotnet-how-to-use-tables.md)來儲存結構化的資料。
    - [使用.NET Azure Blob 儲存體快速入門](storage-dotnet-how-to-use-blobs.md)來儲存非結構化的資料。
    - [使用.NET (C#) SQL 資料庫連線](../sql-database/sql-database-develop-dotnet-simple.md)到儲存關聯式資料。

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
