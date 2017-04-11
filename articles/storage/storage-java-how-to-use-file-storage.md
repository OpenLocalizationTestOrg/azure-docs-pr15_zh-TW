<properties
    pageTitle="如何使用檔案儲存空間，從 Java |Microsoft Azure"
    description="瞭解如何使用 Azure 檔案服務上傳、 下載，清單中，並刪除檔案。 Java 撰寫的範例。"
    services="storage"
    documentationCenter="java"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-file-storage-from-java"></a>如何使用從 Java 檔案儲存空間

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>概觀

本指南中，您將學習如何執行基本作業 Microsoft Azure 檔案儲存空間服務。 透過 Java 撰寫的範例中，您將學習如何建立與目錄、 上傳、] 清單中，並刪除檔案。 如果您是新的 Microsoft Azure 檔案儲存空間服務，透過以下區段中的概念會很有幫助瞭解範例。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>建立 Java 應用程式

若要建立範例，您需要 Java 開發套件 (JDK) 和 [Azure 儲存體 SDK Java] []。 您也應該建立 Azure 儲存體帳戶。

## <a name="setup-your-application-to-use-file-storage"></a>設定您的應用程式將檔案儲存空間

若要使用的 Api Azure 儲存空間，請移至您要存取儲存服務的 Java 檔案頂端新增下列陳述式。

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.file.*;

## <a name="setup-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串

若要使用的檔案儲存空間，您需要連線至 Azure 儲存體帳戶。 第一步就是設定我們會用來連線到您儲存帳戶的連線字串。 讓我們來定義靜態變數來執行這項作業。

    // Configure the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account_name;" +
        "AccountKey=your_storage_account_key";

> [AZURE.NOTE] 使用您儲存的帳戶的實際值取代 your_storage_account_name 和 your_storage_account_key。

## <a name="connecting-to-an-azure-storage-account"></a>連線至 Azure 儲存體帳戶

若要連線至您儲存的帳戶，您需要使用**CloudStorageAccount**物件，它**剖析**的方法傳遞連線字串。

    // Use the CloudStorageAccount object to connect to your storage account
    try {
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
    } catch (InvalidKeyException invalidKey) {
        // Handle the exception
    }

因此您需要將其放在嘗試/攔截**CloudStorageAccount.parse** ，就會擲回 InvalidKeyException。

## <a name="how-to-create-a-share"></a>如何︰ 建立共用

所有檔案和檔案儲存空間中的目錄都位於容器，稱為 [**共用]**。 您儲存的帳戶，可以讓您帳戶的容量允許多共用。 若要取得共用和其內容的存取權，您需要使用的檔案儲存空間用戶端。

    // Create the file storage client.
    CloudFileClient fileClient = storageAccount.createCloudFileClient();

使用的檔案儲存空間用戶端，您可以取得共用的參考。

    // Get a reference to the file share
    CloudFileShare share = fileClient.getShareReference("sampleshare");

若要實際建立共用，請使用**createIfNotExists** CloudFileShare] 物件的方法。

    if (share.createIfNotExists()) {
        System.out.println("New share created");
    }

此時，**共用**保留名為**sampleshare**共用的參考。

## <a name="how-to-upload-a-file"></a>如何︰ 上傳檔案

Azure 檔案儲存空間共用包含至少，在檔案可以所在位置的根目錄。 在 [此節]，您將學習如何從共用的根目錄到本機的儲存空間將檔案上傳。

上傳檔案的第一個步驟是取得目錄的參考其所在的位置。 呼叫 [共用] 物件的**getRootDirectoryReference**方法執行此動作。

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

您已經有共用的根目錄的參照，您可以上傳檔案到使用下列程式碼。

    // Define the path to a local file.
    final String filePath = "C:\\temp\\Readme.txt";

    CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
    cloudFile.uploadFromFile(filePath);

## <a name="how-to-create-a-directory"></a>如何︰ 建立目錄

您也可以將內子目錄，而不是由全部的根目錄中的檔案，以組織儲存空間。 Azure 檔案儲存服務可讓您建立多目錄時可讓您的帳戶。 下列程式碼會建立名為**sampledir**根目錄下的子目錄。

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the sampledir directory
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    if (sampleDir.createIfNotExists()) {
        System.out.println("sampledir created");
    } else {
        System.out.println("sampledir already exists");
    }

## <a name="how-to-list-files-and-directories-in-a-share"></a>如何︰ 清單中共用檔案和目錄

藉由在 CloudFileDirectory 參照呼叫**listFilesAndDirectories**輕鬆地完成取得清單內的共用檔案和目錄。 方法會傳回您可以持續 ListFileItem 物件的清單。 例如，下列程式碼會列出檔案與目錄內的根目錄。

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
        System.out.println(fileItem.getUri());
    }


## <a name="how-to-download-a-file"></a>如何︰ 下載的檔案

針對檔案儲存空間，您將會執行更頻繁作業是以下載檔案。 在下列範例中，將程式碼下載 SampleFile.txt，並顯示其內容。

    //Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    //Get a reference to the directory that contains the file
    CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

    //Get a reference to the file you want to download
    CloudFile file = sampleDir.getFileReference("SampleFile.txt");

    //Write the contents of the file to the console.
    System.out.println(file.downloadText());

## <a name="how-to-delete-a-file"></a>如何︰ 刪除檔案

其他常見的檔案儲存空間作業是刪除檔案。 下列程式碼會刪除命名 SampleFile.txt 存放在名為**sampledir**的目錄內的檔案。


    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory where the file to be deleted is in
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    String filename = "SampleFile.txt"
    CloudFile file;

    file = containerDir.getFileReference(filename)
    if ( file.deleteIfExists() ) {
        System.out.println(filename + " was deleted");
    }


## <a name="how-to-delete-a-directory"></a>如何︰ 刪除目錄

刪除目錄是相當簡單的工作，，但請注意，您無法刪除的目錄，仍會包含檔案或其他目錄。

    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.getRootDirectoryReference();

    // Get a reference to the directory you want to delete
    CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

    // Delete the directory
    if ( containerDir.deleteIfExists() ) {
        System.out.println("Directory deleted");
    }


## <a name="how-to-delete-a-share"></a>如何︰ 刪除共用

刪除共用由 CloudFileShare 物件上呼叫**deleteIfExists**方法。 以下是範例執行的程式碼。

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the file client.
       CloudFileClient fileClient = storageAccount.createCloudFileClient();

       // Get a reference to the file share
       CloudFileShare share = fileClient.getShareReference("sampleshare");

       if (share.deleteIfExists()) {
           System.out.println("sampleshare deleted");
       }
    } catch (Exception e) {
        e.printStackTrace();
    }

## <a name="next-steps"></a>後續步驟

如果您想要進一步瞭解其他 Azure 儲存體 Api，請遵循這些連結。

- [Java 開發人員中心](http://azure.microsoft.com/develop/java/)
- [Azure 儲存體 java SDK](https://github.com/azure/azure-storage-java)
- [Android 版 SDK azure 儲存空間](https://github.com/azure/azure-storage-android)
- [Azure 儲存用戶端 SDK 參考](http://dl.windowsazure.com/storage/javadoc/)
- [Azure 儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- [傳送以 AzCopy 命令列公用程式的資料](storage-use-azcopy.md)
