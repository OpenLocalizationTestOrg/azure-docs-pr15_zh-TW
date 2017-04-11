<properties 
    pageTitle="上傳檔案至媒體服務帳戶使用.NET |Microsoft Azure" 
    description="瞭解如何將媒體服務取得媒體內容建立及上傳的資產。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="juliako"/>



# <a name="upload-files-into-a-media-services-account-using-net"></a>上傳檔案至使用.NET 媒體服務帳戶

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [其餘](media-services-rest-upload-files.md)
 - [入口網站](media-services-portal-upload-files.md)

在媒體服務，您上傳 （或內嵌） 資產將數位檔案。 **資產**實體可以包含視訊、 音訊、 圖像、 縮圖的集合，文字追蹤和字幕檔案 （以及這些檔案的中繼資料。） 上傳檔案後您的內容是安全地儲存在雲端的進一步處理和資料流。

資產中的檔案被稱為**資產檔案**。 **AssetFile**執行個體和實際的媒體檔案是兩個不同的物件。 媒體檔案包含實際的媒體內容時，AssetFile 執行個體包含媒體檔案相關的中繼資料。

>[AZURE.NOTE]選擇 [資產檔案名稱時，就會套用下列事項︰
>
>- 媒體服務串流內容 (例如，http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters。) 建置 Url 時，請使用 IAssetFile.Name 屬性的值因此，不允許的百分比編碼。 [**名稱**] 屬性的值不能有下列[百分比編碼-保留字元](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)︰ !*'();:@&=+$,/?%#[]"。 此外，只能有一個 「。 」 的副檔名。
>
>- 名稱長度不應該大於 260 個字元。

當您建立資產時，您可以指定下列的加密選項。 

- 使用**無**-不加密。 這是預設值。 請注意，使用此選項時，您的內容未受到保護傳送或儲存區中的其餘部分。
如果您打算將 MP4 使用漸進下載時，請使用這個選項。 
- **CommonEncryption** -使用此選項，如果您要上傳的已加密並以一般加密或 PlayReady DRM （例如，平滑串流保護與 PlayReady DRM） 保護內容。
- **EnvelopeEncrypted** – 使用這個選項，如果您要上傳 HLS 以 AES 加密。 請注意，檔案必須有已編碼加密轉換的管理員。
- **StorageEncrypted** -加密本機使用其餘位元 AES 256 加密，然後儲存位置的 Azure 儲存體將加密的上傳您清除的內容。 受到保護的儲存空間加密的資產會自動加密放在之前編碼，加密的檔案系統中和您也可以選擇重新加密之前上傳回到為新的輸出資產。 儲存加密的主要的使用情況時，您要保護高品質輸入的媒體檔案與在其他加密磁碟上。

    媒體服務磁碟上儲存為提供加密資產，不透過線上像數位版權主管 (DRM)。

    如果您的資產加密的儲存空間，您必須設定資產傳遞原則。 如需詳細資訊，請參閱[設定資產傳遞原則](media-services-dotnet-configure-asset-delivery-policy.md)。

如果您指定的加密**CommonEncrypted** ] 選項，或**EnvelopeEncypted**選項您資產，您必須建立您的資產與**ContentKey**之間的關聯。 如需詳細資訊，請參閱[如何建立 ContentKey](media-services-dotnet-create-contentkey.md)。 

如果您指定的加密**StorageEncrypted**選項您資產，媒體服務 SDK.net 會建立您的資產的**StorateEncrypted** **ContentKey** 。


