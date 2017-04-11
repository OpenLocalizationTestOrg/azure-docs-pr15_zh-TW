<properties
    pageTitle="Azure 媒體重新 2 預覽的媒體檔案編製索引作業 |Microsoft Azure"
    description="Azure 媒體重新可讓您以供搜尋的媒體檔案的內容，並產生隱藏式輔助字幕和關鍵字的全文檢索字幕。 本主題說明如何使用 [媒體重新 2 預覽]。"
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/26/2016" 
    ms.author="adsolank;juliako;"/>


# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Azure 媒體重新 2 預覽編製索引的媒體檔案

##<a name="overview"></a>概觀

**Azure 媒體重新 2 預覽**媒體處理器 (MP) 可讓您進行搜尋，媒體檔案和內容，以及產生關閉隱藏式輔助字幕追蹤。 相較於舊版[Azure 媒體索引](media-services-index-content.md)， **Azure 媒體重新 2 預覽**執行更快速地編製索引作業，並提供更廣泛的語言支援。 支援的語言包括英文、 西班牙文、 法文、 德文、 義大利文、 中文、 葡萄牙文及阿拉伯文。

**Azure 媒體重新 2 預覽**MP 目前是在預覽中。

本主題說明如何建立編製索引作業**Azure 媒體重新 2**預覽。

>[AZURE.NOTE]下列事項︰
>
>Azure china （中國） 和 Azure 政府版不支援重新 2。
>
>預覽限於 ~ 10 分鐘的處理，但就可以使用所有的客戶。
>
>當編製索引的內容，請確定使用很清楚語音 （不含背景音樂、 雜訊、 效果或麥克風 hiss） 的媒體檔案。 部分範例適當的內容是︰ 錄製會議、 課堂上或簡報。 下列內容可能不適用於編製索引作業︰ 影片，電視節目，任何與混合的音訊聲音效果不佳錄製與背景噪音 (hiss) 的內容。


本主題提供關於**Azure 媒體重新 2 預覽**的詳細資訊，並示範如何使用.NET 媒體服務 SDK

##<a name="input-and-output-files"></a>輸入與輸出檔案

###<a name="input-files"></a>輸入的檔案

音訊或視訊檔案

###<a name="output-files"></a>輸出檔案

編製索引作業可以產生字幕檔案，下列格式︰  

- **沙米文**
- **TTML**
- **WebVTT**

關閉標題 （副本） 以下列格式的檔案可以用來障礙人士聽力殘障人士音訊和視訊檔案。

##<a name="task-configuration-preset"></a>工作設定 （預設）

**Azure 媒體重新 2**預覽建立編製索引的工作，您必須指定設定預設格式]。

下列 JSON 設定可用的參數。

    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }

##<a name="supported-languages"></a>支援的語言  

Azure 媒體重新 2 預覽，將語音文字支援下列語言版本 （如下所示，請在任務設定，以括弧括住的使用 4 個字元代碼中指定語言名稱） 時︰

- 英文 [EnUs]
- 西班牙文 [EsEs]
- 中文 [ZhCn]
- 法文 [FrFr]
- 德文 [DeDe]
- 義大利文 [ItIt]
- 葡萄牙文 [PtBr]
- 阿拉伯文 （埃及） [ArEg]


## <a name="sample-code"></a>程式碼範例

下列程式顯示的方式︰

1. 建立資產，並將媒體檔案上傳到資產。
1. 建立工作與編製索引的任務，以包含下列 json 預設格式的設定檔。
            
        {
          "version":"1.0",
          "Features":
            [
               {
               "Options": {
                    "Formats":["WebVtt","ttml"],
                    "Language":"enUs",
                    "Type":"RecoOptions"
               },
               "Type":"SpReco"
            }]
        }

1. 下載輸出檔案。 
    
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace IndexContent
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
        
                    // Run indexing job.
                    var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                                @"C:\supportFiles\Indexer\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
                }
        
                static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Indexing Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Indexing Job");
        
                    // Get a reference to Azure Media Indexer 2 Preview.
                    string MediaProcessorName = "Azure Media Indexer 2 Preview";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Indexing Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset to be indexed.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
        
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


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## <a name="related-links"></a>相關的連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)