<properties
    pageTitle="如何使用檔案儲存空間，從 c + + |Microsoft Azure"
    description="Azure 檔案儲存空間的雲端儲存檔案的資料。"
    services="storage"
    documentationCenter=".net"
    authors="seguler"
    manager="jahogg"
    editor="tysonn" />

<tags ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="seguler" />

# <a name="how-to-use-file-storage-from-c"></a>如何使用 c + + 檔案儲存空間

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]
[AZURE.INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>關於本教學課程

在此教學課程中，您將學習如何執行基本作業 Microsoft Azure 檔案儲存空間服務。 透過 c + + 所撰寫的範例，您將學習如何建立與目錄、 上傳、] 清單中，並刪除檔案。 如果您是新的 Microsoft Azure 檔案儲存空間服務，透過以下區段中的概念會很有幫助瞭解範例。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>建立 c + + 應用程式

若要建立範例，您需要安裝 Azure 儲存用戶端程式庫 2.4.0 c + +。 您也應該建立 Azure 儲存體帳戶。

若要安裝 Azure 儲存用戶端 2.4.0 c + +，您可以使用下列方法之一︰

-   **Linux:**請依照 [ [Azure c + + 讀我檔案的儲存空間用戶端程式庫](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)] 頁面中的指示。

-   **Windows:**在 Visual Studio 中，按一下 [**工具&gt;NuGet 封裝管理員&gt;封裝管理員主控台**。 [NuGet 封裝管理員主控台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)中輸入下列命令，然後按**ENTER**。

    安裝套件 wastorage

## <a name="set-up-your-application-to-use-file-storage"></a>設定您的應用程式將檔案儲存空間

新增下列包含您要用來存取檔案 Azure 儲存空間 Api c + + 檔案頂端的陳述式︰

    #include "was/storage_account.h"
    #include "was/file.h"

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存連線字串

若要使用的檔案儲存空間，您需要連線至 Azure 儲存體帳戶。 第一步就是設定我們會用來連線到您儲存帳戶的連線字串。 讓我們來定義靜態變數來執行這項作業。

    // Define the connection-string with your values.
    const utility::string_t 
    storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

## <a name="connecting-to-an-azure-storage-account"></a>連線至 Azure 儲存體帳戶

您可以使用**cloud_storage_account**類別，代表您儲存帳戶的資訊。 若要從儲存連線字串中擷取您儲存的帳戶資訊，您可以使用**剖析**的方法。

    // Retrieve storage account from connection string. 
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);

## <a name="how-to-create-a-share"></a>如何︰ 建立共用

所有檔案和檔案儲存空間中的目錄都位於容器，稱為 [**共用]**。 您儲存的帳戶，可以讓您帳戶的容量允許的最多共用。 若要取得共用和其內容的存取權，您需要使用的檔案儲存空間用戶端。

    // Create the file storage client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();

使用的檔案儲存空間用戶端，您可以取得共用的參考。

    // Get a reference to the file share
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));

若要建立共用，請使用**create_if_not_exists** **cloud_file_share** ] 物件的方法。

    if (share.create_if_not_exists()) { 
        std::wcout << U("New share created") << std::endl;  
    }

此時，**共用**保留名稱，為**我範例共用**的參考。

## <a name="how-to-upload-a-file"></a>如何︰ 上傳檔案

至少，在 Azure 檔案儲存空間共用包含檔案可以所在位置的根目錄。 在 [此節]，您將學習如何從共用的根目錄到本機的儲存空間將檔案上傳。

上傳檔案的第一個步驟是取得目錄的參考其所在的位置。 呼叫 [共用] 物件的**get_root_directory_reference**方法執行此動作。

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();

您已經有共用的根目錄的參照，您可以上傳檔案到該檔案。 此範例中上傳的檔案、 文字及資料流。

    // Upload a file from a stream.
    concurrency::streams::istream input_stream = 
      concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

    azure::storage::cloud_file file1 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
    file1.upload_from_stream(input_stream);

    // Upload some files from text.
    azure::storage::cloud_file file2 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    file2.upload_text(_XPLATSTR("more text"));

    // Upload a file from a file.
    azure::storage::cloud_file file4 = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    file4.upload_from_file(_XPLATSTR("DataFile.txt"));  

## <a name="how-to-create-a-directory"></a>如何︰ 建立目錄

您也可以將內子目錄，而不是由全部的根目錄中的檔案，以組織儲存空間。 Azure 檔案儲存服務可讓您建立多目錄時可讓您的帳戶。 下列程式碼會建立名為**我範例目錄**下的根目錄，以及您在名為**「 我範例子目錄**的子目錄。
    
    // Retrieve a reference to a directory
    azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Return value is true if the share did not exist and was successfully created.
    directory.create_if_not_exists();
    
    // Create a subdirectory.
    azure::storage::cloud_file_directory subdirectory = 
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    subdirectory.create_if_not_exists();

## <a name="how-to-list-files-and-directories-in-a-share"></a>如何︰ 清單中共用檔案和目錄

藉由在**cloud_file_directory**參照呼叫**list_files_and_directories**輕鬆地完成取得清單內的共用檔案和目錄。 若要存取豐富的內容，傳回**list_file_and_directory_item**的方法，您必須呼叫**list_file_and_directory_item.as_file**方法以取得**cloud_file**物件] 或**list_file_and_directory_item.as_directory**方法取得**cloud_file_directory**物件。

下列程式碼會示範如何擷取及輸出的根目錄中共用的每個項目 URI。

    //Get a reference to the root directory for the share.
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    // Output URI of each item.
    azure::storage::list_file_and_diretory_result_iterator end_of_results;
    
    for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
    {
        if(it->is_directory())
        {
            ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
        }
        else if (it->is_file())
        {
            ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
        }       
    }
    

