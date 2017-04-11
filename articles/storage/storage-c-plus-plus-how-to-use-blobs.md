<properties
    pageTitle="如何使用 blob 儲存體 （物件儲存區） 從 c + + |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
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

# <a name="how-to-use-blob-storage-from-c"></a>如何使用 c + + Blob 儲存體  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

Azure Blob 儲存體] 是儲存在雲端的非結構化的資料，為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、 媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為 「 物件儲存空間。

本文將示範如何執行常見的案例使用 Azure Blob 儲存體服務。 範例 c + + 撰寫，並使用[Azure 儲存用戶端文件庫 c + +](http://github.com/Azure/azure-storage-cpp/blob/master/README.md)。 涵蓋的案例包括**上傳**、**清單**、**下載**及**刪除**二進位大型物件。  

>[AZURE.NOTE] 本指南為 c + + 版本 1.0.0 和上方目標 Azure 儲存用戶端的文件庫。 建議的版本是儲存用戶端程式庫 2.2.0，可透過[NuGet](http://www.nuget.org/packages/wastorage)或[GitHub](https://github.com/Azure/azure-storage-cpp)。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>建立 c + + 應用程式
本指南，您會使用它們可以執行 c + + 應用程式中儲存功能。  

若要這麼做，您將需要 c + + 安裝 Azure 儲存用戶端的文件庫和 Azure 訂閱中建立 Azure 儲存體帳戶。   

若要 c + + 安裝 Azure 儲存用戶端的文件庫，您可以使用以下兩種方法︰

-   **Linux:**請依照 [ [Azure c + + 讀我檔案的儲存空間用戶端程式庫](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)] 頁面中的指示。  
-   **Windows:**在 Visual Studio 中，按一下 [**工具 > NuGet 封裝管理員 > 封裝管理員主控台**。 [NuGet 封裝管理員主控台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)中輸入下列命令，然後按**ENTER**。  

        Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>設定您的應用程式，來存取 Blob 儲存體  
新增下列包含您要使用 Azure 儲存體 Api 存取 blob c + + 檔案頂端的陳述式︰  

    #include "was/storage_account.h"
    #include "was/blob.h"

## <a name="setup-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串
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

接下來，取得**cloud_blob_client**類別的參考，因為它可讓您擷取代表容器及 blob 儲存 Blob 儲存體服務中的物件。 下列程式碼會建立使用我們擷取上方的儲存空間帳戶物件**cloud_blob_client**物件︰  

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## <a name="how-to-create-a-container"></a>如何︰ 建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

此範例會示範如何建立容器，如果不存在︰  

    try
    {
        // Retrieve storage account from connection string.
        azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

        // Create the blob client.
        azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

        // Retrieve a reference to a container.
        azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

        // Create the container if it doesn't already exist.
        container.create_if_not_exists();
    }
    catch (const std::exception& e)
    {
        std::wcout << U("Error: ") << e.what() << std::endl;
    }  

根據預設，新的容器是私人且您必須指定您從這個容器下載 blob 的儲存空間便捷鍵。 如果您想讓 [所有人使用容器內的檔案 (blob)，您可以設定的容器是公用使用下列程式碼︰  

    // Make the blob container publicly accessible.
    azure::storage::blob_container_permissions permissions;
    permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
    container.upload_permissions(permissions);  

在網際網路上的任何人都可以看到二進位大型物件在公用容器中，但您可以修改或刪除這些，只有當您有適當的便捷鍵。  

## <a name="how-to-upload-a-blob-into-a-container"></a>如何︰ 上傳至容器的 blob
Azure Blob 儲存體支援區塊二進位大型物件及頁面二進位大型物件。 在大多數情況下，封鎖 blob 會是要用的建議的類型。  

若要將檔案上傳至區塊 blob，取得容器參照並使用它來取得區塊 blob 參照。 Blob 參照之後，您可以上傳資料的任何資料流加以呼叫**upload_from_stream**的方法。 這項作業會建立 blob，如果未在先前存在，或不存在覆寫。 下列範例會示範如何上傳至容器的 blob，並假設容器所建立。  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Create or overwrite the "my-blob-1" blob with contents from a local file.
    concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
    blockBlob.upload_from_stream(input_stream);
    input_stream.close().wait();

    // Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
    // Retrieve a reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
    blob2.upload_text(U("more text"));

    // Retrieve a reference to a blob named "my-blob-3".
    azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
    blob3.upload_text(U("other text"));  

或者，您可以使用**upload_from_file**方法若要將檔案上傳至區塊 blob。

## <a name="how-to-list-the-blobs-in-a-container"></a>如何︰ 清單容器中將二進位大型物件
若要清單方塊中的 blob，先取得容器的參考。 容器的**list_blobs**方法然後可用來擷取二進位大型物件及/或目錄內。 若要存取豐富的內容，傳回**list_blob_item**的方法，您必須呼叫**list_blob_item.as_blob**方法以取得**cloud_blob**物件] 或**list_blob.as_directory**方法取得 cloud_blob_directory 物件。 下列程式碼會示範如何擷取及輸出的 URI**我範例容器**容器中的每個項目︰

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Output URI of each item.
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
        }
        else
        {
            std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
    }

如需詳細清單作業的詳細資訊，請參閱[C + + 清單 Azure 儲存空間的資源](storage-c-plus-plus-enumeration.md)。

## <a name="how-to-download-blobs"></a>如何︰ 下載二進位大型物件
若要下載二進位大型物件，請先擷取 blob 參照，然後呼叫**download_to_stream**方法。 下列範例會使用**download_to_stream**方法，對資料流物件，然後您可以保留為本機檔案傳輸 blob 內容。  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Save blob contents to a file.
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    blockBlob.download_to_stream(output_stream);

    std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();

    outfile.write((char *)&data[0], buffer.size());
    outfile.close();  

或者，您可以使用**download_to_file**方法若要下載之檔案的 blob 的內容。
此外，您也可以使用**download_text**方法若要下載的文字字串形式 blob 內容。  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-2".
    azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

    // Download the contents of a blog as a text string.
    utility::string_t text = text_blob.download_text();

## <a name="how-to-delete-blobs"></a>如何︰ 刪除二進位大型物件
若要刪除 blob，先取得 blob 的參考，然後呼叫**delete_blob**方法在其上。  

    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a previously created container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Retrieve reference to a blob named "my-blob-1".
    azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

    // Delete the blob.
    blockBlob.delete_blob();

## <a name="next-steps"></a>後續步驟
現在，您學到 blob 儲存體的基本概念，請遵循這些連結，深入瞭解 Azure 儲存體。  

-   [如何使用 c + + 佇列中儲存空間](storage-c-plus-plus-how-to-use-queues.md)
-   [如何使用 c + + 資料表儲存體](storage-c-plus-plus-how-to-use-tables.md)
-   [C + + 清單 Azure 儲存體資源](storage-c-plus-plus-enumeration.md)
-   [儲存空間 c + + 參考的用戶端文件庫](http://azure.github.io/azure-storage-cpp)
-   [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
- [傳送含有 AzCopy 命令列公用資料](storage-use-azcopy.md)
