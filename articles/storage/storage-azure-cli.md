<properties
    pageTitle="Azure CLI 使用 Azure 儲存 |Microsoft Azure"
    description="瞭解如何使用 Azure 命令列介面 (Azure CLI)，以建立及管理儲存空間帳戶與使用 Azure blob 及檔案至 Azure 儲存體。 Azure CLI 是跨平台的工具 "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>Azure CLI 使用 Azure 儲存體

## <a name="overview"></a>概觀

Azure CLI 提供一組開啟來源] 命令的 [使用 Azure 平台的跨平台。 它會提供許多相同的功能中[Azure 入口網站](https://portal.azure.com)以及豐富的資料存取功能。

本指南中，我們會探索︰ 使用[Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md)來執行各種不同的 Azure 儲存體開發及管理工作。 我們建議您下載及安裝或升級至最新的 Azure CLI 再使用本指南。

本指南假設您瞭解 Azure 儲存體的基本概念。 快速入門提供指令碼，示範使用 Azure 儲存體 Azure CLI 的使用方式。 請務必一併更新根據您的設定，才能執行每個指令碼的指令碼變數。

> [AZURE.NOTE] 快速入門提供傳統的儲存空間帳戶 Azure CLI 命令和指令碼範例。 請參閱資源管理員儲存帳戶[使用 Mac、 Linux，和 Azure 資源管理 Windows Azure CLI](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) Azure CLI 的命令。

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>開始使用 Azure 儲存體和 Azure CLI 5 分鐘

本指南使用 Ubuntu 範例，但其他 OS 平台應該執行同樣的。

**Azure 的新︰**取得 Microsoft Azure 訂閱，與該訂閱相關聯的 Microsoft 帳戶。 Azure 購買選項的相關資訊，請參閱[免費試用](https://azure.microsoft.com/pricing/free-trial/)、[購買選項](https://azure.microsoft.com/pricing/purchase-options/)，以及[成員提供](https://azure.microsoft.com/pricing/member-offers/)（MSDN、 Microsoft 合作夥伴網路，BizSpark 及其他 Microsoft 程式的成員）。

Azure 訂閱的相關資訊，請參閱[指派管理員角色中 Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) 。

**建立 Microsoft Azure 訂閱與帳戶︰ 之後**

1. 下載並安裝 Azure CLI[安裝 Azure CLI](../xplat-cli-install.md)所述的指示執行。
2. Azure CLI 具有已安裝後，您可以使用您的命令列介面 （艦隊，終端機，命令提示字元） azure] 命令來存取 Azure CLI 命令。 輸入`azure`] 命令，您應該會看到下列輸出。

    ![Azure 命令輸出][Image1]

3. 在命令列介面中，輸入`azure storage`出 azure 儲存空間的所有命令] 清單，並取得的功能的第一印象 Azure CLI 提供。 您可以輸入命令與**-h**參數的名稱 (例如， `azure storage share create -h`) 若要查看命令語法的詳細資料。
4. 現在，我們會告訴您一個簡單的指令碼顯示基本 Azure CLI 命令，以存取 Azure 儲存體。 指令碼會先要求您設定兩個變數儲存帳戶和鍵。 指令碼會在此新的儲存空間帳戶中建立新的容器然後上傳現有的圖像檔案 (blob) 至該容器。 指令碼會列出該容器中的所有 blob 之後，它會將圖像檔案下載到本機電腦上存在於目的目錄。

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. 在您的本機電腦，開啟您慣用的文字編輯器 (例如 vim)。 上述的指令碼輸入文字編輯器。

