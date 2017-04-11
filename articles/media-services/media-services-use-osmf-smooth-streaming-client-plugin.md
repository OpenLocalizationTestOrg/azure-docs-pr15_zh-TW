<properties 
    pageTitle="開啟來源媒體架構平滑串流外掛程式" 
    description="瞭解如何使用 Azure 媒體服務平滑資料流外掛程式 Adobe 開啟來源媒體架構。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>如何使用 Adobe 開啟來源媒體架構串流外掛程式 Microsoft 平滑

##<a name="overview"></a>概觀


開啟來源媒體 Framework 2.0 (SS 的 OSMF) 的 Microsoft 平滑串流外掛程式延伸 OSMF 的預設功能，並新增到新的或現有 OSMF 播放程式的 Microsoft 平滑串流內容播放。 外掛程式也會加入平滑串流播放功能來 Strobe 媒體播放 (SMP)。

SS 的 OSMF 包含兩個版本的外掛程式︰

- 靜態平滑串流的外掛程式，OSMF (.swc)。

- 動態平滑串流的外掛程式，OSMF (.swf)。

這份文件假設讀者一般知識 OSMF 和 OSMF 外掛程式。 如需有關 OSMF 的詳細資訊，請參閱[正式 OSMF 網站](http://osmf.org/)上的文件。

###<a name="smooth-streaming-plugin-for-osmf-20"></a>帶有平滑串流外掛程式 OSMF 2.0

外掛程式支援載入和播放視平滑串流內容下列功能︰

- 視需要平滑串流播放 （播放，暫停，搜尋停駐點）
- 即時平滑串流播放 （播放）
- 即時 DVR 函數 （暫停、 搜尋、 DVR 播放，移至 Live）
- 支援的視訊轉碼器-H.264
- 支援的音訊轉碼器-AAC
- 切換 OSMF 內建的 Api 使用多個音訊語言
- 使用 OSMF 內建 Api 的最大值播放品質選取範圍
- Sidecar 隱藏式輔助字幕與 OSMF 標題外掛程式
- Adobe&reg; Flash&reg; 11.4 或更高的播放程式。
- 此版本僅支援 OSMF 2.0。

## <a name="supported-features-and-known-issues"></a>支援的功能和已知的問題

支援的功能不受支援的功能與已知的問題的完整清單，請參閱[這份文件](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf)。


## <a name="loading-the-plugin"></a>載入增益集
載入 OSMF 外掛程式靜態 （在編譯時間） 或動態 （在執行階段）。 帶有平滑串流 OSMF 下載的外掛程式包含動態和靜態版本。

- 靜態載入︰ 靜態載入，靜態文件庫 (SWC) 檔案是必要。 靜態外掛程式會在編譯時間新增到專案，以及在最後的輸出檔案的合併列印的參考。

- 動態載入︰ 若要動態載入先行 (SWF) 檔案不需要。 動態外掛程式是在執行階段載入，且不包含在專案輸出。 （已編譯的輸出）可以使用 HTTP 和檔案的通訊協定載入動態外掛程式。

如需有關靜態和動態載入的詳細資訊，請參閱正式[OSMF 外掛程式的頁面](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)。

###<a name="ss-for-osmf-static-loading"></a>SS OSMF 靜態載入
下列程式碼片段示範如何靜態 OSMF 載入 SS 外掛程式及播放基本視訊使用 OSMF MediaFactory 類別。 前包括 SS OSMF 程式碼，請確定專案參照包含 「 MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc 」 靜態外掛程式。

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS OSMF 動態載入

下列程式碼片段會顯示如何動態載入 SS 外掛程式的 OSMF 和播放基本視訊使用 OSMF MediaFactory 類別。 包含 OSMF 程式碼 SS 前, 先 「 MSAdaptiveStreamingPlugin-v1.0.3 osmf2.0.swf 的 「 動態外掛程式複製至 [專案] 資料夾，如果您想要載入使用檔案通訊協定，或複製下 HTTP 載入的網頁伺服器。 有不需要在專案參考中包含 「 MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc 」。

 
封裝 {
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe 媒體播放 SS ODMF 動態外掛程式

OSMF 動態外掛程式平滑串流是相容於[Strobe 媒體播放 (SMP)](http://osmf.org/strobe_mediaplayback.html)。 您可以使用 OSMF 外掛程式的 SS 加入 SMP 平滑串流內容播放。 若要這麼做，複製 「 MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf 」 下網頁伺服器，讓 HTTP 載入使用下列步驟︰

1.  瀏覽[Strobe 播放媒體設定] 頁面](http://osmf.org/dev/2.0gm/setup.html)。 
2.  設定 src 平滑串流來源，(例如 http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  進行所需的設定變更，然後按一下 [預覽和更新。
 
    **附註**您的內容的網頁伺服器需要有效的 crossdomain.xml。 
4.  複製並貼上程式碼，以簡單的 HTML 網頁，使用您偏好使用的文字編輯器，例如在下列範例中︰



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. 新增內嵌程式碼平滑串流 OSMF 外掛程式並儲存。

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  儲存您的 HTML 頁面和發佈的網頁伺服器。 瀏覽至發佈的網頁上使用您偏好使用的 Flash&reg;播放程式啟用網際網路瀏覽器 (Internet explorer 是指 Chrome、 Firefox、 等)。
7.  享受平滑串流內容 Adobe&reg; Flash&reg;播放程式。

如需一般 OSMF 開發的詳細資訊，請參閱正式[OSMF 開發頁面](http://osmf.org/resources.html)。

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>另請參閱

[Microsoft 調整 OSMF 更新串流外掛程式](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
