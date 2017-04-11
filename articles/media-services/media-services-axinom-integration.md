<properties 
    pageTitle="使用 Axinom Azure 媒體服務進行 Widevine 授權 |Microsoft Azure" 
    description="本文將說明如何使用 Azure 媒體服務 （反飛彈系統） 傳送反飛彈系統與 PlayReady 和 Widevine DRMs 動態加密資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權已傳送 Axinom 授權伺服器。" 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"   
    ms.author="willzhan;Mingfeiy;rajputam;Juliako"/>

#<a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>使用 Axinom Azure 媒體服務進行 Widevine 授權  

> [AZURE.SELECTOR]
- [castLabs](media-services-castlabs-integration.md)
- [Axinom](media-services-axinom-integration.md)

##<a name="overview"></a>概觀

Azure 媒體服務 （反飛彈系統） 新增 Google Widevine 動態保護 （如需詳細資訊，請參閱[Mingfei 的部落格](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)）。 此外，Azure 媒體播放程式 (AMP) 也加入 Widevine 支援 （如需詳細資訊，請參閱[AMP 文件](http://amp.azure.net/libs/amp/latest/docs/)）。 這是一大成就串流虛線內容 CENC 受到多重-native-DRM （PlayReady 和 Widevine） 中的，在新瀏覽器中配備 MSE 及 EME。

開始使用媒體服務.NET SDK 版本 3.5.2，媒體服務可讓您設定 Widevine 授權範本，並取得 Widevine 授權。 您也可以使用下列反飛彈系統合作夥伴以協助您進行 Widevine 授權︰ [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)， [EZDRM](http://ezdrm.com/)， [castLabs](http://castlabs.com/company/partners/azure/)。

本文將說明如何整合和測試受 Axinom Widevine 授權伺服器。 具體來說，其涵蓋︰  

- 使用多重-DRM （PlayReady 和 Widevine） 與相對應的授權擷取 Url; 設定動態一般加密
- 以符合授權伺服器需求; 產生 JWT 權杖
- 開發控點與 JWT token 驗證; 授權擷取 Azure Media Player 應用程式

完成系統，內容索引鍵、 索引鍵識別碼，金鑰種子 JTW 權杖，其宣告可以由下列圖表最適合描述流程。

![虛線與 CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

##<a name="content-protection"></a>內容的保護

動態保護與主要傳遞原則設定，請參閱 Mingfei 的部落格︰[如何設定 Widevine 包裝 Azure 媒體服務使用](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)。

您可以設定多重 DRM 動態 CENC 保護的虛線串流有下列其中一項︰

1. MS 邊緣與 IE11，可能會有 token 授權限制 PlayReady 防護。 發出的安全權杖服務 (STS)，例如 Azure Active Directory; 的權杖必須隨附的權杖限制的原則
1. 若是 Chrome Widevine 保護，它可以與另一個 STS 所發行的權杖需要權杖驗證。 

請參閱[JWT Token 產生](media-services-axinom-integration.md#jwt-token-generation)為什麼 Azure Active Directory 無法作為 STS Axinom 的 Widevine 授權伺服器的一節。

###<a name="considerations"></a>考量

1. 您必須使用指定 Axinom 鍵種子 (8888000000000000000000000000000000000000) 和您產生或選取金鑰來產生設定金鑰遞送服務的內容索引鍵的識別碼。 Axinom 授權伺服器會發出所有包含內容索引鍵以相同的金鑰種子，這是測試和生產有效的授權。
1. Widevine 授權取得 URL，以進行測試︰ [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)。 允許 HTTP 和 HTTS。

##<a name="azure-media-player-preparation"></a>Azure 媒體播放程式準備

AMP v1.4.0 支援播放動態封裝並同時 PlayReady 和 Widevine DRM 反飛彈系統內容。
如果 Widevine 授權伺服器時，不需要驗證的權杖，沒有任何其他您需要執行以測試受 Widevine 虛線內容。 例如，AMP 小組會提供簡單[範例](http://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevine_notoken.html)，您可以查看其在邊緣與 IE11 與 PlayReady 與 Widevine Chrome。
提供 Axinom Widevine 授權伺服器需要 JWT token 驗證。 JWT 權杖，就必須透過 HTTP 標頭 「 X-AxDRM-郵件 」 的授權要求會提交。 達到這個目的，您需要新增下列 javascript，在網頁上裝載 AMP 之前設定的來源︰

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

其他 AMP 碼是與 AMP 文件的標準 AMP API[以下](http://amp.azure.net/libs/amp/latest/docs/)。

請注意，還是設定自訂授權標頭上述 javascript 之前正式發行中 AMP 長期方法是短期的方法。

##<a name="jwt-token-generation"></a>JWT 權杖產生器

Axinom Widevine 授權伺服器的測試需要 JWT token 驗證。 此外，其中一個 JWT 權杖宣告為，而不是基本資料型別複雜的物件類型。

很抱歉，Azure AD 只能發行 JWT 權杖基本類型。 同樣地，.NET Framework API （System.IdentityModel.Tokens.SecurityTokenHandler 和 JwtPayload） 僅可讓您輸入複雜的物件類型為宣告。 不過，宣告仍序列化為字串。 因此我們無法使用任何的兩個產生 Widevine 授權要求的 JWT 權杖。


John Sheehan [JWT Nuget 套件](https://www.nuget.org/packages/JWT)符合需求，因此我們將使用這個 Nuget 套件。

以下是需要宣告所需的 Axinom Widevine 授權伺服器的測試產生 JWT 權杖的程式碼︰

    
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;
    
    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.
    
                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };
    
                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);
    
                return token;
            }
    
            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }
    
                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }
    
                return HexAsBytes;
            }
    
        }  
    
    }  