6. 現在，您需要更新指令碼變數根據您設定的設定。

    - **< > storage_account_name**在 [指令碼中使用指定的名稱，或輸入您儲存的帳戶的新名稱。 **重要︰**儲存體帳戶名稱必須是唯一 Azure 中。 它必須小寫太 ！

    - **< > storage_account_key**您儲存的帳戶便捷鍵。

    - **< > container_name**在 [指令碼中使用指定的名稱，或輸入您的容器的新名稱。

    - **< > image_to_upload**路徑至圖片上輸入您的本機電腦，例如: 「 ~ / images/HelloWorld.png 」。

    - **< > destination_folder**輸入路徑至本機目錄來儲存檔案下載從 Azure 儲存空間，例如: 「 ~/downloadImages 」。

7. 您已經更新 vim 的必要變數之後，按下按鍵組合 」 esc 鍵，:，wq ！ 」 若要儲存指令碼。

8. 若要執行這個指令碼，只要輸入艦隊主控台中的指令碼檔名。 執行此指令碼後，您應該包含下載的影像檔案的本機的目的地資料夾。 以下螢幕擷取畫面顯示輸出範例︰

執行指令碼之後，您應該包含下載的影像檔案的本機的目的地資料夾。

## <a name="manage-storage-accounts-with-the-azure-cli"></a>管理與 Azure CLI 儲存帳戶

### <a name="connect-to-your-azure-subscription"></a>連線到您 Azure 的訂閱

雖然大部分的 [儲存] 命令會使用沒有 Azure 訂閱，但我們建議您從 Azure CLI 連線到您的訂閱。 若要設定 Azure CLI 使用您的訂閱，請遵循[連線至 Azure CLI Azure 訂閱](../xplat-cli-connect.md)中的步驟進行。

### <a name="create-a-new-storage-account"></a>建立新的儲存空間帳戶

若要使用 Azure 儲存空間，您必須儲存帳戶。 設定您的電腦連線到您的訂閱之後，您可以建立新的 Azure 儲存體帳戶。

        azure storage account create <account_name>

儲存帳戶的名稱必須介於 3 和 24 個字元，並使用數字和只有英文小寫字母。

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>設定環境變數中的預設 Azure 儲存體帳戶

您可以在您的訂閱中有多個儲存的帳戶。 您可以選擇其中一個圖形，並在環境變數中的所有儲存的命令集相同的工作階段。 這可讓您執行 Azure CLI 儲存命令，而不用指定的儲存空間帳戶和明確按鍵。

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

若要設定預設儲存帳戶的另一個方法使用連接字串。 首先以取得連線字串︰

        azure storage account connectionstring show <account_name>

然後複製輸出連線字串，並將它設定為環境變數︰

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>建立及管理二進位大型物件

Azure Blob 儲存體] 是儲存大量的非結構化資料，例如文字或二進位資料，可透過 HTTP 或 HTTPS 的世界從任何位置存取服務。 本節假設您已熟悉 Azure Blob 儲存體概念。 詳細資訊，請參閱[開始使用 Azure Blob 儲存體使用.NET](storage-dotnet-how-to-use-blobs.md)與[Blob 服務的概念](http://msdn.microsoft.com/library/azure/dd179376.aspx)。

### <a name="create-a-container"></a>建立容器

Azure 儲存體中每個 blob 必須容器中。 您可以建立私人的容器使用`azure storage container create`命令︰

        azure storage container create mycontainer

> [AZURE.NOTE] 有三個匿名讀取權限層級︰**關閉** **Blob**，與**容器**。 若要避免匿名存取 blob，權限將參數設定為**關閉**。 根據預設，新的容器是私人且只能由帳戶擁有者可以存取。 若要允許匿名公用讀取權限 blob 的資源，但無法容器中繼資料或 blob 容器中的清單，權限將參數設定為**Blob**。 若要允許完整公用 blob 資源的讀取權限，容器中繼資料和清單的二進位大型物件在容器中，權限將參數設定為**容器**。 如需詳細資訊，請參閱[管理匿名的讀取權限容器及二進位大型物件](storage-manage-access-to-resources.md)。

### <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

Azure Blob 儲存體支援區塊二進位大型物件及頁面二進位大型物件。 如需詳細資訊，請參閱[瞭解區塊 Blob、 附加二進位大型物件和頁面二進位大型物件](http://msdn.microsoft.com/library/azure/ee691964.aspx)。

若要將二進位大型物件中的上傳至容器，您可以使用`azure storage blob upload`。 根據預設，這個命令上傳至區塊 blob 的本機檔案。 若要指定 blob 的類型，您可以使用`--blobtype`參數。

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>從容器下載二進位大型物件

下列範例會示範如何從容器下載二進位大型物件。

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>複製二進位大型物件

您可以非同步複製 blob 或跨儲存帳戶與區域中。

下列範例會示範如何複製到另一個 blob 儲存體帳戶。 在這個範例中，我們建立二進位大型物件在哪裡公開，容器匿名存取。

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

此範例會執行非同步複本。 您可以監視每個複製作業的狀態執行`azure storage blob copy show`作業。

請注意，來源 URL 提供複製作業必須能夠存取，或包含 SA （共用的 access 簽章） 權杖。

### <a name="delete-a-blob"></a>刪除 blob

若要刪除 blob，請使用下列命令︰

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>建立及管理檔案共用

Azure 檔案儲存空間提供使用標準 SMB 通訊協定的應用程式共用的儲存空間。 Microsoft Azure 虛擬機器和雲端服務，以及內部部署的應用程式，可以共用透過裝載共用的檔案資料。 您可以管理檔案共用及透過 Azure CLI 檔案資料。 如需有關 Azure 檔案儲存空間的詳細資訊，請參閱[開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)或[Linux 與 Azure 檔案儲存空間的使用方式](storage-how-to-use-files-linux.md)。

### <a name="create-a-file-share"></a>建立檔案共用

Azure 檔案共用是 Azure 中的 SMB 檔案共用。 在 [檔案共用，必須先建立所有目錄及檔案。 帳戶可包含無限的數目的共用資料夾，以及共用可儲存容量限制的儲存空間帳戶至的檔案數量。 下列範例會建立名稱為**myshare**檔案共用。

        azure storage share create myshare

### <a name="create-a-directory"></a>建立目錄

目錄提供的 Azure 檔案共用區選用的階層式結構。 下列範例會建立名為**myDir**檔案共用中的目錄。

        azure storage directory create myshare myDir

附註的目錄路徑可以包含多個層級，*例如*， **/ b**。 不過，您必須確定所有父目錄都存在。 例如，路徑的**/ b**，您必須建立目錄****，然後建立目錄**b**。

### <a name="upload-a-local-file-to-directory"></a>將本機的檔案上傳至目錄

下列範例上傳的檔案從**~/temp/samplefile.txt** **myDir**目錄。 編輯檔案路徑，讓它指向您的本機電腦上有效的檔案︰

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

請注意，在共用的檔案可以 1 TB 的大小。

### <a name="list-the-files-in-the-share-root-or-directory"></a>清單中的共用根或目錄的檔案

您可以列出的檔案與子目錄共用根或目錄使用下列命令︰

        azure storage file list myshare myDir

請注意，目錄名稱是選擇性的 [清單] 作業。 如果省略則為命令可列出的根目錄共用的內容。

### <a name="copy-files"></a>複製檔案

以 0.9.8 新版 Azure CLI 開頭，您可以將檔案複製到其他檔案、 blob，或要檔案的 blob 的檔案。 以下我們會示範如何執行這些使用 CLI 命令的複製作業。 若要將檔案複製到新的目錄中︰

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

若要將 blob 複製檔案目錄︰

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>後續步驟

以下是一些相關的文章和資源，進一步瞭解 Azure 儲存體。

- [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
- [Azure 儲存體 REST API 參考](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
