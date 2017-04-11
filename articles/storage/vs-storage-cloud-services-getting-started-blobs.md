<properties
    pageTitle="快速入門 blob 儲存體和 Visual Studio 連線服務 （雲端服務） |Microsoft Azure"
    description="如何開始連線到儲存帳戶使用 Visual Studio 連接服務之後，在 Visual Studio 雲端服務專案中使用 Azure Blob 儲存體"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>開始使用 Azure Blob 儲存體與 Visual Studio 連線 （雲端服務的專案） 的服務

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

本文將說明如何建立，或使用 Visual Studio 雲端服務專案中的 [Visual Studio**新增已連線服務**] 對話方塊參照 Azure 儲存體帳戶之後，快速入門 Azure Blob 儲存體。 我們會告訴您如何存取及建立 blob 容器，以及如何執行一般工作，例如上傳、 清單，以及下載二進位大型物件。 範例撰寫 C\#及使用[Microsoft Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

Azure Blob 儲存體] 是儲存大量可透過 HTTP 或 HTTPS 世界從任何地方存取的非結構化資料的服務。 單一 blob，其大小。 二進位大型物件可項目，例如圖像、 音訊和視訊檔案，原始資料及文件檔案。

如同在資料夾中的 [即時檔案，blob 儲存體中的 [即時容器。 建立儲存之後，您可以建立一或多個容器中儲存空間。 例如中稱為 「 剪貼簿] 中的儲存空間，您可以建立容器中稱為 「 圖像 」 將圖片儲存空間，另一個名為 「 音效 」 儲存音訊檔案。 建立容器之後，可以將個別 blob 檔案上傳至。

- 如需有關如何以程式設計方式管理二進位大型物件的詳細資訊，請參閱[快速入門使用.NET Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。
- 一般 Azure 儲存體的詳細資訊，請參閱[儲存文件](https://azure.microsoft.com/documentation/services/storage/)。
- 一般 Azure 雲端服務的詳細資訊，請參閱[雲端服務文件](https://azure.microsoft.com/documentation/services/cloud-services/)。
- 如需有關程式設計 ASP.NET 應用程式的詳細資訊，請參閱[ASP.NET](http://www.asp.net)。

## <a name="access-blob-containers-in-code"></a>在 [程式碼存取 blob 容器

若要以程式控制方式存取二進位大型物件在雲端服務的專案，您需要新增下列項目，如果已經不存在。

1. 移至您要以程式控制方式存取 Azure 儲存體任何 C# 檔案頂端新增下列程式碼命名空間宣告。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 取得**CloudStorageAccount**物件，表示您儲存的帳戶資訊。 使用下列代碼以取得您的儲存空間的連接字串和 Azure 服務設定儲存帳戶資訊。

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. 取得**CloudBlobClient**物件參照在您儲存帳戶現有容器。

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

4. 取得要參照的特定 blob 容器**CloudBlobContainer**物件。

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] 使用所有前面下列各節中的程式碼之前程序中所顯示的程式碼。

## <a name="create-a-container-in-code"></a>在 [程式碼中建立容器

> [AZURE.NOTE] ASP.NET 中執行時的來電至 Azure 儲存體一些 Api 是非同步。 如需詳細資訊，請參閱[非同步程式設計非同步與 Await](http://msdn.microsoft.com/library/hh191443.aspx) 。 下列範例中的程式碼，假設您使用的非同步程式設計的方法。

若要在您儲存帳戶中建立容器，您只需要為加入通話**CreateIfNotExistsAsync**如下列所示︰

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


對所有人進行容器內的檔案，您可以設定使用下列程式碼是公用容器。

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });


在網際網路上的任何人都可以看到二進位大型物件在公用容器中，但您可以修改或刪除這些，只有當您有適當的便捷鍵。

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

Azure 儲存支援區塊二進位大型物件及頁面二進位大型物件。 在大多數情況下，封鎖 blob 會是要用的建議的類型。

若要將檔案上傳至區塊 blob，取得容器參照並使用它來取得區塊 blob 參照。 Blob 參照之後，您可以上傳資料的任何資料流加以呼叫**UploadFromStream**的方法。 如果沒有先前存在，或如果存在覆寫，這項作業會建立 blob。 下列範例會示範如何上傳至容器的 blob，並假設容器所建立。

    // Retrieve a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob

若要清單方塊中的 blob，先取得容器的參考。 容器的**ListBlobs**方法然後可用來擷取二進位大型物件及/或目錄內。 若要存取豐富的屬性和方法所傳回的**IListBlobItem**的您必須先轉換成**CloudBlockBlob**、 **CloudPageBlob**或**CloudBlobDirectory**物件。 如果是未知的類型，您可以使用類型核取來決定要進行轉換。 下列程式碼會示範如何擷取及輸出的 URI**相片**容器中的每個項目︰

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

之前的程式碼範例所示，blob 服務具有目錄容器，以及內的概念。 這是，好讓您可以將組織中的其他類似的資料夾結構您二進位大型物件。 例如，請考慮下列設定中名為**相片**容器中的區塊二進位大型物件︰

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

當您在 （如前一個範例） 容器上呼叫**ListBlobs**時，傳回集合包含**CloudBlobDirectory**和**CloudBlockBlob**代表目錄和包含最上層的二進位大型物件的物件。 以下是輸出結果︰

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


或者，您可以設定**UseFlatBlobListing**的參數**ListBlobs**方法為**true**。 這會傳回**CloudBlockBlob**，無論目錄為每個 blob。 以下是**ListBlobs**通話︰

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

然後，結果如下︰

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

如需詳細資訊，請參閱[CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx)。

## <a name="download-blobs"></a>下載二進位大型物件

若要下載二進位大型物件，請先擷取 blob 參照，然後呼叫**DownloadToStream**方法。 下列範例會使用**DownloadToStream**方法，對資料流物件，然後您可以保留為本機檔案傳輸 blob 內容。

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

您也可以使用**DownloadToStream**方法若要下載的文字字串形式 blob 內容。

    // Get a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>刪除二進位大型物件

若要刪除 blob，先取得 blob 的參考，然後呼叫**刪除**方法。

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>非同步清單頁面中的二進位大型物件

如果您列出大量的二進位大型物件，或您想要控制您在單一清單作業中傳回的結果的數字，您可以列出二進位大型物件中的結果頁面。 此範例會示範如何以非同步方式傳回結果頁面中，以便執行就不會被時傳回的結果大量正在等待。

此範例顯示一般 blob 清單，但您也可以執行的階層式清單， **ListBlobsSegmentedAsync**方法**useFlatBlobListing**參數設**為 false**。

因為範例方法呼叫非同步的方法，必須使用**非同步**關鍵字，做開頭，而且傳回**工作**物件。 指定**ListBlobsSegmentedAsync**方法 await 關鍵字暫停範例方法執行，直到列出工作完成。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        // List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        // When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            // or by calling a different overload.
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

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
