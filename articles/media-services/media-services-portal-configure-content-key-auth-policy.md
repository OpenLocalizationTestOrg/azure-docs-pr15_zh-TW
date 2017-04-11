<properties 
    pageTitle="設定使用 Azure 入口網站的內容金鑰授權原則 |Microsoft Azure" 
    description="瞭解如何設定授權原則的內容索引鍵。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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



#<a name="configure-content-key-authorization-policy"></a>設定內容的金鑰授權原則
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]


##<a name="overview"></a>概觀

Microsoft Azure 媒體服務可讓您進行進階加密標準 (AES) （使用的是 128 位元加密金鑰） 或[Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)受保護的 MPEG 虛線平滑串流、 與 HTTP Live 串流 (HLS) 資料流。 反飛彈系統也可讓您進行以 Widevine DRM 加密的虛線資料流。 每個一般加密 (ISO/IEC 23001 7 CENC) 規格加密 PlayReady 和 Widevine。

媒體服務也會提供**金鑰/授權服務**的用戶端可以取得 AES 金鑰或 PlayReady/Widevine 授權播放加密的內容。

本主題說明如何使用 Azure 入口網站設定內容的金鑰授權原則。 索引鍵稍後可以用於動態加密您的內容。 注意目前您可以加密下列串流格式︰ HLS、 MPEG 虛線，以及帶有平滑串流。 您無法加密 HDS 串流格式]，或循序下載。

當播放程式要求設定為 [動態加密資料流時，媒體服務會使用設定的金鑰動態加密您使用 DRM AES 加密的內容。 若要解密串流，播放程式會從主要傳遞服務要求索引鍵。 若要決定以取得金鑰授權的使用者，服務會評估您指定的金鑰的授權原則。


如果您打算有多個內容索引鍵，或想要指定**金鑰/授權服務**URL 媒體服務主要傳遞服務以外，可以使用媒體服務.NET SDK 或 REST Api。

[設定使用媒體服務.NET SDK 的內容金鑰授權原則](media-services-dotnet-configure-content-key-auth-policy.md)

[設定內容使用媒體服務 REST API 金鑰授權原則](media-services-rest-configure-content-key-auth-policy.md)

###<a name="some-considerations-apply"></a>一些事項︰

- 若要使用動態包裝和動態加密，您必須確定至少有一個串流保留的單位。 如需詳細資訊，請參閱[媒體服務不按比例縮放的方式](media-services-portal-manage-streaming-endpoints.md)。
- 您的資產必須包含一組調整位元率 MP4s 或調整位元率平滑串流檔案。 如需詳細資訊，請參閱[編碼資產](media-services-encode-asset.md)。
- 索引鍵傳送服務和快取 ContentKeyAuthorizationPolicy 其相關的物件 （原則選項和限制） 15 分鐘。  如果您建立 ContentKeyAuthorizationPolicy，指定要使用 「 權杖 」 的限制，然後測試，然後 「 開啟 」 限制更新原則，也需要大約原則會切換至 「 開啟 」 原則的版本前 15 分鐘。


##<a name="how-to-configure-the-key-authorization-policy"></a>如何︰ 設定金鑰的授權原則

若要設定金鑰的授權原則，選取的**內容保護**頁面。

媒體服務支援多種方式可以驗證使用者進行重要的要求。 內容索引鍵的授權原則可以有**開啟**、**權杖**，或**IP**授權限制 （**IP**可以其餘的.NET SDK 設定）。

###<a name="open-restriction"></a>開啟的限制

**開啟**限制表示系統會產生金鑰要求的任何人發表索引鍵。 這項限制可能很適合用於測試之用。

![OpenPolicy][open_policy]

###<a name="token-restriction"></a>權杖限制

若要選擇 token 限制的原則，按下 [ **TOKEN** ] 按鈕。

**權杖**限制原則必須伴隨的權杖發出**安全性 Token 服務**(STS)。 媒體服務支援權杖的**簡單 Web 權杖**([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 格式和**JSON Web Token** (JWT) 的格式。 如需的資訊，請參閱[JWT token 驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。

媒體服務並不提供**安全性權杖服務**。 您可以建立自訂的 STS，或利用 Microsoft Azure ACS 這些問題權杖。 若要建立的權杖簽署您指定的權杖限制設定指定索引鍵和問題宣告必須設定 STS。 媒體服務索引鍵的遞送服務會傳回加密金鑰用戶端，如果權杖有效，而且權杖中的宣告符合所設定的內容索引鍵。 如需詳細資訊，請參閱[使用 Azure ACS 問題權杖](http://mingfeiy.com/acs-with-key-services)。

當設定**TOKEN**限制原則時，您必須設定**驗證鍵**、**發行者**及**簡報對象**] 值。 驗證主索引鍵包含索引鍵的權杖的簽名，發行者不安全的權杖服務的權杖中發生的問題。 （有時稱為範圍） 的對象說明的權杖或資源的權杖授與存取權。 媒體服務索引鍵的遞送服務會驗證這些權杖中的值相符的範本中的值。

###<a name="playready"></a>PlayReady

當保護與**PlayReady**內容時，您需要在您的授權原則中指定的項目是定義 PlayReady 授權範本的 XML 字串。 根據預設，設定下列原則︰

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
      <LicenseTemplates>
        <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
          <ContentKey i:type="ContentEncryptionKeyFromHeader" />
          <LicenseType>Nonpersistent</LicenseType>
          <PlayRight>
            <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
          </PlayRight>
        </PlayReadyLicenseTemplate>
      </LicenseTemplates>
    </PlayReadyLicenseResponseTemplate>

您可以按一下 [**匯入原則 xml** ] 按鈕，並提供 XML 結構描述定義的不同的 XML 其符合[以下](https://msdn.microsoft.com/library/azure/dn783459.aspx)。


##<a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]





[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

