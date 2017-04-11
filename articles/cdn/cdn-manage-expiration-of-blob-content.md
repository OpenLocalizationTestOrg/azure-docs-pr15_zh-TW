<properties
 pageTitle="管理中 Azure CDN Azure 儲存體 blob 內容到期 |Microsoft Azure"
 description="深入了解控制的二進位大型物件 Azure CDN 快取中的 [存留時間的選項。"
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>管理 Azure CDN Azure 儲存體 blob 內容的到期日

> [AZURE.SELECTOR]
- [Azure Web 應用程式/雲端服務、 ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure 儲存 blob 服務](cdn-manage-expiration-of-blob-content.md)

[Azure 儲存體](../storage/storage-introduction.md)中的[blob 服務](../storage/storage-introduction.md#blob-storage)是幾個 Azure 基礎來源與 Azure CDN 整合。  任何公開存取 blob 內容可快取中 Azure CDN 鍵，直到其存留時間 (TTL) 經過。  TTL 取決於[*快取控制*標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)HTTP 從 Azure 儲存體。

>[AZURE.TIP] 您可以選擇設定 blob 沒有 [TTL]。  在此情況下，Azure CDN 會自動套用 [TTL] 的七天的預設值。
>
>如需有關 Azure CDN 存取加快二進位大型物件及其他檔案的運作方式的詳細資訊，請參閱[Azure CDN 概觀](./cdn-overview.md)。
>
>如需 Azure 儲存體 blob 服務的詳細資訊，請參閱[Blob 服務概念](https://msdn.microsoft.com/library/dd179376.aspx)。 

本教學課程說明您可以設定 [TTL] 的 blob Azure 儲存體中的數種方式。  

## <a name="azure-powershell"></a>Azure PowerShell

[PowerShell 的 azure](../powershell-install-configure.md)是快速、 最有效方法之一來管理 Azure 服務。  使用`Get-AzureStorageBlob`cmdlet 來取得參考 blob，然後設定`.ICloudBlob.Properties.CacheControl`屬性。 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] 您也可以使用 PowerShell 來[管理您的 CDN 設定檔和結束點](./cdn-manage-powershell.md)。

## <a name="azure-storage-client-library-for-net"></a>Azure 儲存體.NET 的用戶端文件庫

若要設定使用.NET blob 的 TTL，請使用[Azure 儲存用戶端文件庫的.NET](../storage/storage-dotnet-how-to-use-blobs.md)設定[CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx)屬性。

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] 有可用[的.NET Azure Blob 儲存體範例](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)中的許多其他.NET 程式碼範例。

## <a name="other-methods"></a>其他方法

- [Azure 命令列介面](../xplat-cli-install.md)

    上傳 blob，設定*cacheControl*屬性使用`-p`切換。  本範例會將 [TTL] 為 1 小時 （3600 秒）。

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Azure 儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    明確設定的*x-ms-blob-快取控制項*屬性上[放置 Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)、[將封鎖清單](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)中或[設定 Blob 內容](https://msdn.microsoft.com/library/azure/ee691966.aspx)的邀請。

- 第三方儲存空間管理工具

    某些協力廠商 Azure 儲存體管理工具可讓您設定 blob *CacheControl*屬性。 

## <a name="testing-the-cache-control-header"></a>測試*快取控制*頁首

您可以輕鬆地驗證您的二進位大型物件的 TTL。  使用瀏覽器的[開發人員工具](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)，請測試您 blob 會包括*快取控制*回應標頭。  您也可以使用的工具，例如**wget**、[郵差](https://www.getpostman.com/)或[Fiddler](http://www.telerik.com/fiddler)檢查回應標頭。

## <a name="next-steps"></a>後續步驟

- [閱讀有關的*快取控制*標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [瞭解如何管理雲端服務中的內容 Azure CDN 的到期日](./cdn-manage-expiration-of-cloud-service-content.md)

