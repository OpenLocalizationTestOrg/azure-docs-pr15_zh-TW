<properties 
    pageTitle="概觀︰ 使用 Azure 媒體服務 Live 熱氣騰騰 |Microsoft Azure" 
    description="本主題提供即時熱氣騰騰使用 Azure 媒體服務的概觀。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Live 熱氣騰騰使用 Azure 媒體服務的概觀

##<a name="overview"></a>概觀

進行即時 Azure 媒體服務串流事件時通常涉及下列元件︰

- 用來廣播事件的 [相機]。
- 訊號的相機鏡頭轉換會傳送至即時串流服務的資料流即時視訊 encoder。

    您也可以多個即時同步處理的時間編碼器。 針對特定的要徑 live，視需要更高可用性和體驗的品質，建議採用主動-主動多餘編碼器時間同步處理以獲得順暢的容錯移轉不流失資料的事件。
- 即時串流服務可讓您執行下列動作︰
    
    - 內嵌即時使用各種即時串流通訊協定 （例如 RTMP 或平滑串流） 的內容
    - （選擇性） 將調整位元率串流編碼您的資料流
    - 預覽您的即時資料流
    - 錄製並儲存 ingested 的內容才能串流處理更新版本 (Video-on-Demand)
    - 經由一般串流通訊協定 （例如，MPEG 虛線平滑、 HLS、 HDS） 的內容直接給客戶，或傳送至內容傳遞網路 (CDN) 進一步分配。


**Microsoft Azure 媒體服務**反飛彈 （系統） 提供內嵌、 編碼、 預覽、 儲存及發表即時串流內容的能力。

您的內容為 [客戶] 時您的目標是以不同的裝置在不同的網路的情況下發表高品質視訊。 若要達到此目標，使用即時編碼器編碼您的資料流多重位元率 （自動調整位元率） 視訊資料流。  若要注意串流不同的裝置上，使用媒體服務[動態包裝](media-services-dynamic-packaging-overview.md)動態重新封裝不同的通訊協定您資料流。 媒體服務支援下列調整位元率串流技術的傳遞︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

Azure 媒體服務、**頻道**、**程式**及**StreamingEndpoints**處理所有即時串流功能包括 ingest，格式設定，DVR、 安全性、 延展性和重複。

**頻道**代表的管線處理即時串流的內容。 頻道可接收即時輸入資料流時，下列方式︰

- 內部部署住頻道設定為**通過**傳遞 encoder 傳送多重位元率**RTMP**或**平滑串流**(分散 MP4)。 **通過**傳遞時，ingested 資料流時沒有任何進一步處理通過**頻道**s。 您可以使用輸出多重位元率平滑串流的下列即時編碼器︰ 元素，Envivio，Cisco。  下列即時編碼器輸出 RTMP: Adobe Flash 媒體 Live Encoder (FMLE)、 Telestream Wirecast 和 Tricaster 轉錄。  即時 encoder 也可以將單一位元率資料流傳送到頻道的未啟用即時編碼，但不是建議使用的。 要求時，媒體服務會提供給客戶資料流。

    >[AZURE.NOTE] 使用傳遞的方法是最節省至正在執行多個事件一段長時間，然後您已經有投資在內部部署編碼器執行即時串流的方式。 請參閱[定價](/pricing/details/media-services/)詳細資料。
    
    
- 內部部署即時 encoder 傳送給執行即時編碼方式和下列格式的其中一種媒體服務啟用該頻道的單一位元率串流︰ RTMP 或平滑串流 (分散 MP4)。 同時支援 RTP (MPEG TS)，如果您具備 Azure 資料中心的專用的連線。 使用此類型的頻道已知的 RTMP 輸出的下列即時編碼器︰ Telestream Wirecast、 FMLE。 頻道，然後執行即時編碼的內送至多重-位元率 （自動調整） 視訊資料流單一位元率資料流。 要求時，媒體服務會提供給客戶資料流。


當您建立頻道時，請開始媒體服務 2.10 的版本，您可以指定您要收到輸入資料流頻道的方式，以及您要執行您的資料流即時編碼頻道。 您有兩個選項︰

- **無**（通過） – 指定此值，如果您打算使用內部部署即時編碼器可將輸出多重位元率串流 （傳遞資料流）。 在此情況下，內送的資料流傳遞至輸出不含任何編碼。 這是 2.10 發行之前頻道的行為。  

- **標準**-選擇此值，如果您打算使用媒體服務編碼多重位元率串流您單一位元率即時資料流。 這個方法是經濟型快速縮放的不常用的事件。 請注意，即時編碼帳單影響您應該記得離開即時編碼頻道中的 「 執行中 」 的狀態會對帳單的費用。  建議您即時串流事件避免額外下限費用完成後，立即停止您執行的頻道。 

##<a name="comparison-of-channel-types"></a>頻道類型的比較

下表提供輔助線，來比較兩個頻道類型支援的媒體服務

