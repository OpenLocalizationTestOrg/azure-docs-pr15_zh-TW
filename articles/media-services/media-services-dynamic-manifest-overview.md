<properties 
    pageTitle="篩選和動態資訊清單 |Microsoft Azure" 
    description="本主題說明如何建立篩選，讓您的用戶端可以使用這些資料流特定章節資料流。 媒體服務所建立的封存此串流選擇性的動態資訊清單。" 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="cenkdin;juliako"/>

# <a name="filters-and-dynamic-manifests"></a>篩選和動態資訊清單

開始使用 2.11 發行，媒體服務可讓您定義您的資產的篩選。 這些篩選有伺服器端規則，讓您的客戶選擇項目，例如︰ 播放只有一段視訊 （而非播放整個視訊），或指定的客戶的裝置可處理 （而非所有轉譯資產與相關聯的） 的音訊和視訊轉譯子集。 透過串流視訊，根據指定的篩選器的客戶的要求時所建立的**動態資訊清單**s 封存此篩選您的資產。

本主題探討常見的案例中使用篩選器就會很有幫助您的客戶，以及將示範如何以程式設計方式建立篩選的主題的連結 （目前，您可以建立篩選 REST Api 僅限）。

##<a name="overview"></a>概觀

您的內容為 [客戶] （串流即時事件或 video-on-demand） 時您的目標是以不同的裝置在不同的網路的情況下發表高品質視訊。 若要達到此目標，請執行下列動作︰

