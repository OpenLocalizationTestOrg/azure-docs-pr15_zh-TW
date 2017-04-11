<properties
    pageTitle="如何使用 Blob 儲存體 （物件儲存區） 從 [注音標示 |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>如何使用從 [注音標示的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

Azure Blob 儲存體] 是儲存在雲端的非結構化的資料，為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、 媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為 「 物件儲存空間。

本指南會顯示如何執行常見的案例使用 Blob 儲存體。 使用並列文字 API 寫入範例。 涵蓋的案例包括**上傳、 清單、 下載**和**刪除**二進位大型物件。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>建立並列文字應用程式

建立並列文字的應用程式。 如需相關指示，請參閱[Azure VM 滑軌 Web 應用程式上的 [注音標示](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式，以存取儲存空間

若要使用 Azure 儲存空間，您需要下載並使用並列文字 azure 套件，其中包含一組方便時觀看文件庫與儲存其他服務。

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 取得套件

1. 使用**PowerShell** (Windows)、**終端機**(Mac)，或**被**(Unix) 等命令列介面。

2. 安裝健身和相依性的 [命令] 視窗中，輸入 「 健身安裝 azure 」。

### <a name="import-the-package"></a>匯入套件

使用您偏好使用的文字編輯器，移至您想要使用儲存的位置並列文字檔案頂端新增下列動作︰

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>設定連線至 Azure 儲存體

Azure 模組會讀取環境變數**AZURE\_儲存\_帳戶**和**AZURE\_儲存\_ACCESS_KEY**連線至 Azure 儲存體帳戶所需的資訊。 如果沒有設定這些環境變數，您必須指定下列程式碼中使用**Azure::Blob::BlobService**之前的帳戶資訊︰

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


若要從傳統或 Azure 入口網站中的資源管理員儲存帳戶中取得這些值︰

1. [Azure 入口網站](https://portal.azure.com)登入。
2. 瀏覽至您想要使用的儲存空間帳戶。
3. 在右側設定刀，按一下 [**便捷鍵**。
4. 在 Access 鍵刀出現，您會看到便捷鍵 1 和 2 的便捷鍵。 您可以使用下列其中一項。 
5. 按一下 [複製] 圖示，以將金鑰複製到剪貼簿。 

若要取得這些值傳統的儲存空間帳戶在傳統 Azure 入口網站︰

1. [傳統 Azure 入口網站](https://manage.windowsazure.com)登入。
2. 瀏覽至您想要使用的儲存空間帳戶。
3. 按一下 [**管理便捷鍵**底部的功能窗格]。
4. 在 [pop 設定] 對話方塊中，您會看到儲存體帳戶名稱、 存取主索引鍵和次要便捷鍵。 存取鍵，您可以使用的主索引項目或第二個項目。 
5. 按一下 [複製] 圖示，以將金鑰複製到剪貼簿。

## <a name="create-a-container"></a>建立容器

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**Azure::Blob::BlobService**物件可讓您使用容器和二進位大型物件。 若要建立容器，請使用**建立\_container()**方法。

下列範例會建立容器或列印錯誤時，如果有任何。

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

如果您想要公開容器中的檔案，您可以設定容器的權限。

您可以修改<strong>建立\_container()</strong>傳遞通話**︰ 公用\_存取\_層級**選項︰

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


有效的值**︰ 公用\_存取\_層級**選項是︰

* **blob:**指定容器和 blob 資料的完整公用讀取權限。 用戶端可以列舉透過匿名要求，容器內的二進位大型物件，但無法列舉容器內的儲存空間的帳戶。

* **容器︰**指定公用 blob 的讀取權限。 Blob 此容器中的資料可以讀取透過匿名要求，但不是使用容器資料。 用戶端無法列舉透過匿名要求容器內的二進位大型物件。

或者，您可以修改容器的公開存取層級使用**設定\_容器\_acl()**方法，以指定的公開存取層級。

下列範例會變更為**容器**的公開存取層級︰

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

若要上傳至 blob 的內容，使用**建立\_區塊\_blob()**的方法來建立 blob，使用的檔案或字串 blob 的內容。

名為 「 圖像-blob 」 容器中的新 blob 為下列程式碼上傳的檔案**test.png** 。

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob

若要容器] 清單中，使用**list_containers()**方法。
若要在容器內的二進位大型物件] 清單中，使用**清單\_blobs()**方法。

這會將輸出的帳戶的所有容器中的所有 blob 的 url。

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>下載二進位大型物件

若要下載二進位大型物件，請使用**取得\_blob()**擷取內容的方法。

下列範例會示範如何使用**取得\_blob()**下載 」 圖像 blob 」 的內容和寫入的本機檔案。

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>刪除 Blob
最後，若要刪除 blob，使用**刪除\_blob()**方法。 下列範例會示範如何刪除 blob。

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>後續步驟

若要瞭解更複雜的儲存區工作，請遵循以下連結︰

- [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- 在 GitHub 上的[[注音標示的 azure SDK](https://github.com/WindowsAzure/azure-sdk-for-ruby)存放庫
- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
