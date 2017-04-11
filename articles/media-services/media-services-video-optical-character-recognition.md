<properties
    pageTitle="若要將視訊檔案中的文字內容轉換成數位文字使用 Azure 媒體分析 |Microsoft Azure"
    description="Azure 媒體分析 OCR （光學字元辨識） 可讓您在視訊檔案中的文字內容轉換為可編輯、 可搜尋數位的文字。  這個選項可讓您可以自動化的有意義的中繼資料擷取，從您的媒體的視訊信號。"
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
    ms.author="juliako"/>
 
#<a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>若要將視訊檔案中的文字內容轉換成數位文字使用 Azure 媒體狀況分析 

##<a name="overview"></a>概觀

如果您要從您的視訊檔案中擷取文字內容，並產生一個可編輯、 可搜尋的數位文字時，您應該使用 Azure 媒體分析 OCR （光學字元辨識）。 此 Azure 媒體處理器偵測到您的視訊檔案中的文字內容，並產生供您使用的文字檔案。 OCR 可讓您自動化的有意義的中繼資料擷取，從視訊訊號的媒體。

當搭配使用搜尋引擎，您就可以輕鬆地索引的文字，您的媒體，並強化您的內容的可探索能力。 這是在高文字影片中，例如視訊錄製或投影片放映簡報的螢幕擷取非常有用。Azure OCR 媒體處理器最適合數位的文字。

**Azure 媒體 OCR**媒體處理器目前正在預覽。

本主題提供**Azure 媒體 OCR**的詳細資料，並說明如何使用.NET 媒體服務 SDK。 如需詳細資訊與範例，請參閱[此部落格](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/)。

##<a name="ocr-input-files"></a>OCR 輸入的檔案

視訊檔案。 目前支援下列格式︰ MP4、 MOV 及 WMV。

##<a name="task-configuration"></a>工作設定 

工作設定 （預設）。 **Azure 媒體**ocr 建立工作，您必須指定預設使用 JSON 或 XML 的設定。 

###<a name="attribute-descriptions"></a>屬性描述

屬性名稱|描述
---|---
語言|（選擇性） 將告訴您要尋找的文字的語言。 下列其中一項︰ 自動偵測 （預設）、 阿拉伯文、 ChineseSimplified、 ChineseTraditional、 捷克丹麥文、 荷蘭文、 英文、 芬蘭文、 法文、 德文、 希臘文、 匈牙利文、 義大利文、 日文、 韓文、 挪威文、 波蘭文、 葡萄牙文、 羅馬尼亞文、 俄文、 SerbianCyrillic、 SerbianLatin、 斯洛伐克文、 西班牙文、 瑞典文、 土耳其文。
TextOrientation|（選擇性） 將告訴您要尋找的文字方向。  「 左 」 表示頂端的所有字母指向左。  預設文字 （例如，就可以在活頁簿中找到） 可以被稱為 「 設定 」 方向。  下列其中一項︰ 自動偵測 （預設）、 向上鍵、 向右，向下、 左移。
TimeInterval|（選擇性） 將說明取樣率。  預設值為每個 1/2 第二個。<br/>JSON 格式 – ss。SSS (預設 00:00:00.500)<br/>XML 格式 – W3C XSD 工期基本項目 （預設值 PT0.5）
DetectRegions|（選用）物件的陣列，DetectRegion 指定要偵測文字視訊的範圍內的區域。<br/>DetectRegion 物件是由下列四個整數值︰<br/>向左 – 左邊界的像素<br/>上-從上邊界的像素<br/>寬度-區域像素為單位的寬度<br/>高度-區域像素為單位的高度

####<a name="json-preset-example"></a>JSON 預設的範例
        
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

####<a name="xml-preset-example"></a>XML 預設的範例

    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

##<a name="ocr-output-files"></a>OCR 輸出檔案

輸出的 OCR 媒體處理器是 JSON 檔案。

###<a name="elements-of-the-output-json-file"></a>輸出 JSON 檔案的元素

視訊 OCR 輸出提供在視訊中找到的字元時間分割的資料。  您可以使用屬性，例如語言或方向 hone 集完全您感興趣的分析的字。 

輸出包含下列屬性︰

項目|描述
---|---
時幅|「 刻度 「 每秒的視訊
位移|時間位移時間戳記。 在 1.0 版的影片 Api，一律會是 0。
畫面播放速率|秒的視訊畫面
寬度|視訊像素為單位的寬度
高度|視訊像素為單位的高度
片段|以時間為基礎的區塊，視訊的中繼資料區塊的陣列
開始|在 「 刻度 」 片段的開始時間
持續時間|在 「 刻度 」 片段的長度
間隔|間隔的指定片段內每項事件
事件|陣列，其中包含區域
區域|物件代表偵測到的單字或片語
語言|偵測到區域內的文字使用的語言
方向|偵測到區域內的文字方向
線條|偵測到區域內的文字行的陣列
文字|實際的文字

###<a name="json-output-example"></a>JSON 輸出範例

下列範例中，輸出包含視訊的一般資訊與多個視訊片段。 在每個視訊片段，它包含每個區域而以語言偵測到的 OCR MP 和其文字的方向。 地區也包含線條的文字、 線條的位置與每個 word 中的資訊 （word 內容與位置 confidence） 這一行此區域中的每個 word 線條。 下列範例中，而使一些註解內嵌。

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>程式碼範例

下列程式顯示的方式︰

1. 建立資產，並將媒體檔案上傳到資產。
1. 使用 OCR 設定/預設檔案建立工作。
1. 下載輸出 JSON 檔案。 
         
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
        
        namespace OCR
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
        
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
        
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
        
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
        
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
        
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
        
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
        
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
        
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
        
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
        
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
        
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
