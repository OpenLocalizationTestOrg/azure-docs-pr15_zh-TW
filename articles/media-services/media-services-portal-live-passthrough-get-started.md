<properties 
    pageTitle="如何執行即時資料流的內部部署編碼器使用 Azure 入口網站 |Microsoft Azure" 
    description="本教學課程中會引導您建立頻道設定通過傳遞的步驟。" 
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
    ms.topic="get-started-article"
    ms.date="10/24/2016" 
    ms.author="juliako"/>


#<a name="how-to-perform-live-streaming-with-on-premise-encoders-using-the-azure-portal"></a>如何執行即時資料流的內部部署編碼器使用 Azure 入口網站

> [AZURE.SELECTOR]
- [入口網站]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [其餘]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

本教學課程中會引導您使用 Azure 入口網站**頻道**設定通過傳遞的步驟。 

##<a name="prerequisites"></a>必要條件

下列步驟，才能完成教學課程︰

- Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 
- 媒體服務帳戶。 若要建立媒體服務帳戶，請參閱[如何建立媒體服務帳戶](media-services-portal-create-account.md)。
- 網路攝影機。 例如， [Telestream Wirecast encoder](http://www.telestream.net/wirecast/overview.htm)。

強烈建議您檢閱下列文章︰

- [Azure 媒體服務 RTMP 支援和 Live 編碼器](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Live 熱氣騰騰使用 Azure 媒體服務的概觀](media-services-manage-channels-overview.md)
- [Live 與建立多重位元率資料流的內部部署編碼器串流](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>常見的即時資料流案例

下列步驟說明建立一般即時串流使用的應用程式設定的頻道通過傳遞的的工作。 本教學課程中會顯示如何建立及管理傳遞頻道與即時事件。

1. 將視訊攝影機連接到電腦。 啟動及設定輸出多重位元率 RTMP 或分散 MP4 資料流的內部部署即時編碼器。 如需詳細資訊，請參閱[Azure 媒體服務 RTMP 支援與 Live 編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
    
    建立您的頻道後，也無法執行此步驟。

1. 建立並開始傳遞頻道。
1. 擷取頻道內嵌 URL。 

    Ingest URL 即時編碼器用於將資料流傳送給該頻道。
1. 擷取的頻道預覽 URL。 

    使用此 URL 來驗證您的頻道正確所收到的即時資料流。

3. 建立即時事件/程式。 

    使用時 Azure 入口網站，建立即時事件也會建立資產。 
      
    >[AZURE.NOTE]請確定至少有一個串流串流要串流內容的端點上保留的單位。
1. 當您準備好開始串流和封存，請啟動事件/程式。
2. 您也可以即時 encoder 可以發出開始通知。 廣告插入中輸出資料流。
1. 每當您要停止串流和封存事件時，請停止事件/程式。
1. 刪除事件/程式 （與您也可以刪除資產）。     

>[AZURE.IMPORTANT] 請檢閱[的建立多重位元率資料流的內部部署編碼器的即時資料流](media-services-live-streaming-with-onprem-encoders.md)，進一步瞭解概念和內部部署編碼器與傳遞管道的即時資料流與相關的考量。

##<a name="to-view-notifications-and-errors"></a>若要檢視通知與錯誤

如果您要檢視通知和 Azure 入口網站所產生的錯誤，請按一下 [通知] 圖示。

![通知](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##<a name="configure-streaming-endpoints"></a>設定串流端點 

媒體服務會提供可讓您進行您多重位元率 MP4s 下列串流格式中的動態包裝︰ MPEG 破折號、 HLS、 平滑串流，或 HDS，您不必重新封裝將這些串流格式。 動態包裝您只需要儲存及支付中單一儲存格式的檔案與媒體服務建置，並提供適當的回應，根據用戶端的要求。

若要利用動態包裝，您需要至少有一個串流單位取得串流端點從您打算傳送您的內容。  

若要建立並變更數串流保留的單位，請執行下列動作︰

1. 在[Azure 入口網站](https://portal.azure.com/)登入。
1. 在 [**設定**] 視窗中，按一下 [**串流結束點**]。 

2. 按一下 [預設串流結束點。 

    **預設串流端點詳細資料**視窗隨即出現。

3. 若要指定串流單位數量，請將滑**串流單位**。

    ![串流單位](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. 按一下 [**儲存**] 按鈕，以儲存變更]。

    >[AZURE.NOTE]分派的任何新的單位花費 20 分鐘才能完成。
    
##<a name="create-and-start-pass-through-channels-and-events"></a>建立並開始傳遞頻道和事件

頻道是與相關聯的事件/程式，可讓您控制發佈及儲存空間的即時資料流中的區段。 頻道管理事件。 
    
您可以指定您想要保留錄製的內容程式設定**封存視窗**長度的時數。 此值可以設定 5 分鐘為最小值，最多 25 個小時。 封存視窗長度也會指出時間用戶端的最大可搜尋的時間，從目前的即時位置。 移至指定的時間量，可以執行的事件，但持續捨棄落後視窗長度的內容。 此屬性的值也會決定用戶端資訊清單可以放大多久。

每項事件是資產與相關聯。 若要發佈的事件，您必須建立相關聯的資產視需要的定位器。 具有此定位器可讓您建立的串流的 URL，您可以提供給您的用戶端。

頻道支援最多三個同時執行事件，讓您建立多個封存的相同的內送資料流。 這個選項可讓您發佈，並視需要封存事件的不同部分。 例如，您的業務需求是程式的封存 6 小時，但廣播僅最後 10 分鐘的時間。 若要這麼做，您需要建立兩個同時執行的程式。 一個程式設定為 [封存 6 小時的事件，但不是發佈程式。 其他程式設定為 [封存的 10 分鐘的時間，此程式發佈。

您不應該重複使用現有的即時事件。 不過，建立並開始新的事件，為每個事件。

啟動後即可開始串流，封存的事件。 每當您要停止串流和封存事件時，請停止程式。 

若要刪除封存的內容，停止及刪除事件，然後刪除相關聯的資產。 您無法刪除資產，如果它由事件。必須先刪除該事件。 

即使您停止及刪除事件之後，使用者就無法串流封存的內容為視視訊，為，只要您不會刪除資產。

如果您想要保留封存的內容，但不是提供資料流，刪除串流定位器。

###<a name="to-use-the-portal-to-create-a-channel"></a>若要建立新頻道使用入口網站 

本節說明如何使用 [**快速建立**的選項來建立傳遞頻道。

如需傳遞管道的詳細資訊，請參閱[Live 串流的建立多重位元率資料流的內部部署編碼器使用](media-services-live-streaming-with-onprem-encoders.md)。

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 在 [**設定**] 視窗中，按一下 [ **Live 串流**。 

    ![快速入門](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    **即時資料流**視窗隨即出現。

3. 按一下 [使用 RTMP 建立傳遞頻道的 [**快速建立**內嵌通訊協定。

    **建立新頻道**視窗隨即出現。
4. 輸入新的頻道名稱，然後按一下 [**建立**]。 

    這會傳遞頻道建立含 RTMP 內嵌通訊協定。

##<a name="create-events"></a>建立事件

1. 選取您要新增事件的頻道。
2. 按下**Live 事件**] 按鈕。

![事件](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##<a name="get-ingest-urls"></a>取得內嵌 Url

頻道建立後，您可以取得內嵌即時 encoder 會提供您的 Url。 編碼器會使用下列 Url 輸入即時資料流。

![建立](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##<a name="watch-the-event"></a>觀看事件

若要觀看事件，請按一下 Azure 入口網站中的 [**監看式**或複製串流 URL 並使用您所選擇的播放程式。 
 
![建立](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

即時事件自動取得視內容時停止轉換。

##<a name="clean-up"></a>清理

如需傳遞管道的詳細資訊，請參閱[Live 串流的建立多重位元率資料流的內部部署編碼器使用](media-services-live-streaming-with-onprem-encoders.md)。

- 只有在上的所有事件/程式頻道都已都停止時，可能會都停止頻道。  一旦您停止頻道，它不會不會造成任何費用。 當您需要重新啟動時，會有不同，因此您不需要重新設定您 encoder 內嵌 URL。
- 只有在已刪除該頻道的所有即時事件時，可以刪除頻道。

##<a name="view-archived-content"></a>封存的檢視內容

即使您停止及刪除事件之後，使用者就無法串流封存的內容為視視訊，為，只要您不會刪除資產。 您無法刪除資產，如果它由事件。必須先刪除該事件。 

若要管理您的資產，選取 [**設定**，然後按一下 [**資產**]。

![資產](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##<a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
