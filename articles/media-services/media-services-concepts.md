<properties 
    pageTitle="Azure 媒體服務概念 |Microsoft Azure" 
    description="本主題提供 Azure 媒體服務概念的概觀" 
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

#<a name="azure-media-services-concepts"></a>Azure 媒體服務概念 

本主題提供最重要的媒體服務概念的概觀。

##<a id="assets"></a>資產，儲存空間

###<a name="assets"></a>資產

[資產](https://msdn.microsoft.com/library/azure/hh974277.aspx)包含數位檔案 （包括視訊、 音訊、 圖像、 縮圖的集合，文字追蹤及字幕檔案） 和這些檔案的中繼資料。 數位檔案上傳到資產之後，他們無法用於媒體服務編碼方式和串流工作流程。

資產] 已對應至 blob 容器中的 Azure 儲存體帳戶和資產中的檔案儲存為二進位大型物件的容器中。

當決定上傳並儲存在資產媒體內容，請注意下列事項︰

- 資產應該包含只能有單一且唯一執行個體媒體內容。 例如，單一編輯電視片段、 影片，或通知。
- 資產不能包含多個轉譯或視聽檔案的編輯。 資產不當使用方式的其中一個範例會嘗試儲存多個電視片段、 通知或從單一生產資產內的多個攝影機角度。 將多個轉譯或視聽檔案的編輯儲存在資產，可能會導致送出編碼的工作，串流和保護傳送更新版本中的工作流程資產的問題。  

###<a name="asset-file"></a>資產檔案 
[AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx)代表 blob 容器中所儲存的實際視訊或音訊檔案。 資產檔案一律資產，與相關聯，而資產可能包含一或多個檔案。 媒體服務編碼器工作失敗，如果資產檔案物件無法與 blob 容器中的數位檔案相關聯。

**AssetFile**執行個體和實際的媒體檔案是兩個不同的物件。 媒體檔案包含實際的媒體內容時，AssetFile 執行個體包含媒體檔案相關的中繼資料。

您應該嘗試變更 blob 容器不使用媒體服務 Api 媒體服務所產生的內容。

###<a name="asset-encryption-options"></a>資產加密選項

根據您要上傳、 儲存及發表的內容類型，媒體服務會提供各種不同的加密選項，您可以選擇從。

**無**會不使用任何加密。 這是預設值。 請注意，使用此選項時，您的內容未受到保護傳送或儲存區中的其餘部分。

如果您打算將 MP4 使用漸進下載時，使用這個選項上, 傳您的內容。

**StorageEncrypted** – 使用這個選項來加密本機使用 AES 256 元加密清除內容，然後上傳至 Azure 儲存體將其儲存在其他加密。 受到保護的儲存空間加密的資產會自動加密放在之前編碼，加密的檔案系統中和您也可以選擇重新加密之前上傳回到為新的輸出資產。 儲存加密的主要的使用情況時，您要保護高品質輸入的媒體檔案與在其他加密磁碟上。 

才能進行儲存空間的加密的資產，您必須設定資產的傳遞原則，讓媒體服務可讓您知道您要傳送您的內容的方式。 可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則 （例如，AES、 PlayReady 或不加密） 的內容。 

**CommonEncryptionProtected**使用這個選項，如果您想要加密 （或上傳已加密） 內容與一般加密或 PlayReady DRM （例如，平滑串流受到 PlayReady DRM）。

**EnvelopeEncryptionProtected** – 使用這個選項，如果您想要保護 （或上傳已受保護） HTTP Live 串流 (HLS) 加密與進階加密標準 (AES)。 請注意，是否您要上傳 HLS 已使用 AES 加密，它必須已加密轉換的管理員。

###<a name="access-policy"></a>存取原則 

[AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx)定義資產 （如讀取、 寫入和清單） 的權限及持續時間的存取權。 您通常定位，然後想要用來存取資產所包含的檔案來傳送 AccessPolicy 物件。


###<a name="blob-container"></a>Blob 容器

Blob 容器會提供一組二進位大型物件的群組。 Blob 容器會作為媒體服務中邊界點存取控制及共用 Access 簽章 (SA) 定位器資產。 Azure 儲存體帳戶可以包含 blob 容器數量。 容器可以儲存 blob 數量。

>[AZURE.NOTE]您應該嘗試變更 blob 容器不使用媒體服務 Api 媒體服務所產生的內容。

###<a id="locators"></a>定位器

