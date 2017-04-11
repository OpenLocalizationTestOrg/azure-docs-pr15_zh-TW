<properties 
    pageTitle="如何執行即時串流 Azure 入口網站中建立多重位元率資料流時使用 Azure 媒體服務 |Microsoft Azure" 
    description="本教學課程中會引導您建立頻道接收單一位元率即時資料流，並將該編碼多重位元率串流使用 Azure 入口網站的步驟。" 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
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


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>如何執行即時串流 Azure 入口網站中建立多重位元率資料流時使用 Azure 媒體服務

> [AZURE.SELECTOR]
- [入口網站](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

本教學課程中會引導您建立**頻道**接收單一位元率即時資料流，並將該編碼多重位元率串流的步驟。

>[AZURE.NOTE]概念性相關的已啟用即時編碼頻道，請參閱[Live 串流使用 Azure 媒體服務，來建立多重位元率資料流](media-services-manage-live-encoder-enabled-channels.md)。

##<a name="common-live-streaming-scenario"></a>常見的即時資料流案例

以下是有關建立一般的即時資料流應用程式的一般步驟。

>[AZURE.NOTE] 目前即時事件的最大的建議的期間為 8 小時。 請如果您需要執行頻道的較長的時間，連絡在 Microsoft.com amslived。

1. 將視訊攝影機連接到電腦。 啟動及設定可以輸出下列通訊協定的其中一種單一位元率資料流的內部部署即時 encoder: RTMP、 平滑串流，或 RTP (MPEG TS)。 如需詳細資訊，請參閱[Azure 媒體服務 RTMP 支援與 Live 編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
    
    建立您的頻道後，也無法執行此步驟。

1. 建立並開始頻道。 

1. 擷取頻道內嵌 URL。 

    Ingest URL 即時編碼器用於將資料流傳送給該頻道。
1. 擷取的頻道預覽 URL。 

    使用此 URL 來驗證您的頻道正確所收到的即時資料流。

3. 建立事件/（的程式也會建立資產）。 
1. 發佈 （所建立的相關聯的資產視需要定位器） 的事件。  

    請確定至少有一個串流串流要串流內容的端點上保留的單位。
1. 啟動後即可開始串流，封存的事件。
2. 您也可以即時 encoder 可以發出開始通知。 廣告插入中輸出資料流。
1. 停止每當您要停止串流和封存事件的事件。
1. 刪除事件 （與您也可以刪除資產）。   

##<a name="in-this-tutorial"></a>在本教學課程

在本教學課程，Azure 入口網站用來完成下列工作︰ 

2.  設定串流結束點。
3.  建立新頻道的已啟用執行即時編碼。
1.  若要提供其 live encoder 取得內嵌的 URL。 即時 encoder 會使用此 URL 來內嵌串流到該頻道。 .
1.  建立事件/程式 （與資產）
1.  發佈資產，並取得串流 Url  
1.  播放您的內容 
2.  清除

##<a name="prerequisites"></a>必要條件

下列步驟，才能完成教學課程。

- 若要完成此教學課程中，您需要 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- 媒體服務帳戶。 若要建立媒體服務帳戶，請參閱[建立帳戶](media-services-portal-create-account.md)。
- 網路攝影機，可以傳送單一位元率即時資料流 encoder。

##<a name="configure-streaming-endpoints"></a>設定串流端點 

媒體服務會提供可讓您在下列串流格式發表您多重位元率 MP4s 動態包裝︰ MPEG 破折號、 HLS、 平滑串流，或 HDS，您不必重新封裝成這些串流格式。 動態包裝您只需要儲存及支付中單一儲存格式的檔案與媒體服務會建立並提供適當的回應，根據用戶端的要求。

若要利用動態包裝，您需要至少有一個串流單位取得串流端點從您打算傳送您的內容。  

若要建立並變更數串流保留的單位，請執行下列動作︰

1. 登入[Azure 入口網站](https://portal.azure.com/)，並選取您的反飛彈系統帳戶。
1. 在 [**設定**] 視窗中，按一下 [**串流結束點**]。 

2. 按一下 [預設串流結束點。 

    **預設串流端點詳細資料**視窗隨即出現。

3. 若要指定串流單位數量，請將滑**串流單位**。

    ![串流單位](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-streaming-units.png)

4. 按一下 [**儲存**] 按鈕，以儲存變更]。

    >[AZURE.NOTE]分派的任何新的單位花費 20 分鐘才能完成。

##<a name="create-a-channel"></a>建立頻道

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取媒體服務，然後按一下您媒體服務帳戶的名稱。
2. 選取 [ **Live 串流**]。
3. 選取 [**建立自訂**]。 這個選項可讓您建立啟用即時編碼的頻道。

    ![建立頻道](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
    
4. 按一下 [**設定**]。
    
    1.  選擇**即時編碼**頻道類型。 此類型指定您想要建立新頻道的即時編碼。 這表示內送單一位元率資料流傳送至頻道並使用指定的即時編碼器設定多重位元率串流到編碼。 如需詳細資訊，請參閱[Live 串流使用 Azure 媒體服務，來建立多重位元率資料流](media-services-manage-live-encoder-enabled-channels.md)。 按一下 [確定]。
    2. 指定頻道的名稱。
    3. 按一下畫面底部的 [確定]。
    
5. 選取 [ **Ingest** ] 索引標籤。

    1. 在此頁面上，您可以選取串流通訊協定。 **即時編碼**頻道類型是有效的通訊協定選項︰
        
        - 單一位元率 Fragmented MP4 （平滑串流）
        - 單一位元率 RTMP
        - 透過 RTP RTP (MPEG TS): Mpeg-2 傳輸串流。
        
        如需每個通訊協定的詳細說明，請參閱[Live 串流使用 Azure 媒體服務，來建立多重位元率資料流](media-services-manage-live-encoder-enabled-channels.md)。
    
        您無法變更頻道時的 [通訊協定] 選項，或其相關聯的事件/程式正在執行。 如果您需要不同的通訊協定，您應該建立個別的每個資料流通訊協定的頻道。  

    2. 您可以在 ingest 上套用 IP 限制。 
    
        您可以定義內嵌這個頻道的視訊允許的 IP 位址。 允許的 IP 位址可指定為單一的 IP 位址 (例如 「 10.0.0.1 」)、 IP 範圍使用的 IP 位址和 CIDR 子網路遮罩 (例如 「 10.0.0.1/22 」) 或使用的 IP 位址和虛線小數位數的子網路遮罩 IP 範圍 (例如 「 10.0.0.1(255.255.252.0)')。

        如果沒有 IP 位址已指定，而且沒有規則定義會不允許任何 IP 位址。 若要允許任何 IP 位址，建立規則，並設定 0.0.0.0/0。

6. 在 [**預覽**] 索引標籤上套用預覽 IP 限制。
7. 在 [**編碼**] 索引標籤中，指定編碼的預設格式。 

    目前，只系統預設您可以選取是**預設 720p**。 若要指定自訂的預設格式，開啟 Microsoft 支援票證。 然後，輸入為您建立的預設格式的名稱。 

>[AZURE.NOTE] 目前，頻道開始可能需要 30 分鐘。 重設通道可以需要 5 分鐘。

建立頻道，您可以按一下頻道，然後選取 [您可以在此檢視您的頻道設定的**設定**。 

如需詳細資訊，請參閱[Live 串流使用 Azure 媒體服務，來建立多重位元率資料流](media-services-manage-live-encoder-enabled-channels.md)。


##<a name="get-ingest-urls"></a>取得內嵌 Url

頻道建立後，您可以取得內嵌即時 encoder 會提供您的 Url。 編碼器會使用下列 Url 輸入即時資料流。

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##<a name="create-and-manage-events"></a>建立及管理事件

###<a name="overview"></a>概觀

頻道是與相關聯的事件/程式，可讓您控制發佈及儲存空間的即時資料流中的區段。 頻道管理事件/應用程式。 頻道和程式的關聯性是內容的非常類似傳統媒體其中頻道有穩定，而程式限定為某些定時的事件，頻道]。

您可以指定您想要保留錄製的內容事件設定**封存視窗**長度的時數。 此值可以設定 5 分鐘為最小值，最多 25 個小時。 封存視窗長度也會指出時間用戶端的最大可搜尋的時間，從目前的即時位置。 移至指定的時間量，可以執行的事件，但持續捨棄落後視窗長度的內容。 此屬性的值也會決定用戶端資訊清單可以放大多久。

每項事件是資產與相關聯。 您必須建立相關聯的資產視需要的定位器發佈事件。 具有此定位器會讓您建立的串流的 URL，您可以提供給您的用戶端。

頻道支援最多三個同時執行事件，讓您建立多個封存的相同的內送資料流。 這個選項可讓您發佈，並視需要封存事件的不同部分。 例如，您的業務需求是事件的封存 6 小時，但廣播僅最後 10 分鐘的時間。 若要這麼做，您需要建立兩個同時執行事件。 一個事件設定為 [封存 6 小時的事件，但不是發佈程式。 其他事件設定為 [封存的 10 分鐘的時間，此程式發佈。

您不應該重複使用現有的程式，新的事件。 不過，建立並開始新的程式，為每個事件。

當您準備好開始串流和封存，請啟動事件/程式。 停止每當您要停止串流和封存事件的事件。 

若要刪除封存的內容，停止及刪除事件，然後刪除相關聯的資產。 您無法刪除資產，如果它由事件。必須先刪除該事件。 

即使您停止及刪除事件之後，使用者就無法串流封存的內容為視視訊，為，只要您不會刪除資產。

如果您想要保留封存的內容，但不是提供資料流，刪除串流定位器。

###<a name="createstartstop-events"></a>建立/開始/停止事件

一旦您有傳送到頻道的資料流您可以開始串流事件建立資產、 程式與串流定位器。 將封存資料流並使其可透過串流端點的檢視者。 

有兩種方式開始事件︰ 

1. 從 [**頻道**] 頁面中，按下**Live 事件**設為新增事件。

    指定︰ 事件名稱、 資產名稱、 封存] 視窗中及加密] 選項。
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    如果您向左**發佈現在這個即時事件**] 核取 [將會建立事件發佈的 Url。
    
    每當您已準備好將串流事件時，您可以按 [**開始**]。

    一旦您開始事件時，您可以按**監看式**開始播放的內容。

2. 或者，您可以使用快速鍵，請按 [**頻道**] 頁面上**移 Live**按鈕。 這會建立預設資產，而程式與串流定位器。

    事件為**預設**，[封存] 視窗設定為 8 小時。

您可以觀看**Live 事件**] 頁面的發佈的事件。 

如果您按一下 [**關閉 Air**，它將會停止即時的所有事件。 


##<a name="watch-the-event"></a>觀看事件

若要觀看事件，請按一下 Azure 入口網站中的 [**監看式**或複製串流 URL 並使用您所選擇的播放程式。 
 
![建立](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

即時事件會自動將視內容停止時的事件。

##<a name="clean-up"></a>清理

如果您完成串流事件，並且想要清理佈建較舊版本的資源，請遵循下列程序。

- 停止從編碼器發送資料流。
- 停止頻道。 一旦您停止頻道，它會不會造成任何費用。 當您需要重新啟動時，會有不同，因此您不需要重新設定您 encoder 內嵌 URL。
- 您可以停止您串流結束點，除非您想要繼續提供的即時事件為視串流封存。 如果頻道位於停止狀態時，它會不會造成任何費用。
  
##<a name="view-archived-content"></a>封存的檢視內容

即使您停止及刪除事件之後，使用者就無法串流封存的內容為視視訊，為，只要您不會刪除資產。 您無法刪除資產，如果它由事件。必須先刪除該事件。 

若要管理您的資產，選取 [**設定**，然後按一下 [**資產**]。

![資產](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

##<a name="considerations"></a>考量

- 目前即時事件的最大的建議的期間為 8 小時。 請如果您需要執行頻道的較長的時間，連絡在 Microsoft.com amslived。
- 請確定至少有一個串流串流要串流內容的端點上保留的單位。


##<a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
