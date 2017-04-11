<properties
    pageTitle="如何使用從 iOS 的 Azure Blob 儲存體 |Microsoft Azure"
    description="Azure Blob 儲存體 （物件儲存區） 的雲端儲存非結構化的資料。"
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>如何使用從 iOS Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概觀

本文將說明如何執行常見的案例使用 Microsoft Azure Blob 儲存體。 範例撰寫的目標 C，並使用[IOS 版 Azure 儲存用戶端程式庫](https://github.com/Azure/azure-storage-ios)。 涵蓋的案例包括**上傳**、**清單**、**下載**及**刪除**二進位大型物件。 如需有關二進位大型物件的詳細資訊，請參閱[下一步](#next-steps)] 區段。 您也可以下載的[範例應用程式](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample)，快速查看 iOS 應用程式中的使用 Azure 儲存體。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>將您的應用程式匯入 Azure 儲存體 iOS 文件庫

您可以將匯入 Azure 儲存體 iOS 文件庫應用程式使用[Azure 儲存體 CocoaPod](https://cocoapods.org/pods/AZSClient)或匯入**架構**檔案。

## <a name="cocoapod"></a>CocoaPod

1. 如果您還沒有這麼做，開啟終端機視窗，並執行下列命令，您電腦上[安裝 CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3)

        sudo gem install cocoapods

2. 接著，在專案目錄 (目錄包含您`.xcodeproj`檔案)，建立新檔案稱為`Podfile`（沒有副檔名）。 新增下列`Podfile`和儲存

        pod 'AZSClient'

3. 在終端機視窗中，瀏覽至專案目錄，然後執行下列命令

        pod install

4. 如果您`.xcodeproj`Xcode 中開啟，請將其關閉。 在您的專案目錄中開啟 [建立新的專案檔案，就必須`.xcworkspace`副檔名。 這是您要從查看的現在的檔案。

## <a name="framework"></a>架構
若要使用的 Azure 儲存體 iOS 文件庫，您必須建立架構檔案。

1. 首先，請下載或複製[azure-儲存空間-ios repo](https://github.com/azure/azure-storage-ios)。

2. 移至*azure-儲存空間-ios* -> *的文件庫* -> *Azure 儲存用戶端文件庫*，並開啟`AZSClient.xcodeproj`Xcode 中。

3. 在左上角的 Xcode，變更作用中的配置 「 Azure 儲存用戶端文件庫 」 從 「 架構 」。

4. 建立專案 （⌘ + B）。 這會建立`AZSClient.framework`在桌面上的檔案。

您可以再檔案匯入架構應用程式執行下列動作︰

1. 建立新專案，或開啟現有專案中 Xcode。

2. 按一下左側導覽中的專案，然後按一下頂端的專案編輯器] 中的 [*一般*] 索引標籤。

3. 在 [*連結架構與文件庫*] 區段中，按一下 [新增] 按鈕 （+）。

4. 按一下 [*新增其他...*]。 瀏覽至並新增`AZSClient.framework`您剛剛建立的檔案。

5. 在 [*連結架構與文件庫*] 區段中，請再按一下 [新增] 按鈕 （+）。

6. 在文件庫所提供的清單中，搜尋`libxml2.2.dylib`並將其新增至您的專案。

7. 按一下頂端的專案編輯器] 中的 [*建置設定*] 索引標籤。

8. 在 [*搜尋路徑*] 區段中，按兩下*架構搜尋路徑*並新增路徑您`AZSClient.framework`檔案。

## <a name="import-statement"></a>匯入陳述式
您必須在您要啟動 Azure 儲存體 API 檔案中包含下列匯入陳述式。

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>非同步作業
> [AZURE.NOTE] 所有執行服務所要求的方法是非同步作業。 在 [程式碼範例中，您會找到這些方法已完成處理常式。 內完成處理常式的程式碼會執行**之後**要求完成。 正在進行後完成處理常式會執行**時**要求的程式碼。

## <a name="create-a-container"></a>建立容器
Azure 儲存體中的每個 blob 必須位於容器。 下列範例會示範如何建立容器，稱為*newcontainer*，在您儲存的帳戶不存在。 選擇您的容器的名稱時, 留意前述命名規則。

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

若要確認運作查看 [ [Microsoft Azure 儲存檔案總管](http://storageexplorer.com)，並確認該*newcontainer*清單中的儲存空間帳戶容器]。

## <a name="set-container-permissions"></a>設定容器權限
依預設為**私人**的存取設定容器的權限。 不過，容器提供容器存取幾個不同的選項︰

- **私人**︰ 僅帳戶擁有者可以讀取容器和 blob 的資料。

- **Blob**: Blob 此容器中的資料可以讀取透過匿名要求，但不是使用容器資料。 用戶端無法列舉透過匿名要求容器內的二進位大型物件。

- **容器**︰ 可透過匿名要求讀取容器和 blob 的資料。 用戶端可以列舉透過匿名要求，容器內的二進位大型物件，但無法列舉容器內的儲存空間的帳戶。

下列範例會顯示如何建立容器與**容器**允許公用的唯讀存取網際網路上的所有使用者的存取權限︰

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>上傳至容器的 blob
Blob 儲存體[Blob 服務概念](#blob-service-concepts)一節所述，為使用者提供的二進位大型物件的三種不同類型︰ 封鎖 blob，附加二進位大型物件，然後頁面二進位大型物件。 在此時間，Azure 儲存體 iOS 文件庫只支援區塊二進位大型物件。 在大多數情況下，封鎖 blob 會是要用的建議的類型。

下列範例會示範如何上傳從 NSString 區塊 blob。 如果此容器中已經存在同名的 blob，將會覆寫此 blob 的內容。

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

若要確認運作查看 [ [Microsoft Azure 儲存檔案總管](http://storageexplorer.com)，並確認容器*containerpublic*，包含 blob *sampleblob*]。 在此範例中，我們會使用公用容器，因此您也可以驗證這個工作移至 blob URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

上傳從 NSString 區塊 blob，除了類似的方法有 NSData、 NSInputStream 或本機的檔案。

## <a name="list-the-blobs-in-a-container"></a>清單方塊中 blob
下列範例會示範如何在清單方塊中的所有二進位大型物件。 執行此作業時，請記住下列參數︰     

- **continuationToken** -接續權杖代表清單作業應該開始的位置。 如果提供不權杖，則其會列出二進位大型物件的開頭。 從零進位到最大一組，可以列出二進位大型物件的任何數字。 如果這個方法會傳回零的結果，如果`results.continuationToken`不是零，可能會有服務上未列出的更多 blob。
- **前置詞**-您可以指定要用於 blob 清單的前置字元。 開始使用此前置詞的 blob 便會列出。
- **useFlatBlobListing** -為[命名參考容器和 blob](#naming-and-referencing-containers-and-blobs)一節所述，雖然 Blob 服務是一般的儲存空間配置，您可以建立虛擬階層命名二進位大型物件的路徑資訊。 不過，非平面清單目前不支援。這即將推出。 現在，此值應該是`YES`
- **blobListingDetails** -您可以指定要列出二進位大型物件時包含的項目
    - `AZSBlobListingDetailsNone`︰ 清單交付的 blob，並不會傳回 blob 中繼資料。
    - `AZSBlobListingDetailsSnapshots`︰ 清單交付 blob 和 blob 快照集。
    - `AZSBlobListingDetailsMetadata`︰ 在清單中，傳回每一個 blob 擷取 blob 中繼資料。
    - `AZSBlobListingDetailsUncommittedBlobs`︰ 列出已確認和未認可二進位大型物件。
    - `AZSBlobListingDetailsCopy`︰ 包含在清單中的複製內容。
    - `AZSBlobListingDetailsAll`︰ 列出所有可用的交付的 blob、 未交付的 blob 及快照，並傳回這些二進位大型物件的所有中繼資料] 及 [複製狀態。
- **maxResults** -這項作業傳回的結果數目上限。 若要設定限制使用-1。
- **completionHandler** -以清單運算的結果，來執行的程式碼的區塊。

在此範例中，用來協助程式方法是遞迴通話清單 blob 的方法，每次接續權杖會傳回。

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>下載 blob

下列範例會示範如何下載 blob NSString 的物件。

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>刪除 blob

下列範例會示範如何刪除 blob。

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>刪除 blob 容器

下列範例會示範如何刪除容器。

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>後續步驟

現在，您已經學會如何使用 iOS Blob 儲存體，請遵循這些連結，進一步瞭解 iOS 文件庫和儲存服務。

- [IOS 版 azure 儲存空間的用戶端文件庫](https://github.com/azure/azure-storage-ios)
- [Azure 儲存體 iOS 參考文件](http://azure.github.io/azure-storage-ios/)
- [Azure 儲存服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage)

如果您有問題有關此文件庫隨意張貼至我們的[MSDN Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata)或[堆疊溢位](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)。
如果您有 Azure 儲存體功能建議，請張貼[Azure 儲存空間的意見反應](https://feedback.azure.com/forums/217298-storage/)。
