<properties
    pageTitle="偵測美元和 Azure 媒體分析與情緒 |Microsoft Azure"
    description="本主題示範如何偵測面和心情與 Azure 媒體分析。"
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
 
#<a name="detect-face-and-emotion-with-azure-media-analytics"></a>偵測美元和情緒與 Azure 媒體狀況分析

##<a name="overview"></a>概觀

**Azure 媒體美元偵測**媒體處理器 (MP) 可讓您計算與追蹤計時，甚至評量的對象參與及反應透過臉部的運算式。 這項服務包含兩個功能︰ 

- **美元偵測**

    美元偵測尋找，並追蹤內視訊的人力面。 多個面可以偵測到及後續追蹤為它們移動，以傳回 JSON 檔案中的時間和位置的中繼資料。 在 [追蹤] 會嘗試該人員移動在畫面上，即使對方會受到阻擋或簡要離開圖文框時，讓一致的識別碼相同的圖示。

    >[AZURE.NOTE]此服務並不會執行臉部辨識。 個別使用者離開框架或變成受到阻擋的時間太長時，會指定新的識別碼，就會傳回。

- **情緒偵測**
    
    情緒偵測是美元偵測媒體處理器，傳回上多個情感屬性的分析，從面偵測到，包括快樂、 sadness、 擔心、 anger，及其他功能的選用元件。 

**Azure 媒體美元偵測**MP 目前是在預覽中。

本主題提供**Azure 媒體美元偵測器**的詳細資料，並說明如何使用.NET 媒體服務 SDK。

##<a name="face-detector-input-files"></a>面對偵測輸入的檔案

視訊檔案。 目前支援下列格式︰ MP4、 MOV 及 WMV。

##<a name="face-detector-output-files"></a>面對偵測輸出檔案

美元偵測及追蹤 API 提供高的精確度美元位置偵測和偵測最多 64 人力面視訊中的追蹤。 正面面提供最佳的結果，端面時和小型面 （小於或等於 24 x 24 像素為單位） 可能無法以正確。

使用 （左、 上方、 寬度和高度） 座標傳回偵測到以及追蹤面表示面中像素，以及美元識別碼的數字，表示其追蹤圖像的位置。 美元識別碼的數字容易時遺失或重疊的圖文框中，在正面美元情況下重設快速指定多個識別碼某些個人結果。

###<a id="output_elements"></a>輸出 JSON 檔案的元素

美元偵測及追蹤作業，輸出結果會包含從面內 JSON 格式指定檔案的中繼資料。

美元偵測及追蹤 JSON 包含下列屬性︰

項目|描述
---|---
版本|這是指視訊 API 的版本。
時幅|「 刻度 「 每秒的視訊。
位移|這是時間戳記的時間位移。 在 1.0 版的影片 Api，一律會是 0。 在未來我們支援的情況下，此值可能會變更。
畫面播放速率|每秒的視訊畫面。
片段|中繼資料是稱為片段的不同區段區塊設定。 每個片段包含開始、 持續時間、 間隔數字及事件。
開始|在 「 刻度 」 中的第一個事件的開始時間。
持續時間|片段，在 「 刻度 」 的長度。
間隔|間隔的片段，在 「 刻度] 內的每一個事件項目。
事件|每項事件包含面偵測並追蹤內的持續時間。 事件的陣列的陣列，則。 外部陣列代表一個時間間隔。 內部陣列包含 0 或發生點在時間中的其他事件。 空白的括號 [表示沒有面未偵測到。
識別碼| 正在追蹤的是正面的識別碼。 如果圖示會變成無法偵測，可能會無意間變更這個數字。 指定的個別應有相同的識別碼整個整體的視訊，但這不一定能由於偵測演算法 （閉塞等） 中的限制
X Y|左上方 X 和 Y 座標的正面週框 0.0 到 1.0 的常態化的小數位數] 方塊。 <br/>X 和 Y 座標是相對一律橫向，因此如果您有直向視訊 （或上下，若是 iOS），您將需要會相應地轉置座標。
寬度、 高度|寬度和高度美元週的標準化 0.0 到 1.0 的小數位數] 方塊。
facesDetected|這找到 JSON 結果的結尾，摘要列出的視訊演算法偵測到的表面數目。 因為識別碼可以重設不小心如果圖示會變成無法偵測 （例如美元進入關閉畫面，看起來離開），這個數字可能不會永遠等於視訊中的表面，則為 true 的數字。