功能|通過頻道|標準頻道
---|---|---
單一位元率輸入編碼到雲端中的多個 bitrates|無|[是]
最大的解析度，數字的圖層|1080p、 8 的圖層，60 + 每秒畫面格|720p，6 的圖層，30 每秒畫面格
輸入的通訊協定|RTMP，平滑串流|RTMP 平滑串流、 與 RTP
價格|查看 [[價格] 頁面](/pricing/details/media-services/)，然後按一下 [Live 視訊] 索引標籤上|請參閱 [[價格] 頁面](/pricing/details/media-services/) 
執行階段的最大值|24 x 7|8 小時
插入 slates 的支援|無|[是]
支援 ad 訊號|無|[是]
通過 CEA 608/708 標題|[是]|[是]
能夠在摘要的比重簡短隔間修復|[是]|不 （頻道會開始輸入的資料沒有 6 + 秒後 slating）
不一致的支援輸入 GOPs|[是]|否-輸入必須修正 2 sec GOPs
變數框架工資率輸入支援|[是]|否-輸入必須固定框架工資率。<br/>次要變化則容許，例如，在高移動場景。 但 encoder 無法放到 10 的圖文框秒。
自動-關閉器時輸入頻道的摘要會遺失|無|12 小時後，如果沒有執行的程式 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>使用多重位元率即時資料流接收內部部署編碼器 （通過） 的頻道

下圖顯示相關的主要部分反飛彈系統平台的**通過**工作流程中。

![即時工作流程](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

如需詳細資訊，請參閱[使用該接收多重頻道-內部部署編碼器位元率即時資料流](media-services-live-streaming-with-onprem-encoders.md)。

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>使用已啟用執行即時編碼方式和 Azure 媒體服務的頻道

下圖顯示相關的主要部分反飛彈系統平台的頻道執行即時編碼方式和媒體服務分開的 Live 串流工作流程中。

![即時工作流程](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

如需詳細資訊，請參閱[使用來執行即時編碼與 Azure 媒體服務啟用的頻道](media-services-manage-live-encoder-enabled-channels.md)。

##<a name="description-of-a-channel-and-its-related-components"></a>頻道和及其相關的元件的描述

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


##<a name="billing-implications"></a>帳單寄送的影響

頻道開始計費一經透過 API 是狀態轉換為 [執行]。  

下表顯示如何頻道狀態對應到帳單 API 和 Azure 入口網站中的狀態。 請注意，州稍有不同的 API 與入口網站經驗之間 一旦頻道為 「 使用 」 狀態透過 API，或在 Azure 入口網站的 「 準備 」 或 「 串流] 狀態，計費會變成作用中。

若要停止進一步計費您的頻道，必須停止透過 API 或 Azure 入口網站中該頻道。
您負責停止您的頻道，當您完成該頻道。 若要停止頻道會造成持續帳單。

>[AZURE.NOTE]使用標準頻道工作時，反飛彈系統會自動關閉器仍處於 「 執行中 」 狀態 12 小時後輸入的摘要會遺失，而且沒有程式執行任何頻道。 不過，您仍計費頻道是 「 執行中 」 狀態的時間。

###<a id="states"></a>頻道狀態，以及如何對應到帳單模式 

目前狀態的頻道。 可能的值包括︰

- **已停止]**。 這是該頻道的初始狀態之後建立 （除非自動啟動已選取入口網站中）。不計費就會發生此狀態。 在這個狀態，可以更新頻道內容，但不是允許串流。
- **啟動**。 正啟動頻道。 不計費就會發生此狀態。 更新或串流允許在此狀態。 如果發生錯誤，頻道會傳回停止狀態。
- **執行**。 頻道並處理即時資料流。 現在，它計費使用方式。 您必須停止以防止進一步計費頻道。 
- **停止**。 正在停止頻道。 不計費就會發生此暫時性的狀態。 更新或串流允許在此狀態。
- **刪除**。 正在刪除該頻道。 不計費就會發生此暫時性的狀態。 更新或串流允許在此狀態。

下表顯示如何頻道狀態對應到帳單模式。 
 
頻道狀態|入口網站的使用者介面指標|這是計費？
---|---|---
啟動|啟動|沒有 （暫時性狀態）
執行|準備 （沒有執行程式）<br/>或<br/>串流 （至少有一個執行程式）|[是]
停止|停止|沒有 （暫時性狀態）
停止|停止|無


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>相關的主題

[Azure 媒體服務分散的 MP4 Live 內嵌規格](media-services-fmp4-live-ingest-overview.md)

[使用 [啟用來執行即時編碼 Azure 媒體服務與頻道](media-services-manage-live-encoder-enabled-channels.md)

[使用多重位元率即時資料流接收內部部署編碼器的頻道](media-services-live-streaming-with-onprem-encoders.md)

[配額和限制](media-services-quotas-and-limitations.md)。  

[媒體服務的概念](media-services-concepts.md)
