<properties
    pageTitle="如何使用從 Node.js Blob 儲存體 |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>如何使用從 Node.js Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

本文將示範如何執行常見的案例使用 Blob 儲存體。 透過 Node.js API 寫入範例。 涵蓋的案例說明如何上傳、 清單、 下載及刪除二進位大型物件。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

如需如何建立 Node.js 應用程式的指示，請參閱[建立 Node.js web app Azure 應用程式服務中]，[建立並部署至 Azure 雲端服務的 Node.js 應用程式]--使用 Windows PowerShell 或[建立並部署 Azure 使用 Web 矩陣 Node.js web 應用程式]。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式，以存取儲存空間

若要使用 Azure 儲存空間，您需要 Node.js，其中包含一組方便時觀看文件庫與儲存其餘服務 Azure 儲存體 SDK。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>若要取得套件使用節點套件管理員 (NPM)

1.  使用**PowerShell** (Windows)、**終端機**(Mac)，或**被**(Unix)，例如命令列介面瀏覽至您用來建立範例應用程式的資料夾。

2.  在 [命令] 視窗中，輸入**npm 安裝 azure 儲存體**。 命令輸出非常類似下列程式碼範例。

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  您可以手動執行**1**命令，以確認**節點\_模組**所建立的資料夾。 在該資料夾中，尋找**azure 儲存體**套件，其中包含您要存取儲存的文件庫。

### <a name="import-the-package"></a>匯入套件

使用 [記事本] 或另一個文字編輯器，移至您要使用儲存空間的應用程式**server.js**檔案頂端新增下列︰

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

Azure 模組會讀取環境變數`AZURE_STORAGE_ACCOUNT`和`AZURE_STORAGE_ACCESS_KEY`，或`AZURE_STORAGE_CONNECTION_STRING`，連線至 Azure 儲存體帳戶所需的資訊。 如果沒有設定這些環境變數，您必須在呼叫**createBlobService**時，指定帳戶資訊。

如需設定環境變數 Azure web 應用程式的[Azure 入口網站](https://portal.azure.com)中的範例，請參閱[Node.js web 應用程式使用 Azure 資料表服務]。

## <a name="create-a-container"></a>建立容器

**BlobService**物件可讓您使用容器和二進位大型物件。 下列程式碼會建立**BlobService**物件。 新增**server.js**上方附近下列動作︰

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] 您仍然可以在匿名存取 blob，請使用**createBlobServiceAnonymous**主機地址。 例如，使用`var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`。

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

若要建立新的容器，請使用**createContainerIfNotExists**。 下列範例會建立新的容器名為 「 mycontainer 」:

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

如果為新建立的容器`result.created`為 true。 如果容器已經存在，`result.created`為 false。 `response`包含作業，包括容器的 ETag 資訊的相關資訊。

### <a name="container-security"></a>容器的安全性

根據預設，新的容器都是私人且無法匿名存取。 若要公開，讓您可以匿名存取容器，您可以設定容器的存取層級**blob**或**容器**。

* **blob** -可讓匿名 blob 內容和中繼資料中此容器，而不是容器中繼資料，例如列出容器內的所有 blob 的讀取權限

* **容器**-允許 blob 內容和中繼資料，以及容器中繼資料的匿名讀取權限

下列範例會示範設定**blob**的存取層級︰

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

或者，您可以修改容器的存取層級，請使用**setContainerAcl**指定的存取層級。 下列範例會變更為容器的存取層級︰

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

結果包含作業，包括目前**ETag**容器的相關資訊。

### <a name="filters"></a>篩選

您可以將選用的篩選作業套用至使用**BlobService**執行的作業。 篩選作業，可以包含記錄、 自動重試等。篩選是物件的實作具有簽章的方法︰

    function handle (requestOptions, next)

執行其前置處理要求選項之後, 的方法需要呼叫 [下一步]，傳遞使用下列的簽章的回撥︰

    function (returnObject, finalCallback, next)

