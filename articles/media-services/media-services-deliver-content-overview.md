<properties
    pageTitle="將內容傳送給客戶 |Microsoft Azure"
    description="本主題提供相關內容中進行 Azure 媒體服務與內容的概觀。"
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


# <a name="deliver-content-to-customers"></a>傳送給客戶的內容
您正在進行時串流或 video-on-demand 內容為 [客戶]，您的目標是以不同的裝置在不同的網路的情況下發表高品質視訊。

若要達到這個目的，您可以︰

- 編碼您的資料流多重位元率 （自動調整位元率） 視訊資料流。 這會負責品質和網路的條件。
- 使用 Microsoft Azure 媒體服務[動態包裝](media-services-dynamic-packaging-overview.md)動態重新封裝您串流到不同的通訊協定。 這會負責串流不同的裝置上。 媒體服務支援下列調整位元率串流技術的傳遞︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

本文提供重要的內容傳遞概念的概觀。

若要檢查的已知的問題，請參閱[已知問題](media-services-deliver-content-overview.md#known-issues)。

## <a name="dynamic-packaging"></a>動態包裝

使用動態包裝媒體服務所提供的您可以傳送您調整位元率 MP4 或平滑串流編碼中的內容而不需要重新封裝成這些串流格式串流支援媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 的格式。 我們建議您進行動態包裝的內容。

若要充分利用動態包裝，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案或調整位元率平滑串流檔案編碼 mezzanine （來源） 檔案。
- 取得您要傳送您的內容串流端點至少有一個視串流單位。 如需詳細資訊，請參閱[如何不按比例縮放點播串流保留的單位](media-services-portal-manage-streaming-endpoints.md)。

使用動態包裝，您可以儲存，並支付中單一儲存格式的檔案。 媒體服務會建立並提供適當的回應，根據您的要求。

除了提供動態包裝功能的存取權，視需要串流保留的單位提供給您可以購買增量為 200 Mbps 的專用的出口容量。 根據預設，視需要串流設定的伺服器資源 （例如，計算或出口容量） 共用所有其他使用者共用執行個體模型中。 您可以購買視串流保留的單位改善視串流處理量。

如需詳細資訊，請參閱[動態包裝](media-services-dynamic-packaging-overview.md)。

## <a name="filters-and-dynamic-manifests"></a>篩選和動態資訊清單

您可以定義篩選的資產與媒體服務。 這些篩選功能可協助您執行的動作包括播放視訊的某特定章節，或指定的客戶的裝置可處理 （而非所有轉譯資產與相關聯的） 的音訊和視訊轉譯子集的客戶的伺服器端規則。 這項篩選的方法是透過您的客戶要求串流視訊，根據一或多個指定的篩選時所建立的*動態資訊清單*。

如需詳細資訊，請參閱[篩選和動態資訊清單](media-services-dynamic-manifest-overview.md)。

## <a name="locators"></a>定位器

為您的使用者提供的 URL，可用來串流或下載您的內容，您需要建立定位器發佈您的資產。 定位器提供進入點存取資產所包含的檔案。 媒體服務支援定位器的兩種︰

- OnDemandOrigin 定位器。 這些是用來串流媒體 （例如，MPEG 破折號、 HLS 或平滑串流），或漸進下載檔案。
-  共用的 access 簽章 (SA) URL 定位器。 這些用來將媒體檔案下載到您的本機電腦。

*存取原則*用來定義 （例如讀取、 寫入和清單） 的權限和用戶端具有特定的資產的存取權的時間長度。 清單權限 (AccessPermissions.List) 不應該用於建立 OrDemandOrigin 定位器附註。

定位器有到期日。 Azure 入口網站設定到期日在未來 100 年定位器。

>[AZURE.NOTE] 如果您是使用 Azure 入口網站建立定位器 2015 年 3 月之前，這些定位器已設定兩年後到期。

若要更新定位器到期日，請使用[其餘](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )或[.NET](http://go.microsoft.com/fwlink/?LinkID=533259) Api。 請注意，當您更新 SA 定位器的到期日，URL 就會變更。

若要管理每位使用者存取控制並未設計定位器。 您可以使用數位版權管理 (DRM) 解決方案，以授與個別使用者的不同的存取權限。 如需詳細資訊，請參閱[設定安全性的媒體](http://msdn.microsoft.com/library/azure/dn282272.aspx)。

當您建立定位器時，可能會因為必要的儲存空間和傳播 Azure 儲存體中的程序 30 秒延遲。


## <a name="adaptive-streaming"></a>彈性資料流

調整位元率技術讓判斷網路條件，然後選取 [從數種 bitrates 視訊播放程式應用程式。 當網路通訊降低時，用戶端可以選取較低的位元率，以便播放可以繼續使用較低的視訊品質。 改善網路條件，用戶端可以切換至 [更高的位元率改良的視訊品質。 Azure 媒體服務支援下列調整位元率技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS。

若要串流 Url 提供給使用者，您必須先建立 OnDemandOrigin 定位器。 建立尋找程式可讓您的基底路徑資產，包含您想要將串流的內容。 不過，若要將串流此內容，您需要修改進一步此路徑。 若要建構串流資訊清單檔案的完整 URL，您必須串連定位器路徑值和資訊清單 (filename.ism) 檔案名稱。 然後**附加/資訊清單**，以適當的格式 （如有需要） 至定位器路徑。

>[AZURE.NOTE]您也可以透過 SSL 連線串流內容。 若要這麼做，請確定您串流 Url 的開頭為 HTTPS。

如果串流端點進行內容建立 2014 年 9 月 10 日之後，您僅可以串流透過 SSL。 如果您串流的 Url 根據 2014 年 9 月 10 之後, 建立串流端點 URL 會包含 「 streaming.mediaservices.windows.net 」。 包含 「 origin.mediaservices.windows.net 」 （舊格式） 串流 Url 不支援 SSL。 如果您的 URL 是舊的格式，而且您想要能夠透過 SSL 串流，建立新的資料流結束點。 若要將您的內容串流透過 SSL 使用根據新的資料流端點的 Url。


## <a name="streaming-url-formats"></a>串流 URL 格式

### <a name="mpeg-dash-format"></a>MPEG 虛線格式

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live 串流 (HLS) V4 格式

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live 串流 (HLS) V3 格式

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>以純音訊篩選 Apple HTTP Live 串流 (HLS) 格式

根據預設，HLS 中包含純音訊追蹤資訊清單。 這是必要的 Apple Store 憑證的行動電話網路。 在此情況下，如果用戶端沒有足夠的頻寬，或連線到 2 G 連線，播放會切換至純音訊。 這可協助保護內容串流，而不緩衝，但是並沒有視訊。 在某些情況下，緩衝的播放程式可能會優先會純音訊。 如果您想要移除純音訊追蹤，新增**純音訊 = false**的 url。

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

如需詳細資訊，請參閱[動態資訊清單撰寫支援和 HLS 輸出其他功能](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。


### <a name="smooth-streaming-format"></a>帶有平滑串流格式

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

範例︰

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>帶有平滑串流 2.0 資訊清單 （傳統資訊清單）

根據預設，平滑串流資訊清單格式會包含重複的標記 （r 標記）。 不過，某些播放機不支援 r 標記。 與這些玩家的用戶端可以使用停用 r 標籤的格式︰

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

### <a name="hds-for-adobe-primetimeaccess-licensees-only"></a>HDS （適用於只使用 Adobe 寶貴時間/存取授權）

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)

## <a name="progressive-download"></a>漸進下載

使用漸進下載時，您就可以開始播放媒體之前已下載整個檔案。 您無法漸進下載.ism * （ismv、 isma、 ismt 或 ismc） 檔案。

若要變得下載內容，請使用定位 OnDemandOrigin 類型。 下列範例會顯示為基礎的定位器 OnDemandOrigin 類型的 URL:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

您必須解密任何您想要從漸進下載原點服務串流的儲存空間加密資產。

## <a name="download"></a>下載

若要下載內容的用戶端裝置，您必須建立 SA 定位器。 SA 定位器可讓您存取 Azure 儲存體容器檔案所在的位置。 若要建立下載 URL，您必須內嵌之間的主機和 SA 簽章的檔案名稱。

下列範例顯示根據 SA 定位器 URL:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

下列事項︰

- 您必須解密任何您想要從漸進下載原點服務串流的儲存空間加密資產。
- 尚未完成 12 小時內下載將會失敗。

## <a name="streaming-endpoints"></a>串流端點

串流結束點代表直接用戶端播放程式應用程式或內容傳遞網路 (CDN)，以進一步通訊群組進行內容的串流服務。 即時資料流或媒體服務帳戶中的 video-on-demand 資產，可以是串流端點服務的輸出資料流。 您也可以控制串流端點服務處理成長的頻寬需求來調整串流保留的單位的容量。 您應配置至少有一個保留的單位生產環境中的應用程式。 如需詳細資訊，請參閱[如何不按比例縮放的媒體服務](media-services-portal-manage-streaming-endpoints.md)。

## <a name="known-issues"></a>已知的問題

### <a name="changes-to-smooth-streaming-manifest-version"></a>變更平滑串流資訊清單的版本

2016 年 7 月服務發行-之前時資產所產生的媒體 Encoder 標準、 Media Encoder 進階版工作流程] 或舊版 Azure Media Encoder 的傳送使用動態包裝-平滑串流 2.0 想符合傳回的資訊清單。 在版本 2.0，片段工期，請勿使用所謂的重複 (「 r 」) 標籤。 例如︰

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

2016 年 7 月服務版本中產生的平滑串流資訊清單符合版本 2.2，包含片段工期使用重複的標籤。 例如︰

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

舊版的平滑串流用戶端的一些可能不支援重複標籤，然後將無法載入資訊清單。 若要降低此問題，您可以使用舊版的顯示格式參數**(格式 = fmp4 v20)**或更新您的用戶端的最新版本，支援重複標籤。 如需詳細資訊，請參閱[平滑串流 2.0](media-services-deliver-content-overview.md#fmp4_v20)。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>相關的主題

[更新媒體服務定位器後循環儲存金鑰](media-services-roll-storage-access-keys.md)
