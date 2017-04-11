<properties
    pageTitle="設定和擷取屬性和 Azure 儲存體中的物件的中繼資料 |Microsoft Azure"
    description="將自訂的中繼資料儲存在 Azure 儲存體中的物件，設定並擷取系統內容。"
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

# <a name="set-and-retrieve-properties-and-metadata"></a>設定和擷取屬性和中繼資料 #

## <a name="overview"></a>概觀

Azure 儲存體支援系統內容和使用者定義的中繼資料，除了所包含的資料中的物件︰

*   **系統內容]。** 系統內容存在的每個儲存資源。 將其中一些可讀取或設定，有些則是唯讀狀態。 在幕後一些系統內容會對應到特定標準 HTTP 標頭。 Azure 儲存用戶端文件庫維護這些。  

*   **使用者定義的中繼資料。** 使用者定義的中繼資料是您在指定的資源，請在 [名稱 / 值組的表單中指定的中繼資料。 您可以使用中繼資料儲存其他值的儲存空間資源;這些值自己僅供並不會影響該資源的行為方式。  

擷取屬性和中繼資料值的儲存空間資源是執行的程序。 您可以瞭解這些值之前，您必須明確擷取這些呼叫**FetchAttributes**的方法。

> [AZURE.IMPORTANT] 除非您**FetchAttributes**兩種方法之一，不會填入的儲存空間資源屬性和中繼資料值。 

## <a name="setting-and-retrieving-properties"></a>設定及擷取屬性

若要擷取的屬性值，呼叫**FetchAttributes**在 blob 或容器，填入內容，然後讀取的值。

若要設定物件的屬性，指定屬性的值，然後呼叫**SetProperties**方法。

下列範例會建立容器，並將其屬性值部分寫入主控台視窗︰

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>設定和擷取中繼資料

您可以指定中繼資料為 blob 或容器資源在一或多個名稱 / 值組。 若要設定中繼資料，新增名稱 / 值組**中繼資料**集合的資源，然後呼叫**SetMetadata**方法來儲存服務中的值。

> [AZURE.NOTE] 中繼資料名稱必須符合 C# 識別碼的命名慣例。
 
下列範例會在容器上設定中繼資料。 使用集合的**Add**方法設定一個值。 其他設定值，則使用隱含的索引鍵值語法。 都有效。

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

若要擷取的中繼資料，呼叫**FetchAttributes** blob 或要填入的**中繼資料**集合，容器上然後讀取的值，在下面的範例所示。

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>另請參閱  

- [Azure 儲存體.NET 參照的用戶端文件庫](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [.NET 套件 azure 儲存空間的用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage/) 