- 編碼您的資料流多重位元率 ([自動調整位元率](http://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) 視訊資料流 （這會負責品質和網路條件） 和 
- 您可以使用媒體服務[動態包裝](media-services-dynamic-packaging-overview.md)動態重新封裝您串流到不同的通訊協定 （這會負責串流不同的裝置上）。 媒體服務支援下列調整位元率串流技術的傳遞︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。 

###<a name="manifest-files"></a>資訊清單的檔案 

當您編碼資產調整位元率資料流時，會建立**資訊清單**（播放清單） 的檔案 （檔案是以文字為基礎，或以 XML 為基礎）。 **資訊清單**檔案包含串流中繼資料，例如︰ 追蹤類型 （音訊、 視訊或文字）、 追蹤名稱、 開始和結束時間、 位元率 （品質）、 追蹤語言] 簡報視窗 （固定工期的滑動視窗）、 視訊轉碼器 (FourCC)。 也會指示要擷取的下一個片段提供的下一步可播放視訊片段可用和其位置的相關資訊的播放程式。 片段 （或區段） 的實際 」 」 的區塊將視訊的內容。


資訊清單檔案的範例如下︰ 

    
    <?xml version="1.0" encoding="UTF-8"?>  
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">
    
    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />
    
    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    
    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />
    
    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>
    
    </SmoothStreamingMedia>
    
###<a name="dynamic-manifests"></a>動態資訊清單

當您的用戶端需要更多的彈性，比描述的預設資產的資訊清單檔案時，有[案例](media-services-dynamic-manifest-overview.md#scenarios)。 例如︰

- 特定的裝置︰ 發表只指定的轉譯和 \ 或指定語言曲目所支援的裝置，用來播放 （「 轉譯篩選 」） 的內容。 
- 減少資訊清單，以顯示子即時事件 （「 子多媒體項目篩選 」） 的美工圖案。
- 修剪視訊 （「 修剪視訊 」） 的開頭。
- 調整 [簡報] 視窗 (DVR) 才能提供有限的播放程式 （「 調整簡報視窗 」） DVR 視窗的長度。
 
若要達到此大的彈性，媒體服務會提供**動態資訊清單**根據預先定義的[篩選](media-services-dynamic-manifest-overview.md#filters)。  一旦您定義篩選時，您的用戶端可以使用這些串流特定的轉譯或視訊的子多媒體項目。 他們可以串流 URL 中指定篩選器。 無法套用篩選，以調整串流支援[動態包裝](media-services-dynamic-packaging-overview.md)的通訊協定的位元率︰ HLS、 MPEG 破折號、 平滑串流、 和 HDS。 例如︰

MPEG 虛線 URL，以篩選

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

帶有平滑串流的 URL，以篩選

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


如需有關如何傳送您的內容和建立串流 Url 的詳細資訊，請參閱[傳送內容概觀](media-services-deliver-content-overview.md)。


>[AZURE.NOTE]請注意的動態資訊清單不會變更資產和該資產的預設資訊清單。 您的用戶端可以選擇要求資料流包含或不含篩選。 


###<a id="filters"></a>篩選 

有兩種類型的資產篩選︰ 

- 全域篩選可套用至 Azure 媒體服務帳戶中的任何資產 (擁有帳戶的生命週期） 和 
- 本機篩選 （只能套用至資產的篩選是在建立關聯，有資產的存留時間）。 

全域及本機篩選類型有完全相同的屬性。 兩個主要的差異是哪一個分析藍本何種類型的風格是更適合。 全域篩選通常適用的裝置 （轉譯篩選） 的設定檔位置本機篩選來修剪特定的資產。


##<a id="scenarios"></a>常見的案例 

如前述之前，您的內容為 [客戶] （串流即時事件或 video-on-demand） 時您的目標是以不同的裝置在不同的網路的情況下發表高品質視訊。 此外，您可能會有其他涉及篩選您的資產，以及使用的**動態資訊清單**s 的需求。 下列各節提供不同的篩選案例的概觀。

- 指定音訊及視訊 （而非所有轉譯資產與相關聯的） 可以處理的特定裝置的轉譯的子集。 
- 播放視訊 （而非播放中的整個視訊） 的節。
- 調整 DVR 簡報] 視窗。

##<a name="rendition-filtering"></a>轉譯篩選 

您可以選擇要編碼多個編碼設定檔 H.264 比較基準、 H.264 高、 AACL、 AACH （Dolby 數位加號） 和多個品質 bitrates 您資產。 不過，並非所有用戶端裝置會支援所有資產的設定檔及 bitrates。 例如，較舊的 Android 裝置只支援 H.264 比較基準 + AACL。 傳送較高的 bitrates 裝置無法取得優點，浪費頻寬與裝置的計算。 這種裝置必須解碼所有指定的資訊，只是要縮小顯示。

使用動態資訊清單中，您可以建立裝置設定檔如行動電話，主控台，HD/SD 等，包含追蹤和您想要每一個設定檔的組件的品質。

 
![轉譯篩選的範例][renditions2]

在下列範例中，編碼器用來將轉譯 （從 180 p 1080p) 七 ISO MP4s 視訊編碼 mezzanine 資產。 編碼的資產動態封裝任何下列串流通訊協定︰ HLS、 平滑、 MPEG 破折號和 HDS。  沒有篩選器與資產的 HLS 資訊清單會顯示在圖表的頂端，（包含所有七轉譯）。  在左下角，顯示要套用篩選，名為 「 ott 」 HLS 資訊清單。 若要移除所有 bitrates 1Mbps，導致下兩個品質層級要在回應中去除下方指定 「 ott 」 篩選。  在右下方，顯示要套用篩選，名為 「 行動 」 的 HLS 資訊清單。 若要移除的轉譯解析度大於 720p 產生兩個指定的 「 行動 」 篩選正在中去除 1080p 轉譯。

![轉譯篩選][renditions1]

##<a name="removing-language-tracks"></a>移除語言追蹤

您的資產可能會包含多個音訊的語言，例如英文、 西班牙文、 法文等等。通常，播放程式 SDK 管理員的預設音訊追蹤選取範圍，並使用音訊追蹤每個使用者選取項目。 開發這類播放程式 Sdk 挑戰，需要不同的實作特定裝置的播放程式架構跨。 此外，在某些平台上播放程式 Api 限於與不包含音訊的選取範圍功能，使用者無法選取或變更預設的音訊追蹤的位置。 資產篩選，您可以藉由建立只包含您要的音訊語言的篩選控制行為。

![篩選的語言追蹤][language_filter]


##<a name="trimming-start-of-an-asset"></a>修整資產的開頭 

在大部分的即時資料流事件，運算子會執行實際在事件發生前的一些測試。 例如，它們可能包含前事件的開始著手像這樣: 「 程式會立即開始]。 如果封存程式，測試青資料也封存，並將會包含在簡報中。 不過，這項資訊不應該顯示在用戶端。 使用動態資訊清單中，您可以建立開始時間] 篩選，並從資訊清單移除不想要的資料。

![修整開始][trim_filter]

##<a name="creating-sub-clips-views-from-a-live-archive"></a>從 live 封存建立子多媒體項目 （檢視）

