<properties
    pageTitle="建立及使用 Blob 儲存體 SA |Microsoft Azure"
    description="本教學課程教您如何建立使用共用的 access 簽章與 Blob 儲存體，以及如何從用戶端應用程式使用。"
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>共用 Access 簽章，第 2 部分︰ 建立及使用 SA Blob 儲存體

## <a name="overview"></a>概觀

[第 1 部分](storage-dotnet-shared-access-signature-part-1.md)探索此教學課程的共用 access 簽章 (SA)，並說明使用它們的最佳做法。 第 2 部分會顯示如何產生，然後 Blob 儲存體中使用共用的 access 簽章。 範例以 C# 撰寫，並使用.NET Azure 儲存用戶端的文件庫。 涵蓋的案例包括使用共用的 access 簽章下列特性︰

- 產生容器上共用的 access 簽章
- 產生 blob 上共用的 access 簽章
- 建立儲存的存取原則，以管理容器的資源的簽章
- 測試用戶端應用程式從共用的 access 簽章

## <a name="about-this-tutorial"></a>關於本教學課程

在本教學課程中，我們將焦點放在藉由建立兩個主控台應用程式中建立共用的 access 容器和 blob 的簽章。 第一個主控台應用程式會產生共用的 access 簽章和 blob 容器上。 這個應用程式已儲存的帳戶金鑰。 第二個主控台應用程式，做為用戶端應用程式，存取容器和使用第一個應用程式中建立共用的 access 簽章 blob 資源。 這個應用程式使用共用的 access 簽章來驗證其容器的存取權和 blob 資源-沒有帳戶金鑰]。

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>第 1 部分︰ 建立主控台應用程式來產生共用的 Access 簽章

