<properties 
    pageTitle="常見問題集 |Microsoft Azure" 
    description="常見問題集 (Faq) 的問題" 
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
    ms.date="09/19/2016" 
    ms.author="juliako"/>


#<a name="frequently-asked-questions"></a>常見問題集

##<a name="general-ams-faqs"></a>一般反飛彈系統常見問題集

問︰ 如何調整索引編製功能？

答︰ 保留的單位為相同的編碼與編製索引的工作。 如何[編碼保留刻度](media-services-scale-media-processing-overview.md)依照指示進行。 **附註**的保留單位類型不會影響重新效能。

問︰ 我上傳、 編碼，及發佈視訊。 什麼是視訊的原因不會播放當我嘗試將其串流嗎？

答︰ 其中一個最常見的原因是您不需要至少有一個保留串流單位配置串流從想要播放的端點上。  如何[串流保留刻度](media-services-portal-scale-streaming-endpoints.md)依照指示進行。

問︰ 我如何在即時資料流複合？

答︰ 複合上即時資料流時目前中不提供 Azure 媒體服務，因此您必須在電腦上預先撰寫。

問︰ 可以使用 Azure CDN Live 串流嗎？

答︰ 媒體服務支援與 Azure CDN 整合 （如需詳細資訊，請參閱[如何管理串流端點媒體服務帳戶](media-services-portal-manage-streaming-endpoints.md)）。  您可以使用 Live 串流與 cdn 到底。 Azure 媒體服務會提供平滑串流與 HLS MPEG 虛線輸出。 所有的格式使用 HTTP 傳送資料，並取得 HTTP 快取的效益。 即時資料流中實際視訊/音訊資料的除數片段及此個別片段取得快取中 cdn 到底。 僅限資料重新整理的需求是資訊清單的資料。 Cdn 到底定期重新整理資訊清單的資料。

問︰ 沒有 Azure 媒體服務支援儲存圖像嗎？

答︰ 如果您只想要儲存 JPEG 或 PNG 圖像，您應該保留在 Azure Blob 儲存體。 沒有若要將其放入您的媒體服務帳戶，除非您想要將您的視訊或音訊資產相關聯的好處。 或者您可能需要圖像作為視訊的 encoder 中的覆疊。標準媒體編碼器支援覆疊圖像上方視訊，也就是什麼它會列出 JPEG 以及 PNG 支援輸入格式。 如需詳細資訊，請參閱[建立覆疊](media-services-custom-mes-presets-with-dotnet.md#overlay)。

問︰ 如何可以我複製資產媒體服務帳戶到另一個。

A︰ 若要複製到另一個資產媒體服務帳戶使用.NET [Azure 媒體服務.NET SDK 擴充功能](https://github.com/Azure/azure-sdk-for-media-services-extensions/)存放庫中使用[IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354)擴充方法可用。 如需詳細資訊，請參閱[此](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices)論壇執行緒。

問︰ 什麼是受支援的字元檔案命名時使用反飛彈系統？

答︰ 媒體服務串流內容 (例如，http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters。) 建置 Url 時，請使用 IAssetFile.Name 屬性的值因此，不允許的百分比編碼。 [**名稱**] 屬性的值不能有下列[百分比編碼-保留字元](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)︰ !*'();:@&=+$,/?%#[]"。 此外，只能有一個 「。 」 針對檔案的副檔名。


問︰ 如何使用其餘連接？

答︰ 成功連線至 https://media.windows.net 之後，您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI[連線至媒體服務使用 REST API](media-services-rest-connect-programmatically.md)所述。 


問︰ 如何旋轉視訊編碼的程序期間。

答︰[標準媒體編碼器](media-services-dotnet-encode-with-media-encoder-standard.md)支援 90/180/270 的旋轉角度。 預設行為是 「 自動 」，它嘗試偵測 [內送的 MP4/MOV 檔案中的 [旋轉中繼資料和其賠償。 包含下列其中一個 json 預設格式定義[以下](http://msdn.microsoft.com/library/azure/mt269960.aspx)**來源**項目︰
    
    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [
    
    ...




##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
