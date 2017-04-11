<properties
    pageTitle="偵測與 Azure 媒體分析球體 |Microsoft Azure"
    description="Azure 媒體動作偵測器媒體處理器 (MP) 可讓您以有效率地識別有興趣，否則長和較為簡單的影片中的章節。"
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
    ms.date="10/10/2016"  
    ms.author="milanga;juliako;"/>
 
# <a name="detect-motions-with-azure-media-analytics"></a>偵測與 Azure 媒體分析的動作

##<a name="overview"></a>概觀

**Azure 媒體動作偵測器**媒體處理器 (MP) 可讓您以有效率地識別有興趣，否則長和較為簡單的影片中的章節。 移動偵測可以用於靜態相機資料出移動發生的視訊部分。 它會產生 JSON 檔案包含時間戳記與發生事件的週地區中繼資料。

衝安全性視訊的摘要，這項技術就能將移動分類成相關事件和誤判光源變更陰影等。 這可讓您從相機摘要產生的安全性警告，而不垃圾無限不相關的事件，信件時無法擷取感興趣的時間太長監視視訊。

**Azure 媒體動作偵測器**MP 目前是在預覽中。

本主題提供**Azure 媒體動作偵測器**的詳細資料，並示範如何使用.NET 媒體服務 SDK


##<a name="motion-detector-input-files"></a>移動偵測輸入的檔案

視訊檔案。 目前支援下列格式︰ MP4、 MOV 及 WMV。

##<a name="task-configuration-preset"></a>工作設定 （預設）

**Azure 媒體動作偵測器**建立工作，您必須指定設定預設格式]。 

###<a name="parameters"></a>參數

您可以使用下列參數︰

名稱|選項|描述|預設值
---|---|---|---
sensitivityLevel|字串: '低 」、 「 中 」，「 高 」|在報告的動作設定敏感度層級。 調整此調整誤判數量。|' 中 '
frameSamplingValue|正整數|設定執行何種演算法頻率。 1 等於每個畫面，2 表示每個第 2 圖文框]，依此類推。|1
detectLightChange|布林值: [true]、 [false]|設定是否要在結果中報告精簡的變更|' False'
mergeTimeThreshold|X-時間︰ Ss<br/>範例︰ 00:00:03|指定的時間視窗之間移動事件結合和報告 1 為 2 的事件。|00:00:00
detectionZones|偵測區域的陣列︰<br/>偵測區域是 3 個或多個點的陣列<br/>點會 x 和 y 座標從 0 到 1。|說明用於多邊形偵測區域的清單。<br/>使用區域識別碼，以第一個正在 'id' 為報告結果︰ 0|其中涵蓋整個圖文框的單一區域。

###<a name="json-example"></a>JSON 範例

    
    {
      'version': '1.0',
      'options': {
        'sensitivityLevel': 'medium',
        'frameSamplingValue': 1,
        'detectLightChange': 'False',
        "mergeTimeThreshold":
        '00:00:02',
        'detectionZones': [
          [
            {'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}
           ],
          [
            {'x': 0.3, 'y': 0.3},
            {'x': 0.55, 'y': 0.3},
            {'x': 0.8, 'y': 0.3},
            {'x': 0.8, 'y': 0.55},
            {'x': 0.8, 'y': 0.8},
            {'x': 0.55, 'y': 0.8},
            {'x': 0.3, 'y': 0.8},
            {'x': 0.3, 'y': 0.55}
          ]
        ]
      }
    }


##<a name="motion-detector-output-files"></a>移動偵測輸出檔案

移動偵測工作會傳回 JSON 檔案中輸出資產移動提醒及他們的類別，影片中說明。 檔案會包含時間及期間偵測到視訊中的影片的相關資訊。

物件在移動中有固定的背景視訊 （例如監視視訊） 後，移動偵測器 API 提供標記。 訓練影片偵測器，減少 false 警示，例如光源和陰影的變更。 目前的演算法的限制包含晚上願景視訊、 半透明物件和小型的物件。

###<a id="output_elements"></a>輸出 JSON 檔案的元素

>[AZURE.NOTE]在最新版本，輸出 JSON 格式已變更，可能的一些客戶代表重大變更]。