首先，確定您有安裝的.NET Azure 儲存用戶端的文件庫。 您可以安裝[NuGet 套件](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet 套件")包含最新的用戶端文件庫; 組件這是以確保您有最新的修正建議的方法。 您也可以下載最新版本的[.NET Azure SDK](https://azure.microsoft.com/downloads/)的一部分的用戶端文件庫。

在 Visual Studio 中，建立新的 Windows 主控台應用程式並將其命名**GenerateSharedAccessSignatures**。 新增參照**Microsoft.WindowsAzure.Configuration.dll**和**Microsoft.WindowsAzure.Storage.dll**，使用下列方法之一︰

-   如果您想要安裝 NuGet 套件，第一次安裝[NuGet 用戶端](https://docs.nuget.org/consume/installing-nuget)。 在 Visual Studio 中，選取 [**專案 |管理 NuGet 封包**、 線上搜尋**Azure 儲存體**]，然後依照指示進行安裝。
-   或者，在您安裝的 Azure SDK 中找出組件，並新增它們的參考。

Program.cs 檔案頂端新增下列**使用**陳述式︰

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

編輯 app.config 檔案，使其包含的設定，以指向您儲存的帳戶的連線字串。 App.config 檔案看起來應該像這樣︰

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>容器產生共用的 Access 簽章 URI

首先，我們將會新增產生新的容器上共用的 access 簽章的方法。 在此情況下簽章是不相關聯的預存的存取原則，它會在 URI，指出其到期時間授與的權限的資訊。

首先，將程式碼新增至**main**方法，以驗證您的儲存空間帳戶存取及建立新的容器︰

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

接下來，新增產生容器共用的 access 簽章及傳回 URI 的簽章的方法︰

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

底部的**main （）**的方法之前**Console.ReadLine()**，來電**GetContainerSasUri()**和寫入主控台視窗的簽章 URI 通話中加入下列行︰

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

編譯並將輸出新容器的共用的存取簽章 URI 執行。 URI 會類似下列 URI:       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

當您執行的程式碼時，您在容器建立共用的 access 簽章是有效的下一個 24 小時。 簽章授與清單 blob 容器中與容器中撰寫新的 blob 的用戶端權限。

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Blob 的產生共用的 Access 簽章 URI

接下來，我們要撰寫類似的程式碼，建立容器內的新 blob，並為其產生共用的 access 簽章。 此共用的 access 簽章不儲存的存取原則，與相關聯，因此其包含的開始時間、 到期時間和 URI 權限的詳細資訊。

新增建立新的 blob 的方法，和寫入一些文字，然後產生共用的 access 簽章，並傳回的簽章 URI:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

**Main**方法底部新增下列幾行**Console.ReadLine()**，通話之前撥打**GetBlobSasUri()**，並將共用的 access 簽章 URI 寫入主控台視窗︰    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


編譯並將輸出新 blob 的共用的存取簽章 URI 執行。 URI 會類似下列 URI:

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>在容器上建立儲存的存取原則

現在讓我們來建立容器會定義限制的任何與其相關聯的共用的存取簽章中的 [預存的存取原則。

在上一個範例中，我們已指定的開始時間 （隱含或明確） 的到期時間，與的權限共用的 access 簽名 URI 本身。 在下列範例中，我們會指定這些儲存的存取原則，而不是在共用的 access 簽章。 如此一來，可讓我們變更這些限制式，而不重新發行共用的 access 簽章。

很可能有一或多個共用的 access 簽章的限制式，而餘數儲存的存取原則上。 不過，您可以只指定的開始時間、 到期時間和權限在同一個位置，或其他;例如，您無法指定 [開啟共用的 access 簽章的 [權限和也將其指定儲存的存取原則上。

請注意，當您新增至容器的存取原則，您必須取得容器的現有權限、 新增新的存取原則，然後將容器的權限。

新增容器上建立新的儲存的存取原則，並傳回原則名稱的方法︰

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

**Main （）**的方法， **Console.ReadLine()**，通話之前底部新增下列幾行至第一個清除任何現有的存取原則，然後呼叫**CreateSharedAccessPolicy()**的方法︰    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

請注意的時清除容器的存取原則，您必須先取得容器的現有權限，然後清除權限，然後再次設定權限。

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>產生共用的 Access 簽章使用存取原則的容器 URI

接下來，我們要建立另一個共用的 access 簽章，我們建立更早版本，但這次我們會與存取原則我們在先前範例中所建立的關聯簽名容器上。

新增新的方法來產生容器上的另一個共用的 access 簽章︰

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

**Main （）**的方法， **Console.ReadLine()**，通話之前底部新增下列幾行呼叫**GetContainerSasUriWithPolicy**方法︰

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>產生共用的 Access 簽章上使用存取原則 Blob URI

最後，我們將會新增類似的方法來建立另一個 blob 並產生存取原則與相關聯的共用的存取簽章。

新增新的方法來建立 blob 並產生共用的 access 簽章︰

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

**Main （）**的方法， **Console.ReadLine()**，通話之前底部新增下列幾行呼叫**GetBlobSasUriWithPolicy**方法︰    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

**Main**方法現在看起來應該像這樣完整。 執行撰寫共用的 access 簽章 Uri 至主控台] 視窗中，然後複製並貼到本教學課程中的第二部分中使用的文字檔案。

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

當您執行 GenerateSharedAccessSignatures 主控台應用程式時，您會看到類似以下主控台視窗中的輸出。 這些是您會在第 2 部分的教學課程中使用共用的 access 簽章。

![sa 主控台輸出-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>第 2 部分︰ 建立主控台應用程式來測試共用的 Access 簽章

若要測試的上一個範例中建立共用的 access 簽章，我們要建立第二個主控台應用程式，使用簽章和 blob 容器上執行的作業。

> [AZURE.NOTE] 如果之後您完成教學課程的第一個部分過去超過 24 小時的時間之後，您所產生的簽章將不再有效。 在此情況下，您應該執行程式碼中產生使用全新的共用的存取簽章教學課程的第二部分中的第一個主控台應用程式。

在 Visual Studio 中，建立新的 Windows 主控台應用程式並將其命名**ConsumeSharedAccessSignatures**。 先前一樣，請新增**Microsoft.WindowsAzure.Configuration.dll**和**Microsoft.WindowsAzure.Storage.dll**，參考。

Program.cs 檔案頂端新增下列**使用**陳述式︰

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

本文中的**main**方法，新增下列常數，並更新其值為您產生第 1 部分的教學課程中的共用的 access 簽章。

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>加入方法，請嘗試使用共用的 Access 簽章的容器作業

接下來，我們將會新增測試容器上使用共用的 access 簽章某些代表容器作業的方法。 請注意，共用的 access 簽名已用來傳回容器，驗證的存取權以了解單獨使用的簽章的容器的參照。

加入 Program.cs 下列方法︰

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


更新這兩個容器上所建立的共用的 access 簽章以撥號給**UseContainerSAS()** **main （）**方法︰

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>加入方法，請嘗試使用共用的 Access 簽章 Blob 作業

最後，我們將會新增測試 blob 上使用共用的 access 簽名某些代表 blob 作業的方法。 在此情況下我們使用建構函式**CloudBlockBlob(String)**，在共用的 access 的簽章，傳遞傳回 blob 的參照。 其他都不需要驗證。它會根據單獨的簽章。

加入 Program.cs 下列方法︰

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


更新這兩種 blob 上所建立的共用的 access 簽章呼叫**UseBlobSAS()** **main**方法︰

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

執行主控台應用程式，若要查看哪些作業允許的簽章的輸出可以一同觀看。 輸出主控台視窗中的看起來如下︰

![sa 主控台輸出-2][sas-console-output-2]

## <a name="next-steps"></a>後續步驟

[共用 Access 簽章，第 1 部分︰ 了解 SA 模型](storage-dotnet-shared-access-signature-part-1.md)

[管理容器及 blob 的匿名讀取權限](storage-manage-access-to-resources.md)

[使用共用的 Access 簽章 (REST API) 委派存取](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[資料表和佇列中 SA 簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
