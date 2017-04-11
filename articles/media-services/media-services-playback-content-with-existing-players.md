<properties 
    pageTitle="播放您的內容 |Microsoft Azure" 
    description="本主題列出現有的播放程式，您可以使用來播放您的內容。" 
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
    ms.date="10/12/2016" 
    ms.author="juliako"/>


#<a name="playing-your-content-with-existing-players"></a>播放您使用現有的播放程式的內容

Azure 媒體服務支援許多熱門串流格式，例如平滑串流、 串流 HTTP Live，和 MPEG 虛線。 本主題會指向您現有的播放程式可用來測試您的資料流。

>[AZURE.NOTE]若要播放動態封裝或是動態加密的內容，請務必以取得您要傳送您的內容串流端點至少有一個串流單位。 縮放比例串流單位的相關資訊，請參閱︰[如何調整串流單位](media-services-portal-manage-streaming-endpoints.md)。

### <a name="the-azure-portal-media-services-content-player"></a>Azure 入口網站媒體服務內容播放程式

**Azure**入口網站提供內容，您可以使用以測試您的視訊播放程式。

按一下所要的視訊 （請確定它已[發佈](media-services-portal-publish.md)），按一下 [**播放**] 按鈕，底部的入口網站。

一些事項︰

- **媒體服務內容 PLAYER**播放從預設的串流結束點。 如果您想要從非預設串流端點，請使用其他的播放程式。 例如， [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。


![AMSPlayer][AMSPlayer]

###<a name="azure-media-player"></a>Azure 媒體播放程式

使用[Azure 媒體播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)來播放您的內容 （清除或受保護） 任一下列格式︰

- 帶有平滑串流
- MPEG 破折號
- HLS
- 漸進 MP4


###<a name="flash-player"></a>Flash Player

####<a name="aes-encrypted-with-token"></a>AES 加密與權杖

[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

###<a name="silverlight-players"></a>Silverlight 播放程式

####<a name="monitoring"></a>監控

[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

####<a name="playready-with-token"></a>與權杖 PlayReady

[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>虛線播放程式

[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

###<a name="other"></a>其他

若要測試 HLS Url，您也可以使用︰

- 在 iOS 裝置上的**safari**或
- 在 Windows 上**3ivx HLS 播放程式**。

##<a name="developing-video-players"></a>開發視訊播放程式

如需如何開發您自己的播放程式的資訊，請參閱[開發視訊播放程式](media-services-develop-video-players.md)




##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
