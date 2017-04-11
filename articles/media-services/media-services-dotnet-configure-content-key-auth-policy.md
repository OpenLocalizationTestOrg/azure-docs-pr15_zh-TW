<properties 
    pageTitle="設定內容的金鑰授權原則使用媒體服務.NET SDK |Microsoft Azure" 
    description="瞭解如何設定的內容索引鍵使用媒體服務.NET SDK 授權原則。" 
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
    ms.date="09/15/2016"
    ms.author="juliako;mingfeiy"/>



# <a name="dynamic-encryption-configure-content-key-authorization-policy"></a>動態加密︰ 設定內容的金鑰授權原則

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

##<a name="overview"></a>概觀

Microsoft Azure 媒體服務可讓您進行進階加密標準 (AES) （使用的是 128 位元加密金鑰） 或[Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)受保護的 MPEG 虛線平滑串流、 與 HTTP Live 串流 (HLS) 資料流。 反飛彈系統也可讓您進行以 Widevine DRM 加密的虛線資料流。 每個一般加密 (ISO/IEC 23001 7 CENC) 規格加密 PlayReady 和 Widevine。

媒體服務也會提供**金鑰/授權服務**的用戶端可以取得 AES 金鑰或 PlayReady/Widevine 授權播放加密的內容。

如果您想要加密資產的媒體服務，您需要加密金鑰 （**CommonEncryption**或**EnvelopeEncryption**） 關聯 （為所述[以下](media-services-dotnet-create-contentkey.md)） 資產也設定授權原則鍵 （如本文所述）。

要求資料流時，播放程式，媒體服務會使用指定的索引鍵動態加密您使用 DRM AES 加密的內容。 若要解密串流，播放程式會從主要傳遞服務要求索引鍵。 若要決定以取得金鑰授權的使用者，服務會評估您指定的金鑰的授權原則。

媒體服務支援多種方式可以驗證使用者進行重要的要求。 內容索引鍵的授權原則可能會有一或多個授權限制︰**開啟**或**token**限制。 權杖限制的原則必須伴隨的權杖發出的安全性 Token 服務 (STS)。 媒體服務支援權杖的**簡單 Web 權杖**([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 格式和**JSON Web Token** ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)) 的格式。

媒體服務並不提供安全性權杖服務。 您可以建立自訂的 STS，或利用 Microsoft Azure ACS 這些問題權杖。 若要建立的權杖簽署您所指定的權杖限制設定 （如本文所述） 指定索引鍵和問題宣告必須設定 STS。 媒體服務索引鍵的遞送服務會傳回加密金鑰用戶端，如果權杖有效，而且權杖中的宣告符合所設定的內容索引鍵。

如需詳細資訊，請參閱

[JWT 權杖 authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[整合 Azure 媒體服務 OWIN MVC 應用程式與 Azure Active Directory 與限制內容索引鍵的傳遞根據 JWT 宣告](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[使用 Azure ACS 問題權杖](http://mingfeiy.com/acs-with-key-services)。

###<a name="some-considerations-apply"></a>一些事項︰

- 若要使用動態包裝和動態加密，您必須確定至少有一個串流保留的單位。 如需詳細資訊，請參閱[媒體服務不按比例縮放的方式](media-services-portal-manage-streaming-endpoints.md)。
- 您的資產必須包含一組調整位元率 MP4s 或調整位元率平滑串流檔案。 如需詳細資訊，請參閱[編碼資產](media-services-encode-asset.md)。
- 上傳並編碼您使用**AssetCreationOptions.StorageEncrypted**選項的資產。
- 如果您打算有多個內容鍵需要相同的原則設定，建議建立單一授權原則，並重複使用它與多個內容索引鍵。
- 索引鍵傳送服務和快取 ContentKeyAuthorizationPolicy 其相關的物件 （原則選項和限制） 15 分鐘。  如果您建立 ContentKeyAuthorizationPolicy，指定要使用 「 權杖 」 的限制，然後測試，然後 「 開啟 」 限制更新原則，也需要大約原則會切換至 「 開啟 」 原則的版本前 15 分鐘。
- 如果您新增或更新您的資產傳遞原則，您必須刪除現有的定位器 （如果有的話），並建立新的搜尋。
- 目前您無法加密 HDS 串流格式]，或循序下載。


##<a name="aes-128-dynamic-encryption"></a>AES 128 動態加密

###<a name="open-restriction"></a>開啟的限制

開啟限制表示系統會產生金鑰要求的任何人發表索引鍵。 這項限制可能很適合用於測試之用。

下列範例會建立開啟授權原則，並將其內容索引鍵。

靜態公用 void AddOpenAuthorizationPolicy (IContentKey contentKey) {/ / 建立使用開啟限制的 ContentKeyAuthorizationPolicy / / 並建立授權原則 IContentKeyAuthorizationPolicy 原則 = _context。
ContentKeyAuthorizationPolicies。
CreateAsync （「 開啟授權原則 」）。結果。

清單<ContentKeyAuthorizationPolicyRestriction>限制 = 新清單<ContentKeyAuthorizationPolicyRestriction>（);
    
        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };
    
        restrictions.Add(restriction);
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");
    
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }


