<properties 
    pageTitle="如何建立媒體處理器 |Microsoft Azure" 
    description="瞭解如何建立媒體處理器元件編碼、 轉換格式、 加密或解密媒體內容的 Azure 媒體服務。 程式碼範例以 C# 撰寫，並使用.NET 媒體服務 SDK。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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


#<a name="how-to-get-a-media-processor-instance"></a>如何︰ 取得媒體處理器執行個體

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [其餘](media-services-rest-get-media-processor.md)


##<a name="overview"></a>概觀

在媒體時的處理器，處理特定處理的工作，例如編碼，元件的媒體服務格式轉換加密或解密媒體內容。 當您建立任務編碼、 加密或轉換媒體內容的格式，通常會建立媒體處理器。

下表提供的名稱和描述的每一個可用的媒體處理器。

媒體處理器名稱|描述|詳細資訊
---|---|---
媒體 Encoder 標準|提供視編碼標準的功能。 |[概觀與 Azure Demand 媒體編碼器上的比較](media-services-encode-asset.md)
媒體 Encoder 進階版工作流程|可讓您執行編碼使用媒體 Encoder 進階版工作流程的工作。|[概觀與 Azure Demand 媒體編碼器上的比較](media-services-encode-asset.md)
Azure 媒體索引| 可讓您進行搜尋，媒體檔案和內容，以及產生關閉隱藏式輔助字幕追蹤] 和 [關鍵字。|[Azure 媒體索引](media-services-index-content.md)
Azure 媒體 Hyperlapse （預覽版本）|可讓您平滑出 「 碰撞 」 中使用視訊穩定視訊。 也可讓您充分設定內容到消耗美工圖案。|[Azure 媒體 Hyperlapse](media-services-hyperlapse-content.md)
Azure 媒體 Encoder|使用年限
儲存解密| 使用年限|
Azure 媒體封裝程式|使用年限|
Azure 媒體加密子|使用年限|

##<a name="get-media-processor"></a>取得媒體處理器

下列方法會顯示如何取得媒體處理器執行個體。 程式碼假設一個名為**_context**參考伺服器內容] 區段中所述的模組層級變數使用[如何︰ 連線至以程式設計方式媒體服務](media-services-dotnet-connect-programmatically.md)。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
        return processor;
    }


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-steps"></a>後續步驟

現在，您知道如何取得媒體處理器執行個體，請移至[如何編碼資產](media-services-dotnet-encode-with-media-encoder-standard.md)主題會顯示您如何使用 Media Encoder 標準編碼資產。


