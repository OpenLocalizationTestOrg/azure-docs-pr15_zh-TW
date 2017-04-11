<properties
    pageTitle="面對 redaction 與 Azure 媒體分析 |Microsoft Azure"
    description="本文將示範如何 redact 面與 Azure 媒體分析。"
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
    ms.date="09/12/2016"   
    ms.author="juliako;"/>
 
#<a name="face-redaction-with-azure-media-analytics"></a>美元 redaction 與 Azure 媒體狀況分析

##<a name="overview"></a>概觀

**Azure 媒體 Redactor**是提供可調整美元 redaction 雲端中的[Azure 媒體分析](media-services-analytics-overview.md)媒體處理器 (MP)。 美元 redaction 可讓您以模糊面所選的個人的修改您的視訊。 若要在公用安全和新聞媒體的情況下使用美元 redaction 服務。 幾分鐘的資料包含多個面可能需要小時，以手動方式，redact 但美元 redaction 程序會使用這項服務要求只需要幾個簡單的步驟。 如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/azure-media-redactor/)部落格。

本主題提供**Azure 媒體 Redactor**的詳細資料，並說明如何使用.NET 媒體服務 SDK。

**Azure 媒體 Redactor** MP 目前是在預覽中。

## <a name="face-redaction-modes"></a>美元 redaction 模式

臉部 redaction 的運作方式偵測面視訊的每個畫面中，以及追蹤美元物件兩個轉寄與向後移時間，使相同的個別可以從其他角度以及模糊難辨。 自動化的 redaction 程序很複雜，而且會不一定農產品 100%的所需的輸出，因此媒體分析可讓您只需要幾種方法可以修改最終輸出。

除了 [完全自動模式中，有兩階段工作流程可讓選取/解除-selection 的找到面透過識別碼的清單。 此外，讓每個框架調整 MP 任意使用 JSON 格式的中繼資料檔案。 此工作流程分成**分析**] 和 [ **Redact**模式。 您可以結合在一段的一項工作; 中執行這兩項工作的兩種模式此模式下稱為**組合**。

###<a name="combined-mode"></a>合併的模式

這會自動產生 redacted 的 mp4 沒有任何手動輸入。

階段|檔案名稱|備忘稿
---|---|---
輸入的資產|foo.bar|WMV、 MOV 或 MP4 格式的視訊
輸入的設定|預設工作設定|{[版本]: 「 1.0]、 [選項]: {[模式]: 「 合併 」}}
輸出資產|foo_redacted.mp4|模糊套用視訊

####<a name="input-example"></a>輸入的範例︰

[檢視此影片](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

####<a name="output-example"></a>輸出範例︰

[檢視此影片](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

###<a name="analyze-mode"></a>分析模式

兩階段工作流程的**分析**傳遞接受視訊輸入，並產生 JSON 檔案的美元位置和 jpg 影像的每個偵測到的圖示。

階段|檔案名稱|備忘稿
---|---|----
輸入的資產|foo.bar|WMV、 MPV 或 MP4 格式的視訊
輸入的設定|預設工作設定|{[版本]: 「 1.0]、 [選項]: {[模式]: 「 分析'}}
輸出資產|foo_annotations.json|註釋的美元位置 JSON 格式的資料。 這可以編輯使用者修改模糊週框方塊。 請參閱下面的範例。
輸出資產|foo_thumb%06d.jpg [foo_thumb000001.jpg，foo_thumb000002.jpg]|每個裁剪的 jpg 偵測到的位置的數字會指出正面的 labelId 臉

####<a name="output-example"></a>輸出範例︰

    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… 截斷


###<a name="redact-mode"></a>Redact 模式

工作流程的第二階段所需的時間必須結合為單一的資產的輸入較大的數目。

這包含識別碼模糊、 原始的視訊和註釋 JSON 的清單。 此模式下使用註釋套用模糊畫面上輸入視訊。

分析行程的輸出不包含原始的視訊。 視訊需要上傳到 Redact 模式工作輸入資產和選取作為主要的檔案。

階段|檔案名稱|備忘稿
---|---|---
輸入的資產|foo.bar|視訊 WMV、 MPV 或 MP4 格式。 相同步驟 1 視訊。
輸入的資產|foo_annotations.json|註釋與選用的修改，階段中繼資料檔案。
輸入的資產|foo_IDList.txt （選用）|選用的新行分隔美元識別碼 redact 的清單。 如果是空白的此模糊了所有面。
輸入的設定|預設工作設定|{[版本]: 「 1.0]、 [選項]: {[模式]: 'redact'}}
輸出資產|foo_redacted.mp4|視訊模糊套用根據註釋

####<a name="example-output"></a>範例輸出

這是一個選取的識別碼 IDList 的輸出。

[檢視此影片](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

##<a name="attribute-descriptions"></a>屬性描述

Redaction MP 提供高的精確度美元位置偵測並追蹤，可以偵測最多 64 人力面中的視訊畫面。 正面面提供最佳的結果，端面時和小型 （小於或等於 24 x 24 像素為單位） 面挑戰。

偵測到以及追蹤面會傳回與座標表示的表面，以及美元位置指出其追蹤的識別碼。 美元識別碼的數字容易時遺失或重疊的圖文框中，在正面美元情況下重設快速指定多個識別碼某些個人結果。

屬性的詳細說明，請參閱[偵測表面和 Azure 媒體分析與情緒](media-services-face-and-emotion-detection.md)的主題。

## <a name="sample-code"></a>程式碼範例

下列程式顯示的方式︰

1. 建立資產，並將媒體檔案上傳到資產。
1. 建立工作與美元 redaction 工作根據包含下列 json 預設格式的設定檔。 
                    
        {'version':'1.0', 'options': {'mode':'combined'}}

1. 下載輸出 JSON 檔案。 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceRedaction
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
        
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
        
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
        
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
        
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


##<a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-links"></a>相關的連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
