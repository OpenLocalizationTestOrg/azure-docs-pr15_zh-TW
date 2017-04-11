<properties
    pageTitle="編碼資產 Azure 入口網站使用 Media Encoder 標準 |Microsoft Azure"
    description="本教學課程中會引導您編碼資產 Media Encoder 標準使用 Azure 入口網站的步驟。"
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
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>編碼資產 Azure 入口網站使用標準 Media Encoder

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

使用其中一個最常見的案例進行調整的位元率串流至您的用戶端 Azure 媒體服務。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。 若要調整的位元率串流準備您的視訊，您需要進行來源視訊編碼多重位元率檔案。 您應該使用**Media Encoder 標準**encoder 您的視訊。  

媒體服務也會提供可讓您在下列串流格式發表您多重位元率 MP4s 動態包裝︰ MPEG 破折號、 HLS、 平滑串流，或 HDS，您不必重新封裝成這些串流格式。 動態包裝您只需要儲存及支付中單一儲存格式的檔案與媒體服務會建立並提供適當的回應，根據用戶端的要求。

若要充分利用動態包裝，您需要執行下列動作︰

- 將您的來源檔案編碼成一組多重位元率 MP4 檔案 （編碼的步驟會示範稍後此區段中）。
- 至少有一個串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[設定串流結束點](media-services-portal-vod-get-started.md#configure-streaming-endpoints)。 

若要縮放媒體處理，請參閱[本](media-services-portal-scale-media-processing.md)主題。

## <a name="encode-with-the-azure-portal"></a>使用 [Azure 入口網站

本節說明編碼與媒體 Encoder 標準內容時可採取的步驟。

1.  在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2.  在 [**設定**] 視窗中，選取 [**資產**]。  
2.  在 [**資產**] 視窗中，選取您想要編碼的資產。
3.  按下 [**編碼**] 按鈕。
4.  在 [**編碼資產**] 視窗中，選取 「 Media Encoder 標準 」 處理器及預設格式]。 例如，如果您知道您輸入的視訊具有 1920 x 1080 像素的解析度，然後您可以使用 「 H264 多個位元率 1080p 「 預設。 如需有關預設格式]，請參閱[這](https://msdn.microsoft.com/library/azure/mt269960.aspx)篇文章 – 請務必選取最適合您輸入視訊的預設格式。 如果您有低解析度 (640x360) 視訊，然後您應該不使用預設的 「 H264 多個位元率 1080p 「 預設。
    
    更容易管理，您可以編輯輸出資產的名稱及工作的名稱。
        
    ![編碼資產](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. 按下**建立**。


##<a name="next-step"></a>下一步

[本文](media-services-portal-check-job-progress.md)所述，您可以監視編碼 Azure 入口網站中，使用的工作進度。  

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


