<properties
    pageTitle="如何使用 Azure Blob 儲存體 （物件儲存區） 從 Python |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
    services="storage"
    documentationCenter="python"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-azure-blob-storage-from-python"></a>如何使用從 Python 的 Azure Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

Azure Blob 儲存體] 是儲存在雲端的非結構化的資料，為物件/blob 的服務。 Blob 儲存體可以儲存任何類型的文字或二進位資料，例如文件、 媒體檔案或應用程式安裝程式。 Blob 儲存體也稱為 「 物件儲存空間。

本文將說明如何執行常見的案例使用 Blob 儲存體。 範例 Python 撰寫，並使用[Microsoft Azure 儲存體 SDK python]。 涵蓋的案例包括上傳、 清單、 下載及刪除二進位大型物件。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-container"></a>建立容器

根據您想要使用，請建立一個**BlockBlobService**、 **AppendBlobService**或**PageBlobService**物件 blob 的類型。 下列程式碼會使用**BlockBlobService**物件。 新增下列任何您要以程式控制方式存取 Azure 區塊 Blob 儲存體的 Python 檔案頂端附近。

    from azure.storage.blob import BlockBlobService

下列程式碼會建立使用儲存體帳戶名稱和帳戶金鑰**BlockBlobService**物件。  使用您的帳戶名稱和鍵取代 「 我的帳戶 」 和 「 mykey 」。

    block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

在下列程式碼範例中，您可以使用**BlockBlobService**物件，建立容器，如果不存在。

    block_blob_service.create_container('mycontainer')

根據預設，新的容器是私人的您必須從這個容器下載 blob 指定您儲存便捷鍵 （如前述）。 如果您想要讓所有人都可以使用 blob 容器內，您可以建立容器，並將 [使用下列程式碼的公開存取層級。

    from azure.storage.blob import PublicAccess
    block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

或者，您也可以建立並使用下列程式碼後修改容器。

    block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

此變更後，在網際網路上的任何人都可以看到二進位大型物件在公用容器中，但只有您可以修改，或將它們刪除。

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob

若要建立區塊 blob 上傳的資料，請使用**建立\_blob\_從\_路徑**，**建立\_blob\_從\_串流**，**建立\_blob\_從\_位元組**或**建立\_blob\_從\_文字**方法。 他們的年齡高層級執行必要的區塊時的資料大小超過 64 MB 的方法。

**建立\_blob\_從\_路徑**上傳的檔案，以指定的路徑，從的內容和**建立\_blob\_從\_串流**上傳的檔案/串流已開啟的內容。 **建立\_blob\_從\_位元組**上傳的位元組，陣列及**建立\_blob\_從\_文字**上載使用指定的編碼方式 （預設為 utf-8） 指定的文字值。

下列範例上傳到**myblob** blob 的**sunset.png**檔案的內容。

    from azure.storage.blob import ContentSettings
    block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
                )

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob

若要在容器 blob] 清單中，使用**清單\_blob**方法。 這個方法會傳回一個產生器。 下列程式碼輸出主控台容器中的每個 blob**名稱**。

    generator = block_blob_service.list_blobs('mycontainer')
    for blob in generator:
        print(blob.name)

## <a name="download-blobs"></a>下載二進位大型物件

若要下載從 blob 的資料，請使用**取得\_blob\_至\_路徑**，**取得\_blob\_至\_串流**，**取得\_blob\_至\_位元組**，或**取得\_blob\_至\_文字**。 他們的年齡高層級執行必要的區塊時的資料大小超過 64 MB 的方法。

下列範例會示範使用**取得\_blob\_至\_路徑**下載**myblob** blob 的內容，並將其儲存至**出 sunset.png**檔案。

    block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## <a name="delete-a-blob"></a>刪除 blob

最後，若要刪除 blob，請連絡**delete_blob**。

    block_blob_service.delete_blob('mycontainer', 'myblockblob')

## <a name="writing-to-an-append-blob"></a>若要新增 blob 撰寫

新增 blob 最適合附加作業，例如記錄。 封鎖 blob，例如新增 blob 包含的區塊，，但當您要新增 blob 新增新的區塊，就一定會附加到 blob 的結尾。 您無法更新，或刪除現有的區塊中新增 blob。 對封鎖 blob，不被公開新增 blob 的區塊識別碼。

新增 blob 中的每個區塊，不同的大小，上限為 4 MB，新增 blob 可以包含 50000 區塊的最大值。 新增 blob 的大小上限為因此稍微高於 195 GB （4 MB X 50000 區塊）。

下列範例會建立新的附加 blob，並將一些資料附加，模擬簡單的記錄作業。

    from azure.storage.blob import AppendBlobService
    append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

    # The same containers can hold all types of blobs
    append_blob_service.create_container('mycontainer')

    # Append blobs must be created before they are appended to
    append_blob_service.create_blob('mycontainer', 'myappendblob')
    append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

    append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## <a name="next-steps"></a>後續步驟

現在，您學到 Blob 儲存體的基本概念，請遵循這些連結，瞭解更多。

- [Python 開發人員中心](/develop/python/)
- [Azure 儲存服務 REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure 儲存體小組部落格]
- [Microsoft Azure 儲存體 python SDK]

[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure 儲存體 python SDK]: https://github.com/Azure/azure-storage-python
