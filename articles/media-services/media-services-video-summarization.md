<properties
    pageTitle="若要建立視訊的摘要使用 Azure 媒體視訊的縮圖 |Microsoft Azure"
    description="視訊摘要可協助您建立的較長的視訊摘要自動選取最感興趣的程式碼片段，從來源視訊。 當您想要提供的發生的事長視訊中的快速概觀，這是很有用。"
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016"   
    ms.author="milanga;juliako;"/>

#<a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>若要建立視訊的摘要使用 Azure 媒體視訊的縮圖
##<a name="overview"></a>概觀

**Azure 媒體視訊縮圖**媒體處理器 (MP) 可讓您建立的視訊，只是要預覽的長視訊摘要客戶有用的摘要。 例如，客戶可能會想要查看簡短 」 摘要視訊 」 時，將游標停留在縮圖。 調整設定預設透過**Azure 媒體視訊縮圖**的參數，您可以使用 MP 強大擷取的偵測和串連技術以演算法產生描述性的 subclip。  

**Azure 媒體視訊縮圖**MP 目前是在預覽中。

本主題提供**Azure 媒體視訊縮圖**的詳細資料，並示範如何使用.NET 媒體服務 SDK

##<a name="video-summary-example"></a>視訊的摘要範例 

以下是一些範例 Azure 媒體視訊縮圖媒體處理器可以執行︰

###<a name="original-video"></a>原始的影片

[原始的影片](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###<a name="video-thumbnail-result"></a>視訊縮圖的結果

[視訊縮圖的結果](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="task-configuration-preset"></a>工作設定 （預設）

在建立視訊的縮圖工作與**Azure 媒體視訊縮圖**，您必須指定設定預設格式]。 使用下列基本 JSON 設定所建立上述縮圖的範例︰

    {"version":"1.0"}

目前，您可以變更下列參數︰

參數|描述
---|---
outputAudio|指定結果的視訊包含在所有音訊。 <br/>允許的值︰ True 或 False。 預設值為 True。
fadeInFadeOut|指定是否淡出轉換使用之間的不同的影片縮圖。  <br/>允許的值︰ True 或 False。  預設值為 True。
maxMotionThumbnailDurationInSecs|指定應該是多久整個結果視訊的整數。  預設取決於原始視訊的持續時間。


下表將說明預設期間，不使用**maxMotionThumbnailInSecs**時。

||||
---|---|---|---|---
視訊的持續時間|d < 3 個最小值|3 min < d < 15 分鐘|15 分鐘 < d < 30 分鐘| 30 分鐘 < d
縮圖的持續時間|15 秒 （2-3 場景）| 30 秒 （3-5 場景）|60 秒 （5 到 10 場景）|90 秒 （10-15 場景）


下列 JSON 設定可用的參數。
    
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>程式碼範例

下列程式顯示的方式︰

1. 建立資產，並將媒體檔案上傳到資產。
1. 建立工作與視訊的縮圖工作，根據包含下列 json 預設格式的設定檔。 
        
        {               
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }

1. 下載輸出檔案。 

###<a name="net-code"></a>.NET 程式碼
     
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;
    
    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            static void Main(string[] args)
            {
    
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    

                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");
    
                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }
    
            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);
    
                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");
    
                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";
    
                var processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);
    
                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);
    
                // Specify the input asset.
                task.InputAssets.Add(asset);
    
                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
    
                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
                // Launch the job.
                job.Submit();
    
                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    
                progressJobTask.Wait();
    
                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }
    
                return job.OutputMediaAssets[0];
            }
    
            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);
    
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);
    
                return asset;
            }
    
            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }
    
            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();
    
                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));
    
                return processor;
            }
    
            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
    
                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
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
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }
    
        }
    }

###<a name="video-thumbnail-output"></a>視訊的縮圖輸出

[視訊的縮圖輸出](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>相關的連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)