<properties
    pageTitle="Azure 媒體服務分析概觀 |Microsoft Azure"
    description="Azure 媒體服務可提供公用預覽的 Azure 媒體分析，在企業、 法規遵循、 安全性和到的語音及電腦願景服務的集合。 Azure 媒體分析服務可利用核心 Azure 媒體服務平台元件，因此可處理處理在上一天的媒體。 "
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
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Azure 媒體服務分析概觀

##<a name="overview"></a>概觀

更多組織與企業會為訓練員工、 客戶與文件商務功能加入慣用中運用視訊。 雲端運算可讓您更有效率來儲存、 串流和存取這些大型的媒體檔案，但是當公司放大其視訊的內容文件庫，必須是平均有效方法從視訊建立更有意義，解壓縮新的深入見解個人化與他們的對象以及其企業進階用法。

若要解決此越來越需要在 marketplace，Azure 媒體服務會提供媒體分析，會使其更輕鬆地組織及衍生當中的深入見解從其視訊檔案的企業語音和願景元件 （在企業、 法規遵循、 安全性和到） 的集合。 Azure 媒體分析服務可利用核心 Azure 媒體服務平台元件，因此可處理處理在上一天的媒體。

Azure 媒體分析讓開發人員快速入門提供了一些功能，在受限制的視訊，並將此應用程式到進階功能。 使用完整的小數位數、 法規遵循、 安全性和到所需的大型組織的企業環境的內建 azure 媒體分析。

下圖顯示**媒體分析**及其他媒體服務平台的主要部分。 

![VoD 工作流程](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

媒體分析媒體處理器產生 MP4 檔案或 JSON 檔案。 如果媒體處理器所產生的 MP4 檔案，您可以逐步下載檔案。 如果媒體處理器產生 JSON 檔案，您可以下載檔案從 Azure blob 儲存體。 

## <a name="azure-media-analytics-services"></a>Azure 媒體分析服務

- **重新**– Azure 媒體重新可讓您，讓內容搜尋，以及產生關閉隱藏式輔助字幕追蹤。 Azure 媒體服務發行**Azure 媒體重新 2 Preview**的速度更快編製索引及狀況提出更廣義的語言支援。 支援的語言包括英文、 西班牙文、 法文、 德文、 義大利文、 中文、 葡萄牙文及阿拉伯文。 如需詳細的資訊與範例，請參閱[Azure 媒體重新 2 的程序影片](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Microsoft Hyperlapse 是結果是 20 年的電腦願景研究在 Microsoft 研究 (MSR)，結合視訊穩定和時間 lapsing 若要從長表單內容建立快速、 消耗美觀的影片。 除了建立時間漏洞，您也可以使用 Hyperlapse 若要從擷取透過行動電話及攝影機 shaky 視訊建立穩定的影片。 如需詳細的資訊與範例，請參閱[Azure 媒體 Hyperlapse Hyperlapse 媒體檔案](media-services-hyperlapse-content.md)
 
- **移動偵測**– 您可以使用這項服務偵測信箋背景的視訊中的影片。 這是理想的誤判移動事件監視視訊摘要監控相機所偵測到想要檢查的客戶。 如需詳細的資訊與範例，請參閱[移動偵測 Azure 媒體分析](media-services-motion-detection.md)。
 
- **美元偵測和美元心情**– 使用這項服務，您可以將人員的各個面和其 「 心情 」 包括快樂、 sadness、 意外、 anger、 contempt、 擔心、 disgust 和 indifference/中性偵測。 有數種實用產業應用程式]，如下所述，包括總結及分析的人員參加事件反應。 如需詳細的資訊與範例，請參閱[美元和情緒偵測 Azure 媒體分析](media-services-face-and-emotion-detection.md)。
 
- **視訊摘要**– 影片摘要可協助您建立的較長的視訊摘要自動選取最感興趣的程式碼片段，從來源視訊。 當您想要提供的發生的事長視訊中的快速概觀，這是很有用。 如需詳細的資訊與範例，請參閱[使用 Azure 媒體視訊縮圖，即可建立視訊摘要](media-services-video-summarization.md)

- **光學字元辨識**Azure 媒體分析 OCR （光學字元辨識） 可讓您在視訊檔案中的文字內容轉換為可編輯、 可搜尋數位的文字。 這個選項可讓您可以自動化的有意義的中繼資料擷取，從您的媒體的視訊信號。
 
- **調整美元 redaction** - **Azure 媒體 Redactor**會提供可調整美元 redaction 在雲端 Azure 媒體分析 MP。 美元 redaction 可讓您以模糊面所選的個人的修改您的視訊。 若要在公用安全和新聞媒體的情況下使用美元 redaction 服務。 幾分鐘的資料包含多個面可能需要小時，以手動方式，redact 但美元 redaction 程序會使用這項服務要求只需要幾個簡單的步驟。 如需詳細資訊，請參閱[本文](media-services-face-redaction.md)。

 
## <a name="common-scenarios"></a>常見的案例

以下是幾個案例 Azure 媒體分析可協助組織及企業產業搜集從視訊建立更多個人化的對象和員工的互動，新的深入見解位置，以及更有效率地管理大量視訊內容︰

- **通話中心**– Even 社交媒體、 客戶通話中心仍利於進行大量的客戶服務交易推出。 此音訊的資料中編碼是豐富的客戶，您可以改善產品指南和也訓練通話中心員工，以獲得更高的客戶滿意度分析的相關資訊。 透過 Azure 媒體索引，客戶可擷取文字與建立的搜尋索引和儀表板擷取周圍最常用的智慧抱怨，來源抱怨和其他這類相關的資料。

- **使用者會產生內容管制**– 從監督者部門新聞媒體插座許多組織有公用具入口網站，對方接受 UGC 媒體，例如視訊與圖像的位置。 內容的數量可以特殊因為非預期的事件。 在下列情況下，它是靠近無法進行查看其適的內容有效手動檢查項目。 客戶可以依賴內容管制服務，將焦點放在適當的內容。

- **監視**-IP 相機成長就分裂的監視影片。 手動檢閱監視影片是使用大量，容易人力錯誤的時間。 Azure 媒體分析提供幾個元件，例如移動偵測、 美元偵測及 Hyperlapse 進行檢閱、 管理及建立衍生的程序。

## <a name="media-services-analytics-media-processors"></a>媒體服務分析媒體處理器 

本節會列出所有媒體服務分析媒體處理器 (MP) 並會顯示如何使用.NET 或其餘取得 MP 物件。

### <a name="mp-names"></a>MP 名稱


- Azure 媒體重新 2 預覽
- Azure 媒體索引
- Azure 媒體 Hyperlapse
- Azure 媒體美元偵測器
- Azure 媒體動作偵測器
- Azure 媒體視訊的縮圖
- Azure 媒體 OCR

### <a name="net"></a>.NET

下列函數會使用其中一個指定 MP 名稱，然後傳回 MP 物件。

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


## <a name="rest"></a>其餘

要求︰

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
回應︰
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>示範

[Azure 媒體分析示範](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>後續步驟

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>相關的文章

[媒體服務分析公告](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
