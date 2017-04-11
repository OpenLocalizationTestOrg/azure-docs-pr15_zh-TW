<properties
    pageTitle="移動資料與 Azure Blob 儲存體使用 Python |Microsoft Azure"
    description="移動資料使用 Python Azure Blob 儲存體"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>移動資料使用 Python Azure Blob 儲存體

本主題說明如何清單、 上傳和下載使用 Python API 的二進位大型物件。 使用 Python API 提供 Azure SDK，您可以︰

- 建立容器
- 上傳至容器的 blob
- 下載二進位大型物件
- 清單方塊中 blob
- 刪除 blob

如需有關使用 Python API 的詳細資訊，請參閱[從 Python Blob 儲存體服務使用方式](../storage/storage-python-how-to-use-blob-storage.md)。

用來將資料移到及/或從 Azure Blob 儲存體技術指南以下連結︰

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] 如果您使用的 VM 所設定的[資料科學虛擬機器 Azure 中](machine-learning-data-science-virtual-machines.md)所提供的指令碼，然後 AzCopy 已安裝 VM 上。

> [AZURE.NOTE] Azure blob 儲存體完成簡介，請參閱[Azure Blob 的基本概念](../storage/storage-dotnet-how-to-use-blobs.md)，並[Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。


## <a name="prerequisites"></a>必要條件

這份文件，假設您有 Azure 訂閱儲存的帳戶，與該帳戶的對應的儲存空間鍵。 上傳/下載前的資料，您必須知道您 Azure 儲存體帳戶名稱和帳戶金鑰]。

- 若要設定的 Azure 訂閱，請參閱[一個月免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。

- 如需建立儲存帳戶的指示及取得帳戶和重要資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。


## <a name="upload-data-to-blob"></a>上傳至 Blob 的資料

新增您想要以程式控制方式存取 Azure 儲存體中的任何 Python 程式碼的上方附近的下列程式碼片段︰

    from azure.storage.blob import BlobService

**BlobService**物件可讓您使用容器和二進位大型物件。 下列程式碼會建立使用儲存體帳戶名稱和帳戶金鑰 BlobService 物件。 與您的實際帳戶金鑰取代客戶名稱] 和 [帳戶金鑰]。

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

若要上傳至 blob 的資料使用以下兩種方法︰

1. 將\_區塊\_blob\_從\_路徑 （上傳的檔案，以從指定的 path 的內容）
2. 將\_block_blob\_從\_（上傳的內容從已經開啟檔案/資料流） 的檔案
3. 將\_區塊\_blob\_從\_位元組 （上傳的位元組陣列）
4. 將\_區塊\_blob\_從\_（上傳指定之文字值，使用指定的編碼方式） 的文字

下列程式碼上傳至容器的本機檔案︰

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

下列程式碼上傳至 blob 儲存體本機目錄中的 （不含目錄） 的所有檔案︰

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>從 Blob 下載資料

您可以使用下列方法，從 blob 下載資料︰
1. 取得\_blob\_至\_路徑
2. 取得\_blob\_至\_檔案
3. 取得\_blob\_至\_位元組
4. 取得\_blob\_至\_文字

執行所需的區塊時的資料大小超過 64 MB 這些方法。

下列範例會下載至本機檔案 blob 容器中的內容︰

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

下列範例會從容器下載所有二進位大型物件。 它會使用清單\_blob 取得容器中的可用的二進位大型物件的清單和其下載至本機的目錄。

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
