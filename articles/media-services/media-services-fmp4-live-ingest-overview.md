<properties 
    pageTitle="分散的 MP4 live azure 媒體服務內嵌規格 |Microsoft Azure" 
    description="此規格描述通訊協定和分散 MP4 基礎即時串流的 ingestion Microsoft Azure 媒體服務的格式。 Microsoft Azure 媒體服務會提供可讓客戶資料流即時事件且廣播即時內容即時串流服務使用 Microsoft Azure 雲端平台。 這份文件也討論的最佳作法 live 建置高度多餘和強大內嵌機制。" 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>分散的 MP4 live azure 媒體服務內嵌規格

此規格描述通訊協定和分散 MP4 基礎即時串流的 ingestion Microsoft Azure 媒體服務的格式。 Microsoft Azure 媒體服務會提供可讓客戶資料流即時事件且廣播即時內容即時串流服務使用 Microsoft Azure 雲端平台。 這份文件也討論的最佳作法 live 建置高度多餘和健全內嵌機制。


##<a name="1-conformance-notation"></a>1.一致性標記法

關鍵字 「 必須 」，"NOT 必須 」、 「 REQUIRED 」、 「 SHALL 」、 」 不應該 」、 「 應該 」、 」 不應該 」、 「 建議 」、 「 可能 」 和 「 選擇性 」 這份文件中的會被解譯 RFC 2119 所述。

##<a name="2-service-diagram"></a>2.服務圖表 

下圖顯示 Microsoft Azure 媒體服務高階架構的即時資料流服務︰

1.  即時 Encoder 將會建立並佈建頻道透過 Microsoft Azure 媒體服務 SDK 即時摘要。
2.  頻道、 程式與串流中 Microsoft Azure 媒體服務控點的端點所有即時串流功能包括 ingest，格式設定，雲端 DVR、 安全性、 延展性和重複。
3.  您也可以選擇客戶可以選擇部署串流端點與用戶端端點之間 CDN 圖層。
4.  用戶端結束點資料流串流端點使用 HTTP 彈性資料流通訊協定 （例如平滑串流、 破折號、 HDS 或 HLS）。

