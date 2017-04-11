<properties 
    pageTitle="使用 castLabs Azure 媒體服務進行 Widevine 授權 |Microsoft Azure" 
    description="本文將說明如何使用 Azure 媒體服務 （反飛彈系統） 傳送反飛彈系統與 PlayReady 和 Widevine DRMs 動態加密資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權已傳送 castLabs 授權伺服器。" 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="Mingfeiy;willzhan;Juliako"/>


#<a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>使用 castLabs Azure 媒體服務進行 Widevine 授權

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##<a name="overview"></a>概觀

本文將說明如何使用 Azure 媒體服務 （反飛彈系統） 傳送反飛彈系統與 PlayReady 和 Widevine DRMs 動態加密資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權已傳送**castLabs**授權伺服器。

若要播放串流保護 CENC （PlayReady 及/或 Widevine） 的內容，您可以使用[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。 如需詳細資訊，請參閱[AMP 文件](http://amp.azure.net/libs/amp/latest/docs/)。

下圖示範的高層級的 Azure 媒體服務和 castLabs 整合架構。

![整合](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##<a name="typical-system-set-up"></a>設定一般系統

- 媒體內容會儲存於反飛彈系統。
- 內容索引鍵的金鑰識別碼會儲存在 castLabs 和反飛彈系統。
- castLabs 和反飛彈系統都有內建的權杖驗證。 下列各節討論驗證的權杖。 
- 當用戶端的要求串流視訊時，是以**一般加密**(CENC) 動態加密的內容，以及動態封裝的反飛彈系統平滑串流和虛線。 我們也會提供 PlayReady M2TS HLS 串流通訊協定的基礎資料流加密。
- 從反飛彈系統授權伺服器，擷取 PlayReady 授權，並從 castLabs 授權伺服器，擷取 Widevine 授權。 
- Media Player 自動決定要擷取的授權根據用戶端的平台功能。 

##<a name="authentication-token-generation-for-getting-a-license"></a>驗證的權杖產生的授權

同時 castLabs 和反飛彈系統支援 JWT （JSON Web 權杖），即可授權授權所使用的權杖格式。 

###<a name="jwt-token-in-ams"></a>反飛彈系統中的 JWT 權杖 

下表說明反飛彈系統中的 JWT 權杖。 

發行者|從選擇的發行者字串安全性 Token 服務 (STS)
---|---
對象|使用 STS 對象字串
宣告|一組宣告
NotBefore|開始的權杖
到期|結束有效性的權杖
SigningCredentials|PlayReady 授權伺服器，castLabs 授權伺服器，STS，之間共用的金鑰可能是對稱或非對稱鍵。

###<a name="jwt-token-in-castlabs"></a>在 [castLabs JWT 權杖

下表說明在 castLabs JWT 權杖。 

名稱|描述
---|---
optData|JSON 字串，包含您的相關資訊。 
crt|JSON 字串，包含的資訊資產，其授權的資訊及播放權限。
iat|期間中目前的日期時間。
jti|此 token 加 （每個權杖可以只使用一次 castLabs 系統） 相關的唯一識別碼。

##<a name="sample-solution-set-up"></a>設定的範例方案 

[範例方案](https://github.com/AzureMediaServicesSamples/CastlabsIntegration)包含兩個專案︰

-   可用來設定資產已經 ingested PlayReady 和 Widevine DRM 限制主控台應用程式。
-   Web 應用程式，使傳遞出權杖，可能會被視為非常簡化 STS 版本。


若要使用主控台應用程式︰

1.  變更設定反飛彈系統的認證與 castLabs 認證、 STS 設定共用的金鑰 app.config。
2.  將資產上傳到反飛彈系統。
3.  從上傳的資產，取得 UUID，並變更線條 32 Program.cs 檔案中︰

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  使用 AssetId 命名 castLabs 系統 (Program.cs 檔案中的線條 44) 中的資產。

    您必須設定 AssetId **castLabs**;它必須是唯一的英數字元字串。

5.  執行的程式。


若要使用 Web 應用程式 (STS):

1.  變更設定 castlabs 商店 web.config 識別碼與 STS 設定共用的金鑰。
2.  部署至 Azure 網站。
3.  瀏覽至網站。

##<a name="playing-back-a-video"></a>播放視訊

播放視訊以一般加密 （PlayReady 及/或 Widevine） 加密，您可以使用[Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。 執行中時主控台應用程式，[內容索引鍵識別碼] 和 [資訊清單的 URL 被回應。

1.  開啟新的索引標籤，並啟動您的 STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]。
2.  移至[Azure 媒體播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
3.  在 [串流的 URL 貼上。
4.  按一下 [**進階選項]**核取方塊。
5.  在 [**保護**] 下拉式清單中，選取 [PlayReady 及/或 Widevine。
6.  貼上您要向您 STS 權杖] 文字方塊中的 token。 
    
    CastLab 授權伺服器不需要 「 承載者 = 」 token 前面的前置字元。 因此，請移除的送出權杖前。
7.  更新播放程式。
8.  應該播放視訊。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
