<properties 
    pageTitle="Avanced Media Encoder 進階版工作流程教學課程" 
    description="這份文件包含示範如何執行進階的 Media Encoder 進階版工作流程工作，以及如何建立複雜的工作流程與工作流程設計工具的逐步解說。" 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>進階的 Media Encoder 進階版工作流程教學課程

##<a name="overview"></a>概觀 

這份文件包含顯示如何自訂工作流程與**工作流程設計工具**的逐步解說。 您可以找到的實際工作流程檔案[以下](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)。  

##<a name="toc"></a>目錄

包含下列主題︰

- [將單一位元率 MP4 編碼 MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [開始新的工作流程](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [使用媒體檔案輸入](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [透過檢查媒體串流](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [新增視訊編碼器。MP4 檔案產生器](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [編碼音訊](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [多工音訊和視訊資料流 MP4 容器](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [撰寫 MP4 檔案](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [從輸出檔案建立媒體服務資產](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [測試本機完成的工作流程](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [將 multibitrate MP4s-編碼 MXF 動態包裝啟用](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [新增一或多個額外的 MP4 輸出](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [設定檔案輸出名稱](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [新增不同的音訊追蹤](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [新增。ISM SMIL 檔案](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [編碼 MXF 到 multibitrate MP4-增強的藍圖](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [增強的工作流程概觀](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [檔案命名慣例](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [發佈到根工作流程元件屬性](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [產生輸出檔案名稱依賴已發佈的屬性值](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [加入 multibitrate MP4 輸出的縮圖](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [若要新增的縮圖的工作流程概觀](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [新增 JPG 編碼](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [處理色彩空間轉換](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [撰寫縮圖](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [工作流程中偵測錯誤](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [完成的工作流程](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [以時間為基礎的 trimming multibitrate MP4 輸出的](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [開始加上要調整的工作流程概觀](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [使用資料流修剪器](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [完成的工作流程](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [介紹指令碼的元件](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [工作流程指令碼︰ 認識全球](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [以範圍為基礎的 trimming multibitrate MP4 輸出的](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [開始加上要調整的藍圖概觀](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [使用 [XML 中的 [美工圖案] 清單](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [修改從指令碼元件的 [美工圖案] 清單](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [新增 ClippingEnabled 便利性屬性](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>將單一位元率 MP4 編碼 MXF
 
在此逐步解說，我們要建立單一位元率。AAC 他 MP4 檔案編碼從音訊。輸入的檔案 MXF。 

###<a id="MXF_to_MP4_start_new"></a>開始新的工作流程 

開啟工作流程設計工具，然後選取 「 檔案 」 的 「 新的工作區 「-「 轉碼藍圖 」 

新的工作流程會顯示 3 個項目︰ 

- 主要的來源檔案
- XML 的多媒體項目清單
- 輸出檔案/資產  

![新編碼的工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*新編碼的工作流程*

###<a id="MXF_to_MP4_with_file_input"></a>使用媒體檔案輸入

若要接受我們輸入的媒體檔案，請啟動與新增媒體檔案輸入元件。 若要新增工作流程元件，存放庫的 [搜尋] 方塊中，尋找並拖曳所要的項目拖曳到設計工具窗格。 媒體檔案輸入執行這個動作，並從 [媒體檔案輸入，連線到檔名輸入 pin 的主要的來源檔案元件。

![連線的媒體檔案的輸入](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*連線的媒體檔案的輸入*

我們可以執行許多其他之前，我們必須指出工作流程設計工具我們想要使用設計工作流程使用何種範例檔案。 若要這麼做，請按一下 [設計工具窗格背景並尋找右側的屬性窗格的主要的來源檔案屬性。 按一下資料夾圖示，然後選取所要的檔案，若要測試的工作流程。 一旦完成後請媒體檔案輸入元件會檢查檔案，並填入其輸出 pin，以反映，檢查的檔案。

![填入的媒體檔案的輸入](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*填入的媒體檔案的輸入*

雖然這會指定哪些輸入我們想要使用，不知道還編碼的輸出應該移到的位置。 如何以類似的主要來源檔案已設定好，現在設定輸出變數資料夾的屬性，正下方。

![已設定的輸入與輸出屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*已設定的輸入與輸出屬性*

###<a id="MXF_to_MP4_streams"></a>透過檢查媒體串流

通常，有想要知道如何串流看起來像這樣流透過工作流程。 若要檢查的資料流中的工作流程的任何點，只要按一下輸出或輸入的 pin 上任何元件。 在此情況下，請按一下從我們的媒體檔案輸入壓縮視訊輸出 pin。 一個對話方塊，將會開啟，讓檢查輸出的視訊。

![透過檢查未壓縮的視訊輸出 pin 碼](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*透過檢查未壓縮的視訊輸出 pin 碼*

在我們的情況下，它會告訴我們，例如我們正在處理的 4 秒的 24 畫面 1920 x 1080 輸入︰ 2:2 取樣幾乎 2 分鐘視訊。

###<a id="MXF_to_MP4_file_generation"></a>新增視訊編碼器。MP4 檔案產生器

請注意，現在、 未壓縮的視訊與多個壓縮音訊輸出釘可供使用，我們的媒體檔案輸入。 為了編碼傳入的視訊，我們需要編碼的元件-在這種情況下產生。MP4 檔案。

若要編碼 H.264 的視訊資料流，新增 AVC 視訊 Encoder 元件設計工具介面。 此元件會輸入為解壓縮視訊資料流，且能提供其輸出 pin AVC 壓縮視訊資料流。

![未連線的 AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*未連線的 AVC Encoder*

其內容會決定如何編碼完全發生。 讓我們來看看的一些重要的設定︰

- 輸出寬度] 和 [輸出高度︰ 這些決定編碼視訊的解析度。 我們來使用 640x360 我們大小寫
- 框架率︰ 當設定為 [傳遞，只會採用來源框架工資率，可能是透過覆寫。 請注意，這類畫面播放速率轉換為不移動補償。
- 設定檔和層級︰ 這些決定 AVC 設定檔和層級。 若要此便利地取得不同層級和設定檔的詳細資訊，按一下問號圖示 AVC 影片 Encoder 元件，[說明] 頁面會顯示每個層級的更多詳細。 我們的範例中，我們來使用 3.2 捨位 （預設） 層級的主要設定檔。
- 控制模式和位元率 (kbps) 的比率︰ 我們的情境中操作我們選擇常數位元率 (CBR) 1200 kbps 輸出
- 視訊格式︰ 這是關於寫入到 H.264 資料流 （可能是由增強顯示解碼器但並非正確解碼側資訊） VUI （影片合用性資訊）︰
- NTSC （美國或日本，使用 30 每秒畫面格一般）
- 朋友 （歐洲，使用 25 每秒畫面格一般）
- GOP 大小模式︰ 我們會針對我們的目的使用金鑰間隔的 2 秒鐘與關閉 GOPs 設定固定 GOP 大小。 如此一來，可確保提供動態包裝 Azure 媒體服務與相容性]。

若要摘要我們 AVC encoder，連接壓縮視訊輸出 pin 媒體檔案輸入元件從 AVC 編碼器壓縮視訊輸入 pin。

![連線的 AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*連線的 AVC 主要 encoder*

###<a id="MXF_to_MP4_audio"></a>編碼音訊

現在，我們已編碼視訊，但是原始的未壓縮音訊串流壓縮。 這個我們會使用 AAC 編碼 AAC Encoder (Dolby) 元件。 將其新增至工作流程。

![未連線的 AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*未連線的 AAC encoder*

現在不相容︰ 有時，只單一壓縮音訊輸入的 pin AAC Encoder 從多個可能的媒體檔案輸入會有兩個不同壓縮音訊串流可用︰ 左音訊頻道和權限。 （如果您正在處理環繞音效，這是 6 頻道）。因此，無法直接連接到 AAC 音訊 encoder 媒體檔案輸入來源音訊。 AAC 元件預期所謂的 「 交錯 」 音訊串流︰ 有左邊和右邊頻道與彼此的單一資料流。 我們知道從我們的來源媒體檔案的音軌是在來源中的哪個位置，我們可以產生這類交錯的音訊串流使用向左鍵和向的已指派正確的演講者位置。

第一次一個要從所需的來源音訊頻道產生交錯的資料流。 音訊串流 Interleaver 元件處理這我們。 將其新增至工作流程，然後從媒體檔案輸入的音訊輸出連線到它。

![連線的音訊串流 Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*連線的音訊串流 Interleaver*

現在，我們使用交錯音訊串流，我們仍然未指定指派至左或向右喇叭位置的位置。 如果要指定此問題，我們可以運用喇叭位置 Assigner。

![新增演講者位置 Assigner](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*新增演講者位置 Assigner*

設定用於喇叭位置 Assigner 透過 Encoder 預設篩選器的 「 自訂 」，稱為 「 2.0 （L、 R） 」 通道預設的立體輸入資料流。 （這會指派左的喇叭位置頻道 1 和右喇叭位置頻道 2。）

連線喇叭位置 Assigner 的輸出至 AAC Encoder 的輸入。 然後，告知 AAC 編碼器使用 「 2.0 （L、 R） 」 通道預設，這樣就知道處理做為輸入立體音訊。

###<a id="MXF_to_MP4_audio_and_fideo"></a>多工音訊和視訊資料流 MP4 容器

我們 AVC 指定編碼的視訊資料流和我們 AAC 編碼音訊串流，我們可以擷取到。MP4 容器。 將單一混合不同資料流時的程序稱為 「 多工 」 （或 「 muxing 」）。 在此情況下，我們交錯的音訊及視訊資料流時，在單一保持一致。MP4 套件。 協調此元件。MP4 容器稱為 ISO mpeg-4 多工器。 新增至設計工具的介面並連接 AVC 影片 Encoder 和 AAC Encoder 到其輸入。

![連線的 MPEG4 多工器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*連線的 MPEG4 多工器*

###<a id="MXF_to_MP4_writing_mp4"></a>撰寫 MP4 檔案

在撰寫輸出檔案時，會使用檔案輸出元件。 我們可以連線這的 ISO mpeg-4 多工器輸出，讓它的輸出寫入到磁碟。 若要這麼做，連接容器 (MPEG 4) 輸出釘選到寫入輸入 pin 檔案輸出。

![連線檔案輸出](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*連線檔案輸出*

將使用的檔案名稱取決於檔案內容。 雖然該屬性可能是指定的值的硬式編碼，很可能是一個會想要設定透過運算式。

若要將工作流程自動判定，輸出檔案名稱] 屬性的運算式，請按一下 （資料夾圖示旁） 的檔案名稱旁邊的按鈕。 從下拉式功能表中，按一下 [運算式]。 這會顯示運算式編輯器。 先清除編輯器] 中的內容。

![空白的運算式編輯器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*空白的運算式編輯器*

運算式編輯器，可讓輸入任何文字的值，混合一或多個變數。 變數開頭為貨幣符號。 當您按 $ 鍵時，編輯器] 中會顯示可用的變數選擇在下拉式清單方塊。 在此例中，我們將使用輸出目錄變數和基底輸入的檔案名稱變數的組合︰

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![填滿出運算式編輯器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*填滿出運算式編輯器*

>[AZURE.NOTE]若要查看，請參閱輸出檔案的編碼 Azure 中工作，您必須提供運算式編輯器中的值。 

當您可以按 [確定]，以確認運算式時，[屬性] 視窗會預覽什麼值在目前的檔案屬性會解析。

![檔案運算式解析輸出 dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*檔案運算式解析輸出 dir*

###<a id="MXF_to_MP4_asset_from_output"></a>從輸出檔案建立媒體服務資產

雖然我們撰寫 MP4 輸出檔案，我們仍需要表示此檔案屬於輸出資產媒體服務會產生當做執行此工作流程。 為此，會使用工作流程畫布上的輸出檔案/資產節點。 這個節點將所有內送的檔案，就能產生 Azure 媒體服務資產的一部分。

若要完成工作流程的輸出檔案/資產元件連線檔案輸出元件。

![完成的工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*完成的工作流程*

###<a id="MXF_to_MP4_test"></a>測試本機完成的工作流程

若要測試本機工作流程，按下 [頂端] 工具列中的 [播放] 按鈕。 完成工作流程執行，檢查設定的輸出資料夾中產生的輸出。 您會看到編碼 MXF 輸入的來源檔案中完成的 MP4 輸出檔案。

##<a id="MXF_to_MP4_with_dyn_packaging"></a>將 MP4-multibitrate 編碼 MXF 動態包裝啟用

在此逐步解說我們會建立一組多個位元率 MP4 檔案與 AAC 編碼音訊從單一。輸入的檔案 MXF。

當多重位元率資產輸出想要用於組合 Azure 媒體服務，每個不同的位元率與解析度需要產生的多個 GOP 對齊 MP4 檔案所提供的動態包裝功能。 若要這麼做，請[將單一位元率 MP4 編碼的 MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)逐步解說提供絕佳起點。

![啟動工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*啟動工作流程*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>新增一或多個額外的 MP4 輸出

在我們產生 Azure 媒體服務資產的每個 MP4 檔案會支援不同的位元率與解析度。 現在就讓我們將一或多個 MP4 輸出檔案新增至工作流程中。

若要確保我們已使用相同的設定建立我們視訊編碼器，會比較方便複製現有的 AVC 視訊 Encoder 並設定其他的解析度和位元率 （讓我們來新增 960 x 540 其中一項，以 2,5 Mbps 秒的 25 畫面） 的組合。 若要複製現有 encoder，複製貼在設計工具介面。

連接到壓縮視訊輸出 pin 的媒體檔案輸入到我們新 AVC 元件。

![第二個連線的 AVC encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*第二個連線的 AVC encoder*

現在調整輸出 960 x 540 以 2,5 Mbps 我們新 AVC encoder 的設定。 （使用其內容 」 輸出寬度 」、 「 輸出高度 」 和 「 位元率 (kbps) 」 的）。

提供我們想要使用與 Azure 媒體服務的動態包裝產生的資產，串流端點必須是可從這些 MP4 檔案會完全對齊彼此方式不同 bitrates 之間進行切換的用戶端取得的單一平滑的連續視訊和音訊體驗的 HLS/Fragmented MP4/虛線片段產生。 若要讓發生這種情形，我們需要確認，兩個 AVC 編碼器 GOP （「 群組圖片的 」） 的內容中的兩個 MP4 檔案大小設定為 2 秒鐘，即可︰

- GOP 大小模式設為固定 GOP 大小和
- 索引鍵框架兩秒間隔。
- 也設定關閉 GOP，以確保所有 GOP GOP IDR 控制站自己不相依性

若要讓工作流程方便理解，重新命名第一個 AVC 編碼器 」 AVC 影片 Encoder 640x360 1200 kbps 」 和第二個 AVC 編碼器 」 AVC 影片 Encoder 960 x 540 2500 kbps 」。

現在，新增第二個 ISO mpeg-4 工和第二個的檔案輸出。 將多工器連線到新的 AVC encoder，並確認其輸出導向到檔案輸出。 同時連線 AAC 音訊 encoder 輸出至新多工器的輸入。 檔案輸出依次然後可將其新增至將會建立媒體服務資產的輸出檔案/資產節點連線。

![第二個 Muxer 和檔案輸出連線](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*第二個 Muxer 和檔案輸出連線*

如 Azure 媒體服務動態包裝與相容性，設定多工器的區塊模式 GOP 字數統計] 或 [工期及每區塊 GOPs 設為 1。 （應該是預設值。）

![Muxer 區塊模式](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer 區塊模式*

附註︰ 您可能會想要的任何其他位元率與解析度組合您想要新增至資產輸出重複此程序。

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>設定檔案輸出名稱

我們會有一個以上的單一檔案新增至輸出資產。 這會提供需要確認名為每個輸出檔案會不同於其他並，使其變成檔案名稱中的 [清除您要處理的是不是偶數套用檔案命名慣例。

透過設計工具中的運算式可以控制檔案輸出命名。 開啟 [屬性] 窗格，其中一個檔案輸出元件，並開啟 [檔案] 屬性的運算式編輯器。 我們的第一個輸出檔案已設定好透過下列運算式 （請參閱從[單一位元率 MP4 輸出至 MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)的教學課程）︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

這表示我們的檔案名稱會由兩個變數︰ 輸出目錄撰寫和來源檔案基底名稱。 前者公開為工作流程的根的屬性，後者由內送的檔案。 請注意，輸出目錄是您用於本機測試。這個屬性會覆寫工作流程引擎 Azure 媒體服務中的雲端媒體處理器來執行工作流程時。
若要提供我們輸出檔案一致的輸出命名，變更命名運算式的第一個檔案︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

與第二個︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

執行執行，請確定兩個 MP4 輸出檔案會正確產生中繼測試。

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>新增不同的音訊追蹤

我們會產生.ism 檔案到我們 MP4 輸出檔案時，我們會看到更新版本，我們也會我們彈性的資料流的要求音訊追蹤為純音訊 MP4 檔案。 若要建立此檔案，新增其他 muxer 至工作流程 （ISO mpeg-4 多工器） 並將連接 AAC 編碼器的輸出 pin 其輸入 pin 與追蹤 1。

![新增音訊 Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*新增音訊 Muxer*

建立第三個檔案輸出元件輸出 muxer 的輸出資料流和設定檔案命名為運算式︰
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![建立檔案輸出的音訊 Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*建立檔案輸出的音訊 Muxer*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>新增。ISM SMIL 檔案

動態包裝搭配使用 MP4 檔案 （及純音訊 MP4） 中我們媒體服務資產，因此我們也需要資訊清單的檔案 (也稱為 「 SMIL 」 檔案︰ 同步處理的多媒體整合語言)。 此檔案表示 Azure 媒體服務哪些 MP4 檔案適用於動態包裝和哪些時應考量的音訊串流。 MP4 的一組與單一音訊串流的一般資訊清單檔案看起來像這樣︰
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

.Ism 檔案會包含在切換陳述式，每個個別的 MP4 視訊檔案以及除了這些僅包含音訊 MP4 也一個 （或更多） 的音訊檔案參照的參照。

產生的資訊清單我們的 MP4 的設定檔可以經由元件，稱為 「 反飛彈系統資訊清單作者 」。 將其拖曳至介面，連接反飛彈系統資訊清單作者輸入三個檔案輸出元件 「 撰寫完成 」 的輸出 pin。 請確定連線的反飛彈系統資訊清單作者輸出至輸出檔案/資產。

就跟我們檔案輸出元件，設定.ism 檔案輸出名稱的運算式︰

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

我們已完成的工作流程的外觀如下︰

![完成的 MXF multibitrate MP4 工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*完成的 MXF multibitrate MP4 工作流程*

##<a id="MXF_to__multibitrate_MP4"></a>編碼 MXF 到 multibitrate MP4-增強的藍圖

[上一個工作流程逐步解說](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)中我們看到如何單一的 MXF 輸入的資產可以轉換成輸出資產多重位元率 MP4 檔案，為純音訊 MP4 檔與資訊清單 Azure 媒體服務動態包裝搭配使用的檔案。

如何對部分可以增強型和做更方便，會顯示這個逐步解說。

###<a id="MXF_to_multibitrate_MP4_overview"></a>增強的工作流程概觀

![增強的 Multibitrate MP4 工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*增強的 Multibitrate MP4 工作流程*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>檔案命名慣例

中的上一個工作流程中，我們會指定簡單的運算式為基礎產生輸出檔案名稱。 雖然我們有一些重複︰ 所有個別輸出檔案元件指定這類運算式。

例如，我們檔案輸出元件的第一個的視訊檔案設定使用此運算式︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

時，第二個輸出視訊，我們有運算式，例如︰

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

不是很簡單，更容易且更方便，如果我們無法移除此重複的部分，使項目設定改為使用錯誤？ 我們可以幸運︰ 我們的工作流程的根上建立的自訂屬性的組合的設計工具的運算式功能可讓我們便利性的額外。

例如，假設我們會從 bitrates 個別 MP4 檔案的磁碟機 filename 組態。 我們會在同一個地方 （在我們的圖形的根目錄），其中他們會來存取設定與產生的磁碟機檔案名稱中設定的目標這些 bitrates。 若要這麼做，我們來啟動發佈到的工作流程，根目錄中的兩個 AVC 編碼器位元率屬性，讓它變成可從兩個根目錄以及寄件者的 AVC 編碼器存取。 （即使顯示兩個不同的點，有一個基礎值。）

###<a id="MXF_to__multibitrate_MP4_publishing"></a>發佈到根工作流程元件屬性

開啟第一個 AVC encoder，請位元率 (kbps) 屬性，從下拉式清單中，選擇 [發佈]。

![發佈的位元率] 屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*發佈的位元率] 屬性*

設定 [發佈] 對話方塊，發佈到我們工作流程的圖表的根目錄的 「 video1bitrate 」 的已發佈的名稱，以及 」 影片 1 位元率 」 可讀的顯示名稱。 設定自訂群組名稱稱為 「 串流 Bitrates 」，並按發佈。

![發佈的位元率] 屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*發佈位元率屬性] 對話方塊*

位元率] 屬性的第二個 AVC encoder 重複執行相同，並將其命名為 「 video2bitrate 」 與 「 影片 2 位元率 」，顯示名稱相同的自訂群組 」 串流 Bitrates 」。

現在，我們會檢查工作流程的根屬性，我們將會看到顯示我們自訂群組的兩個已發佈的屬性。 同時會反映其個別 AVC encoder 位元率的值。

![在根工作流程的已發佈的位元率屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

每當我們想要從程式碼或運算式來存取這些屬性，我們可以執行像這樣︰

- 從 [內嵌程式碼，從根的正下方元件︰ node.getPropertyAsString('../ video1bitrate'、 null)
- 在運算式中: ${ROOT_video1bitrate}
 
現在就讓我們發佈我們音訊追蹤位元率它以及完成 「 串流 Bitrates 」 群組。 AAC Encoder 的屬性，內搜尋的位元率設定並從旁邊的下拉式清單中選取 [發佈]。 發佈到該圖表與名稱 「 audio1bitrate 」 的根目錄，並顯示 「 串流 Bitrates 「 我們自訂群組中的名稱 「 音訊 1 位元率 」。

![音訊的位元率發佈] 對話方塊](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*音訊的位元率發佈] 對話方塊*

![在根產生的視訊及音訊屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*在根產生的視訊及音訊屬性*

請注意，變更任何這些三個值也重新設定，變更的值，分別與連結的元件 （和位置從已發佈）。

###<a id="MXF_to__multibitrate_MP4_output_files"></a>產生輸出檔案名稱依賴已發佈的屬性值

而不是硬式編碼我們產生的檔案名稱，我們現在可以變更每個檔案輸出元件依賴我們在圖形根發佈的位元率屬性我們 filename 運算式。 開始使用我們的第一份檔案輸出，找出檔案屬性及編輯的運算式，像這樣︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

可以存取和由正中錢幣符號在鍵盤上，在 [運算式] 視窗中輸入不同的參數，此運算式中。 其中一個可用的參數是我們先前已發佈我們 video1bitrate 屬性。

![存取在運算式中的參數](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*存取在運算式中的參數*

執行我們的第二個視訊檔案輸出效果相同的動作︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

及純音訊檔案輸出︰

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

如果我們現在會變更位元速率任何視訊或音訊檔案，請分別 encoder 會重新設定，然後的位元率為基礎的檔案名稱慣例會接受所有自動]。

##<a id="thumbnails_to__multibitrate_MP4"></a>加入 multibitrate MP4 輸出的縮圖

從產生[multibitrate MXF MP4 輸出輸入](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)工作流程開始，我們將現在會尋找將新增至輸出的縮圖。

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>若要新增的縮圖的工作流程概觀

![若要從開始 Multibitrate MP4 工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*若要從開始 Multibitrate MP4 工作流程*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>新增 JPG 編碼

我們的縮圖產生的核心會 JPG Encoder 元件，能夠輸出 JPG 檔案。

![JPG Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

我們不過直接無法將 JPG encoder 連接媒體檔案輸入我們壓縮視訊資料流。 不過，其預期會收到個別框架。 我們可以透過影片框架門元件執行。

![連線至 JPG 編碼器的圖文框閘道](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*連線至 JPG 編碼器的圖文框閘道*

框架門一次每有這麼多秒或圖文框可讓通過視訊畫面。 間隔時間位移與其中發生這種情況，請設定屬性中。

![視訊的圖文框門屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*視訊的圖文框門屬性*

現在就讓我們建立縮圖每分鐘 60 的間隔時間 （秒） 來設定模式。

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>處理色彩空間轉換

雖然似乎邏輯框架門與媒體檔案輸入兩個壓縮視訊 pin 現在已連線，我們會收到警告，如果我們想要執行此作業。

![輸入的色彩空間的錯誤訊息](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*輸入的色彩空間的錯誤訊息*

這是因為哪些色彩資訊表示的方式我們原始原始壓縮視訊資料流中，我們 MXF，來自不同 JPG Encoder 預期的人員。 更明確地說，所謂 「 色彩空間 」 的 「 RGB 」 或 「 灰階 」 預期會傳送。 這表示視訊的圖文框門傳入的視訊資料流需要擁有轉換，第一次套用有關其色彩的空間。

拖曳到工作流程色彩空間轉換程式-Intel 並將其連線至我們框架閘道。

![連線色彩空間轉換程式](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*連線色彩空間轉換程式*

在 [屬性] 視窗中，挑選 [預設格式] 清單中的 BGR 24 項目]。

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>撰寫縮圖

從我們的 MP4 視訊不同，JPG Encoder 元件會輸出多個檔案。 以處理此問題，可以使用場景搜尋 JPG 檔案作者元件︰ 它會採取的內送的 JPG 縮圖，並將它們，寫的後置有不同的數字的每個檔案名稱。 （數字，通常表示數秒/單位的縮圖所繪製的資料流中。）


![介紹場景搜尋 JPG 檔案作者](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*介紹場景搜尋 JPG 檔案作者*

使用運算式來設定輸出資料夾路徑] 屬性: ${ROOT_outputWriteDirectory} 

並使用 [檔案名稱字首] 屬性︰ 

    ${ROOT_sourceFileBaseName}_thumb_

前置詞會決定如何要命名的縮圖的檔案。 他們會加與數字，表示資料流中的縮圖的位置。


![場景搜尋 JPG 檔案作者屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*場景搜尋 JPG 檔案作者屬性*

連接場景搜尋 JPG 檔案寫入到輸出檔案/資產節點。

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>工作流程中偵測錯誤

原始的未壓縮視訊輸出連線色彩空間轉換程式的輸入。 現在，請執行工作流程的執行的本機測試。 有的機會工作流程會突然停止執行和紅色外框上的元件發生錯誤，表示︰

![色彩空間轉換程式錯誤](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*色彩空間轉換程式錯誤*

按一下右上角的 [請參閱什麼是原因編碼嘗試色彩空間轉換程式元件失敗的小紅色的 「 電子 」 圖示。

![色彩空間轉換程式錯誤] 對話方塊](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*色彩空間轉換程式錯誤] 對話方塊*

事實上，您可以看到，標準色彩空間轉換程式的內送的色彩空間已為 rec601 YUV rgb 我們要求轉換。 明顯並未串流不表示它的 rec601。 （應收帳 601 是編碼交錯類比視訊訊號數位視訊的表單中的標準。 它會指定包含 720 亮度範例，以及每一行的 360 色度範例使用中的區域。 色彩編碼系統稱為 YCbCr 4:2:2。)

若要修正此問題，我們會表示我們正在處理的 rec601 內容我們串流的中繼資料上。 若要這麼做，我們將使用視訊資料類型的更新程式元件，我們會將它放我們原始來源與色彩空間轉換元件之間。 此資料類型的更新程式可讓您手動更新的某些視訊的資料類型的屬性。 將它設定為指出色彩空間標準的 「 應收帳 601 」。 這會導致視訊資料類型更新者，若要標記 」 應收帳 601 」 色彩空格串流，如果尚未定義沒有色彩空間。 （它不會覆寫任何現有的中繼資料，除非覆寫] 核取方塊已核取。）

![在 [資料類型更新程式的更新色彩空間標準](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*在 [資料類型更新程式的更新色彩空間標準*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>完成的工作流程

現在我們我們的工作流程完成後，執行其他的測試執行] 來查看其通過。

![完成的工作流程的多重 mp4 輸出縮圖](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*完成的工作流程的多重 mp4 輸出縮圖*

##<a id="time_based_trim"></a>以時間為基礎的 trimming multibitrate MP4 輸出的

從產生[multibitrate MXF MP4 輸出輸入](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)工作流程開始，我們將現在會尋找將修整根據時間戳記的來源視訊。

###<a id="time_based_trim_start"></a>開始加上要調整的工作流程概觀

![若要新增至修整開始工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*若要新增至修整開始工作流程*

###<a id="time_based_trim_use_stream_trimmer"></a>使用資料流修剪器

將資料流修剪器元件，可讓修剪的開頭和結尾為基礎的預存時間 （秒數，分鐘，...） 的資訊輸入資料流。 修剪器不支援以範圍為基礎的調整。

![資料流修剪器](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*資料流修剪器*

而不是直接在媒體檔案輸入中連結的 AVC 編碼器及喇叭位置 assigner，我們會將放之間的資料流修剪器。 （一個視訊訊號，一個用於交錯音訊訊號。）

![將資料流修剪器之間](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*將資料流修剪器之間*

讓我們來設定修剪器，以便我們只會處理視訊和音訊 15 秒數與視訊中的 60 秒之間。

移至 [視訊資料流修剪器的屬性，然後設定開始時間 (15) 和結束時間 (60s) 屬性。 若要確保同時我們音訊和視訊修剪器永遠設定相同的開始和結束值，我們將發佈至根工作流程。

![發佈從資料流修剪器的開始時間屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*發佈從資料流修剪器的開始時間屬性*

![發佈屬性] 對話方塊的 [開始時間](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*發佈屬性] 對話方塊的 [開始時間*

![發佈屬性] 對話方塊的 [結束時間](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*發佈屬性] 對話方塊的 [結束時間*


現在，我們會檢查我們的工作流程的根目錄，如果這兩個屬性會變成整齊顯示並可設定從該位置。

![在根的可用的已發佈的內容](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*在根的可用的已發佈的內容*

現在音訊修剪器從開啟的調整屬性，並設定運算式參照工作流程的根的已發佈內容的同時開始和結束時間。

修剪音訊的開始時間︰

    ${ROOT_TrimmingStartTime}

以及它的結束時間︰

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>完成的工作流程

![完成的工作流程](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*完成的工作流程*


##<a id="scripting"></a>介紹指令碼的元件

指令碼的元件可以在工作流程的執行階段期間執行任意指令碼。 有四個不同的指令碼可以執行的每個人工作流程生命週期中的位置與特定的特性︰

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

上述各項的更多詳細資料會建立指令碼元件的文件。 在[[動作] 區段](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)中， **realizeScript**指令碼元件用來建構即時 cliplist xml，當工作流程開始。 這個指令碼稱為元件在安裝期間，這種一次在它的週期。


###<a id="scripting_hello_world"></a>工作流程指令碼︰ 認識全球

拖曳到設計介面上的指令碼元件和重新命名 (例如，「 SetClipListXML 」)。

![新增指令碼的元件](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*新增指令碼的元件*

當您檢查執行指令碼元件的屬性時，四種不同的指令碼類型會顯示，每個都可以設定不同的指令碼。

![指令碼的元件屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*指令碼的元件屬性*

清除 processInputScript，然後開啟 realizeScript 的編輯器]。 現在我們正在設定並準備好開始指令碼。

Groovy，保留 java 的相容性的 Java 平台的動態編譯指令碼語言撰寫指令碼。 事實上，大部分的 Java 程式碼是有效的 Groovy 程式碼。

讓我們來撰寫簡單認識全球 groovy 指令碼我們 realizeScript 的內容中。 在編輯器] 中，輸入下列︰

    node.log("hello world");

立即執行本機測試執行。 執行此之後 （透過指令碼元件上的 [系統] 索引標籤） 檢查 [記錄] 屬性。

![認識全球記錄輸出](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*認識全球記錄輸出*

節點物件我們通話記錄方法，是指我們目前的 「 節點 」 或我們內指令碼元件。 每個元件為具有輸出記錄資料，可以透過 [系統] 索引標籤的功能。 在此案例中，我們輸出字串常值"hello world 」。 若要瞭解下面這可以證明準確的偵錯工具，讓您充分上實際做什麼指令碼重要事項。

從在指令碼環境中，我們也可以存取內容的其他元件。 請嘗試︰


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

我們記錄視窗會顯示下列︰

![記錄檔輸出存取節點路徑](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*記錄檔輸出存取節點路徑*


##<a id="frame_based_trim"></a>以範圍為基礎的 trimming multibitrate MP4 輸出的

從產生[multibitrate MXF MP4 輸出輸入](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)工作流程開始，我們將現在會尋找將修整框架計算所根據的來源視訊。

###<a id="frame_based_trim_start"></a>開始加上要調整的藍圖概觀

![工作流程開始新增至修整](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*工作流程開始新增至修整*

###<a id="frame_based_trim_clip_list"></a>使用 [XML 中的 [美工圖案] 清單

在所有上一個工作流程教學課程中，我們可以使用媒體檔案輸入元件為我們的視訊輸入來源。 此特定案例，我們將使用多媒體項目清單來源元件改為。 請注意，這不應該慣用的工作方式。理由執行此作業時，只使用美工圖案的來源清單 (中大小寫，我們要進行的位置下方的多媒體項目清單調整功能的用途)。

若要切換至多媒體項目清單來源我們媒體檔案輸入，拖曳多媒體項目清單來源元件到設計介面並連接多媒體項目清單 XML 釘選至工作流程設計工具] 的多媒體項目清單 XML 節點。 此應填入輸出 pin 與來源多媒體項目清單，根據我們輸入視訊。 現在連線的壓縮視訊 」 和 「 未壓縮音訊的 pin，從個別 AVC 編碼器及音訊串流 Interleaver 來源多媒體項目清單。 現在移除媒體檔案輸入。

![取代來源多媒體項目清單中的媒體檔案輸入](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*取代來源多媒體項目清單中的媒體檔案輸入*

多媒體項目清單來源元件需要做為輸入 「 清單 XML 美工圖案 」。 選取時若要測試使用本機來源檔案，此多媒體項目清單 xml 是為您自動填入。

![自動填入多媒體項目清單 XML 屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*自動填入多媒體項目清單 XML 屬性*

尋找 xml 有點深入瞭解，這是如何它看起來像︰

![編輯多媒體項目清單] 對話方塊](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*編輯多媒體項目清單] 對話方塊*

此但不會反映多媒體項目清單 xml 的功能。 我們的其中一個選項是將兩個視訊及音訊來源] 下，像這樣的 「 調整 」 項目︰

![修剪的項目新增至 [美工圖案] 清單](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*修剪的項目新增至 [美工圖案] 清單*

如果您修改如下上方的多媒體項目清單 xml 並執行本機測試執行時，您會看到視訊正確已修剪視訊 10 到 20 的秒數之間。

相反當您透過執行本機的執行時，會發生什麼情況，此完全相同的 cliplist xml 沒有 Azure 媒體服務中執行的工作流程的相同的效果。 Azure 進階版 Encoder 啟動時，每次，接著再根據輸入編碼工作所指定的檔案，會產生 cliplist xml。 這表示，我們 xml 所做的任何變更會很抱歉覆寫。

若要應付 cliplist xml 定義的編碼工作啟動時，我們可以重新產生，即時只要開始後的工作流程。 這類自訂動作可以採取透過稱為 「 建立指令碼元件]。 如需詳細資訊，請參閱[介紹指令碼元件](media-services-media-encoder-premium-workflow-tutorials.md#scripting)。


拖曳到設計介面上的指令碼元件和重新命名為 「 SetClipListXML 」。

![新增指令碼的元件](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*新增指令碼的元件*

當您檢查執行指令碼元件的屬性時，四種不同的指令碼類型會顯示，每個都可以設定不同的指令碼。

![指令碼的元件屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*指令碼的元件屬性*


###<a id="frame_based_trim_modify_clip_list"></a>修改從指令碼元件的 [美工圖案] 清單

我們可以重新寫入的工作流程啟動時產生 cliplist xml，我們就必須有權存取的 cliplist xml 屬性和內容。 我們可以執行像這樣︰

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![內送的多媒體項目清單記錄](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*內送的多媒體項目清單記錄*

首先，我們需要判斷哪一個點我們想要修剪視訊到哪一個點為止的方式。 方便才能讓工作流程的較不技術使用者，請將該圖表的根目錄發佈兩個屬性。 若要這麼做，請以滑鼠右鍵按一下設計工具的介面並選取 [新增屬性]:

- 第一個屬性: 「 ClippingTimeStart 」 的類型: 「 播放 」
- 第二個屬性: 「 ClippingTimeEnd 」 的類型: 「 播放 」

![新增屬性] 對話方塊的 [剪輯開始時間](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*新增屬性] 對話方塊的 [剪輯開始時間*

![發佈工作流程的根剪輯時間屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*發佈工作流程的根剪輯時間屬性*

設定兩個屬性，以適當的值︰

![設定剪輯開始和結束屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*設定剪輯開始和結束屬性*

現在，我們的指令碼內我們可以從存取這兩個屬性，像這樣︰

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![記錄檔視窗顯示開始和結束的剪輯](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*記錄檔視窗顯示開始和結束的剪輯*

現在就讓我們將更方便使用表單中，使用簡單的規則運算式剖析的時間碼字串︰
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![記錄檔視窗的輸出剖析時間碼](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*記錄檔視窗的輸出剖析時間碼*

我們現在可以修改 cliplist xml，以反映所需的圖文框正確剪輯的影片的開始和結束時間先後此資訊。

![若要新增 trim 元素的指令碼](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*若要新增 trim 元素的指令碼*

這是透過標準字串處理作業。 所產生的已修改的多媒體項目清單 xml 就會寫入 clipListXML 屬性工作流程的根上的 [setProperty] 方法。 之後另一個測試執行中的 [記錄] 視窗會顯示下列動作︰

![記錄產生的多媒體項目清單](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*記錄產生的多媒體項目清單*

執行以查看視訊及音訊串流已裁剪的如何測試執行。 當您將會執行有不同的值，調整點的多個測試執行，您會發現的人員會不列入不過 ！ 原因是，設計工具中的，不同於 Azure 執行階段，不會覆寫 cliplist xml 每執行。 第一次您設定並結束點，只會導致轉換，所有其他的時間，我們防衛隊子句 xml 這表示 (如果 (clipListXML.indexOf (「<trim>") = =-1)) 會防止工作流程已經有一個簡報時，新增另一個剪輯的項目。

若要讓工作流程方便本機測試，我們最佳新增盡可能地壓縮如果修剪的項目已經有一些屋保留程式碼。 如果是這樣，我們可以將其移除，先修改 xml 有新的值。 而不是使用純文字字串操作，可能會先會執行此動作透過剖析的實際 xml 物件模型。

我們可以透過新增這類程式碼之前，我們需要先新增匯入陳述式的數字開頭的指令碼︰
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

之後，我們可以新增必要的清理程式碼︰

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
這個程式碼會上方用以新增 trim 元素 cliplist xml 的點。

現在，我們可以執行，並修改工作流程時一樣，我們希望時無法將變更套用時的時間。    

###<a id="frame_based_trim_clippingenabled_prop"></a>新增 ClippingEnabled 便利性屬性

當您永遠可以修剪發生，讓我們來關閉工作流程來完成新增方便的布林旗標表示我們要啟用修整 / 剪輯。

就之前，發佈新名為 「 ClippingEnabled 」 工作流程的根屬性的類型 」 布林值 」。

![發佈啟用剪輯的屬性](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*發佈啟用剪輯的屬性*

使用下方簡單防衛隊子句，我們可以核取 [如果是必要的調整，並決定是否我們的多媒體項目清單為需要或不修改。

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>完整的程式碼

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>另請參閱 

[介紹編碼 Azure 媒體服務的進階版](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[如何使用編碼方式 Azure 媒體服務的進階版](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Azure 媒體服務編碼視內容](media-services-encode-asset.md#media_encoder_premium_workflow)

[媒體 Encoder 進階版工作流程格式及轉碼器](media-services-premium-workflow-encoder-formats.md)

[範例工作流程檔案](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure 媒體服務總管] 工具](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