###<a name="token-restriction"></a>權杖限制

本節說明如何建立內容的重要授權原則，並將它與內容索引鍵關聯。 授權原則說明必須符合哪些授權需求，以決定是否要將使用者授權接收 （例如，用途的 「 驗證金鑰 」 清單包含權杖簽署與索引鍵） 鍵。

若要設定的權杖限制選項，您需要使用 XML 描述權杖的授權需求。 權杖限制設定 XML 必須符合下列 XML 結構描述。

####<a id="schema"></a>權杖限制結構描述
    
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>

當設定**token**限制原則時，您必須指定主要**驗證鍵**、**發行者**及**簡報對象**參數。 **驗證主索引鍵**包含索引鍵的權杖的簽名，**發行者**不安全的權杖服務的權杖中發生的問題。 **對象**（有時稱為**範圍**） 說明的權杖或資源的權杖授與存取權。 媒體服務索引鍵的遞送服務會驗證這些權杖中的值相符的範本中的值。 

使用**.NET 媒體服務 SDK**，您可以使用**TokenRestrictionTemplate**類別來產生的限制 token。
下列範例會建立授權原則 token 的限制。 在此範例中，用戶端有簡報包含的權杖︰ 登入鍵 (VerificationKey)、 token 發行者和必要的宣告。
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();
    
        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };
    
        restrictions.Add(restriction);
    
        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );
    
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    
        return tokenTemplateString;
    }
    
    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
    
        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
    
        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
    
        return TokenRestrictionTemplateSerializer.Serialize(template);
    }

####<a id="test"></a>測試權杖

若要根據所用的主要授權原則的權杖限制的測試權杖，請執行下列動作。
    
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
    
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();


##<a name="playready-dynamic-encryption"></a>PlayReady 動態加密 

媒體服務可讓您設定的權限及您要 PlayReady DRM 執行階段強制當使用者嘗試播放受保護的內容時的限制。 

當保護與 PlayReady 內容時，您需要在您的授權原則中指定的項目是定義[PlayReady 授權範本](media-services-playready-license-template-overview.md)的 XML 字串。 媒體服務.net sdk，您可以在**PlayReadyLicenseResponseTemplate**和**PlayReadyLicenseTemplate**類別可協助您定義 PlayReady 授權範本。

[本主題](media-services-protect-with-drm.md)說明如何加密**PlayReady**與**Widevine**內容。

###<a name="open-restriction"></a>開啟的限制
    
開啟限制表示系統會產生金鑰要求的任何人發表索引鍵。 這項限制可能很適合用於測試之用。

下列範例會建立開啟授權原則，並將其內容索引鍵。

    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
    
        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
    
    
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }

###<a name="token-restriction"></a>權杖限制

若要設定的權杖限制選項，您需要使用 XML 描述權杖的授權需求。 權杖限制設定 XML 必須符合[此](#schema)區段中所顯示的 XML 結構描述。
    
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();
    
        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;
    
        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };
    
        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
    
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);
    
        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;
                
        policy.Options.Add(policyOption);
    
        // Add ContentKeyAutorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);
    
        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    
        return tokenTemplateString;
    }
    
    static private string GenerateTokenRequirements()
    {
    
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
    
        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
    
    
        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
    
        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 
    
    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
       
        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to playback the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }


根據所用的重要授權的權杖限制的測試權杖原則請參閱[本節](#test)。 

##<a id="types"></a>定義 ContentKeyAuthorizationPolicy 時所使用的類型

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="next-step"></a>下一步
既然您已設定內容金鑰的授權原則，請移至[如何設定資產傳遞原則](media-services-dotnet-configure-asset-delivery-policy.md)主題。
 
