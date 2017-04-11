<properties
    pageTitle="快速入門佇列中儲存和 Visual Studio 連線服務 (ASP.NET) |Microsoft Azure"
    description="如何開始使用 Visual Studio 的 ASP.NET 專案中的 Azure 佇列中儲存之後連線到使用 Visual Studio 儲存的帳戶已連線服務"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services"></a>開始使用 Azure 佇列中儲存和 Visual Studio 連線服務

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本文將說明如何開始使用 Visual Studio 中 Azure 佇列中儲存之後您建立或參照 Azure 儲存體帳戶 ASP.NET 專案中的使用 Visual Studio**新增已連線服務**] 對話方塊。

我們會告訴您如何建立和存取 Azure 佇列中，在您儲存的帳戶。 我們也會告訴您如何執行基本的佇列中的作業，例如新增、 修改、 讀取及移除佇列中的郵件。 範例撰寫 C# 程式碼，並使用[Microsoft Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。 如需有關 ASP.NET 的詳細資訊，請參閱[ASP.NET](http://www.asp.net)。

Azure 佇列中的儲存空間是儲存大量郵件的可透過 HTTP 或 HTTPS 經過驗證的來電世界從任何地方存取服務。 佇列中最多可以包含數百萬儲存帳戶的容量總數限制的郵件，而單一佇列郵件可大小，最多 64 KB。

## <a name="access-queues-in-code"></a>Access 佇列中的程式碼

若要存取佇列 ASP.NET 專案中的，您需要包括任何 C# 來源檔案存取 Azure 佇列中儲存的下列項目。

1. 請確定在 C# 檔案頂端的命名空間宣告包含這些**使用**陳述式。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. 取得**CloudStorageAccount**物件，表示您儲存的帳戶資訊。 使用下列代碼以取得您的儲存空間的連接字串和 Azure 服務設定儲存帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得**CloudQueueClient**物件參照佇列中的物件您儲存的帳戶。  

        // Create the CloudQueueClient object for this storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得要參照的特定佇列**CloudQueue**物件。

        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**附註**使用下列範例中的所有代碼前面以上的程式碼。

## <a name="create-a-queue-in-code"></a>建立佇列中的程式碼

若要建立 Azure 佇列程式碼中，只要將**CreateIfNotExists**在通話新增上面的程式碼。

    // Create the messageQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>新增訊息佇列

若要插入的現有佇列中的郵件，請建立新的**CloudQueueMessage**物件，然後呼叫**AddMessage**方法。

從格式的字串 （utf-8） 或位元組陣列可以建立**CloudQueueMessage**物件。

以下是範例插入 「 Hello，全球 」 的訊息。

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>閱讀佇列中的郵件

您可以查看前面佇列訊息，而不移除呼叫 PeekMessage() 方法佇列。

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>讀取和佇列中移除郵件

您的程式碼可以移除 （解除佇列） 來自兩個步驟佇列中的訊息。
1. [撥號給 GetMessage() 取得佇列中的下一封郵件。 傳回從 GetMessage() 訊息會變成看不到任何其他佇列中讀取郵件的程式碼。 根據預設，此訊息皆會保持不可見 30 秒的時間。
2.  若要完成移除佇列中的郵件，請連絡**DeleteMessage**。

移除郵件的兩個步驟程序可確保如果您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 下列程式碼處理郵件後，通話**DeleteMessage**權限。

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-for-de-queuing-messages"></a>使用其他選項，就能取消佇列的郵件

有兩種方法您可以自訂佇列中的郵件擷取。
首先，您可以取得郵件 （最多 32) 以批的次。 第二，您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。 下列範例會使用一個通話取得 20 郵件**GetMessages**方法。 然後，它會處理使用**foreach**迴圈每一封郵件。 它也會針對每一封郵件的 5 分鐘設定隱形術逾時。 請注意，5 分鐘啟動所有郵件的同時，因此後有 5 分鐘**GetMessages**，任何未被刪除的郵件通話一次會顯示自從。

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

您可以在佇列中取得的郵件數的估計值。 **FetchAttributes**方法要求擷取佇列中的屬性，包括郵件計數 queueservice。 **ApproximateMethodCount**屬性會傳回不呼叫 queueservice 擷取**FetchAttributes**的方法，最後一個值。

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-async-await-pattern-with-common-queueapis"></a>使用含常見 queueAPIs 非同步等圖樣

此範例會示範如何使用含常見 queueAPIs 非同步等模式。 樣本通話非同步版本的每個指定的方法，非同步後修正的每一種方法可看見此。 使用非同步方法時非同步-等圖樣暫停本機執行，直到呼叫完成。 這個問題，可讓目前執行緒處理其他可協助您避免效能瓶頸，並可改善您的應用程式的整體回應。 如需.NET 中使用非同步 Await 模式的詳細資訊，請參閱 [非同步和 Await （C# 和 Visual Basic）] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>刪除佇列

若要刪除佇列中所包含的所有郵件，呼叫**都刪除**方法佇列中物件上。

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]