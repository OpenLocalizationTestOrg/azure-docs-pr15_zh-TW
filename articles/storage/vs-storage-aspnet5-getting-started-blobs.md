<properties
    pageTitle="快速入門 blob 儲存體和 Visual Studio 連線服務 (ASP.NET 5) |Microsoft Azure"
    description="如何建立使用連線的 Visual Studio services 儲存帳戶後，使用 Azure Blob 儲存體 Visual Studio ASP.NET 5 專案中快速入門"
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

# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>開始使用 Azure Blob 儲存體和 Visual Studio 連線服務 (ASP.NET 5)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

##<a name="overview"></a>概觀

本文將說明如何開始使用 Visual Studio Azure Blob 儲存體之後您建立或參照 Azure 儲存體帳戶 ASP.NET 5 專案中的使用 Visual Studio 新增已連線服務] 對話方塊。

Azure Blob 儲存體] 是儲存大量可透過 HTTP 或 HTTPS 世界從任何地方存取的非結構化資料的服務。 單一 blob，其大小。 二進位大型物件可項目，例如圖像、 音訊和視訊檔案，原始資料及文件檔案。 本文將說明如何使用 Visual Studio**新增已連線服務**] 對話方塊中的 ASP.NET 5 專案建立 Azure 儲存體帳戶後，快速入門 blob 儲存體。

如同在資料夾中的 [即時檔案，blob 儲存體中的 [即時容器。 建立儲存之後，您可以建立一或多個容器中儲存空間。 例如中稱為 「 剪貼簿] 中的儲存空間，您可以建立容器中稱為 「 圖像 」 將圖片儲存空間，另一個名為 「 音效 」 儲存音訊檔案。 建立容器之後，可以將個別 blob 檔案上傳至。 如需有關以程式設計方式管理二進位大型物件，請參閱[快速入門使用.NET Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)。

##<a name="access-blob-containers-in-code"></a>在 [程式碼存取 blob 容器

若要以程式控制方式存取 blob ASP.NET 5 專案中的，您需要新增下列項目，如果已經不存在。

1. 移至您要以程式控制方式存取 Azure 儲存任何 C# 檔案頂端新增下列程式碼命名空間宣告。

        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;

2. 取得**CloudStorageAccount**物件，表示您儲存的帳戶資訊。 使用下列代碼以取得您的儲存空間的連接字串和 Azure 服務設定儲存帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **附註︰**使用下列各節中的所有代碼前面以上的程式碼。


3. 若要在您儲存帳戶取得**CloudBlobContainer**參照至現有的容器使用**CloudBlobClient**物件。

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##<a name="create-a-container-in-code"></a>在 [程式碼中建立容器

您也可以使用**CloudBlobClient**儲存帳戶中建立容器。 您要做為加入通話**CreateIfNotExistsAsync**如下列所示︰

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**附註︰**ASP.NET 5 中執行的來電至 Azure 儲存空間的 Api 是非同步。 如需詳細資訊，請參閱[非同步程式設計非同步與 Await](http://msdn.microsoft.com/library/hh191443.aspx) 。 下列程式碼假設正在使用非同步程式設計的方法。

對所有人進行容器內的檔案，您可以設定使用下列程式碼是公用容器。

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##<a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

若要將 blob 檔案上傳至容器，取得容器參照並使用它來取得 blob 的參考。 Blob 參考之後，您可以上傳資料的任何資料流加以呼叫**UploadFromStreamAsync**的方法。 如果沒有出現，或會覆寫它存在，這項作業會建立 blob。 下列範例會示範如何上傳至容器的 blob，並假設容器所建立。

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##<a name="list-the-blobs-in-a-container"></a>清單方塊中 blob
若要清單方塊中的 blob，先取得容器的參考。 此外，您就可以呼叫容器的**ListBlobsSegmentedAsync**方法來擷取二進位大型物件及/或目錄內。 若要存取豐富的屬性和方法所傳回的**IListBlobItem**的您必須先轉換成**CloudBlockBlob**、 **CloudPageBlob**或**CloudBlobDirectory**物件。 如果您不知道 blob 類型，您可以使用類型核取來決定要進行轉換。 下列程式碼會示範如何擷取及輸出的 URI 容器中的每個項目。

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
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
    } while (token != null);

有其他方法可以清單 blob 容器中的內容。 如需詳細資訊，請參閱[快速入門使用.NET Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container)。

##<a name="download-a-blob"></a>下載 blob
若要下載 blob，先取得 blob 的參照，然後呼叫**DownloadToStreamAsync**的方法。 下列範例會使用**DownloadToStreamAsync**方法，對您可儲存的本機檔案資料流物件傳輸 blob 內容。

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

有其他方法可以將 blob 儲存為檔案。 如需詳細資訊，請參閱[快速入門使用.NET Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md#download-blobs)。

##<a name="delete-a-blob"></a>刪除 blob
若要刪除 blob，先取得 blob 的參照，然後呼叫**DeleteAsync**方法在其上。

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
