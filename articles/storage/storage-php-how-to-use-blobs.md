<properties
    pageTitle="如何使用 blob 儲存體 （物件儲存區） 從 PHP |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>如何使用從 PHP blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

Azure Blob 儲存體] 是儲存在雲端的非結構化的資料，為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、 媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為 「 物件儲存空間。

本指南為您示範如何執行常見的案例使用 Azure blob 服務。 撰寫 PHP 的範例，並使用[PHP Azure SDK] [download]。 涵蓋的案例包括**上傳**、**清單**、**下載**及**刪除**二進位大型物件。 如需有關二進位大型物件的詳細資訊，請參閱[下一步](#next-steps)] 區段。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>建立 PHP 應用程式

建立 PHP 應用程式存取 Azure blob 服務的唯一的需求是參照中的 Azure SDK 類別從 PHP 的程式碼中。 您可以使用任何開發工具來建立您的應用程式，包括記事本。

本指南中，您可以使用服務功能，可以在 PHP 應用程式中稱為本機或 Azure 網頁角色、 工作者角色或網站中執行的程式碼。

## <a name="get-the-azure-client-libraries"></a>取得 Azure 用戶端文件庫

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>設定存取 blob 服務應用程式

若要使用的 Azure blob 服務 Api，您需要︰

1. 參照自動載入器檔案使用[require_once]陳述式，和
2. 參照您可以使用任何類別。

下列範例會示範如何包含自動載入器檔案，並參考**ServicesBuilder**類別。

> [AZURE.NOTE] 此範例 （和本文中的其他範例） 假設您已安裝編輯器透過 Azure PHP 用戶端文件庫。 如果您手動安裝文件庫，您需要參考`WindowsAzure.php`自動載入器檔案。

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


在下面的範例，`require_once`陳述式一律會顯示但所參照只執行範例所需的類別。

## <a name="set-up-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

若要產生 Azure blob 服務用戶端，您必須先有效的連接字串。 Blob 服務連線字串的格式是︰

存取即時服務︰

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

以存取儲存模擬器︰

    UseDevelopmentStorage=true


若要建立任何 Azure 服務用戶端，您需要使用**ServicesBuilder**類別。 您可以︰

* 傳遞的連接字串，以便直接或
* 使用**CloudConfigurationManager (CCM)**核取多個的外部來源的連線字串︰
    * 根據預設，其會出現一個外部來源-環境變數的支援。
    * 您可以新增新的來源擴充**ConnectionStringSource**類別。

此處所述的範例，如會直接傳遞的連接字串。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**BlobRestProxy**物件可讓您建立的 blob 容器使用**createContainer**方式。 建立時容器，您可以設定在容器上的選項，但是這麼做，因此不需要。 （下列範例會顯示如何設定的容器存取控制清單 (ACL) 和容器中繼資料）。

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

呼叫**setPublicAccess (PublicAccessType::CONTAINER\_與\_BLOB)**讓容器和 blob 資料可透過匿名要求存取。 呼叫**setPublicAccess(PublicAccessType::BLOBS_ONLY)**可讓您只 blob 資料可透過匿名要求存取。 如需有關容器 Acl 的詳細資訊，請參閱[設定容器 ACL (REST API)][container-acl]。

如需有關服務錯誤碼 Blob 的詳細資訊，請參閱[Blob 服務錯誤碼][error-codes]。

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

若要上傳的檔案作為 blob，請使用 [ **BlobRestProxy]-> [createBlockBlob**的方法。 不存在，或不會覆寫它如果是這樣，這項作業會建立 blob。 下列程式碼範例假設容器已經建立和使用[fopen] [fopen]若要開啟的檔案，以資料流。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

請注意前一個範例上傳當作資料流 blob。 不過，blob 可以也上傳為字串使用，例如[檔案\_取得\_內容][file_get_contents]函數。 若要執行此使用前一個範例，請變更`$content = fopen("c:\myfile.txt", "r");`至`$content = file_get_contents("c:\myfile.txt");`。

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob

若要容器中將二進位大型物件] 清單中，使用**BlobRestProxy]-> [listBlobs**方法**foreach**迴圈，透過結果。 下列程式碼會顯示為輸出容器中的每個 blob 的名稱，並顯示在瀏覽器其 URI。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>下載 blob

若要下載 blob，呼叫**BlobRestProxy]-> [getBlob**的方法，然後呼叫**getContentStream**方法所產生的**GetBlobResult**物件。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

請注意，上述範例會取得 blob 做為資料流資源 （預設行為）。 不過，您可以使用[串流\_取得\_內容][stream-get-contents]函數，傳回的資料流轉換成字串。

## <a name="delete-a-blob"></a>刪除 blob

若要刪除 blob，為了 blob 名稱與容器名稱**BlobRestProxy]-> [deleteBlob**。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>刪除 blob 容器

最後，若要刪除 blob 容器，請將容器名稱傳遞給**BlobRestProxy]-> [deleteContainer**。

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>後續步驟

現在，您學到的 Azure blob 服務的基本概念，請遵循這些連結，瞭解更複雜的儲存空間工作。

- 請造訪[Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- 請參閱[PHP 區塊 blob 範例](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php)。
- 請參閱[PHP 頁面 blob 範例](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php)。
- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
 
如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
