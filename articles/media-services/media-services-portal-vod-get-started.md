<properties
    pageTitle=" 開始使用視需要使用 Azure 入口網站上發表內容 |Microsoft Azure"
    description="本教學課程中會引導您執行基本的 Video-on-Demand (VoD) 內容傳遞服務 Azure 媒體服務 （反飛彈系統） 應用程式使用 Azure 入口網站的步驟。"
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>開始使用視需要使用 Azure 入口網站上發表內容

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

本教學課程中會引導您執行基本的 Video-on-Demand (VoD) 內容傳遞服務 Azure 媒體服務 （反飛彈系統） 應用程式使用 Azure 入口網站的步驟。

> [AZURE.NOTE] 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 

本教學課程中會包含下列工作︰

1.  建立 Azure 媒體服務帳戶。
2.  設定串流結束點。
1.  上傳的視訊檔案。
1.  將一組調整位元率 MP4 檔案編碼來源檔案。
1.  發佈資產取得串流和漸進下載 Url。  
1.  播放您的內容。


## <a name="create-an-azure-media-services-account"></a>建立 Azure 媒體服務帳戶

本節中的步驟會顯示如何建立反飛彈系統帳戶。

1. 在[Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下**[+ 新增** > **Web + 行動** > **媒體服務**。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. 在**建立媒體服務帳戶**輸入必要的值。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. 在 [**帳戶名稱**] 中，輸入新的反飛彈系統帳戶的名稱。 媒體服務帳戶名稱是全部小寫的數字或字母不加空格，，3 至 24 個字元的長度。
    2. 在訂閱中，選取 [之間的不同的 Azure 訂閱，您有權存取]。
    
    2. 在 [**資源] 群組**中，選取新的或現有的資源。  資源群組是共用生命週期、 權限及原則的資源的集合。 深入瞭解[以下](azure-resource-manager/resource-group-overview.md#resource-groups)。
    3. 在 [**位置**]，選取地理區域用來儲存您的媒體服務帳戶的媒體及中繼資料記錄。 此區域用來處理和串流媒體。 使用媒體服務區域會出現在下拉式清單方塊中。 
    
    3. 在**儲存帳戶**，選取儲存的帳戶提供媒體內容媒體服務帳戶的 blob 儲存體。 您可以選取現有的儲存空間帳戶中相同的地理區域，為您的媒體服務帳戶，或者您也可以建立儲存帳戶。 在同一個區域中建立新的儲存空間帳戶。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。

        進一步瞭解儲存空間[以下](storage-introduction.md)。

    4. 選取 [**固定至儀表板**，若要查看帳戶部署的進度。
    
7. 按一下 [**建立**底部的表單]。

    帳戶已成功建立之後，狀態會變更為**執行**。 

    ![媒體服務設定](./media/media-services-portal-vod-get-started/media-services-settings.png)

    若要管理您的反飛彈系統帳戶 （例如上, 傳視訊編碼資產、 監控工作進度） 使用 [**設定**] 視窗。

## <a name="manage-keys"></a>管理索引鍵

您需要客戶名稱] 和 [以程式控制方式存取媒體服務帳戶主索引鍵的資訊。

1. 在 Azure 入口網站中，選取您的帳戶。 

    [**設定**] 視窗會顯示在右側。 

2. 在 [**設定**] 視窗中，選取 [**索引鍵**。 

    **管理金鑰**windows 顯示的帳戶名稱，並顯示主要和次要鍵。 
3. 按下的值，複製 [複製] 按鈕。
    
    ![媒體服務索引鍵](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>設定串流端點

使用其中一個最常見的案例會將透過調整的位元率串流視訊至您的用戶端 Azure 媒體服務。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

媒體服務會提供可讓您在發表您調整的位元率串流媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 只的時間，您不必儲存的每個串流格式預先封裝的版本支援的格式編碼 MP4 內容的動態包裝。

若要充分利用動態包裝，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案 （示範稍後在本教學課程編碼步驟） 編碼 mezzanine （來源） 檔案。  
- 建立至少有一個串流單位*串流端點*從您打算傳送您的內容。 下列步驟說明如何變更串流單位數量。

動態包裝，您只需要儲存及支付中單一儲存格式的檔案與媒體服務建置，並提供適當的回應，根據用戶端的要求。

若要建立並變更數串流保留的單位，請執行下列動作︰


1. 在 [**設定**] 視窗中，按一下 [**串流結束點**]。 

2. 按一下 [預設串流結束點]。 

    **預設串流端點詳細資料**視窗隨即出現。

3. 若要指定串流單位數量，請將滑**串流單位**。

    ![串流單位](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. 按一下 [**儲存**] 按鈕，以儲存變更]。

    >[AZURE.NOTE]分派的任何新的單位花費 20 分鐘才能完成。

## <a name="upload-files"></a>上傳檔案

串流影片使用 Azure 媒體服務，您需要上傳來源視訊、 將它們編碼成多個 bitrates，及發佈結果。 本節中涵蓋的第一個步驟。 

1. 在 [**設定**] 視窗中，按一下 [**資產**]。

    ![上傳檔案](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. 按一下 [**上傳**] 按鈕。

    [**上傳視訊的資產**] 視窗隨即出現。

    >[AZURE.NOTE] 不有任何檔案的大小限制。
    
4. 瀏覽至您的電腦上所要的影片、 加以選取，並按 [確定]。  

    上傳啟動，而且您可以查看進度的檔名。  

上傳完成後，您會看到新**的資產**] 視窗中列出的資產。 

## <a name="encode-assets"></a>編碼資產

使用其中一個最常見的案例進行調整的位元率串流至您的用戶端 Azure 媒體服務。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。 若要調整的位元率串流準備您的視訊，您需要進行來源視訊編碼多重位元率檔案。 您應該使用**標準媒體 Encoder** encoder 您的視訊。  

媒體服務也會提供可讓您進行您多重位元率 MP4s 下列串流格式中的動態包裝︰ MPEG 破折號、 HLS、 平滑串流，或 HDS，您不必重新封裝將這些串流格式。 動態包裝，您只需要儲存及支付中單一儲存格式的檔案與媒體服務建置，並提供適當的回應，根據用戶端的要求。

若要充分利用動態包裝，您需要執行下列動作︰

- 將您的來源檔案編碼成一組多重位元率 MP4 檔案 （編碼的步驟會示範稍後此區段中）。
- 至少有一個串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[設定串流結束點](media-services-portal-vod-get-started.md#configure-streaming-endpoints)。 

### <a name="to-use-the-portal-to-encode"></a>若要使用編碼的入口網站

本節說明編碼與媒體 Encoder 標準內容時可採取的步驟。

1.  在 [**設定**] 視窗中，選取 [**資產**]。  
2.  在 [**資產**] 視窗中，選取您想要編碼的資產。
3.  按下 [**編碼**] 按鈕。
4.  在 [**編碼資產**] 視窗中，選取 「 Media Encoder 標準 」 處理器及預設格式]。 例如，如果您知道您輸入的視訊具有 1920 x 1080 像素的解析度，然後您可以使用 「 H264 多個位元率 1080p 「 預設。 如需有關預設格式]，請參閱[這](https://msdn.microsoft.com/library/azure/mt269960.aspx)篇文章 – 請務必選取最適合您輸入視訊的預設格式。 如果您有低解析度 (640x360) 視訊，然後您應該不使用預設的 「 H264 多個位元率 1080p 「 預設。
    
    更容易管理，您可以編輯輸出資產的名稱及工作的名稱。
        
    ![編碼資產](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. 按下**建立**。

### <a name="monitor-encoding-job-progress"></a>監控編碼的工作進度

要監視的編碼工作進度，請按一下 [**設定**] （在頁面頂端），然後選取**工作**。

![工作](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>發佈內容

若要可讓您的使用者可以用於串流或下載內容的 URL，您必須建立定位器 「 發佈 」 您的資產。 定位器提供存取資產中所包含的檔案。 媒體服務支援定位器的兩種︰ 

- 串流 (OnDemandOrigin) 定位器，用於調整串流 （例如，將資料流 MPEG 破折號、 HLS 或平滑串流）。 若要建立串流定位器您的資產必須包含.ism 檔案。 
- 漸進 (SA) 定位器，用於傳遞視訊經由漸進下載。


串流 URL 具有下列的格式，您可以使用它來播放平滑串流資產。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要建立串流 URL HLS，附加 (格式 = m3u8 aapl) 的 url。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要建立串流 URL 的 MPEG 破折號，附加 (格式 = mpd-時間-csf) 的 url。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


SA URL 有下列格式。

    {blob container name}/{asset name}/{file name}/{SAS signature}

>[AZURE.NOTE] 如果您是使用入口網站建立定位器 2015 年 3 月之前，會建立定位器兩年到期日期。  

若要更新定位器到期日，請使用[其餘](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )或[.NET](http://go.microsoft.com/fwlink/?LinkID=533259) Api。 當您更新 SA 定位器的到期日時，URL 就會變更。

### <a name="to-use-the-portal-to-publish-an-asset"></a>使用發佈資產的入口網站

若要使用入口網站發佈資產，請執行下列動作︰

1. 選取 [**設定** > **資產**。
1. 選取您想要發佈的資產。
1. 按一下 [**發佈**] 按鈕。
1. 選取定位器類型。
2. 按下**新增**。

    ![發佈](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL 會新增至**發佈 Url**的清單。

## <a name="play-content-from-the-portal"></a>播放入口網站中的內容

Azure 入口網站提供內容，您可以使用以測試您的視訊播放程式。

按一下所要的視訊，然後按一下 [**播放**] 按鈕。

![發佈](./media/media-services-portal-vod-get-started/media-services-play.png)

一些事項︰

- 請確定已發行視訊。
- 此**Media player**播放從預設的串流結束點。 如果您想要從非預設串流端點，請按一下 [複製 URL，並使用其他的播放程式。 例如， [Azure 媒體服務播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

##<a name="next-steps"></a>後續步驟

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


