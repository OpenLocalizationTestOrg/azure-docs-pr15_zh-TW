<properties 
    pageTitle="將現有的 Blob 複製到媒體服務資產 |Microsoft Azure" 
    description="本主題說明如何將現有的 blob 複製到媒體服務資產。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/13/2016" 
    ms.author="juliako"/>

#<a name="copying-an-existing-blob-into-a-media-services-asset"></a>將現有的 Blob 複製到媒體服務資產

本主題說明如何複製到新的 Microsoft Azure 媒體服務資產 blob 儲存體帳戶。

將二進位大型物件無法存在於媒體服務帳戶，或是不是與媒體服務帳戶相關聯的儲存空間帳戶相關聯的儲存空間帳戶。 本主題示範如何複製到媒體服務資產 blob 儲存體帳戶。 請注意，您也可以複製整個資料中心。 不過，可能會有這樣所產生的費用。 如需有關價格的詳細資訊，請參閱[資料傳輸](https://azure.microsoft.com/pricing/#header-11)。

>[AZURE.NOTE] 您應該嘗試變更 blob 容器不使用媒體服務 Api 媒體服務所產生的內容。

##<a name="download-sample"></a>下載範例

取得，並執行從[這裡](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)的範例。

##<a name="prerequisites"></a>必要條件

- 新的或現有的 Azure 訂閱中的兩個媒體服務帳戶。 請參閱[如何建立媒體服務帳戶](media-services-portal-create-account.md)的主題。
- 作業系統︰ Windows 10、 Windows 7、 Windows 2008 R2 或 Windows 8。
- .NET framework 4.5。
- Visual Studio 2010 SP1 （Professional、 Premium、 Ultimate 或 Express） 或更新版本。

##<a name="set-up-your-project"></a>設定您的專案

此區段中，您會建立並設定 C# 主控台應用程式專案。

1. 使用 Visual Studio 建立新的方案包含 C# 主控台應用程式專案。 
2. 輸入 CopyExistingBlobsIntoAsset 名稱，然後按一下 [確定]。
1. 若要新增媒體服務相關 Dll 參考使用 Nuget。 在 Visual Studio 主要功能表中，選取工具]-> [文件庫封裝管理員]-> [封裝管理員主控台。 在主控台視窗類型安裝套件 windowsazure.mediaservices 並按 enter。
1. 新增所需的 [此專案的其他參照︰ System.Configuration。
1. 使用預設的下列項目新增至 Programs.cs 檔案陳述式的取代︰
        
        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure;
        using System.Web;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Auth;

1. .Config 檔案中新增和 appSettings 區段及更新您的媒體服務與儲存金鑰所根據的值和名稱值。 

        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
          <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
          <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
          <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
          <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
        </appSettings>


##<a name="copy-blobs-from-a-storage-account-into-a-media-services-asset"></a>二進位大型物件複製到媒體服務資產的儲存空間帳戶

下列範例會執行下列工作︰

1. 建立 CloudMediaContext 執行個體。 
1. 建立 CloudStorageAccount 執行個體︰ _sourceStorageAccount 和 _destinationStorageAccount。
1. 上傳至位於 _sourceStorageAccount blob 容器的平滑串流本機目錄中的檔案。 
1. 建立新的資產。 Blob 的容器所建立的這項資產位於 _destinationStorageAccount。 
1. 使用 Azure 儲存體 SDK，將指定的二進位大型物件複製到資產相關聯的容器。

    >[AZURE.NOTE]如果定位器已過期，複製作業會不會引發例外狀況。

1. 後，在此範例中我們所複製投影片平滑串流檔案，此範例會顯示如何設定.ism 檔案是主要的檔案。 如果，例如，我們會複製.mp4 檔案，則會將 mp4 檔案設定為主要的檔案。
1. 建立資產相關聯的 OnDemandOrigin 定位器平滑串流 URL。 
            
        class Program
        {
            // Read values from the App.config file. 
            static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
            static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
            static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];

            private static MediaServicesCredentials _cachedCredentials = null;
            private static CloudMediaContext _context = null;

            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;

            static void Main(string[] args)
            {
            _cachedCredentials = new MediaServicesCredentials(
                    _accountName,
                    _accountKey);
            // Use the cached credentials to create CloudMediaContext.
            _context = new CloudMediaContext(_cachedCredentials);

            // In this example the storage account from which we copy blobs is not 
            // associated with the Media Services account into which we copy blobs.
            // But the same code will work for coping blobs from a storage account that is 
            // associated with the Media Services account.
            //
            // Get a reference to a storage account that is not associated with a Media Services account
            // (an external account).  
            StorageCredentials externalStorageCredentials =
                new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
            _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);

            //Get a reference to the storage account that is associated with a Media Services account. 
            StorageCredentials mediaServicesStorageCredentials =
                new StorageCredentials(_storageAccountName, _storageAccountKey);
            _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);

            // Upload Smooth Streaming files into a storage account.
            string localMediaDir = @"C:\supportFiles\streamingfiles";
            CloudBlobContainer blobContainer =
                UploadContentToStorageAccount(localMediaDir);

            // Create a new asset and copy the smooth streaming files into 
            // the container that is associated with the asset.
            IAsset asset = CreateAssetFromExistingBlobs(blobContainer);

            // Get the streaming URL for the smooth streaming files 
            // that were copied into the asset.   
            string urlForClientStreaming = CreateStreamingLocator(asset);
            Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);

            Console.ReadLine();
            }

            /// <summary>
            /// Uploads content from a local directory into the specified storage account.
            /// In this example the storage account is not associated with the Media Services account.
            /// </summary>
            /// <param name="localPath">The path from which to upload the files.</param>
            /// <returns>The container that contains the uploaded files.</returns>
            static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
            {
            CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();

            CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");

            externalMediaBlobContainer.CreateIfNotExists();

            // Upload files to the blob container.  
            DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
            foreach (var file in uploadDirectory.EnumerateFiles())
            {
                CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);

                blob.UploadFromFile(file.FullName, FileMode.Open);
            }

            return externalMediaBlobContainer;
            }

            /// <summary>
            /// Creates a new asset and copies blobs from the specifed storage account.
            /// </summary>
            /// <param name="mediaBlobContainer">The specified blob container.</param>
            /// <returns>The new asset.</returns>
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
            {
            // Create a new asset. 
            IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);

            IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                TimeSpan.FromHours(24), AccessPermissions.Write);
            ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);

            CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

            // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
            string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];

            CloudBlobContainer assetContainer =
                destBlobStorage.GetContainerReference(destinationContainerName);

            if (assetContainer.CreateIfNotExists())
            {
                assetContainer.SetPermissions(new BlobContainerPermissions
                {
                PublicAccess = BlobContainerPublicAccessType.Blob
                });
            }

            var blobList = mediaBlobContainer.ListBlobs();
            foreach (var sourceBlob in blobList)
            {
                var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
                CopyBlob(sourceBlob as ICloudBlob, assetContainer);
                assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                assetFile.Update();
            }

            asset.Update();

            destinationLocator.Delete();
            writePolicy.Delete();

            // Since we copied a set of Smooth Streaming files, 
            // set the .ism file to be the primary file. 
            // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
            SetISMFileAsPrimary(asset);

            return asset;
            }

            /// <summary>
            /// Creates the OnDemandOrigin locator in order to get the streaming URL.
            /// </summary>
            /// <param name="asset">The asset that contains the smooth streaming files.</param>
            /// <returns>The streaming URL.</returns>
            static public string CreateStreamingLocator(IAsset asset)
            {
            var ismAssetFile = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();

            // Create a 30-day readonly access policy. 
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                TimeSpan.FromDays(30),
                AccessPermissions.Read);

            // Create a locator to the streaming content on an origin. 
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

            return originLocator.Path + ismAssetFile.Name + "/manifest";
            }

            /// <summary>
            /// Copies the specified blob into the specified container.
            /// </summary>
            /// <param name="sourceBlob">The source container.</param>
            /// <param name="destinationContainer">The destination container.</param>
            static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
            {
            var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
            {
                Permissions = SharedAccessBlobPermissions.Read,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
            });

            ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

            if (destinationBlob.Exists())
            {
                Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
            }
            else
            {

                // Display the size of the source blob.
                Console.WriteLine(sourceBlob.Properties.Length);

                Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));

                while (true)
                {
                // The StartCopyFromBlob is an async operation, 
                // so we want to check if the copy operation is completed before proceeding. 
                // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                destinationBlob.FetchAttributes();
                if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                {
                    break;
                }
                //It's still not completed. So wait for some time.
                System.Threading.Thread.Sleep(1000);
                }


                // Display the size of the destination blob.
                Console.WriteLine(destinationBlob.Properties.Length);

            }
            }

            /// <summary>
            /// Sets a file with the .ism extension as a primary file.
            /// </summary>
            /// <param name="asset">The asset that contains the smooth streaming files.</param>
            static private void SetISMFileAsPrimary(IAsset asset)
            {

            //If you expect the asset to contain the .ism asset file, set the .ism file as the primary file.
            var ismAssetFiles = asset.AssetFiles.ToList().
                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();

            // The following code assigns the first .ism file as the primary file in the asset.
            // An asset should have one .ism file.  
            ismAssetFiles.First().IsPrimary = true;
            ismAssetFiles.First().Update();
            }
        }

 

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
