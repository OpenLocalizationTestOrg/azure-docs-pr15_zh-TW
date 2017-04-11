<properties
    pageTitle=" 縮放比例串流 Azure 入口網站的結束點 |Microsoft Azure"
    description="本教學課程中會引導您縮放串流端點 Azure 入口網站的步驟。"
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


# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>不按比例縮放串流端點 Azure 入口網站

##<a name="overview"></a>概觀

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

使用其中一個最常見的案例會將透過調整的位元率串流視訊至您的用戶端 Azure 媒體服務。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

媒體服務會提供可讓您在發表您調整的位元率串流媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 只的時間，您不必儲存的每個串流格式預先封裝的版本支援的格式編碼 MP4 內容的動態包裝。

若要充分利用動態包裝，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案 （示範稍後在本教學課程編碼步驟） 編碼 mezzanine （來源） 檔案。  
- 建立至少有一個串流單位*串流端點*從您打算傳送您的內容。 下列步驟說明如何變更串流單位數量。

動態包裝您只需要儲存及支付中單一儲存格式的檔案與媒體服務會建立並提供適當的回應，根據用戶端的要求。

此外，您可以控制串流端點服務處理成長的頻寬需求來調整串流單位的容量。 建議您為生產環境中的應用程式配置一或多個刻度] 單位。 串流單位為您提供購買 200 Mbps 及其他功能的幅度哪些功能包括兩個專用的出口容量︰[動態包裝](media-services-dynamic-packaging-overview.md)、 CDN 整合，以及進階的設定。 如需詳細資訊，請參閱[Azure 入口網站管理串流的結束點](media-services-portal-manage-streaming-endpoints.md)。

## <a name="scale-streaming-endpoints"></a>不按比例縮放串流端點

若要建立並變更數串流保留的單位，請執行下列動作︰

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [**設定**] 視窗中，選取 [**串流結束點**]。
3. 按一下您想要不按比例縮放的串流端點。 
4. 移動滑桿，以指定串流單位的數字
 
![串流端點](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

下列事項︰

- 分派的任何新的資料流單位可能需要大約 20 分鐘才能完成。 
- 目前，從公式列前往串流單位為無任何正值，可以停用視的串流達一小時。
- 最多 24 小時制期間所指定的單位用於計算的成本。 定價詳細資料的相關資訊，請參閱[媒體服務價格詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。

##<a name="next-steps"></a>後續步驟

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