本主題說明如何使用 [上傳檔案到媒體服務資產的 [媒體服務.NET SDK，以及媒體服務.NET SDK 副檔名。

 
## <a name="upload-a-single-file-with-media-services-net-sdk"></a>上傳媒體服務.NET SDK 單一檔案 

下列範例會使用.NET SDK 來執行下列工作︰ 

- 建立空白的資產。
- 建立我們想要與資產關聯 AssetFile 執行個體。
- 建立 AccessPolicy 執行個體的資產定義的權限及持續時間的存取權。
- 建立資產提供存取定位器執行個體。
- 上傳到媒體服務的單一媒體檔案。 

        
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
        }

##<a name="upload-multiple-files-with-media-services-net-sdk"></a>使用上傳多個檔案媒體服務.NET SDK 

下列程式碼會顯示如何建立資產及上傳多個檔案。

程式碼會執行下列動作︰
    
-   建立使用 CreateEmptyAsset 方法在上一個步驟中定義的空白資產。
    
-   建立**AccessPolicy**執行個體的資產定義的權限及持續時間的存取權。
    
-   建立資產提供存取**定位器**執行個體。
    
-   建立**BlobTransferClient**執行個體。 此類型代表操作 Azure blob 的用戶端。 在此範例中，我們會使用用戶端來監控上傳進度。 
    
-   列舉指定目錄中的檔案，並建立**AssetFile**執行個體的每個檔案。
    
-   將媒體服務使用**UploadAsync**方法上, 傳檔案。 
    
>[AZURE.NOTE] 使用 [UploadAsync 方法，確定不會封鎖來電且平行上傳的檔案是。
    
    
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
    
    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }



上傳大型資產的數字，請考慮下列。

- 建立新的**CloudMediaContext**物件，每個執行緒。 **CloudMediaContext**類別不安全的執行緒。
 
- 增加 NumberOfConcurrentTransfers 從 2 的預設值，以較高的值，例如 5。 設定此屬性會影響**CloudMediaContext**的所有例項。 
 
- 保留 ParallelTransferThreadCount 在 10 的預設值。
 
##<a id="ingest_in_bulk"></a>使用媒體服務.NET SDK 大量 ingesting 資產 

上傳大型資產檔案可瓶頸資產建立期間。 Ingesting 大量或 「 大量 Ingesting 」 中的資產，包括耦合資產建立從上傳程序。 若要使用大量 ingesting 的方法，建立資訊清單 (IngestManifest)，其中說明資產，其相關聯的檔案。 上傳至資訊清單的 blob 容器的關聯的檔案，然後使用您所選擇的 [上傳] 方法。 Microsoft Azure 媒體服務監看式資訊清單相關聯的 blob 容器。 將檔案上傳至 blob 容器後 Microsoft Azure 媒體服務就會完成資產建立根據資訊清單 (IngestManifestAsset) 中資產的設定。


若要建立新的 IngestManifest 通話公開 CloudMediaContext IngestManifests 集合的建立方式。 這個方法會建立新的 IngestManifest 使用所提供的資訊清單名稱。

    IIngestManifest manifest = context.IngestManifests.Create(name);

建立會出現大量 IngestManifest 與相關聯的資產。 設定的大量 ingesting 資產的所需的加密選項。

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

與的大量 ingesting 大量 IngestManifest IngestManifestAsset 產生關聯的資產。 它也將會構成每個資產 AssetFiles 相關聯。 若要建立 IngestManifestAsset，使用 [建立方法伺服器內容。

下列範例會示範建立關聯大量先前建立的兩個資產的新增兩個新 IngestManifestAssets 內嵌資訊清單。 每個 IngestManifestAsset 也會在大量 ingesting 將一組的每個資產將上傳的檔案。  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;
    
    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
    
您可以使用任何高速用戶端應用程式中的上傳到 blob 儲存容器 URI IngestManifest 的**IIngestManifest.BlobStorageUriForUpload**屬性所提供的資產檔案。 一個主要高速上傳服務是[Aspera 上 Azure 應用程式的需求](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6)。 您也可以撰寫程式碼上, 傳的資產檔案，如下列範例所示。
    
    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
    
            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
    
            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);
    
            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });
    
        copytask.Start();
    }

上傳在本主題中使用範例的資產檔案的程式碼如下列範例所示。
    
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
    

您可以決定所有相關**IngestManifest**輪詢**IngestManifest**的統計資料] 屬性的資產大量 ingesting 的進度。 若要更新的進度資訊，請您必須使用新的**CloudMediaContext**投票的統計資料] 屬性每次。

下列範例會示範投票，以利用**識別碼**IngestManifest。
    
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
    
          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
    
                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }
    
             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
    


##<a name="upload-files-using-net-sdk-extensions"></a>上傳檔案使用.NET SDK 擴充功能 

下列範例會示範如何使用.NET SDK 擴充功能將單一檔案上傳。 在此情況下使用**CreateFromFile**方法，但非同步版本也是使用 (**CreateFromFileAsync**)。 **CreateFromFile**方法可讓您指定的檔案名稱、 加密] 選項，以及回撥，才能報表的檔案上傳進度。


    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });
    
        Console.WriteLine("Asset {0} created.", inputAsset.Id);
    
        return inputAsset;
    }

下列範例會撥打 UploadFile 函數，並指定 [資產] 建立選項儲存加密。  


    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-step"></a>下一步

既然您已上傳資產的媒體服務，請移至[如何取得媒體處理器][]主題。

[如何取得媒體處理器]: media-services-get-media-processor.md
 