下表說明輸出 JSON 檔案的項的目。

項目|描述
---|---
版本|這是指視訊 API 的版本。 目前使用的是 2。
時幅|「 刻度 「 每秒的視訊。
位移|「 刻度 」 的時間戳記的時間位移。 在 1.0 版的影片 Api，一律會是 0。 在未來我們支援的情況下，此值可能會變更。
畫面播放速率|每秒的視訊畫面。
寬度、 高度|參照的寬度和高度像素為單位的視訊。
開始|在 「 刻度 」 開始時間戳記。
持續時間|事件，請在 「 刻度 」 的長度。
間隔|間隔的事件，請在 [刻度] 中的每個項目。
事件|每個事件片段包含偵測到的持續時間內移動。
類型|在目前的版本，這是永遠一般動作的 [2]。 此標籤可讓影片 Api 彈性分類移動在未來版本。
RegionID|如上所述，這一定會在這個版本中的 0。 此標籤可讓影片 API 的彈性，未來的版本中的不同區域中尋找移動。
區域|是指您的視訊想移動的區域。 <br/><br/>-「 識別碼 」 表示的區域區域 – 在這個版本中有一個，識別碼 0。 <br/>為 「 type 」 代表您想移動的地區的圖案。 目前支援 「 矩形 」 和 「 多邊形]。<br/> 如果您指定 「 矩形 」，地區有維度的 x，Y、 寬度和高度。 X 和 Y 座標代表 0.0 到 1.0 標準化刻度區域的左上 XY 座標。 寬度和高度代表 0.0 到 1.0 標準化刻度區域的大小。 在目前的版本，X、 Y、 寬度和高度被固定在 0，0 和 1，1。 <br/>如果您指定 「 多邊形 」，區域會有尺寸，以點為單位。 <br/>
片段|中繼資料是稱為片段的不同區段區塊設定。 每個片段包含開始、 持續時間、 間隔數字及事件。 沒有事件片段代表期間的開始時間及期間偵測到沒有移動。
括號]|每個方括弧表示事件中的一個間隔。 清空 [括弧括住該間隔表示沒有移動已偵測到的。
位置|此事件] 下的新項目會列出移動發生的位置。 這是更具體比偵測區域。

以下是 JSON 輸出範例

    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
    
    …
##<a name="limitations"></a>限制

- 支援的輸入視訊格式包含 MP4、 MOV 及 WMV。
- 移動偵測最適合固定背景的視訊。 演算法著重減少 false 警示，例如光源變更與陰影。
- 有些影片可能無法偵測因為技術的挑戰。例如晚上願景視訊、 半透明的物件，以及小型的物件。


## <a name="sample-code"></a>程式碼範例

下列程式顯示的方式︰

1. 建立資產，並將媒體檔案上傳到資產。
1. 建立工作與視訊移動偵測工作根據包含下列 json 預設格式的設定檔。 
                    
        {
          'Version': '1.0',
          'Options': {
            'SensitivityLevel': 'medium',
            'FrameSamplingValue': 1,
            'DetectLightChange': 'False',
            "MergeTimeThreshold":
            '00:00:02',
            'DetectionZones': [
              [
                {'x': 0, 'y': 0},
                {'x': 0.5, 'y': 0},
                {'x': 0, 'y': 1}
               ],
              [
                {'x': 0.3, 'y': 0.3},
                {'x': 0.55, 'y': 0.3},
                {'x': 0.8, 'y': 0.3},
                {'x': 0.8, 'y': 0.55},
                {'x': 0.8, 'y': 0.8},
                {'x': 0.55, 'y': 0.8},
                {'x': 0.3, 'y': 0.8},
                {'x': 0.3, 'y': 0.55}
              ]
            ]
          }
        }

1. 下載輸出 JSON 檔案。 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace VideoMotionDetection
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
        
                    // Run the VideoMotionDetection job.
                    var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\VideoMotionDetection\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
                }
        
                static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Video Motion Detection Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Video Motion Detection Job");
        
                    // Get a reference to Azure Media Motion Detector.
                    string MediaProcessorName = "Azure Media Motion Detector";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
        
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
[Azure 媒體服務動作偵測器部落格](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
