<properties 
    pageTitle="在 html5 版應用程式與 DASH.js 內嵌 MPEG 虛線調整串流視訊 |Microsoft Azure" 
    description="本文將示範如何在 html5 版應用程式與 DASH.js 內嵌 MPEG 虛線調整串流視訊。" 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>在 html5 版應用程式與 DASH.js 內嵌 MPEG 虛線調整串流視訊

##<a name="overview"></a>概觀

MPEG 破折號是 ISO 標準提供嚴重優點那些人想要傳遞高品質、 調整視訊資料流輸出的視訊內容彈性資料流。 MPEG 破折號，視訊資料流會自動放到較低的定義時變成壅塞網路。 這會減少看到 「 暫停 」 視訊播放程式下載播放 （又稱緩衝） 的下一個幾秒時檢視器的可能性。 降低網路壅塞，當視訊播放程式依次會傳回較高品質資料流。 這項功能所需的頻寬，調整也會導致視訊更快速的開始時間。 這表示，可以播放的前幾秒上手-下載較低品質區段中，最高品質一次足夠的內容的步驟然後已緩衝。

Dash.js 是開啟來源 MPEG 虛線視訊播放程式撰寫 JavaScript。 其目標是提供強大、 跨平台的播放程式可在應用程式中的視訊播放自由重複使用。 它會提供任何支援的瀏覽器 W3C 媒體來源副檔名 (MSE)，目前使用的 Chrome、 Microsoft Edge IE11 （其他的瀏覽器有表示其目的支援 MSE） 中的 MPEG 虛線播放。 Js DASH.js 的詳細資訊，請參閱 GitHub dash.js 存放庫。


##<a name="creating-a-browser-based-streaming-video-player"></a>建立瀏覽器型串流視訊播放程式

若要建立一個簡單的網頁，會顯示與預期的視訊播放程式控制項的這類播放暫停、 播放後自動倒帶等，您需要︰

1. 建立 HTML 頁面
1. 新增視訊
1. 新增 dash.js 播放程式
1. 初始化播放程式
1. 新增一些 CSS 樣式
1. 實作 MSE 在瀏覽器中檢視結果

在剛少數幾行 JavaScript 程式碼可以完成初始化播放程式。 使用 dash.js，其實會在瀏覽器為基礎的應用程式中嵌入 MPEG 虛線視訊的簡單。

##<a name="creating-the-html-page"></a>建立 HTML 頁面

第一步是建立包含**視訊**的項目，請儲存為 basicPlayer.html，此檔案的標準 HTML 頁面，如下列範例所示︰

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>新增 DASH.js 播放程式

若要新增應用程式 dash.js 參考實作，必須擷取從 1.0 dash.js 專案的 dash.all.js 檔案。 此應該會儲存在您的應用程式中的 [JavaScript] 資料夾。 此檔案是便利性檔案的所有必要的 dash.js 程式碼協同成單一檔案。 如果您有查看周圍 dash.js 存放庫時，會找出個別檔案，請測試程式碼等等，但如果您想要執行的是使用 dash.js，然後 dash.all.js 檔案是您的需要。

若要新增應用程式的 dash.js 播放程式，將指令碼標籤新增 basicPlayer.html 的標頭區段︰

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


接下來，建立初始化播放程式頁面載入時函數。 後的載入 dash.all.js 新增下列指令碼︰

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

此函數會先建立 DashContext。 這用來設定在特定執行階段環境的應用程式。 從技術性觀點來看，它會定義建構應用程式時，應使用的相依性注入架構的類別。 在大部分情況下，您會使用 Dash.di.DashContext。

接下來，產生 dash.js 架構，MediaPlayer 主要的類別。 此課程包含的核心方法，例如需要播放與暫停、 管理與視訊的項目關聯和也管理描述播放視訊的媒體簡報描述 (MPD) 檔案的轉譯。

若要確定何時能夠播放視訊播放程式稱為 MediaPlayer 類別的 startup() 函數。 在其他項目此函數可確保的所有必要的類別 （如內容所定義） 已載入。 播放程式後，您可以附加視訊的項目，以便使用 attachView() 函數。 這可讓視訊資料流中插入項目，並控制播放視 MediaPlayer。

讓它知道預期播放視訊的相關，則您可以為了 MediaPlayer MPD 檔案的 URL。要執行完整載入頁面之後必須剛剛建立的 setupVideo() 函數。 使用本文元素的這段執行此動作。 變更您<body>項目︰

    <body onload="setupVideo()">

最後，設定使用 CSS 視訊元素的大小。 在調整串流環境中，這是特別重要因為播放調整變化的網路情況可能會變更播放視訊的大小。 在這個簡單的示範只強制要使用的瀏覽器視窗的 80%頁面的 [標題] 區段中加入下列 CSS 視訊的項目︰
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>播放視訊

若要播放視訊，指向您的瀏覽器，在 basicPlayback.html 檔案，按一下 [播放上顯示的視訊播放程式。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>另請參閱

[開發視訊播放程式的應用程式](media-services-develop-video-players.md)

[GitHub dash.js 存放庫](https://github.com/Dash-Industry-Forum/dash.js) 
