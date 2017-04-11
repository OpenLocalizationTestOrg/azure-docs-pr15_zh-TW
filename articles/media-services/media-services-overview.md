<properties 
    pageTitle="Azure 媒體服務概觀與常見的案例 |Microsoft Azure" 
    description="本主題提供 Azure 媒體服務的概觀" 
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
    ms.topic="hero-article" 
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>

#<a name="azure-media-services-overview-and-common-scenarios"></a>Azure 媒體服務概觀與常見的案例

Microsoft Azure 媒體服務是可延伸雲端平台，讓開發人員建立可調整的媒體管理與傳遞的應用程式。 媒體服務為基礎 REST Api，讓您能夠安全地上傳、 儲存、 編碼及封裝視及即時資料流傳送郵件給各種用戶端 （例如，電視、 電腦上和行動裝置） 的視訊或音訊內容。

您可以建立使用完全媒體服務的端對端工作流程。 您也可以選擇要用於您的工作流程的某些部分中的第三方元件。 例如，編碼使用協力廠商 encoder。 然後上, 傳、 保護、 封裝、 發表使用媒體服務。

您可以選擇的串流 live 或發表內容，視您的內容。 本主題說明常見的案例，可將您內容[live](media-services-overview.md#live_scenarios)或[依需求](media-services-overview.md#vod_scenarios)。 主題也會連結到相關的其他主題。

## <a name="sdks-and-tools"></a>Sdk 和工具

若要建立媒體服務方案，您可以使用︰

- [媒體服務 REST API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- 可用的用戶端 Sdk 其中一項︰
- [Azure 媒體服務.NET SDK](https://github.com/Azure/azure-sdk-for-media-services)
- [Java azure SDK](https://github.com/Azure/azure-sdk-for-java)，
- [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)，
- [Node.js azure 媒體服務](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js)（這是 Node.js SDK 的非 Microsoft 版本。 它由社群維護並目前沒有 100%的涵蓋範圍反飛彈系統 Api）。
- 現有的工具︰
- [Azure 入口網站](https://portal.azure.com/)
- [Azure 媒體服務-總管](https://github.com/Azure/Azure-Media-Services-Explorer)(Azure 媒體服務總管 (AMSE) 是 Winforms / C# 應用程式在 Windows 版)

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

您可以檢視反飛彈系統學習路徑這裡︰

- [反飛彈系統 Live 串流的工作流程](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [反飛彈系統視串流工作流程](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##<a name="prerequisites"></a>必要條件

若要開始使用 Azure 媒體服務，您應該下列動作︰

3. Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com)。
2. Azure 媒體服務帳戶。 使用 Azure 入口網站、.NET 或 REST API 來建立 Azure 媒體服務帳戶。 如需詳細資訊，請參閱[建立帳戶](media-services-portal-create-account.md)。
3. （選用）設定的開發環境。 選擇 [.NET 或 REST API 的開發環境。 如需詳細資訊，請參閱[設定環境](media-services-dotnet-how-to-use.md)。

此外，瞭解如何連線以程式設計方式[連線](media-services-dotnet-connect-programmatically.md)。
4. （建議使用）配置一或多個刻度] 單位。 建議您為生產環境中的應用程式配置一或多個刻度] 單位。   如需詳細資訊，請參閱[管理串流結束點](media-services-portal-manage-streaming-endpoints.md)。

##<a name="concepts-and-overview"></a>概念，以及概觀

Azure 媒體服務的概念，請參閱[概念](media-services-concepts.md)。

介紹您所有的主要元件 Azure 媒體服務的使用方法數列，請參閱[Azure 媒體服務逐步解說教學課程](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series)。 本系列有概念的概觀，而且它使用 AMSE 工具，示範反飛彈系統的工作。 請注意，AMSE 工具 Windows 工具。 這項工具可支援大部分的一種以程式設計方式與[反飛彈系統 SDK.net](https://github.com/Azure/azure-sdk-for-media-services)、 [Java Azure SDK](https://github.com/Azure/azure-sdk-for-java)或[Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)的工作。

##<a id="vod_scenarios"></a>進行媒體視需要使用 Azure 媒體服務︰ 常見的案例與工作

本節說明常見的案例，並提供相關主題的連結。 下圖顯示中進行相關的主要部分的媒體服務平台上視需要的內容。 

![VoD 工作流程](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


###<a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>保護內容的儲存空間，並傳送中清除資料流媒體 （非加密）

1. 上傳到資產的高品質 mezzanine 檔案。
    
    建議您套用至您資產的儲存空間加密] 選項，才能在上傳和保護您的內容時，其餘的儲存空間。
 
1. 編碼的調整位元率 MP4 檔案集。 

    建議您要套用至輸出資產的儲存空間加密] 選項，來保護您在其餘的內容。
    
1. 設定資產傳遞原則 （由動態包裝）。 
    
    如果您的資產加密的儲存空間，您**必須**設定資產傳遞原則。 

1. 藉由建立視需要定位器發佈資產。

    請確定至少有一個串流串流要串流內容的端點上保留的單位。

1. 資料流發佈內容。

###<a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>保護內容的儲存空間、 發表動態加密資料流媒體  

若要使用動態加密，您必須先在您要傳送加密的內容串流串流的端點上取得至少有一個串流保留的單位。

1. 上傳到資產的高品質 mezzanine 檔案。 套用儲存加密選項資產。
1. 編碼的調整位元率 MP4 檔案集。 適用於輸出資產儲存加密選項。
1. 建立您想要在播放時動態加密資產加密內容索引鍵。
2. 設定內容的重要授權原則。
1. 設定資產傳遞原則 （由動態包裝和動態加密）。
1. 藉由建立視需要定位器發佈資產。
1. 資料流發佈內容。 

###<a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>若要從您的視訊衍生當中的深入見解使用媒體分析 

媒體分析是語音和願景元件，可讓您更輕鬆地組織及企業衍生當中的深入見解從其視訊檔案的集合。 如需詳細資訊，請參閱[Azure 媒體服務分析概觀](media-services-analytics-overview.md)。

1. 上傳到資產的高品質 mezzanine 檔案。
2. 使用其中一個下列媒體分析服務處理您的視訊︰
    
    - **重新**– [Azure 媒體重新 2 的程序影片](media-services-process-content-with-indexer2.md)
    - **Hyperlapse** – [Azure 媒體 Hyperlapse Hyperlapse 媒體檔案](media-services-hyperlapse-content.md)
    - **移動偵測**–[移動偵測 Azure 媒體分析](media-services-motion-detection.md)。
    - **美元偵測和美元心情**–[表面和情緒偵測 Azure 媒體分析](media-services-face-and-emotion-detection.md)。
    - **視訊摘要**︰[使用 Azure 媒體視訊縮圖，即可建立視訊摘要](media-services-video-summarization.md)
3. 媒體分析媒體處理器產生 MP4 檔案或 JSON 檔案。 如果媒體處理器所產生的 MP4 檔案，您可以逐步下載檔案。 如果媒體處理器產生 JSON 檔案，您可以下載檔案從 Azure blob 儲存體。 


###<a name="deliver-progressive-download"></a>發表漸進下載 

1. 上傳到資產的高品質 mezzanine 檔案。
1. 編碼到單一 MP4 檔案。
1. 藉由建立視需要或 SA 定位器發佈資產。

    如果使用視需要定位器，確定至少有一個串流串流您打算漸進下載內容的端點上保留的單位。

    如果使用 SA 定位器，從 Azure blob 儲存體下載內容。 在此情況下，您不需要有串流保留的單位。
  
1. 逐漸下載內容。

##<a id="live_scenarios"></a>進行即時 Azure 媒體服務串流事件

使用 Live 串流時通常涉及下列元件︰

- 用來廣播事件的 [相機]。
- 訊號的相機鏡頭轉換會傳送至即時串流服務的資料流即時視訊 encoder。

您也可以多個即時同步處理的時間編碼器。 針對特定的要徑 live 事件，視需要更高可用性和體驗的品質，建議採用主動-主動多餘編碼器 synchronizationto 獲得順暢的容錯移轉不流失資料的時間。
- 即時串流服務可讓您執行下列動作︰

- 內嵌即時使用各種即時串流通訊協定 （例如 RTMP 或平滑串流） 的內容
- （選擇性） 將調整位元率串流編碼您的資料流
- 預覽您的即時資料流
- 錄製並儲存 ingested 的內容才能串流處理更新版本 (Video-on-Demand)
- 經由一般串流通訊協定 （例如，MPEG 虛線平滑、 HLS、 HDS） 的內容直接給客戶，或傳送至內容傳遞網路 (CDN) 進一步分配。


**Microsoft Azure 媒體服務**反飛彈 （系統） 提供內嵌、 編碼、 預覽、 儲存及發表即時串流內容的能力。

您的內容為 [客戶] 時您的目標是以不同的裝置在不同的網路的情況下發表高品質視訊。 若要處理的品質及網路的條件，使用即時編碼器編碼您的資料流多重位元率 （自動調整位元率） 視訊資料流。  若要注意串流不同的裝置上，使用媒體服務[動態包裝](media-services-dynamic-packaging-overview.md)動態重新封裝不同的通訊協定您資料流。 媒體服務支援下列調整位元率串流技術的傳遞︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

Azure 媒體服務、**頻道**、**程式**及**StreamingEndpoints**處理所有即時串流功能包括 ingest，格式設定，DVR、 安全性、 延展性和重複。

**頻道**代表的管線處理即時串流的內容。 頻道可接收即時輸入資料流時，下列方式︰

- 內部部署住頻道設定為**通過**傳遞 encoder 傳送多重位元率**RTMP**或**平滑串流**(分散 MP4)。 **通過**傳遞時，ingested 資料流時沒有任何進一步處理通過**頻道**s。 您可以使用輸出多重位元率平滑串流的下列即時編碼器︰ 元素，Envivio，Cisco。  下列即時編碼器輸出 RTMP: Adobe Flash Live、 Telestream Wirecast 和 Tricaster 轉錄。  即時 encoder 也可以將單一位元率資料流傳送到頻道的未啟用即時編碼，但不是建議使用的。 要求時，媒體服務會提供給客戶資料流。

>[AZURE.NOTE] 使用傳遞的方法是最節省至正在執行多個事件一段長時間，然後您已經有投資在內部部署編碼器執行即時串流的方式。 請參閱[定價](/pricing/details/media-services/)詳細資料。

- 內部部署即時編碼器傳送給執行即時編碼方式和下列格式的其中一種媒體服務啟用該頻道的單一位元率串流︰ RTP (MPEG TS)、 RTMP，或平滑串流 (分散 MP4)。 頻道，然後執行即時編碼的內送至多重-位元率 （自動調整） 視訊資料流單一位元率資料流。 要求時，媒體服務會提供給客戶資料流。


###<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>使用多重位元率即時資料流接收內部部署編碼器 （通過） 的頻道

下圖顯示相關的主要部分反飛彈系統平台的**通過**工作流程中。

![即時工作流程][live-overview2]

如需詳細資訊，請參閱[使用該接收多重頻道-內部部署編碼器位元率即時資料流](media-services-live-streaming-with-onprem-encoders.md)。

###<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>使用已啟用執行即時編碼方式和 Azure 媒體服務的頻道

下圖顯示相關的主要部分反飛彈系統平台的頻道執行即時編碼方式和媒體服務分開的 Live 串流工作流程中。

![即時工作流程][live-overview1]

如需詳細資訊，請參閱[使用來執行即時編碼與 Azure 媒體服務啟用的頻道](media-services-manage-live-encoder-enabled-channels.md)。


##<a name="consuming-content"></a>使用內容

Azure 媒體服務提供的工具，您需要建立豐富的動態的用戶端包括的大部分平台版的播放程式應用程式︰ iOS 裝置、 Android 裝置、 Windows、 Windows Phone、 Xbox 和設定頁首方塊。 下列主題提供連結 Sdk，您可以使用開發自己的用戶端應用程式，都可以使用媒體服務串流媒體的播放程式架構。

[開發的視訊播放程式應用程式](media-services-develop-video-players.md)

##<a name="enabling-azure-cdn"></a>啟用 Azure 的 CDN

媒體服務支援與 Azure CDN 整合。 如何啟用 Azure CDN 的資訊，請參閱[如何管理串流端點媒體服務帳戶](media-services-portal-manage-streaming-endpoints.md)。

##<a name="scaling-a-media-services-account"></a>縮放比例媒體服務帳戶

您可以調整**媒體服務**所指定的**串流保留單位**] 及 [**編碼保留單位**您想要佈建與您的帳戶。

您也可以調整您的媒體服務帳戶以加上儲存的帳戶。 每個儲存帳戶僅限於 500 TB。 若要展開的預設限制，超出儲存空間，您可以選擇將多個儲存帳戶附加至單一媒體服務帳戶。

[本](media-services-portal-scale-streaming-endpoints.md)主題會連結到相關的主題。

##<a name="support"></a>支援

[Azure 支援](https://azure.microsoft.com/support/options/)提供 Azure，包括媒體服務的支援選項。

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="service-level-agreement-sla"></a>服務等級協定 (SLA)

- 媒體服務編碼，我們保證 99.9%的 REST API 交易的可用性。
- 我們成功會串流的服務要求] 與現有媒體內容 99.9%可用性保證時至少有一個串流單位購買。
- 針對 Live 頻道我們保證執行頻道會有外部連線至少 99.9%的時間。
- 內容保護，我們保證，我們會順利完成鍵要求至少 99.9%的時間。
- 索引器，我們會成功服務索引子任務要求以編碼保留處理的時間單位 99.9%。

如需詳細資訊，請參閱[Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 
