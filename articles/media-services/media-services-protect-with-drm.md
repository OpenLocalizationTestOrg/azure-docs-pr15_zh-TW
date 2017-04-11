<properties
    pageTitle="使用 PlayReady 及/或 Widevine 動態一般加密 |Microsoft Azure"
    description="Microsoft Azure 媒體服務可讓您進行 MPEG 虛線平滑串流、 與 Http Live 串流 (HLS) 資料流時使用 Microsoft PlayReady DRM 保護。 也可讓您傳送以 Widevine DRM 加密的虛線。 本主題說明如何動態 PlayReady 與 Widevine DRM 加密。"
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
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>使用 PlayReady 及/或 Widevine 動態一般加密

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure 媒體服務可讓您進行 MPEG 虛線平滑串流、 與 HTTP Live 串流 (HLS) 資料流時使用[Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)保護。 也可讓您與 Widevine DRM 授權傳送加密的虛線資料流。 每個一般加密 (ISO/IEC 23001 7 CENC) 規格加密 PlayReady 和 Widevine。 您可以使用 （從開始 3.5.1 的版本） 的[反飛彈系統.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)或 REST API 來設定您要使用 Widevine AssetDeliveryConfiguration。

媒體服務會提供可 PlayReady 及 Widevine DRM 授權的服務。 媒體服務也會提供可讓您設定的權限及您要 PlayReady 或 Widevine DRM 執行階段強制使用者播放時的限制的 Api 受保護的內容。 當使用者要求 DRM 受保護的內容時，播放程式應用程式會反飛彈系統授權服務要求授權。 反飛彈系統授權服務會發出授權播放程式如果授權。 PlayReady 或 Widevine 授權包含可用於用戶端播放程式解密並串流內容解密金鑰。