![image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3.元資料流格式-ISO 14496 12 分散 MP4

線格式即時資料流內嵌這份文件中所描述的根據 [14496-12]。 請參考分散的 MP4 格式與副檔名這兩個 video-on-demand 檔案的詳細說明的[[MS SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) ，然後 live 串流 ingestion。

###<a name="live-ingest-format-definitions"></a>Live 內嵌格式定義 

以下是特殊格式套用至 live 定義內嵌到 Microsoft Azure 媒體服務的清單︰

1. 必須每個要求 （HTTP 文章） 傳送 'ftyp' LiveServerManifestBox，與 'moov] 方塊。  必須將它傳送資料流開頭，隨時編碼器必須重新連線到繼續串流內嵌。  請參閱 [1] 中的區段 6 如需詳細資訊。
2. [1] 中的區段 3.3.2 定義稱為 StreamManifestBox 的即時選用方塊內嵌。 Microsoft Azure 負載平衡器路由邏輯，因為此方塊的使用方式取代時 ingesting 到 Microsoft Azure 媒體服務, 不應該會出現。 如果此方塊，Azure 媒體服務火車會忽略該。
3. 在 [1] 中的 3.2.3.2 中定義 TrackFragmentExtendedHeaderBox 必須簡報的每個片段。
4. 版本 2 TrackFragmentExtendedHeaderBox 應該會用來產生多個資料中心的媒體區段與相同的 Url。 片段 index 功能變數所需的跨資料中心的索引型串流的容錯移轉格式，例如 Apple HTTP Live 串流 (HLS) 和索引為基礎的 MPEG 虛線。  若要啟用跨資料中心容錯移轉，片段索引必須同步處理多個編碼器，以及增加 1 的每個連續的媒體片段，甚或 encoder 重新啟動或失敗。
5. [1] 中的區段 3.3.6 定義稱為 MovieFragmentRandomAccessBox (「 mfra 」) 可能會指出 EOS （結束資料流） 頻道傳送即時 ingestion 結尾的方塊。 Azure 媒體服務 ingest 邏輯，因為已被取代 EOS （結束資料流） 使用方式，而不應該傳送即時 ingestion 的 「 mfra] 方塊。 如果已傳送，正在 Azure 媒體服務火車會忽略該。 我們建議用來重設狀態的 ingest 點的 [[頻道重設](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels)，也建議使用[程式停止](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs)結束的簡報與資料流。
6. MP4 片段工期應該是常數，以減少用戶端資訊清單的大小，以及改善用戶端下載 heuristics 透過使用重複的標籤。  工期可能波動才能賠償非整數框架費率。
7. MP4 片段期間應該大約 2 到 6 秒的時間。
8. 以遞增順序送達 MP4 片段的時間戳記和索引 （TrackFragmentExtendedHeaderBox fragment_ absolute_ 時間和 fragment_index）。  雖然 Azure 媒體服務同時重複片段，具有非常有限重新排列根據媒體時間表片段的能力。

##<a name="4-protocol-format--http"></a>4.通訊協定格式 – HTTP

ISO 分散 MP4 live 基礎內嵌執行一段時間 HTTP 文章的標準要求傳輸編碼的媒體資料封裝服務的分散的 MP4 格式的 Microsoft Azure 媒體服務使用。 每個 HTTP 文章傳送完成分散 MP4 元串流 （「 資料流 」） 從開始標題方塊 （'ftyp 」、 「 Live 伺服器資訊清單方塊 」 及 「 moov 」 方塊），再繼續進行一系列片段 （「 moof 」 及 「 mdat' 方塊）。 請參閱 [9.2 1] 中的一節的 HTTP 文章要求的 URL 語法。 文章 URL 的範例是︰ 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>需求

以下是詳細的需求︰

1. Encoder 應該具有空白 」 本文 」 （零內容的長度） 使用相同的 ingestion URL HTTP 文章要求傳送開始播放。 如果是有效的即時 ingestion 端點，如果有任何驗證或其他必要的條件快速偵測到，這可以幫助。 每個 HTTP 通訊協定，伺服器無法傳送返回 HTTP 回應，直到收到整個要求包括張貼內容。 指定即時事件，沒有這個步驟，長時間執行性質編碼器可能無法偵測到任何錯誤，直到完成傳送的所有資料。
2. Encoder 必須處理任何錯誤或驗證挑戰當做 (1)。 如果 (1) 成功 200 回應，以繼續。
3. Encoder 必須分散 MP4 串流開始新的 HTTP 文章要求。  內容的開頭必須後面接著片段的頁首方塊。  即使編碼器必須重新連線，因為先前要求終止資料流結尾之前，必須與每個要求，傳送附註 （依此順序） 'ftyp 」、 「 Live 伺服器資訊清單方塊 」 及 「 moov 」 方塊。 
4. Encoder 必須使用區塊傳輸編碼的上傳因為它是無法預測即時事件的整個內容長度。
5. 事件結束後，最後一個片段之後, 編碼器必須正常結尾 （大部分 HTTP 用戶端堆疊的處理方式自動） 的區塊傳輸編碼訊息順序。 傳回最終的回應程式碼，然後終止連線至服務必須等到 encoder。 
6. Encoder 必須使用 Events() 名詞中所述 [9.2 1] 中的 [即時 ingestion 到 Microsoft Azure 媒體服務。
7. 如果 HTTP 文章要求終止或發生逾之前 TCP 錯誤資料流結尾，編碼器必須新的文章要求使用新的連線，並遵循上方的需求，與其他需求編碼器必須重新傳送資料流，在每個追蹤的上一個兩個 MP4 片段，並繼續介紹媒體時間表中的卻沒有。  重新傳送的每個追蹤的最後兩個 MP4 片段，可確保不會遺失任何資料。  換句話說，如果資料流含有兩個音訊和視訊追蹤，且目前文章要求失敗，編碼器必須重新連線並重新傳送的最後兩個片段的音訊追蹤，先前已成功傳送及的最後兩個片段的視訊追蹤，先前已成功送給，才能確保不會遺失任何資料。  編碼器必須維護媒體片段，將其重新傳送重新連線時 「 轉寄 」 緩衝。

##<a name="5-timescale"></a>5。 時幅 

[[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx)將 「 時幅] 的使用狀況說明 SmoothStreamingMedia （章節 2.2.2.1）、 StreamElement （章節 2.2.2.3）、 StreamFragmentElement(2.2.2.6) 和 LiveSMIL （章節 2.2.7.3.1）。 如果時幅值不存在，所使用的預設值是 10000000 (10 MHz)。 雖然平滑串流格式規格不會封鎖其他時幅的值，大部分的 encoder 實作用途使用此預設值 (10 MHz) 來產生平滑串流內嵌的資料。 由於[Azure 媒體動態包裝](media-services-dynamic-packaging-overview.md)功能，建議使用 90 kHz 視訊資料流時幅及秒 44.1 或 48.1 kHz 進行音訊資料流。 如果不同的時幅值會使用不同的資料流時，必須傳送資料流層級時幅。 請參閱[[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx)。     

##<a name="6-definition-of-stream"></a>6.定義的 「 資料流 」  

「 資料流 」 是基本作業即時 ingestion 撰寫即時簡報、 處理串流錯誤移轉及重複案例的單位。 「 資料流 」 會定義為一個唯一分散 MP4 元資料流其中可能包含單一的追蹤或多個追蹤。 完整即時簡報可能包含一或多個資料流根據即時 encoder(s) 的設定。 下面的範例說明使用 stream(s) 撰寫完整即時簡報的各種不同的選項。

**範例︰** 

客戶想要建立包含下列的音訊/視訊 bitrates 的即時資料流簡報︰

影片-3000 kbps 1500 kbps、 750 kbps

音訊 – 128kbps

###<a name="option-1-all-tracks-in-one-stream"></a>選項 1︰ 在一個資料流中的所有追蹤

此選項，在單一 encoder 產生所有音訊/視訊追蹤，封裝這些一個分散 MP4 元資料流透過單一 HTTP 文章連線然後會傳送到]。 在此範例中，有只有一個資料流此 live 的簡報︰

![image2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>選項 2︰ 每個追蹤個別資料流中

在此選項，只將其中一個 encoder(s) 追蹤每個片段 MP4 元串流到，張貼的所有資料流在多個不同的 HTTP 連線]。 這可能編碼器一或多個編碼器完成。 從 live ingestion 的觀點來看，此即時簡報被由四個資料流。

![image3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>選項 [3︰ 將一個資料流封裝最低的位元率視訊追蹤與音訊追蹤

在此選項，客戶選擇封裝曲目以一個片段 MP4 元串流到最低的位元率視訊追蹤，而其他兩個視訊追蹤每個正在自己的資料流。 


![image4][image4]


###<a name="summary"></a>摘要

以上顯示的內容，不是這個範例中的所有可能的 ingestion 選項的詳細清單。 為事實上，組成追蹤資料流時將任何已受即時 ingestion。 客戶及 encoder 廠商可以選擇自己的實作根據工程複雜度、 encoder 容量和重複性和容錯移轉考量。 但是請注意在大部分情況下有整個即時簡報的只有一個音訊追蹤，請務必確保 ingest 資料流包含音訊追蹤 healthiness。 考量通常會導致音訊追蹤置於自己的資料流 （如選項 2) 或 （如選項 3) 最低的位元率視訊追蹤與搭售它。 也更好的重複性和容錯能力，兩個不同資料流 (與多餘的音軌選項 2) 傳送相同的音訊追蹤或搭售兩個以上的曲目最低位元率視訊，追蹤 (至少兩個視訊資料流中結合在一起的音訊選項 3) 我們建議 live 內嵌到 Microsoft Azure 媒體服務。

##<a name="7-service-failover"></a>7.服務容錯移轉 

指定的即時資料流的性質，很好的容錯移轉支援會對確保的服務。 Microsoft Azure 媒體服務是設計用來處理的失敗次數，包括網路錯誤、 伺服器錯誤、 存放裝置問題等各種類型。配合即時 encoder 端適當的容錯移轉邏輯中使用時，客戶可達到雲端可靠的即時資料流服務。

在此區段中，我們將討論服務容錯移轉案例。 在此情況下，失敗發生服務中的某處，然後以網路錯誤。 以下是一些建議 encoder 實作處理服務容錯移轉︰


1. 用於建立 TCP 連接 10 的第二個逾時。  如果嘗試建立的連線所需的時間超過 10 秒內，中止作業，然後再試一次。 
2. 傳送 HTTP 要求訊息的區塊中使用簡短逾時。  如果 N 秒的目標 MP4 片段期間，使用 N 與 2N 秒; 之間傳送逾時例如，使用到 12 6 秒的逾時，如果 MP4 片段期間 6 秒。  如果發生逾時，重設連線，開啟新的連線，並繼續串流內嵌在新的連線。 
3. 維護包含已成功且完全傳送至服務的最後兩個片段，為每個追蹤，循環緩衝。  如果 HTTP 文章要求資料流終止或發生逾早於資料流，結尾開啟新的連線開始另一個 HTTP 文章要求、 重新傳送資料流標題、 重新傳送的每個追蹤的最後兩個片段，並繼續資料流不必媒體時間表中的不一致。  這將會減少資料遺失的機率。
4. 建議編碼器不會限制建立連線，或繼續串流之後發生 TCP 錯誤的次數。
5. 之後的 TCP 錯誤︰
    1. 必須關閉目前的連線，和新的 HTTP 文章要求必須建立新的連線。
    2. 新 HTTP 文章 URL 必須是初始文章 URL 相同。
    3. 新的 HTTP 文章必須包含串流標題 （'ftyp 」、 「 Live 伺服器資訊清單方塊 」 及 「 moov 」 方塊） 相同的資料流標頭中最初的文章。
    4. 必須重新傳送已送出每個追蹤的最後兩個片段，並不會引入媒體時間表中的連續串流繼續。  MP4 片段時間戳記，必須持續增加，甚或 HTTP POST 要求。
6. 如果資料不傳送 parallel MP4 片段持續時間與速度，編碼器應該終止 HTTP 文章要求。  不會傳送資料的 HTTP 文章要求可以防止 Azure 媒體服務快速中斷編碼器發生服務的更新。  因此，如 HTTP 文章疏鬆 （ad 訊號） track 應該簡短生活將會變，終止就會立即傳送疏鬆片段。

##<a name="8-encoder-failover"></a>8.encoder 容錯移轉

Encoder 容錯移轉是需要解決的端對端的即時資料流傳送的容錯移轉案例的第二個類型。 在此案例中，錯誤情況發生 encoder 側。 

![image5][image5]


以下是從即時 ingestion 端點的期望，encoder 容錯移轉發生時︰

1. 上述圖表 （3000 k 虛線視訊資料流） 中所示，應才能繼續串流，建立新 encoder 執行個體。
2. 新 encoder 必須使用相同的 URL HTTP 文章要求的失敗的執行個體。
3. 新的 encoder 文章要求必須包含與失敗的執行個體的相同分散的 MP4 頁首方塊。
4. 必須在所有其他執行編碼器相同的即時簡報以產生同步處理的音訊/視訊範例與對齊的片段界限與正確地同步處理新 encoder。
5. 在頁首和片段層級必須語意與上一個資料流相當於並互換新的資料流。
6. 新 encoder 應該嘗試最小化資料遺失。  Fragment_absolute_time 和 fragment_index 的媒體片段應增加編碼器上次停止的點。  Fragment_absolute_time 和 fragment_index 應該增加連續的方式，但可以允許介紹不一致，如有必要。  Azure 媒體服務會忽略片段，它已接收並處理，因此也側邊重傳超過至介紹媒體時間表中的卻片段。 

##<a name="9-encoder-redundancy"></a>9。 重複 encoder 

針對特定的要徑 live，視需要更高可用性和體驗的品質，建議採用作用作用中的重複編碼器，以獲得順暢的容錯移轉不流失資料的事件。

![image6][image6]

如圖所示，有兩個群組的同時將兩份每一個資料流推入即時服務編碼器。 此設定為支援，因為 Microsoft Azure 媒體服務能夠為篩選依據資料流識別碼和片段的時間戳記的重複片段。 產生的即時資料流和封存會以單一複本的所有資料流時的最佳可能彙總來自兩個來源的。 例如，假設特殊情況下，只要有一個 encoder （不一定非得是同一個） 在任何指定的點執行的每個串流的時間，從服務產生的即時資料流會連續而不遺失資料。 

這種情況需求的方式幾乎為 Encoder 容錯移轉的主要編碼器相同同時執行第二個編碼器一組中的例外狀況的大小寫的需求。

##<a name="10-service-redundancy"></a>10.服務重複  

高度多餘的全域分散] 中，有時需要有跨地區備份] 可處理地區損毀。 展開上 「 Encoder 重複 」 拓撲，客戶可以選擇在不同區域的已連結，第 2 編碼器一組有多餘的服務部署。 客戶可能也使用 CDN 提供者部署 GTM （流量全域管理員） 前面流暢地將用戶端傳輸路由的兩個服務部署。 編碼器需求是 「 Encoder 重複 「 第二個編碼器一組需要指向不同 live 只例外狀況的大小寫相同內嵌結束點。 下圖顯示此設定︰

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11。 特殊類型的 Ingestion 格式 

本節中的某些特殊格式類型的即時 ingestion 是設計用來處理某些特定案例。

###<a name="sparse-track"></a>疏鬆追蹤

經驗豐富的用戶端的即時資料流簡報時，通常是必要內容時間同步處理事件或訊號頻內的主要媒體資料。 其中一個範例，這是動態即時廣告插入。 此類型的事件訊號是一般音訊/視訊串流因其疏鬆自然而不同。 換句話說，信號資料通常不會發生持續和間隔可以很難預測。 疏鬆追蹤的概念被專為內嵌和廣播頻內信號資料。

以下是 ingesting 疏鬆追蹤建議的實作︰

1. 建立另一個分散 MP4 元資料流只包含疏鬆 track(s) 不音訊/視訊追蹤。
2. 在 「 Live 伺服器資訊清單方塊 」 所定義 6] 區段中 [1] 中，使用 「 parentTrackName 」 參數至指定的父追蹤名稱。 請參閱的更多詳細資料] 區段中 [1] 的 4.2.1.2.1.2。
3. 「 Live 伺服器資訊清單方塊 」，manifestOutput 必須設定為 「 true 」。
4. 指定疏鬆信號事件的性質，建議︰
    1. 即時事件開頭 encoder 傳送初始的頁首方塊服務會讓登錄疏鬆追蹤用戶端資訊清單中的服務。
    2. 編碼器應該終止 HTTP 文章要求時不傳送資料。  長時間執行 HTTP 文章，不會傳送資料可以防止 Azure 媒體服務快速中斷編碼器服務更新或伺服器重新開機，時為通訊端上接收作業中暫時已封鎖媒體伺服器。 
    3. 期間信號資料時無法使用，編碼器應該關閉 HTTP 文章要求。  編碼器文章要求作用中時，應該傳送資料 
    4. 當傳送疏鬆片段，encoder 可以設定明確內容長度頁首，如果有的話。
    5. 當傳送新的連線疏鬆片段，encoder 應該能如常傳送從後面的新片段的頁首方塊。 這是處理容錯移轉之間有及的位置建立新疏鬆連線至新的伺服器具有不會看見前的疏鬆追蹤的情況。
    6. 疏鬆追蹤片段將可供用戶端時的相對應的父系追蹤片段含有等於或更大的時間戳記值提供用戶端。 如果疏鬆片段有 t 的時間戳記，例如 = 1000年預期之後用戶端看見 （假設上層追蹤名稱視訊） 的視訊片段 1000年的時間戳記或進階，它可以在這裡下載疏鬆片段 t = 1000年。 請注意，實際訊號很好可用於簡報時間表中的不同位置為其指定的目的。 在上述範例中，則可能的 t 的疏鬆片段 = 1000年具有這是在幾秒鐘的位置插入廣告的 XML 內容更新版本。
    7. 內容的疏鬆追蹤片段可以在各種不同的格式 （例如 XML 或文字或二進位等） 取決於不同情況。 