美元偵測器會使用的片段 （位置可以中斷中繼資料，以時間為基礎的區塊中和您可以下載只所需），及分割技巧 （位置事件的來中斷，以避免他們收到太大）。 一些簡單計算可協助您轉換的資料。 例如，如果事件 6300 （刻度），在入門 2997 （刻度/秒） 的時幅和畫面播放速率的 29.97 （框架/秒），然後︰

- 開始/時幅 = 2.1 （秒）
- 秒 （畫面播放速率/時幅） x = 63 圖文框

以下是擷取到 JSON 的簡單範例每美元偵測和追蹤的圖文框格式︰
    
    var faceDetectionResultJsonString = operationResult.ProcessingResult;
    var faceDetecionTracking = 
         JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##<a name="face-detection-input-and-output-example"></a>偵測輸入並輸出範例

###<a name="input-video"></a>輸入的視訊

[輸入的視訊](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>工作設定 （預設）

**Azure 媒體美元偵測器**建立工作，您必須指定設定預設格式]。 下列設定預設只適用於美元偵測。

    {"version":"1.0"}

###<a name="json-output"></a>JSON 輸出

下列範例 JSON 輸出的已被截斷。

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

##<a name="emotion-detection-input-and-output-example"></a>輸入與輸出的情緒偵測範例


###<a name="input-video"></a>輸入的視訊

[輸入的視訊](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###<a name="task-configuration-preset"></a>工作設定 （預設）

**Azure 媒體美元偵測器**建立工作，您必須指定設定預設格式]。 若要建立 JSON 根據情緒偵測指定下列設定預設。
    
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


####<a name="attribute-descriptions"></a>屬性描述

屬性名稱|描述
---|---
模式|面︰ 僅面對偵測 <br/>框架中的所有面 AggregateEmotion︰ 傳回平均情緒值。
AggregateEmotionWindowMs|如果選取 AggregateEmotion 模式，請使用。 指定視訊用來產生每一個彙總的結果，以毫秒為單位的長度。
AggregateEmotionIntervalMs|如果選取 AggregateEmotion 模式，請使用。 指定哪些頻率，以產生彙總的結果。

####<a name="aggregate-defaults"></a>彙總的預設值

以下被建議的彙總的視窗和間隔設定值。 AggregateEmotionWindowMs 應該超過 AggregateEmotionIntervalMs。

   |預設值 (s)|Max(s)|Min(s)
---|---|---|---
AggregateEmotionWindowMs|0.5|2|0.25
AggregateEmotionIntervalMs|0.5|1|0.25

###<a name="json-output"></a>JSON 輸出

JSON 輸出的彙總情緒 （截斷）︰
 
    
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


##<a name="limitations"></a>限制

- 支援的輸入視訊格式包含 MP4、 MOV 及 WMV。
- 偵測美元大小範圍是 24 x 24 至 2048 x 2048 像素為單位。 將不會偵測此範圍面。
- 每一段影片中，傳回面數目上限是 64。
- 某些面不會偵測因為技術的挑戰。例如很大美元角度 （不對-碰到），然後大型閉塞。 正面和附近正面面有最佳的結果。


## <a name="sample-code"></a>程式碼範例

下列程式顯示的方式︰

1. 建立資產，並將媒體檔案上傳到資產。
1. 建立工作與美元偵測工作根據包含下列 json 預設格式的設定檔。 
                    
        {
            "version": "1.0"
        }

1. 下載輸出 JSON 檔案。 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace FaceDetection
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
        
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
        
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
        
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
        
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

##<a name="related-links"></a>相關的連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)