## <a name="how-to-download-a-file"></a>如何︰ 下載的檔案

若要下載檔案，請先擷取檔案，然後呼叫**download_to_stream**方法，您可以在本機檔案保留資料流物件中傳輸檔案內容。 或者，您可以使用**download_to_file**方法為本機檔案下載檔案的內容。 若要下載的檔案作為文字字串中的內容，您可以使用**download_text**方法。

下列範例會使用**download_to_stream**和**download_text**方法，示範下載前一節中所建立的檔案。

    // Download as text
    azure::storage::cloud_file text_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
    utility::string_t text = text_file.download_text();
    ucout << "File Text: " << text << std::endl;
    
    // Download as a stream.
    azure::storage::cloud_file stream_file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
    concurrency::streams::ostream output_stream(buffer);
    stream_file.download_to_stream(output_stream);
    std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
    std::vector<unsigned char>& data = buffer.collection();
    outfile.write((char *)&data[0], buffer.size());
    outfile.close();

## <a name="how-to-delete-a-file"></a>如何︰ 刪除檔案

其他常見的檔案儲存空間作業是刪除檔案。 下列程式碼會刪除命名我-範例-檔案-3 儲存的根目錄] 下的檔案。

    // Get a reference to the root directory for the share. 
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    azure::storage::cloud_file_directory root_dir = 
      share.get_root_directory_reference();
    
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
    
    file.delete_file_if_exists();

## <a name="how-to-delete-a-directory"></a>如何︰ 刪除目錄

刪除目錄是簡單的工作，但請注意，您無法刪除的目錄，仍會包含檔案或其他目錄。
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // Get a reference to the directory.
    azure::storage::cloud_file_directory directory = 
      share.get_directory_reference(_XPLATSTR("my-sample-directory"));
    
    // Get a reference to the subdirectory you want to delete.
    azure::storage::cloud_file_directory sub_directory =
      directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
    
    // Delete the subdirectory and the sample directory.
    sub_directory.delete_directory_if_exists();
    
    directory.delete_directory_if_exists();

## <a name="how-to-delete-a-share"></a>如何︰ 刪除共用

刪除共用由 cloud_file_share 物件上呼叫**delete_if_exists**方法。 以下是範例執行的程式碼。
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    // delete the share if exists
    share.delete_share_if_exists();

## <a name="set-the-maximum-size-for-a-file-share"></a>設定檔案共用的大小上限

您可以設定檔案共用，在 gb 的配額 （或最大值）。 您也可以檢查以查看目前共用儲存的資料量。

藉由設定共用的配額，您可以限制的總儲存在共用的檔案大小。 如果檔案共用上的總大小超過配額共用設定，然後用戶端將無法以增加現有檔案的大小或建立新的檔案，除非這些檔案是空的。

下列範例會顯示如何檢查共用目前的使用方式，以及如何設定共用的配額。

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client.
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    // Get a reference to the share.
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    if (share.exists())
    {
        std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();
    
        //This line sets the quota to 2560GB
        share.resize(2560);
    
        std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();
    
    }

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>產生共用的 access 簽章的檔案或檔案共用

您可以產生共用的 access 簽章 (SA) 檔案共用或個別檔案。 您也可以在 [管理共用的 access 簽章的檔案共用上建立共用的 access 原則。 建立共用的 access 原則建議，因為它提供一種如果應該遭到盜用撤銷 SA。

下列範例會建立一個共用的 access 原則共用，並再使用該原則提供 SA 上的共用的檔案限制式。

    // Parse the connection string for the storage account.
    azure::storage::cloud_storage_account storage_account = 
      azure::storage::cloud_storage_account::parse(storage_connection_string);
    
    // Create the file client and get a reference to the share
    azure::storage::cloud_file_client file_client = 
      storage_account.create_cloud_file_client();
    
    azure::storage::cloud_file_share share = 
      file_client.get_share_reference(_XPLATSTR("my-sample-share"));
    
    if (share.exists())
    {
        // Create and assign a policy
        utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

        azure::storage::file_shared_access_policy sharedPolicy = 
          azure::storage::file_shared_access_policy();

        //set permissions to expire in 90 minutes
        sharedPolicy.set_expiry(utility::datetime::utc_now() + 
          utility::datetime::from_minutes(90));

        //give read and write permissions
        sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

        //set permissions for the share
        azure::storage::file_share_permissions permissions; 

        //retrieve the current list of shared access policies
        azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;
        
        //add the new shared policy
        policies.insert(std::make_pair(policy_name, sharedPolicy));

        //save the updated policy list
        permissions.set_policies(policies);
        share.upload_permissions(permissions);

        //Retrieve the root directory and file references
        azure::storage::cloud_file_directory root_dir = 
          share.get_root_directory_reference();
        azure::storage::cloud_file file = 
          root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

        // Generate a SAS for a file in the share 
        //  and associate this access policy with it.       
        utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);
        
        // Create a new CloudFile object from the SAS, and write some text to the file.     
        azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
        utility::string_t text = _XPLATSTR("My sample content");        
        file_with_sas.upload_text(text);        
        
        //Download and print URL with SAS.
        utility::string_t downloaded_text = file_with_sas.download_text();      
        ucout << downloaded_text << std::endl;      
        ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;
    
    }

如需有關建立和使用共用的 access 簽章的詳細資訊，請參閱[使用共用 Access 簽章 (SA)](storage-dotnet-shared-access-signature-part-1.md)。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解 Azure 儲存空間，請探索這些資源︰

-   [儲存空間 c + + 的用戶端文件庫](https://github.com/Azure/azure-storage-cpp)

-   [Azure 儲存檔案總管](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)

-   [Azure 儲存文件](https://azure.microsoft.com/documentation/services/storage/)