[定位器](https://msdn.microsoft.com/library/azure/hh974308.aspx)s 提供進入點存取資產所包含的檔案。 存取原則用來定義的權限及持續時間的用戶端有權存取特定的資產。 定位器可以有對存取原則，一個關聯，例如不同定位器可提供不同的開始時間和連線類型不同的用戶端所有使用相同的權限和持續時間設定; 時不過，Azure 儲存服務所設定的共用的存取原則限制，因為您不能超過五個唯一的定位器一次指定的資產與相關聯。 

媒體服務支援兩種類型的定位器︰ OnDemandOrigin 定位器，用來將串流媒體 （例如，MPEG 破折號、 HLS 或平滑串流） 或漸進下載 [媒體及 SA URL 定位器用來上傳或下載媒體檔案 to\from Azure 儲存空間。 

請注意，建立 OrDemandOrigin 定位器時，不應使用 [清單] 權限 (AccessPermissions.List)。 

###<a name="storage-account"></a>儲存帳戶

透過儲存帳戶完成所有存取 Azure 儲存體。 媒體服務帳戶可以使用一或多個儲存帳戶建立關聯。 帳戶的總大小為底下 500 TB 每個儲存帳戶時，可以包含容器，數量。  媒體服務會提供 SDK 層級的工具，讓您管理多個儲存帳戶和負載的平衡在這些帳戶上傳您的資產的分佈根據指標或隨機通訊群組。 如需詳細資訊，請參閱使用[Azure 儲存體](https://msdn.microsoft.com/library/azure/dn767951.aspx)。 

##<a name="jobs-and-tasks"></a>工作和工作

通常是[工作](https://msdn.microsoft.com/library/azure/hh974289.aspx)程序 （例如，索引或編碼） 一個音訊/視訊簡報。 如果您正在處理多個視訊，建立每個視訊編碼的工作。

工作會包含相關執行處理中繼資料。 每項工作包含一或多個[任務](https://msdn.microsoft.com/library/azure/hh974286.aspx)s 指定原子處理任務時，其輸入資產，輸出資產、 媒體處理器以及其相關聯的設定。 可以在一起，鏈結中工作的工作位置輸出資產的一項工作給為輸入資產的下一個工作。 在這種方式一項工作，也可以包含所有必要的媒體簡報的處理。

##<a id="encoding"></a>編碼

Azure 媒體服務會提供多個選項的雲端中的媒體編碼方式。

當開始與媒體服務，務必瞭解轉碼器與檔案格式之間的差異。
轉碼器是而檔案格式是按住壓縮的視訊的容器實作壓縮/解壓縮演算法的軟體。

媒體服務會提供可讓您進行資料流支援的媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 格式中調整位元率 MP4 或平滑串流編碼內容的動態包裝，您不必重新封裝成這些串流格式。

若要充分利用[動態包裝](media-services-dynamic-packaging-overview.md)，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案或平滑串流 （示範稍後在本教學課程編碼步驟） 之檔案的自動調整位元率編碼 mezzanine （來源） 檔案。
- 至少有一個視串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[如何視需要串流保留刻度](media-services-portal-manage-streaming-endpoints.md)。

媒體服務上本文所述的需求編碼器支援下列動作︰

- [媒體 Encoder 標準](media-services-encode-asset.md#media-encoder-standard)
- [媒體 Encoder 進階版工作流程](media-services-encode-asset.md#media-encoder-premium-workflow)

編碼器支援的相關資訊，請參閱[編碼器](media-services-encode-asset.md)。


##<a name="live-streaming"></a>Live 串流

頻道 Azure 媒體服務，表示的管線處理即時串流的內容。 頻道會收到即時輸入資料流時，在兩種方法之一︰

- 內部部署即時編碼器會將多重位元率 RTMP 或平滑串流 (分散 MP4) 傳送給該頻道。 您可以使用輸出多重位元率平滑串流的下列即時編碼器︰ 元素，Envivio，Cisco。 下列即時編碼器輸出 RTMP: Adobe Flash Live、 Telestream Wirecast 和 Tricaster 轉錄。 Ingested 資料流通過不含任何進一步處理的頻道。 要求時，媒體服務會提供給客戶資料流。

- 單一位元率串流 (下列格式的其中一種︰ RTP (MPEG TS))，RTMP，或平滑串流 (分散 MP4)) 會傳送至已執行即時編碼方式和媒體服務啟用該頻道。 頻道，然後執行即時編碼的內送至多重-位元率 （自動調整） 視訊資料流單一位元率資料流。 要求時，媒體服務會提供給客戶資料流。

###<a name="channel"></a>頻道

在媒體服務[頻道](https://msdn.microsoft.com/library/azure/dn783458.aspx)s 負責處理即時串流的內容。 頻道提供輸入的端點 （內嵌 URL），然後提供至即時 transcoder。 頻道即時輸入資料流接收即時 transcoder，並可透過一或多個 StreamingEndpoints 資料流。 頻道也會提供您用來預覽和驗證您的資料流進一步處理及傳送之前預覽結束點 (預覽 URL)。

當您建立頻道時，您可以取得 ingest URL 和預覽的 URL。 若要取得這些 Url，頻道沒有要啟動的狀態。 當您準備好開始將資料從即時 transcoder 發送到頻道時，必須先啟動頻道。 一旦即時 transcoder 啟動 ingesting 資料時，您可以預覽您的資料流。

每個媒體服務帳戶可包含多個頻道、 多個程式，以及多個 StreamingEndpoints。 根據頻寬與安全性的需求，StreamingEndpoint 服務能單獨使用一或多個頻道。 任何 StreamingEndpoint 可以從任何頻道拉取。


###<a name="program"></a>程式

[程式](https://msdn.microsoft.com/library/azure/dn783463.aspx)可讓您控制發佈及儲存空間的即時資料流中的區段。 頻道管理應用程式。 頻道和程式的關聯性是內容的非常類似傳統媒體其中頻道有穩定，而程式限定為某些定時的事件，頻道]。
您可以指定您想要保留錄製的內容程式**ArchiveWindowLength**屬性設定時數。 此值可以設定 5 分鐘為最小值，最多 25 個小時。

ArchiveWindowLength 也會指出時間用戶端的最大可搜尋的時間，從目前的即時位置。 移至指定的時間量，可以執行的程式，但會持續捨棄落後視窗長度的內容。 此屬性的值也會決定用戶端資訊清單可以放大多久。

每個程式是資產與相關聯。 若要發佈程式中，您必須建立定位器的相關聯的資產。 具有此定位器會讓您建立的串流的 URL，您可以提供給您的用戶端。

頻道支援最多三個同時執行的程式，因此您可以建立多個封存的相同的內送資料流。 這個選項可讓您發佈，並視需要封存事件的不同部分。 例如，您的業務需求是程式的封存 6 小時，但廣播僅最後 10 分鐘的時間。 若要這麼做，您需要建立兩個同時執行的程式。 一個程式設定為 [封存 6 小時的事件，但不是發佈程式。 其他程式設定為 [封存的 10 分鐘的時間，此程式發佈。


如需詳細資訊，請參閱︰

- [使用 [啟用來執行即時編碼 Azure 媒體服務與頻道](media-services-manage-live-encoder-enabled-channels.md)
- [使用多重位元率即時資料流接收內部部署編碼器的頻道](media-services-live-streaming-with-onprem-encoders.md)
- [配額和限制](media-services-quotas-and-limitations.md)。

##<a name="protecting-content"></a>保護內容

###<a name="dynamic-encryption"></a>動態加密

Azure 媒體服務可讓您以保護您的媒體離開您的電腦儲存、 處理及傳送的時間。 媒體服務可讓您傳送加密動態一般加密 (CENC) 使用 PlayReady 及/或 Widevine DRM 進階加密標準 (AES) （使用的是 128 位元加密金鑰） 與您的內容。 媒體服務也會提供授權用戶端進行 AES 索引鍵和 PlayReady 授權的服務。

目前，您可以將下列串流格式的加密︰ HLS、 MPEG 虛線，以及帶有平滑串流。 您無法加密 HDS 串流格式]，或循序下載。

如果您想要加密資產的媒體服務，您需要加密金鑰 （CommonEncryption 或 EnvelopeEncryption） 與您的資產也設定金鑰的授權原則。

如果您想要將串流儲存空間的加密的資產，您必須設定資產的傳遞原則，才能指定您要傳送您的資產的方式。

要求資料流時，播放程式，媒體服務會使用指定的按鍵來動態加密您使用的信封加密 （含 AES) 或一般加密 （PlayReady 或 Widevine） 的內容。 若要解密串流，播放程式會從主要傳遞服務要求索引鍵。 若要決定以取得金鑰授權的使用者，服務會評估您指定的金鑰的授權原則。


###<a name="token-restriction"></a>權杖限制

內容索引鍵的授權原則可能會有一或多個授權限制︰ 開啟、 權杖限制或 IP 限制。 權杖限制的原則必須伴隨的權杖發出的安全性 Token 服務 (STS)。 媒體服務支援的簡單 Web 權杖 (SWT) 格式和 JSON Web Token (JWT) 格式的權杖。 媒體服務並不提供安全性權杖服務。 您可以建立自訂的 STS，或利用 Microsoft Azure ACS 這些問題權杖。 若要建立的權杖簽署您指定的權杖限制設定指定索引鍵和問題宣告必須設定 STS。 媒體服務索引鍵的遞送服務會傳回要求的索引鍵 （或授權） 如果權杖是有效的用戶端與索引鍵 （或授權） 的權杖符合這些設定中的宣告。

當設定權杖限制原則時，您必須指定驗證主索引鍵、 發行者及簡報對象參數值。 驗證主索引鍵包含索引鍵的權杖的簽名，發行者不安全的權杖服務的權杖中發生的問題。 （有時稱為範圍） 的對象說明的權杖或資源的權杖授與存取權。 媒體服務索引鍵的遞送服務會驗證這些權杖中的值相符的範本中的值。

如需詳細資訊，請參閱下列文章︰

[保護內容概觀](media-services-content-protection-overview.md)
[保護與 AES 128](media-services-protect-with-aes128.md)
[DRM 使用的保護](media-services-protect-with-drm.md)

##<a name="delivering"></a>進行

###<a id="dynamic_packaging"></a>動態包裝

使用時建議將調整的位元率 MP4 編碼 mezzanine 檔案的媒體服務設定，然後再轉換使用[動態包裝](media-services-dynamic-packaging-overview.md)所要的格式中的設定。


###<a name="streaming-endpoint"></a>串流端點

StreamingEndpoint 代表串流服務所傳送的內容，直接用戶端播放程式的應用程式，或以內容傳遞網路 (CDN) 進一步分配 （Azure 媒體服務現在提供 Azure CDN 整合）。即時資料流或隨選資產媒體服務帳戶中的視訊，可使用 StreamingEndpoint 服務的輸出資料流。 此外，您可以控制能力 StreamingEndpoint 服務的處理成長的頻寬需求，藉由調整刻度] 單位 （也稱為串流的單位）。 建議您為生產環境中的應用程式配置一或多個刻度] 單位。 刻度] 單位提供給您可以購買 200 Mbps 量的專用的出口容量和其他功能，包括目前使用動態包裝。

建議使用動態包裝和 \ 或動態加密。 若要使用這些功能，您必須至少有一個串流您打算將串流的端點單位。 如需詳細資訊，請參閱[縮放比例串流單位](media-services-portal-manage-streaming-endpoints.md)。

根據預設，您可以讓最多 2 串流媒體服務帳戶中的端點。 若要要求較高的限制，請參閱[配額和限制](media-services-quotas-and-limitations.md)。

您只被付費您 StreamingEndpoint 時的執行狀態。

###<a name="asset-delivery-policy"></a>資產傳遞原則

其中一個步驟中的媒體服務內容傳遞工作流程正在設定您想要傳送的[資產的傳遞原則](https://msdn.microsoft.com/library/azure/dn799055.aspx)。 資產傳遞原則會告訴媒體服務要用於您要傳送的資產的方式︰ 將應您資產動態封裝 （如 MPEG 破折號、 HLS、 平滑串流、 或全部），無論是否要動態加密您資產，以及如何哪些串流通訊協定 （信封或一般加密）。

如果您有儲存加密的資產，，可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則的內容。 例如，進行進階加密標準 (AES) 加密金鑰以加密您資產，設原則類型 DynamicEnvelopeEncryption。 若要移除儲存加密，並將串流中清除資產，將原則類型設為 NoDynamicEncryption。

###<a name="progressive-download"></a>漸進下載

漸進下載可讓您要開始播放媒體之前已下載整個檔案。 您可以只漸進下載 MP4 檔案。

請注意，是否您希望他們可供漸進下載，則必須解密加密的資產。

為使用者提供漸進下載 Url，您必須先建立 OnDemandOrigin 定位器。 建立尋找程式，可讓您的基底路徑資產。 然後，您需要新增 MP4 檔案的名稱。 例如︰

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

###<a name="streaming-urls"></a>串流 Url

串流您用戶端的內容。 若要串流 Url 提供給使用者，您必須先建立 OnDemandOrigin 定位器。 建立尋找程式，可讓您的基底路徑資產，包含您想要將串流的內容。 不過，若要能夠串流這個內容您需要修改進一步此路徑。 若要建構串流資訊清單檔案的完整 URL，您必須串連定位器路徑值和資訊清單 (filename.ism) 檔案名稱。 然後附加定位器路徑 /Manifest 及適當的格式 （如有需要）。

您也可以透過 SSL 連線串流內容。 若要這麼做，請確定您串流 Url 的開頭為 HTTPS。

請注意，您可以只串流透過 SSL 是否 2014 年 9 月 10 日之後建立串流端點進行您的內容。 如果您串流的 Url 根據串流年 9 月 10 以後建立的結束點，URL 會包含 「 streaming.mediaservices.windows.net 」 （新格式）。 包含 「 origin.mediaservices.windows.net 」 （舊格式） 串流 Url 不支援 SSL。 如果您的 URL 是舊的格式，而且您想要能夠透過 SSL 串流，建立新的資料流結束點。 若要將您的內容串流透過 SSL 使用根據新的資料流端點建立的 Url。

下列清單說明不同串流格式，並讓範例︰

- 帶有平滑串流

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG 破折號

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP Live 串流 (HLS) V4

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP Live 串流 (HLS) V3

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS （適用於只使用 Adobe 寶貴時間/存取授權）

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f)


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
