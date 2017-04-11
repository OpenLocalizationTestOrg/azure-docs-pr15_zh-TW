<properties
    pageTitle="如何使用 Azure 檔案儲存空間，從 Python |Microsoft Azure"
    description="瞭解如何使用上傳、 清單、 下載]，從 Python Azure 檔案儲存空間，並刪除檔案。"
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>如何使用 Python 從 Azure 檔案儲存空間

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>概觀

本文將說明如何執行常見的案例使用檔案儲存空間。 範例 Python 撰寫，並使用[Microsoft Azure 儲存體 SDK python]。 涵蓋的案例包括上傳、 清單、 下載及刪除的檔案。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>建立共用

**FileService**物件可讓您使用的共用、 目錄及檔案。 下列程式碼會建立**FileService**物件。 新增下列任何您要以程式控制方式存取 Azure 儲存體的 Python 檔案頂端附近。

    from azure.storage.file import FileService

下列程式碼會建立使用儲存體帳戶名稱和帳戶金鑰**FileService**物件。  使用您的帳戶名稱和鍵取代 「 我的帳戶 」 和 「 mykey 」。

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

在下列程式碼範例中，您可以使用**FileService**物件，建立共用]，如果不存在。

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>將檔案上傳到共用

Azure 檔案儲存空間共用包含至少，在檔案可以所在位置的根目錄。 在 [此節]，您將學習如何從共用的根目錄到本機的儲存空間將檔案上傳。

若要建立檔案上傳的資料，請使用**建立\_檔案\_從\_路徑**，**建立\_檔案\_從\_串流**，**建立\_檔案\_從\_位元組**或**建立\_檔案\_從\_文字**方法。 他們的年齡高層級執行必要的區塊時的資料大小超過 64 MB 的方法。

**建立\_檔案\_從\_路徑**上傳的檔案，以指定的路徑，從的內容和**建立\_檔案\_從\_串流**上傳的檔案/串流已開啟的內容。 **建立\_檔案\_從\_位元組**上傳的位元組，陣列及**建立\_檔案\_從\_文字**上傳使用指定的編碼方式 （預設為 utf-8） 指定的文字值。

下列範例上傳到**myfile**檔案的**sunset.png**檔案的內容。

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>如何︰ 建立目錄

您也可以將內子目錄，而不是由全部的根目錄中的檔案，以組織儲存空間。 Azure 檔案儲存服務可讓您建立可讓您的帳戶的目錄。 下列程式碼會建立名為**sampledir**根目錄下的子目錄。

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>如何︰ 清單中共用檔案和目錄

若要在清單中的檔案和共用中的目錄，使用**清單\_目錄\_和\_檔案**方法。 這個方法會傳回一個產生器。 下列程式碼輸出的每個檔案及主控台的共用資料夾中的目錄**名稱**。

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>下載檔案

若要下載檔案中的資料，請使用**取得\_檔案\_至\_路徑**，**取得\_檔案\_至\_串流**，**取得\_檔案\_至\_位元組**，或**取得\_檔案\_至\_文字**。 他們的年齡高層級執行必要的區塊時的資料大小超過 64 MB 的方法。

下列範例會示範使用**取得\_檔案\_至\_路徑**下載**myfile**檔案的內容，並將其儲存至**出 sunset.png**檔案。

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>刪除檔案

最後，若要刪除檔案，請連絡**delete_file**。

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>後續步驟

現在，您學到檔案儲存空間的基本概念，請遵循這些連結，瞭解更多。

- [Python 開發人員中心](/develop/python/)
- [Azure 儲存服務 REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure 儲存體小組部落格]
- [Microsoft Azure 儲存體 python SDK]

[Azure 儲存體小組部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure 儲存體 python SDK]: https://github.com/Azure/azure-storage-python
