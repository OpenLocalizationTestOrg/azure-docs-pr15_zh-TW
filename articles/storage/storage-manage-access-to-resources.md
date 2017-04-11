<properties
    pageTitle="管理匿名讀取權限容器及 blob |Microsoft Azure"
    description="瞭解如何將開放容器及 blob 匿名存取]，以及如何以程式控制方式存取。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>管理容器及 blob 的匿名讀取權限

## <a name="overview"></a>概觀

根據預設，只有儲存帳戶擁有者可以存取儲存在該帳戶的資源。 對於 Blob 儲存體只中,，您可以設定允許匿名讀取權限的容器，其 blob，容器的權限，好讓您可以將這些資源的存取權授與不共用您的帳戶金鑰。

匿名存取最適合案例中，您可以使用匿名讀取權限的特定二進位大型物件。 精密的控制，您可以建立共用的 access 簽名，這可讓您使用不同的權限的代理人限制存取，然後移至指定的時間間隔。 如需建立共用的 access 簽章的詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>授與容器及二進位大型物件匿名使用者權限

根據預設，容器和內任何 blob 可能存取只的儲存空間帳戶擁有者。 為了讓匿名使用者讀取容器和其二進位大型物件的權限，您可以設定允許公用存取的容器權限。 匿名使用者可以讀取公開存取容器內的二進位大型物件，而驗證要求。

容器提供管理容器存取下列選項︰

- **完整公用的讀取權限︰**可以透過匿名要求讀取容器和 blob 的資料。 用戶端可以列舉透過匿名要求，容器內的二進位大型物件，但無法列舉容器內的儲存空間帳戶。

- **公用閱讀只二進位大型物件的存取權︰**Blob 此容器中的資料可以讀取透過匿名要求，但不是使用容器資料。 用戶端無法列舉透過匿名要求容器內的二進位大型物件。

- **公用讀取權限︰**僅限帳戶擁有者可以讀取容器和 blob 的資料。

您可以透過下列方式來設定容器權限︰

- 從[Azure 入口網站](https://portal.azure.com)。
- 以程式設計方式，您可以使用的儲存空間用戶端文件庫或 REST API。
- 使用 PowerShell。 若要瞭解從 PowerShell 的 Azure 設定容器權限，請參閱[使用 Azure PowerShell 的 Azure 儲存體](storage-powershell-guide-full.md#how-to-manage-azure-blobs)。

### <a name="setting-container-permissions-from-the-azure-portal"></a>從 Azure 入口網站設定容器權限

若要從[Azure 入口網站](https://portal.azure.com)設定容器權限，請遵循下列步驟︰

1. 瀏覽至您的儲存空間帳戶的儀表板。
2. 從清單中選取的容器名稱。 按一下名稱顯示在所選容器 blob
3. 在工具列上選取**存取原則**。
4. 在下面的螢幕擷取畫面所示，請在**Access 類型**] 欄位中，選取您想要的權限等級。

    ![編輯容器中繼資料] 對話方塊](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>設定以程式設計方式使用.NET 容器權限

若要設定容器使用.NET 用戶端文件庫的權限，第一次呼叫**GetPermissions**方法來擷取容器的現有權限。 然後設定**GetPermissions**方法傳回**BlobContainerPermissions**物件的**PublicAccess**屬性。 最後，呼叫**SetPermissions**方法，使用更新的權限。

下列範例會將容器的權限設定為完整公用讀取權限。 若要設定權限公用 blob 的讀取權限、 設定 [ **PublicAccess**屬性**BlobContainerPublicAccessType.Blob**。 若要移除所有匿名使用者的權限，將屬性設定為**BlobContainerPublicAccessType.Off**。

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## <a name="access-containers-and-blobs-anonymously"></a>以匿名方式存取容器及二進位大型物件

以匿名方式存取容器及二進位大型物件的用戶端可以使用不需要認證的建構函式。 下列範例顯示數種不同的方式，以匿名方式參照 Blob 服務資源。

### <a name="create-an-anonymous-client-object"></a>建立匿名用戶端物件

您可以建立新的服務用戶端物件匿名存取帳戶提供 Blob service 端點。 不過，您也必須知道的容器中所提供的匿名存取該帳戶的名稱。

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### <a name="reference-a-container-anonymously"></a>以匿名方式參照容器

如果您有以匿名方式提供的容器的 URL，您可以使用其直接參照容器。

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### <a name="reference-a-blob-anonymously"></a>以匿名方式參照 blob

如果您有使用匿名存取 blob 的 URL，您可以參照 blob 直接使用的 URL:

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## <a name="features-available-to-anonymous-users"></a>匿名使用者可使用的功能

下表顯示的作業可能會被稱為匿名使用者容器的 ACL 設為允許公開存取。

| 其他作業                                         | 使用完整公用讀取權限 | 使用公用只二進位大型物件的讀取權限的權限 |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| 清單容器                                        | 僅擁有者                              | 僅擁有者                                        |
| 建立容器                                       | 僅擁有者                              | 僅擁有者                                        |
| 取得容器屬性                               | 所有                                     | 僅擁有者                                        |
| 取得容器的中繼資料                                 | 所有                                     | 僅擁有者                                        |
| 設定容器中繼資料                                 | 僅擁有者                              | 僅擁有者                                        |
| 取得容器 ACL                                      | 僅擁有者                              | 僅擁有者                                        |
| 設定容器 ACL                                      | 僅擁有者                              | 僅擁有者                                        |
| 刪除容器                                       | 僅擁有者                              | 僅擁有者                                        |
| 清單二進位大型物件                                             | 所有                                     | 僅擁有者                                        |
| 將 Blob                                               | 僅擁有者                              | 僅擁有者                                        |
| 取得 Blob                                               | 所有                                     | 所有                                               |
| 取得 Blob 屬性                                    | 所有                                     | 所有                                               |
| 設定 Blob 屬性                                    | 僅擁有者                              | 僅擁有者                                        |
| 取得 Blob 中繼資料                                      | 所有                                     | 所有                                               |
| 設定 Blob 的中繼資料                                      | 僅擁有者                              | 僅擁有者                                        |
| 將 [區塊                                              | 僅擁有者                              | 僅擁有者                                        |
| 取得區塊清單 （僅限已確認區塊）                 | 所有                                     | 所有                                               |
| 取得 （僅限未認可的區塊或所有區塊） 的封鎖清單 | 僅擁有者                              | 僅擁有者                                        |
| 將區塊清單                                         | 僅擁有者                              | 僅擁有者                                        |
| 刪除 Blob                                            | 僅擁有者                              | 僅擁有者                                        |
| 複製 Blob                                              | 僅擁有者                              | 僅擁有者                                        |
| 快照 Blob                                          | 僅擁有者                              | 僅擁有者                                        |
| 租賃 Blob                                             | 僅擁有者                              | 僅擁有者                                        |
| 將頁面                                               | 僅擁有者                              | 僅擁有者                                        |
| 取得頁面範圍                                        | 所有                                     | 所有                                                  |
| 附加 Blob                                            | 僅擁有者                              | 僅擁有者                                                  |


## <a name="see-also"></a>另請參閱

- [驗證 Azure 儲存服務](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [使用共用的 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)
- [使用共用的 Access 簽章的委派存取](https://msdn.microsoft.com/library/azure/ee395415.aspx)
