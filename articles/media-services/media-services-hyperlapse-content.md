<properties
    pageTitle="Hyperlapse 媒體檔案與 Azure 媒體 Hyperlapse |Microsoft Azure"
    description="Azure 媒體 Hyperlapse 會從第一個人員或動作相機內容建立平滑的時間屆視訊。 本主題說明如何使用媒體索引。"
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Azure 媒體 Hyperlapse Hyperlapse 媒體檔案

Azure 媒體 Hyperlapse 是媒體處理器 (MP) 會從第一個人員或動作相機內容建立平滑的時間屆視訊。  雲端同層級[Microsoft Research 桌面 Hyperlapse Pro 和電話型 Hyperlapse Mobile](http://aka.ms/hyperlapse)，Microsoft Hyperlapse 的 Azure 媒體服務使用大量的縮放比例 Azure 媒體服務媒體處理平台的水平不按比例縮放及平行大量 Hyperlapse 處理。

>[AZURE.IMPORTANT]Microsoft Hyperlapse 是設計用來搭配最適合在第一人稱內容移動攝影機。  雖然仍相機資料仍然可以運作，效能與品質 Azure 媒體 Hyperlapse 媒體處理器不一定能針對其他類型的內容。  若要進一步瞭解 Microsoft Hyperlapse Azure 媒體服務，請參閱部分範例影片，請查看[簡介部落格文章](http://aka.ms/azurehyperlapseblog)從公用預覽。

工作會採用做為 Azure 媒體 Hyperlapse 輸入 MP4、 MOV 或 WMV 資產檔案以及指定視訊的畫面應該時間屆的設定檔，哪些速度 （例如︰ 第一個 10000 框架 2 x）。  輸出是輸入視訊的 stabilized 和時間屆轉譯。

Azure 媒體 Hyperlapse 最新的更新，請參閱[媒體服務部落格](https://azure.microsoft.com/blog/topics/media-services/)。

## <a name="hyperlapse-an-asset"></a>Hyperlapse 資產

首先，您必須您所要的輸入的檔案上傳至 Azure 媒體服務。  若要進一步瞭解上傳及管理內容的概念，請閱讀[內容管理文件](media-services-portal-vod-get-started.md)。

###  <a id="configuration"></a>設定預設的 Hyperlapse

在您的媒體服務帳戶內容後，您必須建構您設定預設格式]。  下表說明使用者定義欄位︰

 功能變數 | 描述
-------|-------------
StartFrame|Microsoft Hyperlapse 處理應開始結合框架。
NumFrames|處理框架數目
速度|若要加快輸入視訊的因素。

以下是 XML 和 JSON 標準設定檔的範例︰

**XML 預設格式︰**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON 預設格式︰**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse 與反飛彈系統.NET SDK

下列方法上傳的媒體檔案以資產，而且 Azure 媒體 Hyperlapse 媒體處理器建立工作。

> [AZURE.NOTE] 您應該已經 CloudMediaContext 中要使用這個程式碼的名稱 」 內容 」 的範圍。  若要深入瞭解此問題，請閱讀[內容管理文件](media-services-dotnet-get-started.md)。

> [AZURE.NOTE] 字串引數 」 hyperConfig 」 預期上所述，在 JSON 或 XML 預設標準設定。

靜態 bool RunHyperlapseJob （字串輸入、 字串輸出字串 hyperConfig） {/ / 輸入的檔案 IAsset 資產建立資產 = 內容。資產。CreateAssetAndUploadSingleFile （輸入，「 我輸入 Hyperlapse 」，AssetCreationOptions.None）;

抓取 Azure 媒體 Hyperlapse MP IMediaProcessor mp 的執行個體 = 內容。MediaProcessors。GetLatestMediaProcessorByName (「 Azure 媒體 Hyperlapse 」)。

建立工作與 Hyperlapse 工作 IJob 工作 = 內容。工作。建立 (a0 （「 Hyperlapse {0} 」，輸入）)。

如果 (String.IsNullOrEmpty(hyperConfig)) {/ / 設定不能是空白的傳回 false;}

hyperConfig = File.ReadAllText(hyperConfig);

ITask hyperlapseTask = 作業。Tasks.AddNew （「 Hyperlapse 工作 」 mp、 hyperConfig，TaskOptions.None）;hyperlapseTask.InputAssets.Add(asset);hyperlapseTask.OutputAssets.AddNew （「 Hyperlapse 輸出 」，AssetCreationOptions.None）;


工作。Submit();

建立進行列印和查詢工作任務 progressPrintTask = 新 Task(() = > {

IJob jobQuery = null。執行 {var progressContext = 內容; jobQuery = progressContext.Jobs。位置 (j = > j.Id = = 工作。識別碼）。First （);Console.WriteLine （字串。格式 (「 {0} \t {1} \t {2} 」，DateTime.Now jobQuery.State、 jobQuery.Tasks[0]。進度））。Thread.Sleep(10000);} 時 (jobQuery.State ！ = JobState.Finished & & jobQuery.State ！ = JobState.Error & & jobQuery.State ！ = JobState.Canceled);});progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>支援的檔案類型

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>相關的連結

[Azure 媒體服務分析概觀](media-services-analytics-overview.md)

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
