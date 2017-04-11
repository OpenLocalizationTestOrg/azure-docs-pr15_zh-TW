<properties 
    pageTitle="開發視訊播放程式的應用程式" 
    description="主題提供播放程式架構與外掛程式]，您可以使用開發自己的用戶端應用程式，都可以使用媒體服務串流媒體的連結。" 
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


#<a name="develop-video-player-applications"></a>開發視訊播放程式的應用程式

##<a name="overview"></a>概觀

Azure 媒體服務提供的工具，您需要建立豐富的動態的用戶端包括的大部分平台版的播放程式應用程式︰ iOS 裝置、 Android 裝置、 Windows、 Windows Phone、 Xbox 和設定頁首方塊。 本主題也會提供 Sdk，您可以使用開發自己的用戶端應用程式，都可以使用從 Azure 媒體服務串流媒體的播放程式架構的連結。

##<a name="azure-media-player"></a>Azure 媒體播放程式

[Azure Media Player](http://aka.ms/ampinfo)是內建播放媒體內容從 Microsoft Azure 媒體服務各種不同的瀏覽器和裝置上的視訊播放程式網頁。 Azure Media Player 利用業界標準，例如 HTML5、 媒體來源副檔名 (MSE) 及加密媒體副檔名 (EME)，提供 enriched 調整串流體驗。 無法在裝置上，或在瀏覽器中使用這些標準時，Azure 媒體播放程式會使用 Flash 和 Silverlight 為後援技術。 使用播放技術，無論開發人員會有存取 Api 整合的 JavaScript 介面。 這個選項可讓內容由 Azure 媒體服務播放整個範圍的裝置與瀏覽器不含任何額外的工作。

Microsoft Azure 媒體服務可讓內容提供，以虛線、 平滑串流，及 HLS 串流格式播放內容。 Azure 媒體播放程式會考慮下列各種不同的格式，並自動播放最佳根據平台/瀏覽器功能的連結。 Microsoft Azure 媒體服務也可讓 PlayReady 加密的資產的動態加密或 AES 128 位元信封加密。 Azure Media Player 的 PlayReady 解密並 AES 128 位元加密當設定妥當之後的內容。 

如需詳細資訊︰

- [Azure 媒體播放程式](http://aka.ms/ampinfo)
- [Azure 媒體播放程式文件](http://aka.ms/ampdocs) 
- [Azure 媒體播放程式快速入門部落格](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [登入以掌握最新的最新版的從 Azure Media Player](http://aka.ms/ampsignup)
- [新增新的功能要求、 想法、 意見反應](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>其他工具來建立播放程式應用程式

您也可以使用任何的下列 Sdk:

- [平滑串流用戶端 SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [帶有平滑串流的 Windows 市集應用程式](media-services-build-smooth-streaming-apps.md)
- [Microsoft 媒體平台︰ 播放程式架構](http://playerframework.codeplex.com/) 
- [Html5 版播放程式 Framework 文件](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Microsoft 平滑的 OSMF 串流外掛程式](https://www.microsoft.com/download/details.aspx?id=36057) 
- [授權 Microsoft® 平滑串流用戶端移轉套件](http://aka.ms/sspk) 
- [XBOX 視訊的應用程式開發](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>通知

Azure 媒體服務提供支援透過 Windows Media 平台插入廣告︰ 播放程式架構。 Ad 支援的播放程式架構，可用於 Windows 8、 Silverlight、 Windows Phone 8、 和 iOS 裝置。 每個播放程式架構包含範例將示範如何實作播放程式的應用程式。 有三種不同的您可以插入媒體的廣告︰

等差級數 – 暫停主視訊的完整的圖文框廣告

非線性 – 為主要的視訊播放時所顯示的覆疊廣告、 通常標誌或其他靜態圖像放在播放程式

小幫手] – 會顯示播放程式以外的廣告

廣告可以放在主要視訊的時間列上的任一點。 您必須在何時播放 ad，若要播放的廣告告訴播放程式。 這是使用一組標準的 xml 檔案︰ 視訊 Ad 服務範本 (VAST)、 數位影片多個 Ad 播放清單 (VMAP)、 媒體抽象排序範本 （第） 及數位視訊播放程式 Ad 介面定義 (VPAID)。 大量檔案指定要顯示哪些廣告。 VMAP 檔案，指定何時播放各種廣告及包含大量的 XML。 第檔案會在另一種方法其中也可以包含大量 XML 順序廣告。 VPAID 檔案定義的視訊播放程式和 ad 或 ad 伺服器之間介面。 如需詳細資訊，請參閱[插入廣告](https://msdn.microsoft.com/library/dn387398.aspx)。

關閉式輔助字幕和廣告支援 Live 串流影片的相關資訊，請參閱[字幕支援與 Ad 插入標準](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad)。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>另請參閱

[在 html5 版應用程式與 DASH.js 內嵌 MPEG 虛線調整串流視訊](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js 存放庫](https://github.com/Dash-Industry-Forum/dash.js)
 
