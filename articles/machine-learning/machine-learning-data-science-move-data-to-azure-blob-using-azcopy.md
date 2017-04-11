<properties
    pageTitle="移動資料與 Azure Blob 儲存體使用 AzCopy |Microsoft Azure"
    description="移動資料使用 AzCopy Azure Blob 儲存體"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>移動資料使用 AzCopy Azure Blob 儲存體

AzCopy 是專為上傳、 下載及複製資料，以及從 Microsoft Azure blob、 檔案及資料表儲存體的命令列公用。

如需安裝 AzCopy 和其他資訊使用 Azure 平台上的指示，請參閱[快速入門 AzCopy 命令列公用程式](../storage/storage-use-azcopy.md)。

用來將資料移到及/或從 Azure Blob 儲存體技術指南以下連結︰

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] 如果您使用的 VM 所設定的[資料科學虛擬機器 Azure 中](machine-learning-data-science-virtual-machines.md)所提供的指令碼，然後 AzCopy 已安裝 VM 上。

> [AZURE.NOTE] Azure blob 儲存體完成簡介，請參閱[Azure Blob 的基本概念](../storage/storage-dotnet-how-to-use-blobs.md)，並[Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。


## <a name="prerequisites"></a>必要條件

這份文件假設您有 Azure 訂閱儲存帳戶與該帳戶的對應的儲存空間鍵。 上傳/下載前的資料，您必須知道您 Azure 儲存體帳戶名稱和帳戶金鑰]。

- 若要設定的 Azure 訂閱，請參閱[一個月免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。

- 如需建立儲存帳戶的指示及取得帳戶和重要資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。


## <a name="run-azcopy-commands"></a>執行 AzCopy 命令

若要執行 AzCopy 命令，請開啟命令視窗，然後瀏覽至 AzCopy 安裝目錄，您可執行 AzCopy.exe 所在的電腦。 

基本 AzCopy 命令的語法是︰

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] 您可以將 AzCopy 安裝位置新增至您的系統路徑，然後再執行任何目錄中的 [命令。 根據預設，AzCopy 已安裝*%programfiles(x86) %\Microsoft SDKs\Azure\AzCopy*或*%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*。

## <a name="upload-files-to-an-azure-blob"></a>上傳檔案到 Azure blob

若要上傳檔案，請使用下列命令︰

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>從 Azure blob 下載的檔案

若要從 Azure blob 下載檔案，請使用下列命令︰

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Azure 容器之間傳送二進位大型物件

若要傳送 blob Azure 容器之間，使用下列命令︰

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>使用 AzCopy 的秘訣

> [AZURE.TIP]   
> 1. 當**上傳**的檔案， */S*上傳的檔案遞迴。 沒有此參數，子目錄中是無法上傳的檔案。  
> 2. 當**下載**的檔案， */S*搜尋容器遞迴到指定的目錄及其子目錄中的所有檔案或符合特定的模式指定的目錄及其子目錄中的所有檔案時，會下載。  
> 3.  您無法指定**特定 blob 檔案**下載使用*/Source*參數。 若要下載的特定檔案，指定 blob 的檔案名稱，以下載使用*/Pattern*參數。 **/S**參數可以用於有 AzCopy 尋找的檔案名稱圖樣遞迴。 不含圖樣參數，AzCopy 下載該目錄中的所有檔案。