這回撥，和 [後中處理 returnObject （回應要求從伺服器），回撥必須 [下一步呼叫若存在繼續處理其他篩選] 或 [直接叫用 finalCallback 結束服務叫用。

實作重試邏輯的兩個篩選所包含的 Node.js、 **ExponentialRetryPolicyFilter**及**LinearRetryPolicyFilter**Azure SDK 的。 下列範例會建立使用**ExponentialRetryPolicyFilter**的**BlobService**物件︰

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

有三種類型的二進位大型物件︰ 封鎖二進位大型物件、 頁面二進位大型物件和附加二進位大型物件。 封鎖 blob 可讓您更有效率地上傳大型資料。 附加 blob 適用於附加作業。 頁面 blob 適用於讀/寫作業。 如需詳細資訊，請參閱[瞭解區塊 Blob、 附加二進位大型物件和頁面二進位大型物件](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

### <a name="block-blobs"></a>封鎖二進位大型物件

若要上傳至區塊 blob 資料使用下列步驟︰

* 建立新的區塊 blob **createBlockBlobFromLocalFile** -，然後上傳檔案的內容

* **createBlockBlobFromStream** -建立新的區塊 blob 並上傳的資料流內容

* 建立新的區塊 blob **createBlockBlobFromText** -，然後上傳的字串的內容

* **createWriteStreamToBlockBlob** -提供寫入到的資料流區塊 blob

下列範例上傳到**myblob**的**test.txt**檔案的內容。

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

`result`傳回這些方法所包含的作業，例如**ETag** blob 的相關資訊。

### <a name="append-blobs"></a>附加二進位大型物件

若要上傳至新的附加 blob 資料使用下列步驟︰

* 建立新的附加 blob **createAppendBlobFromLocalFile** -，然後上傳檔案的內容

* **createAppendBlobFromStream** -建立新的附加 blob 並上傳的資料流內容

* **createAppendBlobFromText** -建立新的附加 blob 並上傳的字串的內容

* 建立新的附加 blob **createWriteStreamToNewAppendBlob** -，並提供資料流寫入

下列範例上傳到**myappendblob**的**test.txt**檔案的內容。

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

若要附加至現有的附加 blob 的區塊，請使用下列步驟︰

* **appendFromLocalFile** -將檔案的內容新增至現有的附加 blob

* **appendFromStream** -將資料流內容新增至現有的附加 blob

* **appendFromText** -附加至現有的附加 blob 的字串的內容

* **appendBlockFromStream** -將資料流內容新增至現有的附加 blob

* **appendBlockFromText** -附加至現有的附加 blob 的字串的內容

> [AZURE.NOTE] appendFromXXX Api 會執行用戶端驗證失敗快速若要避免 unncessary 伺服器通話。 appendBlockFromXXX 不會。

下列範例上傳到**myappendblob**的**test.txt**檔案的內容。

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>頁面二進位大型物件

若要上載至頁面 blob 的資料，請使用下列步驟︰

* **createPageBlob** -建立新的頁面 blob 的特定的長度

* 建立新的頁面 blob **createPageBlobFromLocalFile** -，然後上傳檔案的內容

* 建立新的頁面 blob **createPageBlobFromStream** -，然後上傳的資料流內容

* **createWriteStreamToExistingPageBlob** -提供至現有的頁面 blob 寫入資料流

* 建立新的頁面 blob **createWriteStreamToNewPageBlob** -，並提供資料流寫入

下列範例上傳到**mypageblob**的**test.txt**檔案的內容。

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] 頁面 blob 包含 512 個位元組 」 頁面 」。 上傳大小的不是 512 的倍數的資料時，您會收到錯誤。

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob

若要在容器 blob] 清單中，使用**listBlobsSegmented**方法。 如果您想要傳回與特定的前置字元的二進位大型物件，請使用 [ **listBlobsSegmentedWithPrefix**。

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

`result`包含`entries`集合，此為陣列的描述每個 blob 的物件。 如果無法傳回所有 blob，`result`也會提供`continuationToken`，而您可以使用當做第二個參數以擷取其他項目。

## <a name="download-blobs"></a>下載二進位大型物件

若要下載從 blob 的資料，請使用下列步驟︰

* **getBlobToLocalFile** -將 blob 內容檔案

* **getBlobToStream** -寫入資料流 blob 內容

* **getBlobToText** -將 blob 內容寫入字串

* **createReadStream** -提供讀取 blob 資料流

下列範例示範**getBlobToStream**下載**myblob** blob 的內容，並使用資料流將其儲存至**output.txt**檔案︰

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

`result`包含 blob，包括**ETag**資訊的相關資訊。

## <a name="delete-a-blob"></a>刪除 blob

最後，若要刪除 blob，請連絡**deleteBlob**。 下列範例會刪除命名**myblob**blob。

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>並行存取

若要從多個用戶端或多個程序執行個體中支援至 blob 並行存取，您可以使用**Etag**或**租用**。

* **Etag** -會提供偵測的方式 blob，或已由其他處理程序修改容器

* **租賃**-提供取得獨佔、 儲值、 撰寫或刪除存取 blob 的一段時間的方式

### <a name="etag"></a>ETag

若要允許多個用戶端或執行個體，寫入到封鎖 Blob 或頁面的使用 Etag 同時 Blob。 ETag 可讓您判斷是否容器或 blob，因為您最初讀取或建立，可讓您避免覆寫變更另一個用戶端或處理程序來修改。

您可以使用 [選擇性設定 ETag 條件`options.accessConditions`參數。 下列範例只上傳**test.txt**檔案，如果 blob 已存在具有 ETag 值所包含的`etagToMatch`。

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

當您使用的 Etag 時，一般模式是︰

1. 取得 ETag 建立、] 清單中或取得作業的結果。

2. 執行動作，檢查不修改 ETag 值。

如果已修改的值，表示的另一個用戶端或執行個體或修改過 blob 容器取得 ETag 值之後。

### <a name="lease"></a>租賃

您可以取得新租用使用**acquireLease**方法，指定您想要在取得租用 blob 或容器。 例如，下列程式碼會取得上**myblob**租用。

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

必須提供後續作業**myblob** `options.leaseId`參數。 識別碼會傳回的租用`result.id`從**acquireLease**。

> [AZURE.NOTE] 根據預設，租用期間是無限制。 您可以藉由指定非無限之間的工期 （15 到 60 秒）`options.leaseDuration`參數。

若要移除租用，請使用**releaseLease**。 若要中斷租用，但防止其他人取得新租用過期後，原始的持續時間，使用**breakLease**。

## <a name="work-with-shared-access-signatures"></a>使用共用的 access 簽章

共用的 access 簽章 (SA) 是安全的方法，但不供您儲存體帳戶名稱或鍵提供細微存取 blob 與容器。 共用的 access 簽章通常用於提供您的資料，例如 [允許存取 blob 的行動應用程式的有限存取權。

> [AZURE.NOTE] 雖然您也可以允許匿名存取 blob，共用的 access 簽章可讓您提供更多的控制的存取，您必須產生 SA。

信任的應用程式，例如雲端服務產生使用的**BlobService**， **generateSharedAccessSignature**共用的 access 簽章，並將其提供給受信任或完全信任應用程式上，例如行動應用程式。 共用的 access 簽章所產生的使用原則，說明開始和結束的日期的期間共用的 access 簽章是有效的以及共用的 access 簽章持有者授與存取層級。

下列範例會產生新的共用的存取原則，可讓共用的 access 簽章擁有者，了解在上執行作業**myblob** blob 和到期 100 分鐘後就會建立的時間。

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

請注意，主機必須提供資訊也，因為它是必要的共用的存取簽章持有者嘗試存取容器時。

然後，用戶端應用程式使用**BlobServiceWithSAS**執行作業 blob 共用的 access 簽章。 下列取得**myblob**的相關資訊。

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

之後如果嘗試修改 blob 的唯讀存取權，產生共用的 access 簽章，會傳回錯誤。

### <a name="access-control-lists"></a>存取控制清單

您也可以使用存取控制清單 (ACL) 若要設定 SA 存取原則。 這是很有用，如果您想要允許多個用戶端存取容器，但每個用戶端提供不同的存取原則。

ACL 實作存取原則的陣列，使用每個原則相關聯的識別碼。 下列範例會定義兩個原則，一個 'user1'，一個用於 'user2 」:

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

下列範例會取得**mycontainer**，目前 ACL，然後新增 [使用**setBlobAcl**的新原則。 可讓這種方法︰

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

一旦 ACL 設定]，然後您可以建立共用的 access 簽章根據原則的識別碼。 下列範例會建立新的共用的存取簽章的 「 user2 」:

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源。

-   [Azure 儲存體 SDK 節點 API 參考][]
-   [Azure 儲存體小組部落格][]
-   在 GitHub [Azure 儲存體 SDK 節點][]存放庫
-   [Node.js 開發人員中心](/develop/nodejs/)
-   [傳送含有 AzCopy 命令列公用資料](storage-use-azcopy.md)

[Azure 儲存體 SDK 節點]: https://github.com/Azure/azure-storage-node

[Azure 應用程式服務中建立 Node.js web 應用程式]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[使用 Azure 資料表服務 Node.js web 應用程式]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[建立並部署 Node.js web 應用程式至 Azure 使用 Web 矩陣]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[建立並部署至 Azure 雲端服務的 Node.js 應用程式]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure 儲存體 SDK 節點 API 參考]: http://dl.windowsazure.com/nodestoragedocs/index.html
