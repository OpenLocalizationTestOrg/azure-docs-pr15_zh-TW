<properties 
    pageTitle="管理媒體服務資產跨多個儲存帳戶 |Microsoft Azure" 
    description="此文章提供您指引如何管理多個儲存帳戶之間的媒體服務資產。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"    
    ms.author="juliako"/>


#<a name="managing-media-services-assets-across-multiple-storage-accounts"></a>管理媒體服務資產跨多個儲存帳戶

開始使用 Microsoft Azure 媒體服務 2.2，您可以將多個儲存帳戶附加至單一媒體服務帳戶。 若要將多個儲存帳戶附加到媒體服務帳戶的功能會提供下列優點︰

- 負載平衡跨多個儲存帳戶的資產。
- 縮放比例媒體服務大量內容處理 （如目前為單一儲存帳戶有 500 TB 的最大限制）。 

本主題示範如何將多個儲存帳戶附加至 Azure 服務管理 REST API 媒體服務帳戶。 也會顯示如何建立使用媒體服務 SDK 的資產時，指定不同的儲存帳戶。 

##<a name="considerations"></a>考量

將多個儲存帳戶附加至您的媒體服務帳戶時, 注意下列事項︰

- 附加至媒體服務帳戶的所有儲存帳戶必須都使用相同的資料中心，為媒體服務帳戶。
- 目前後儲存帳戶附加到指定的媒體服務帳戶時，, 它不會中斷連結。
- 主要儲存帳戶是媒體服務帳戶建立時間期間所指定的項目。 目前您無法變更預設儲存的帳戶。 

其他考量︰

媒體服務串流內容 (例如，http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters。) 建立 Url 時，請使用**IAssetFile.Name**屬性的值因此，不允許的百分比編碼。 [名稱] 屬性的值不能有下列[百分比編碼-保留字元](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)︰ !*'();:@&=+$,/?%#[]"。 此外，只能有一個 「。 」 針對檔案的副檔名。

##<a name="to-attach-a-storage-account-with-azure-service-management-rest-api"></a>若要將附加 Azure 服務管理 REST API 儲存帳戶

目前，附加多個儲存帳戶的唯一方法是使用[Azure 服務管理 REST API](http://msdn.microsoft.com/library/azure/dn167014.aspx)。 在 [程式碼範例[如何︰ 使用媒體服務管理 REST API](https://msdn.microsoft.com/library/azure/dn167656.aspx)主題定義**AttachStorageAccountToMediaServiceAccount**方法，將儲存空間帳戶附加至指定的媒體服務帳戶。 在同一主題中的程式碼定義**ListStorageAccountDetails**方法會列出附加至指定的媒體服務帳戶的所有儲存帳戶。


##<a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>若要管理多個儲存帳戶之間的媒體服務資產

下列程式碼會使用最新的媒體服務 SDK 來執行下列工作︰

1. 顯示與指定的媒體服務帳戶相關聯的所有儲存帳戶。
1. 擷取預設儲存體帳戶的名稱。
1. 在預設儲存帳戶中建立新的資產。
1. 在指定的儲存帳戶建立輸出資產的編碼的工作。
    
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace MultipleStorageAccounts
        {
            class Program
            {
                // Location of the media file that you want to encode. 
                private static readonly string _singleInputFilePath =
                    Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");
        
                private static readonly string MediaServicesAccountName = 
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string MediaServicesAccountKey = 
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static CloudMediaContext _context;
                private static MediaServicesCredentials _cachedCredentials = null;
    
                static void Main(string[] args)
                {
    
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    MediaServicesAccountName,
                                    MediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
    
        
                    // Display the storage accounts associated with 
                    // the specified Media Services account:
                    foreach (var sa in _context.StorageAccounts)
                        Console.WriteLine(sa.Name);
        
                    // Retrieve the name of the default storage account.
                    var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
                    Console.WriteLine("Name: {0}", defaultStorageName.Name);
                    Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);
        
                    // Retrieve the name of a storage account that is not the default one.
                    var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
                    Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
                    Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);
                    
                    // Create the original asset in the default storage account.
                    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, 
                        defaultStorageName.Name, _singleInputFilePath);
                    Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);
                    
                    // Create an output asset of the encoding job in the other storage account.
                    IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
                    if(outputAsset!=null)
                        Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);
        
                }
        
                static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
                {
                    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                    
                    // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
                    var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);
        
                    var fileName = Path.GetFileName(singleFilePath);
        
                    var assetFile = asset.AssetFiles.Create(fileName);
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    return asset;
                }
        
                static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My encoding job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset", storageName,
                        AssetCreationOptions.None);
        
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new
                            EventHandler<JobStateChangedEventArgs>(StateChanged);
        
                    // Launch the job.
                    job.Submit();
        
                    // Check job execution and wait for job to finish. 
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                    progressJobTask.Wait();
        
                    // Get an updated job reference.
                    job = GetJob(job.Id);
        
                    // If job state is Error the event handling 
                    // method for job progress should log errors.  Here we check 
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        Console.WriteLine("\nExiting method due to job error.");
                        return null;
                    }
        
                    // Get a reference to the output asset from the job.
                    IAsset outputAsset = job.OutputMediaAssets[0];
        
                    return outputAsset;
                }
        
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                private static void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
        
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("********************");
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine("Please wait while local tasks or downloads complete...");
                            Console.WriteLine("********************");
                            Console.WriteLine();
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            Console.WriteLine("An error occurred in {0}", job.Id);
                            break;
                        default:
                            break;
                    }
                }
        
                static IJob GetJob(string jobId)
                {
                    // Use a Linq select query to get an updated 
                    // reference by Id. 
                    var jobInstance =
                        from j in _context.Jobs
                        where j.Id == jobId
                        select j;
                    // Return the job reference as an Ijob. 
                    IJob job = jobInstance.FirstOrDefault();
        
                    return job;
                }
            }
        }
 

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