您也可以使用下列反飛彈系統合作夥伴以協助您進行 Widevine 授權︰ [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)， [EZDRM](http://ezdrm.com/)， [castLabs](http://castlabs.com/company/partners/azure/)。 如需詳細資訊，請參閱︰ 整合[Axinom](media-services-axinom-integration.md)與[castLabs](media-services-castlabs-integration.md)。

媒體服務支援多種方式可以進行重要要求使用者的授權。 內容索引鍵的授權原則可能會有一或多個授權限制︰ 開啟或權杖限制。 權杖限制的原則必須伴隨的權杖發出的安全性 Token 服務 (STS)。 媒體服務支援權杖的[簡單 Web 權杖](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(SWT) 格式和[JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 的格式。 如需詳細資訊，請參閱設定內容金鑰的授權原則。

若要充分利用動態加密，必須有包含一組多重位元率 MP4 檔案或多重位元率平滑串流來源檔案的資產。 您也需要設定傳遞的原則資產 （本主題稍後的描述）。 然後，根據串流 URL 中指定的格式，點播串流伺服器，可確保資料流已傳送中您所選擇的通訊協定。 如此一來，您只需要儲存及支付中單一儲存格式的檔案和媒體服務會建立並提供適當的 HTTP 回應根據每個從用戶端的要求。

本主題會處理傳遞的多個 DRMs，例如 PlayReady 和 Widevine 受保護的媒體的應用程式的開發人員有用。 主題說明如何使用授權原則設定 PlayReady 授權服務，以便僅授權的用戶端，可能會收到 PlayReady 或 Widevine 授權。 也會顯示如何使用動態加密加密 PlayReady 或 Widevine DRM 透過虛線。

>[AZURE.NOTE]若要開始使用動態加密，您必須先取得至少有一個刻度] 單位 （也稱為串流單位）。 如需詳細資訊，請參閱[媒體服務不按比例縮放的方式](media-services-portal-manage-streaming-endpoints.md)。


##<a name="download-sample"></a>下載範例

您可以下載從[以下](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)本文所述的範例。

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>設定動態常見的加密和 DRM 授權傳送服務

以下是您所需執行時保護您的資產與 PlayReady，使用 「 媒體授權傳遞服務，並可使用 [動態加密的一般步驟。

1. 建立資產，並將檔案上傳到資產。
1. 編碼包含要調整的位元率 MP4 設定的檔案的資產。
1. 建立內容索引鍵和關聯編碼的資產。 媒體服務中的內容索引鍵包含資產的加密金鑰。
1. 設定內容金鑰的授權原則。 內容索引鍵的授權原則必須由您與符合的用戶端的內容索引鍵順序傳遞至用戶端。

在建立內容索引鍵的授權原則時，您必須指定下列︰ 傳遞方法 （PlayReady 或 Widevine） 限制 （開啟或權杖） 及主要傳遞類型定義索引鍵 （[PlayReady](media-services-playready-license-template-overview.md)或[Widevine](media-services-widevine-license-template-overview.md)授權範本） 用戶端的傳送方式的特定資訊。
1. 設定資產的傳遞原則。 傳遞原則設定包含︰ 傳送通訊協定 （例如，MPEG 破折號、 HLS、 HDS、 平滑串流或全部）、 的動態加密 （例如，一般加密），PlayReady 或 Widevine 授權取得 URL 類型。

您可以套用不同的原則，每個通訊協定上相同的資產。 例如，您可以套用 PlayReady 加密平滑/破折號和 HLS AES 信封。 傳遞原則中未定義的任何通訊協定 （例如，您新增單一原則，只有指定 HLS 通訊協定） 會從串流遭到封鎖。 此例外是如果您有定義完全沒有資產傳遞原則。 然後清除中所允許的所有通訊協定。
1. 若要取得串流 URL 建立視需要定位器。

您會看到完整的.NET 範例主題的結尾。

下圖示範上述工作流程。 以下使用權杖進行驗證。

![使用 PlayReady 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

本主題其餘提供詳細的說明與程式碼範例，告訴您如何達到上述工作的主題的連結。

##<a name="current-limitations"></a>目前的限制

如果您新增或更新的資產傳遞原則，您必須刪除相關聯的定位器 （如果有的話），然後建立新的搜尋。

加密與 Widevine 與 Azure 媒體服務時的限制︰ 目前不支援多個內容索引鍵。

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>建立資產，並將檔案上傳到資產

才能管理、 編碼，以及串流視訊，您必須先將 Microsoft Azure 媒體服務上載您的內容。 上傳後您的內容是安全地儲存在雲端進一步處理和資料流。

如詳細資訊，請參閱[上傳檔案至媒體服務帳戶](media-services-dotnet-upload-files.md)。

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>編碼資產包含要調整的位元率 MP4 設定的檔案

使用動態加密您只需要是建立包含一組多重位元率 MP4 檔案或多重位元率平滑串流來源檔案的資產。 然後，根據資訊清單和片段邀請中指定的格式，點播串流伺服器會確保您收到資料流中您所選擇的通訊協定。 如此一來，您只需要儲存及支付中單一儲存格式的檔案和媒體服務會建立並提供適當的回應，根據用戶端的要求。 如需詳細資訊，請參閱[動態包裝概觀](media-services-dynamic-packaging-overview.md)主題。

瞭解如何編碼，請參閱[如何編碼資產使用 Media Encoder 標準](media-services-dotnet-encode-with-media-encoder-standard.md)的相關指示。


##<a id="create_contentkey"></a>建立內容索引鍵和關聯編碼資產

媒體服務中的內容索引鍵會包含您想要加密與資產的金鑰。

如詳細資訊，請參閱[建立內容索引鍵](media-services-dotnet-create-contentkey.md)。


##<a id="configure_key_auth_policy"></a>設定內容金鑰的授權原則

媒體服務支援多種方式可以驗證使用者進行重要的要求。 內容索引鍵的授權原則必須由您與傳遞至用戶端鍵順序符合用戶端 （播放程式）。 內容索引鍵的授權原則可能會有一或多個授權限制︰ 開啟或權杖限制。

如詳細資訊，請參閱[設定內容的金鑰授權原則](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption)。

##<a id="configure_asset_delivery_policy"></a>設定資產傳遞原則 

設定您的資產的傳遞原則。 資產傳遞原則設定包含的一些事項︰

- DRM 授權取得 URL。 
- 資產傳送通訊協定 （例如，MPEG 破折號、 HLS、 HDS、 平滑串流或全部）。 
- （在此例中，一般加密） 的動態加密類型。 

詳細資訊，請參閱[設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)。

##<a id="create_locator"></a>建立若要取得串流 URL 串流定位器視需要

您必須為您的使用者提供平滑、 虛線或 HLS 串流的 URL。

>[AZURE.NOTE]如果您新增或更新您的資產傳遞原則，您必須刪除現有的定位器 （如果有的話），並建立新的搜尋。

如需如何發佈資產，並建立串流 URL 的指示，請參閱[建立串流的 URL](media-services-deliver-streaming-content.md)。

##<a name="get-a-test-token"></a>取得測試權杖

取得測試 token 根據所用的主要授權原則的權杖限制。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

    
您可以使用[反飛彈系統播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)來測試您的資料流。

##<a id="example"></a>範例


下列範例會示範功能中 Azure 媒體服務 SDK 介紹.net-版本 3.5.2 （特別是，來定義 Widevine 授權範本，並從 Azure 媒體服務要求 Widevine 授權能力）。 安裝套件時，使用下列 Nuget 套件命令︰

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. 建立新的主控台專案。
1. 使用安裝並新增 Azure 媒體服務.NET SDK NuGet。
2. 新增其他參考資料︰ System.Configuration。
2. 新增設定檔含有客戶名稱] 和 [重要資訊︰
    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. 至少有一個串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱︰[設定串流結束點](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal)。

1. 使用此區段中所顯示的程式碼會覆寫 Program.cs 檔案中的程式碼。
    
    請務必一併更新為指向輸入的檔案的所在位置的資料夾的變數。
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
                    Console.ReadLine();
                }
        
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
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
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
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
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
                
                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice, 
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
            
                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;
        
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>下一步

檢閱媒體服務學習路徑。

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>另請參閱

[CENC 多重 DRM 與存取控制](media-services-cenc-with-multidrm-access-control.md)

[設定 Widevine 包裝反飛彈系統](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[宣佈使用 Google Widevine 授權傳送服務中 Azure 媒體服務](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
