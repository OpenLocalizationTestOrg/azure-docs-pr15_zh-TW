<properties
    pageTitle="開始使用 Azure Blob 儲存體 （物件儲存區） 使用.NET |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-blob-storage-using-net"></a>使用.NET Azure Blob 儲存體快速入門

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

Azure Blob 儲存體] 是儲存在雲端的非結構化的資料，為物件/二進位大型物件的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、 媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為 「 物件儲存空間。

### <a name="about-this-tutorial"></a>關於本教學課程

本教學課程中會顯示如何撰寫.NET 程式碼的一些常見的案例使用 Azure Blob 儲存體。 涵蓋的情況包括上傳、 清單、 下載及刪除二進位大型物件。

**估計完成時間︰** 45 分鐘

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure 儲存體.NET 的用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [.Net azure 組態管理員](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- [Azure 儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>更多範例

使用 Blob 儲存體的其他範例，請參閱[開始使用.net Azure Blob 儲存體](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)。 您可以下載的範例應用程式並執行，或瀏覽 GitHub 的程式碼。


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>新增命名空間宣告

新增下列`using`陳述式以頂端`program.cs`檔案︰

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### <a name="parse-the-connection-string"></a>剖析的連接字串

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>建立 Blob 服務用戶端

**CloudBlobClient**類別可讓您擷取容器和 blob 儲存在 Blob 儲存體。 以下是建立服務用戶端的其中一個方法︰

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

現在您已經準備好要撰寫程式碼的讀取和寫入 Blob 儲存體中的資料。

## <a name="create-a-container"></a>建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

此範例會示範如何建立容器，如果不存在︰

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

根據預設，新的容器是私人，這表示您必須指定您從這個容器下載 blob 的儲存空間便捷鍵。 如果您想要讓所有人都可以使用容器內的檔案，您可以設定的容器是公用使用下列程式碼︰

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

在網際網路上的任何人都可以看到二進位大型物件在公用容器中，但您可以修改或刪除這些，只有當您有適當的帳戶便捷鍵或共用的 access 簽章。

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

Azure Blob 儲存體支援區塊二進位大型物件及頁面二進位大型物件。  在大多數情況下，封鎖 blob 會是要用的建議的類型。

若要將檔案上傳至封鎖 blob，取得容器參照並使用它來取得封鎖 blob 參照。 Blob 參照之後，您可以上傳資料的任何資料流加以呼叫**UploadFromStream**的方法。 這項作業會建立 blob，如果未在先前存在，或不存在覆寫。

下列範例會示範如何上傳至容器的 blob，並假設容器所建立。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob

若要容器中將二進位大型物件] 清單中，先取得容器的參考。 容器的**ListBlobs**方法然後可用來擷取二進位大型物件及/或目錄內。 若要存取豐富的屬性和方法所傳回的**IListBlobItem**的您必須先轉換成**CloudBlockBlob**、 **CloudPageBlob**或**CloudBlobDirectory**物件。  如果是未知的類型，您可以使用類型核取來決定要進行轉換。  下列程式碼示範如何擷取及輸出的每個項目 URI`photos`容器︰

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

如上所示，您可以命名二進位大型物件在其名稱中的路徑資訊。 這樣會建立可以讓您組織和往返即傳統的檔案系統的虛擬目錄結構。 請注意，只是虛擬目錄結構-Blob 儲存體中可用的唯一資源是容器和二進位大型物件。 不過，儲存用戶端文件庫提供**CloudBlobDirectory**物件參照虛擬目錄，簡化的工作會以這種方式組織的二進位大型物件的程序。

例如，請考慮下列設定中名為容器中的區塊 blob `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

當您在 「 相片 」 容器 （如上述範例中） 上呼叫**ListBlobs**時，會傳回的階層式清單。 簡報包含**CloudBlobDirectory** ] 與 [ **CloudBlockBlob**物件，代表目錄二進位大型物件在容器中，分別。 輸出結果看起來像︰

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


或者，您可以設定**UseFlatBlobListing**的參數**ListBlobs**方法為**true**。 在此情況下，容器中的每個 blob 會傳回**CloudBlockBlob**物件。 返回一般清單**ListBlobs**通話看起來像這樣︰

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

然後，結果看起來像這樣︰

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## <a name="download-blobs"></a>下載二進位大型物件

若要下載二進位大型物件，請先擷取 blob 參照，然後呼叫**DownloadToStream**方法。 下列範例會使用**DownloadToStream**方法，對資料流物件，然後您可以保留為本機檔案傳輸 blob 內容。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

您也可以使用**DownloadToStream**方法若要下載的文字字串形式 blob 內容。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>刪除二進位大型物件

若要刪除 blob，請先取得 blob 的參考，接著呼叫上的 [**刪除**的方法。

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>非同步清單頁面中的二進位大型物件

如果您列出大量的二進位大型物件，或您想要控制您在單一清單作業中傳回的結果的數字，您可以列出二進位大型物件中的結果頁面。 此範例會示範如何以非同步方式傳回結果頁面中，以便執行就不會被時傳回的結果大量正在等待。

此範例顯示一般 blob 清單，但您也可以執行的階層式清單，藉由設定`useFlatBlobListing`參數的**ListBlobsSegmentedAsync**方法`false`。

因為範例方法呼叫非同步的方法，必須開頭與`async`關鍵字，而且必須傳回**任務**的物件。 指定**ListBlobsSegmentedAsync**方法 await 關鍵字暫停範例方法執行，直到列出工作完成。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## <a name="writing-to-an-append-blob"></a>若要新增 blob 撰寫

新增 blob 是一種新的版本推出 blob 5.x.NET Azure 儲存用戶端文件庫。 新增 blob 最適合附加作業，例如記錄。 封鎖 blob，例如新增 blob 包含的區塊，，但當您要新增 blob 新增新的區塊，就一定會附加到 blob 的結尾。 您無法更新，或刪除現有的區塊中新增 blob。 對封鎖 blob，不被公開新增 blob 的區塊識別碼。

新增 blob 中的每個區塊，不同的大小，上限為 4 MB，並新增 blob 可以包含 50000 區塊的最大值。 新增 blob 的大小上限為因此稍微高於 195 GB （4 MB X 50000 區塊）。

下列範例會建立新的附加 blob，並將一些資料附加，模擬簡單的記錄作業。

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

如需有關 blob 的三種類型之間的差異，請參閱[瞭解區塊 Blob、 頁面 Blob 和附加二進位大型物件](https://msdn.microsoft.com/library/azure/ee691964.aspx)。

## <a name="managing-security-for-blobs"></a>管理二進位大型物件的安全性

依預設，Azure 儲存體保留您的資料安全限制存取帳戶擁有者，擁有的帳戶便捷鍵的人員。 當您需要共用 blob 儲存體帳戶中的資料時，請務必執行此作業，而不會危害您的帳戶便捷鍵的安全性。 此外，您可以加密 blob 資料，以確保安全進行網路連線和 Azure 儲存體。

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>控制 blob 資料的存取權

根據預設，blob 中的資料您儲存的帳戶是只有儲存帳戶擁有者可以存取。 依預設，驗證對 Blob 儲存體要求需要帳戶便捷鍵。 不過，您可能想讓其他使用者可以使用特定 blob 資料。 您有兩個選項︰

- **匿名存取︰**您可以讓容器或其二進位大型物件可公開使用匿名存取。 如需詳細資訊，請參閱[管理匿名的讀取權限容器及二進位大型物件](storage-manage-access-to-resources.md)。
- **共用 access 簽章︰**您可以使用共用的 access 簽章 (SA)，可提供委派的存取您儲存的帳戶，在資源，與您指定的權限，以及在您指定的時間間隔提供用戶端。 如需詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md) 。

### <a name="encrypting-blob-data"></a>加密 blob 的資料

Azure 儲存支援加密 blob 資料在用戶端與伺服器上︰

- **用戶端加密︰**儲存空間用戶端文件庫.NET 支援加密至 Azure 儲存體上, 傳和下載至用戶端時解密資料之前的用戶端應用程式中的資料。 文件庫儲存帳戶金鑰管理也支援與 Azure 金鑰保存庫整合。 如需詳細資訊，請參閱[針對 Microsoft Azure 儲存體.net 的用戶端加密](storage-client-side-encryption.md)。 另請參閱[教學課程︰ 加密並解密二進位大型物件中使用 Azure 金鑰保存庫 Microsoft Azure 儲存體](storage-encrypt-decrypt-blobs-key-vault.md)。
- **伺服器端加密**︰ Azure 儲存體現在支援的伺服器端加密。 請參閱[Azure 儲存服務加密的其餘部分 （預覽版本） 的資料](storage-service-encryption.md)。

## <a name="next-steps"></a>後續步驟

現在，您學到 Blob 儲存體的基本概念，請遵循這些連結，瞭解更多。

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 儲存檔案總管
- [Microsoft Azure 儲存檔案總管 (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 可讓您以視覺方式 Azure 儲存體資料上使用 Windows 與 OS X Linux 的免費，獨立應用程式。

### <a name="blob-storage-samples"></a>Blob 儲存體範例

- [快速入門.net Azure Blob 儲存體](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Blob 儲存體參照

- [儲存.NET 參照的用戶端文件庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST API 參照](http://msdn.microsoft.com/library/azure/dd179355)

### <a name="conceptual-guides"></a>概念性輔助線

- [傳送含有 AzCopy 命令列公用資料](storage-use-azcopy.md)
- [快速入門.net 的檔案儲存空間](storage-dotnet-how-to-use-files.md)
- [如何使用 WebJobs SDK Azure blob 儲存體](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
