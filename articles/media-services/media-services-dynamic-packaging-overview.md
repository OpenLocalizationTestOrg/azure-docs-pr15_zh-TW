<properties
    pageTitle="動態包裝概觀 |Microsoft Azure"
    description="主題提供和動態包裝的概觀。"
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


# <a name="dynamic-packaging"></a>動態包裝

##<a name="overview"></a>概觀

Microsoft Azure 媒體服務可提供許多的媒體來源檔案格式，串流格式的媒體及內容保護格式至各種不同的用戶端技術 (例如，iOS XBOX Silverlight、 Windows 8)。 這些用戶端瞭解不同的通訊協定，例如 iOS 需要 HTTP Live 串流 (HLS) V4 格式和 Silverlight 和 Xbox 需要平滑串流。 如果您有彈性的位元率 （多重-位元率） 一組 MP4 （ISO 基底媒體 14496 12） 檔案或一組調整位元率平滑串流檔案，您想要了解 MPEG 破折號、 HLS 或平滑串流的用戶端支援，您應該利用媒體服務動態包裝。

使用動態包裝您只需要是建立包含一組調整位元率 MP4 檔案或調整位元率平滑串流檔案的資產。 然後，根據指定的格式，在資訊清單或片段邀請中，點播串流伺服器會確保您收到資料流中您所選擇的通訊協定。 如此一來，您只需要儲存及支付中單一儲存格式的檔案和媒體服務會建立並提供適當的回應，根據用戶端的要求。

下圖顯示的傳統編碼及靜態包裝工作流程。

![靜態編碼](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

下圖顯示動態包裝工作流程。

![動態編碼](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]若要利用動態包裝，必須先至少有一個視串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[如何比例媒體服務](media-services-portal-manage-streaming-endpoints.md)。

##<a name="common-scenario"></a>常見的案例

1. 上傳輸入的檔案 （稱為 mezzanine 檔案）。 例如，H.264、 MP4 或 WMV （適用於支援的格式，請參閱[支援的媒體 Encoder 標準的格式](media-services-media-encoder-standard-formats.md)的清單。

1. 編碼 H.264 MP4 調整位元率組 mezzanine 檔案。

1. 發佈包含調整的位元率 MP4 設定建立點播定位器資產。

1. 建立存取及串流內容串流的 Url。


##<a name="preparing-assets-for-dynamic-streaming"></a>準備資產動態串流

動態資料流時，準備您的資產，您會有兩個選項︰

1. [上傳主檔案](media-services-dotnet-upload-files.md)。
2. [使用標準 Media Encoder 編碼器產生 H.264 MP4 調整位元率集](media-services-dotnet-encode-with-media-encoder-standard.md)。
3. [資料流內容](media-services-deliver-content-overview.md)。


##<a id="unsupported_formats"></a>不支援的動態包裝的格式

動態包裝不支援下列來源檔案格式。

- Dolby 數位 mp4 檔案。
- Dolby 數位平滑檔案。

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
