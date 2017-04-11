<properties
    pageTitle="快速入門佇列中儲存和 Visual Studio 連線服務 (ASP.NET 5) |Microsoft Azure"
    description="如何開始使用 Visual Studio 中 ASP.NET 5 專案中的 Azure 佇列中的儲存空間"
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
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>快速入門佇列中儲存和 Visual Studio 連線服務 (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

##<a name="overview"></a>概觀

本文將說明如何開始使用 Visual Studio 中 Azure 佇列中儲存之後您建立或參照 Azure 儲存體帳戶 ASP.NET 5 專案中的使用 Visual Studio**新增已連線服務**] 對話方塊。 [**已連線服務**] 作業安裝適當的 NuGet 封裝來存取您專案中的 Azure 儲存空間，並新增您的專案設定檔儲存帳戶的連線字串。

Azure 佇列中的儲存空間是儲存大量郵件的可透過 HTTP 或 HTTPS 經過驗證的來電世界從任何地方存取服務。 單一佇列訊息最多 64 kb 的大小，而佇列中最多可以包含數百萬儲存帳戶的容量總數限制的郵件。

若要開始，您必須建立 Azure 佇列中，在您儲存的帳戶。 我們會告訴您如何建立佇列中的程式碼。 我們也會告訴您如何執行基本的佇列中的作業，例如新增、 修改、 讀取及移除佇列中的郵件。 範例撰寫 C\#程式碼，並使用.NET Azure 儲存用戶端的文件庫。 如需有關 ASP.NET 的詳細資訊，請參閱[ASP.NET](http://www.asp.net)。

**附註︰**ASP.NET 5 中執行的來電至 Azure 儲存空間的 Api 部分是非同步。 如需詳細資訊，請參閱[非同步程式設計非同步與 Await](http://msdn.microsoft.com/library/hh191443.aspx) 。 下列程式碼假設正在使用非同步程式設計的方法。

- 如需有關以程式設計方式管理佇列，請參閱[快速入門使用.NET Azure 佇列中儲存空間](storage-dotnet-how-to-use-queues.md)。
- 有關 Azure 儲存體的一般資訊，請參閱[儲存文件](https://azure.microsoft.com/documentation/services/storage/)。
- 如需 Azure 雲端服務的一般資訊，請參閱[雲端服務文件](https://azure.microsoft.com/documentation/services/cloud-services/)。
- 如需撰寫 ASP.NET 應用程式的相關資訊，請參閱[ASP.NET](http://www.asp.net) 。





##<a name="access-queues-in-code"></a>Access 佇列中的程式碼

若要存取佇列 ASP.NET 5 專案中的，您需要包含下列任何 C# 來源檔案存取 Azure 佇列中儲存的項目。

1. 請確定在 C# 檔案頂端的命名空間宣告包含這些**使用**陳述式。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 取得**CloudStorageAccount**物件，表示您儲存的帳戶資訊。 使用下列代碼以取得您的儲存空間的連接字串和 Azure 服務設定儲存帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. 取得**CloudQueueClient**物件參照佇列中的物件您儲存的帳戶。  

        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. 取得要參照的特定佇列**CloudQueue**物件。

        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");


**附註︰**使用下列範例中的所有代碼前面以上的程式碼。

###<a name="create-a-queue-in-code"></a>建立佇列中的程式碼

若要建立 Azure 佇列中，在程式碼中，只要加入**CreateIfNotExistsAsync**通話。

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

##<a name="add-a-message-to-a-queue"></a>新增訊息佇列

若要插入的現有佇列中的郵件，請建立新的**CloudQueueMessage**物件，然後呼叫**AddMessageAsync**方法。

從格式的字串 （utf-8） 或位元組陣列可以建立**CloudQueueMessage**物件。

以下是範例插入 「 Hello，全球 」 的訊息。

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

##<a name="read-a-message-in-a-queue"></a>閱讀佇列中的郵件

您可以查看前面佇列訊息，而不移除呼叫**PeekMessageAsync**方法佇列。

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


##<a name="read-and-remove-a-message-in-a-queue"></a>讀取和佇列中移除郵件

您的程式碼可以移除 （解除佇列） 來自兩個步驟佇列中的訊息。
1. 呼叫**GetMessageAsync**取得佇列中的下一封郵件。 傳回從**GetMessageAsync**訊息會變成看不到任何其他佇列中讀取郵件的程式碼。 根據預設，此訊息皆會保持不可見 30 秒的時間。
2.  若要完成移除佇列中的郵件，請連絡**DeleteMessageAsync**。

移除郵件的兩個步驟程序可確保如果您的程式碼無法處理的訊息，因為硬體或軟體失敗，另一個程式碼執行個體可以取得相同的訊息並再試一次。 下列程式碼處理郵件後，通話**DeleteMessageAsync**權限。

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>運用佇列郵件的其他選項

有兩種方法您可以自訂佇列中的郵件擷取。
首先，您可以取得郵件 （最多 32) 以批的次。 第二，您可以設定拉長或縮短隱形術逾時，可讓您的程式碼，更多或更少的時間才能完全處理每一封郵件。 下列範例會使用**GetMessages**方法一通電話取得 20 的郵件。 然後，它會處理使用**foreach**迴圈每一封郵件。 它也會設定為 5 分鐘的每一封郵件的隱形術逾時。 請注意，5 分鐘開始所有郵件的同時，因此後有 5 分鐘傳遞**GetMessages**，就會顯示一次不刪除任何郵件呼叫之後。

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>取得佇列長度

您可以在佇列中取得的郵件數的估計值。 **FetchAttributes**方法要求擷取佇列中的屬性，包括郵件計數佇列服務。 **ApproximateMethodCount**屬性會傳回擷取**FetchAttributes**的方法，而不撥打佇列服務的最後一個值。

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>使用含一般佇列 Api 非同步等模式

此範例會示範如何使用含一般佇列 Api 非同步等模式。 樣本通話非同步版本的每個指定的方法。 這可以看到非同步後修正的每一種方法。 使用 [非同步方法時，非同步等模式等到完成通話後，就會暫停本機執行。 這個問題，可讓目前執行緒處理其他可協助您避免效能瓶頸，並可改善您的應用程式的整體回應。 如需.NET 中使用非同步 Await 模式的詳細資訊，請參閱 [非同步和 Await （C# 和 Visual Basic）] (https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## <a name="delete-a-queue"></a>刪除佇列

若要刪除佇列中所包含的所有郵件，呼叫佇列中物件上的 [**都刪除**的方法。

    // Delete the queue.
    messageQueue.Delete();


##<a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