Axinom Widevine 授權伺服器

    <add key="ax:laurl" value="http://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

###<a name="considerations"></a>考量

1.  即使反飛彈系統 PlayReady 授權服務需要 「 承載者 = 」 前面驗證權杖，Axinom Widevine 授權伺服器未使用它。
2.  Axinom 通訊鍵作為簽署金鑰。 請注意，關鍵是十六進位的字串，不過它必須被視為一系列位元組不是字串時編碼。 這被達成 ConvertHexStringToByteArray 的方法。

##<a name="retrieving-key-id"></a>擷取金鑰識別碼

您可能已經注意到產生 JWT 的程式碼 token、 鍵 ID 是必要。 由於 JWT token 必須是之前載入 AMP player，準備好按鍵識別碼需要擷取才能產生 JWT 權杖。

有多個方法可以取得按住鍵的課程的識別碼。 例如，其中一個可能存放區金鑰識別碼與資料庫中的內容中繼資料。 您可以擷取或金鑰從虛線 MPD （媒體簡報描述） 檔案的識別碼。 下列程式碼適用於後者。

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }
    
        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "http://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();
    
        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);
    
        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }
    
        return key_id;
    }

##<a name="summary"></a>摘要

使用最新加入 Widevine 支援 Azure 媒體服務內容保護與 Azure Media Player 中，我們就能實作與反飛彈系統和 Widevine 授權伺服器 Axinom 從下列現代化的瀏覽器中同時 PlayReady 授權服務串流虛線 + 多重-native-DRM （PlayReady + Widevine） 的項目︰

- Chrome
- 在 Windows 10 上的 Microsoft Edge
- IE Windows 8.1 及 Windows 10 上 11
- (不 iOS) 在 Mac 上的 Safari 和 Firefox （桌面） 也支援透過 Silverlight 和 Azure Media Player 與相同的 URL

在迷你解決方案運用 Axinom Widevine 授權伺服器需要下列參數。 除了鍵 ID，其餘的參數所提供的 Axinom 根據其 Widevine 伺服器設定。


參數|如何使用它
---|---
通訊金鑰識別碼|必須包含做為 JWT 權杖宣告 「 com_key_id 」 的值 （請參閱[本節](media-services-axinom-integration.md#jwt-token-generation)）。
通訊索引鍵|做為 JWT 權杖簽署索引鍵 （請參閱[本節](media-services-axinom-integration.md#jwt-token-generation)）。
索引鍵種子|必須用來產生與指定的任何內容的內容索引鍵金鑰識別碼 （請參閱[本節](media-services-axinom-integration.md#content-protection)）。
Widevine 授權取得 URL|必須使用中的虛線串流[（請參閱本節）](media-services-axinom-integration.md#content-protection)設定資產傳遞原則。
內容的金鑰識別碼|必須包含的 JWT 權杖權益訊息宣告的值 （請參閱[本節](media-services-axinom-integration.md#jwt-token-generation)）。 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>通知 

我們想要確認下列提供建立這份文件的人員︰ Kristjan Jõgi 的 Axinom 與 Mingfei Yan Amit Rajput。