###<a name="redundant-audio-track"></a>重複的音訊追蹤

在一般 HTTP 彈性資料流案例中 （例如平滑串流或虛線），有，通常會在整份簡報中的只有一個音訊追蹤。 不同於其有多個用戶端錯誤情況中選擇的品質等級視訊 track、 如果包含音訊追蹤資料流 ingestion 是失敗的中斷音訊追蹤時，可以是失敗的單一。 

若要解決此問題，Microsoft Azure 媒體服務支援即時 ingestion 的多餘的音軌。 是，相同的音訊追蹤可以傳送多次中不同資料流。 雖然服務會只 register 音訊追蹤一次在用戶端資訊清單中，但卻能夠使用重複的音軌為備份擷取音效片段，如果主索引的音訊追蹤有問題。 若要內嵌多餘的音軌，編碼器您必須︰

1. 在多個片段 MP4 元資料流中建立相同的音訊追蹤。 在頁首和片段層級必須語意相當於完全相同的片段時間戳記，而且互換多餘的音軌。
2. 確定 「 音效 」 Live 伺服器資訊清單 ([1] 中的區段 6) 中的項目是相同的所有重複的音軌。

以下是多餘的音軌的建議的實作︰

1. 傳送資料流中的每個唯一的音訊追蹤本身。  也將重複的資料流傳送的每個位置的第 2 的資料流與第 1 僅限 HTTP 文章 URL 中的識別項這些音訊追蹤資料流: {通訊協定}:// {伺服器位址} / {發佈點 path}/Streams({identifier})。
2. 使用不同的資料流傳送兩個最小的視訊 bitrates。 每個這些資料流也應該包含每個唯一的音訊追蹤的複本。  例如時支援多種語言，這些資料流應該包含的每種語言的音軌。
3. 使用不同的伺服器 （編碼器） 執行個體編碼及傳送重複的資料流中所提及 （1） 和 (2)。 



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 