許多即時事件長時間執行，而且即時封存可能會包含多個事件。 即時事件後結束廣播者可能會想要分割成邏輯程式啟動即時封存] 或 [停止順序。 然後，分別文章處理即時封存不需要建立個別的資產 （這不會在現有的快取片段的好處中 Cdn） 不發佈這些虛擬的程式。 這類虛擬程式 （子多媒體項目） 的範例是足球或籃球遊戲，在棒球中，innings 季的奧運程式下午的個別的事件。

使用動態資訊清單中，您可以建立使用開始/結束時間的篩選，並移至您的即時封存頂端建立虛擬的檢視。 

![Subclip 篩選][subclip_filter]

篩選的資產︰

![滑雪][skiing]

##<a name="adjusting-presentation-window-dvr"></a>調整 [簡報] 視窗 (DVR)

目前，Azure 媒體服務可提供循環封存其中設定持續時間之間 5 分鐘-25 個小時。 資訊清單篩選可以用來建立循環 DVR 視窗頂端的封存中，但不刪除媒體。 有許多案例廣播者想要提供哪些移動即時的邊緣，同時保留在較大的封存視窗有限的 DVR 視窗的位置。 廣播者可能會想要使用的資料，超出 DVR 視窗醒目提示的多媒體項目，或 he\she 可能會想要針對不同的裝置提供不同的 DVR 視窗。 例如，大部分的行動裝置不處理大型 DVR windows （適用於桌面用戶端可以有 2 分鐘 DVR 視窗適用於行動裝置與 1 小時）。

![DVR 視窗][dvr_filter]

##<a name="adjusting-livebackoff-live-position"></a>調整 LiveBackoff （即時位置）

資訊清單篩選可以用來移除即時程式的即時邊緣幾秒鐘的時間。 這個選項可讓廣播者觀看預覽出版物點上的簡報，並建立廣告插入點之前檢閱者接收 （通常備份-關閉 30 秒） 資料流。 廣播者可以發送這些廣告到他們的用戶端架構時間才收到然後程序之前廣告商機的資訊。

除了的廣告支援、 LiveBackoff 可以用於調整用戶端即時下載位置，以便用戶端沒有，然後按下即時邊緣他們仍然可以存取片段從伺服器，而不是發生 404 或 412 HTTP 錯誤。

![livebackoff_filter][livebackoff_filter]


##<a name="combining-multiple-rules-in-a-single-filter"></a>合併多個規則，在單一的篩選

您可以結合多個篩選的規則，在單一篩選中。 例如，您可以定義要移除即時封存著手，也篩選可用 bitrates 範圍規則。 多個篩選的規則的結果是這些規則的組合 （僅限交集處）。

![多個規則][multiple-rules]

##<a name="create-filters-programmatically"></a>以程式設計方式建立篩選

下列主題探討篩選相關的媒體服務項目。 本主題也會說明如何以程式設計方式建立篩選。  

[使用 REST Api 建立篩選](media-services-rest-dynamic-manifest.md)。

## <a name="combining-multiple-filters-filter-composition"></a>合併多個篩選 （篩選撰寫中）

您也可以合併多個篩選器中的單一的 URL。 

下列案例說明您可能會想要合併篩選︰

1. 您要篩選您的行動裝置，例如 Android 或 iPAD 的視訊品質 （若要限制視訊品質）。 若要移除不想要的品質，您會建立適用於裝置設定檔的全域篩選器。 如上所述，全域篩選可用於進一步關聯不相同的媒體服務帳號] 之下的所有的資產。 
2. 您也要修剪資產的開始和結束時間。 若要達到此目標，您會建立本機篩選，然後設定開始/結束時間。 
3. 您想要合併兩個這些篩選器 （不含您想要加入修整篩選器會讓篩選使用難以品質篩選 （組合）。

若要合併篩選，您需要設定為資訊清單/播放清單的 [篩選名稱以分號分隔的 URL。 例如，假設您有名為*MyMobileDevice*篩選品質，而且您有另一個命名的*MyStartTime*若要設定特定的開始時間篩選。 您可以結合像這樣︰

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

您可以結合最多 3 的篩選。 

如需詳細資訊，請參閱[此](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)部落格。


##<a name="know-issues-and-limitations"></a>知道問題和限制

- 動態資訊清單運作 GOP 界限 （鍵框架），因此修整具有 GOP 精確度。 
- 您可以使用相同本機和全域篩選的篩選名稱。 請注意，局部篩選優先順序，並會覆寫全域篩選。
- 如果您要更新的篩選，可能需要重新整理規則串流端點最多 2 分鐘。 如果內容提供一些篩選服務 （和快取中 proxy 和 CDN 快取），則更新這些篩選會導致播放程式失敗。 建議後更新篩選清除快取。 如果不可行這個選項，請考慮使用不同的篩選。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>另請參閱

[將內容傳遞給客戶概觀](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
 