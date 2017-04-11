<properties 
    pageTitle="保護您用 Apple FairPlay 及/或 Microsoft PlayReady 內容 HLS |Microsoft Azure" 
    description="本主題提供概觀，並說明如何使用 Azure 媒體服務動態加密與 Apple FairPlay HTTP Live 串流 (HLS) 內容。 也會顯示如何使用媒體服務授權遞送服務進行 FairPlay 授權用戶端。" 
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
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>保護您用 Apple FairPlay 及/或 Microsoft PlayReady 內容 HLS

Azure 媒體服務可讓您以動態方式加密 HTTP Live 串流 (HLS) 內容使用下列格式︰  

- **AES 128 信封清除索引鍵** 

    整個區塊已加密使用**AES 128 CBC**模式。 資料流解密是由 iOS 和 OSX 播放程式原本即支援。 如需詳細資訊，請參閱[本文](media-services-protect-with-aes128.md)。

- **Apple FairPlay** 

    使用**AES 128 CBC**模式，來加密個別的視訊及音訊範例。 **FairPlay 串流**裝置作業系統上，使用原生支援 iOS 及 Apple 電視整合 （每秒畫面格）。 OS X 上的 safari 可讓每秒畫面格使用加密媒體副檔名 (EME) 介面支援。
- **Microsoft PlayReady**

下圖顯示**HLS + FairPlay 及/或 PlayReady 動態加密**工作流程。

![使用 FairPlay 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

本主題示範如何使用 Azure 媒體服務動態加密與 Apple FairPlay HLS 內容。 也會顯示如何使用媒體服務授權遞送服務進行 FairPlay 授權用戶端。

>[AZURE.NOTE] 如果您也想要加密與 PlayReady HLS 內容，您需要建立一般內容索引鍵，並與您的資產。 您也需要設定內容金鑰的授權原則，[使用 PlayReady 動態一般加密](media-services-protect-with-drm.md)主題中所述。

    
## <a name="requirements-and-considerations"></a>需求和考量

- 以下是必要時進行 HLS FairPlay 使用加密和進行 FairPlay 授權，請使用反飛彈系統。

    - Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](/pricing/free-trial/?WT.mc_id=A261C142F)。
    - 媒體服務帳戶。 若要建立媒體服務帳戶，請參閱[建立帳戶](media-services-portal-create-account.md)。
    - 註冊[Apple 開發程式](https://developer.apple.com/)。
    - Apple 需要內容的擁有者，以取得[部署套件](https://developer.apple.com/contact/fps/)。 狀態已經實作 KSM （鍵安全性模組） 與 Azure 媒體服務及您正在要求最終每秒畫面格套件的要求。 會產生憑證，並取得詢問，您會使用設定 FairPlay 最終每秒畫面格封裝中的指示進行。 

    - Azure 媒體服務.NET SDK 版本**3.6.0**或更新版本。

- 反飛彈系統主要傳遞端必須設定下列項目︰
    - **應用程式的憑證 (AC)** -包含私密金鑰.pfx 檔。 此檔案是建立客戶，並以密碼加密相同的客戶。 
        
        當客戶設定主要傳遞原則時，他們必須提供的密碼] 和 [base64 格式.pfx。

        下列步驟說明如何為 FairPlay 產生 pfx 憑證。
        
        1. 從 https://slproweb.com/products/Win32OpenSSL.html 安裝 OpenSSL
        
            移至 FairPlay 憑證和 Apple 由其他檔案的所在資料夾。
        
        2. 若要將 cer 轉換成 pem 的命令列︰
        
            「 C:\OpenSSL-Win32\bin\openssl.exe 」 x509-通知 der-中 fairplay.cer-fairplay out.pem 出
        
        3. 將 pfx pem 轉換私密金鑰 （OpenSSL，然後會要求 pfx 檔案的密碼） 的命令列。
        
            「 C:\OpenSSL-Win32\bin\openssl.exe 」 pkcs12-匯出-出 fairplay out.pfx-inkey privatekey.pem-fairplay out.pem-passin file:privatekey-pem-pass.txt 中 
        
    - **應用程式憑證密碼**-建立.pfx 檔客戶密碼。
    - **應用程式憑證密碼識別碼**-客戶必須上傳如何其上傳其他反飛彈系統按鍵和使用**ContentKeyType.FairPlayPfxPassword**列舉值類似的密碼。 最後，就會看到這是想要使用金鑰傳遞原則選項內的反飛彈系統識別碼。
    - **iv** 16 位元隨機值，必須符合的資產傳遞原則中 iv。 客戶產生 IV，然後放在這兩個地方︰ 資產傳遞原則和鍵傳送原則選項。 
    - **ASK** ASK 產生使用 Apple 開發人員中心憑證時收到 （應用程式密碼鍵）。 每個開發小組會獲得唯一的 ASK。 請儲存 [詢問複本，並將其儲存在安全的地方。 您必須設定 ASK 為 FairPlayAsk 稍後 Azure 媒體服務。 
    -  當客戶上傳到反飛彈系統的 ASk 取得**要求 ID** 。 客戶必須上傳 ASk 使用**ContentKeyType.FairPlayASk**列舉值。 反飛彈系統識別碼會傳回結果，並這是什麼時，應使用設定金鑰傳送原則選項。

- 必須由每秒畫面格用戶端設定下列項目︰
    - **應用程式的憑證 (AC)** -包含公開金鑰 OS 使用來加密部分內容的.cer/.der 檔案。 反飛彈系統必須知道它，因為它所需的播放程式。 主要傳遞服務解密並使用對應的私密金鑰。

- 播放 FairPlay 加密資料流，您需要取得實數 ASK 第一個，然後產生 [真實的憑證。 該程序會建立所有 3 部分︰

    -  .der， 
    -  .pfx 和 
    -  .pfx 密碼。
 
- 支援 HLS **AES 128 CBC**加密的用戶端︰ OS X，Apple 電視 iOS 上的 Safari。

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>設定 FairPlay 動態加密和授權傳送服務的步驟

以下是您所需執行時保護您的資產與 FairPlay，使用 「 媒體授權傳遞服務，並可使用 [動態加密的一般步驟。

1. 建立資產，並將檔案上傳到資產。 
1. 編碼包含要調整的位元率 MP4 設定的檔案的資產。
1. 建立內容索引鍵和關聯編碼的資產。  
1. 設定內容金鑰的授權原則。 建立內容索引鍵的授權原則，當您需要指定下列項目︰ 
    
    - （在此例中 FairPlay），傳送方法 
    - FairPlay 原則選項設定。 如需如何設定 FairPlay 的詳細資訊，請參閱下面範例中的 ConfigureFairPlayPolicyOptions() 方法。
    
        >[AZURE.NOTE] 通常，您想要一次，設定 FairPlay 原則選項，因為您只會有一組憑證和 ASK。
-（開啟或權杖）-的限制和主要傳遞類型定義鍵用戶端的傳送方式的特定資訊。 
    
2. 設定資產傳遞原則。 傳遞原則設定包含︰ 

    - 傳送通訊協定 (HLS) 
    - 動態加密 （一般 CBC 加密） 類型 
    - 授權取得 URL。 
    
    >[AZURE.NOTE]如果您想要傳送加密的 FairPlay + 另一個 DRM 資料流時，您必須設定個別的傳遞原則 
    >
    >- 設定虛線 CENC （PlayReady + WideVine） 並與 PlayReady 平滑的一個 IAssetDeliveryPolicy。 
    >- 若要設定 HLS FairPlay 的另一個 IAssetDeliveryPolicy

1. 建立以取得串流 URL 視需要定位器。

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>使用 FairPlay 主要傳遞播放程式/用戶端應用程式

客戶可能開發使用 iOS SDK 的播放程式應用程式。 無法播放 FairPlay 內容客戶必須實作授權 exchange 通訊協定。 Apple 不被指定授權 exchange 通訊協定。 是由每一個應用程式如何傳送鍵傳送邀請。 反飛彈系統 FairPlay 鍵傳送服務需要更多為下列格式的 www-form-url 編碼的張貼訊息 SPC: 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player 不支援 FairPlay 播放預設工作。 客戶必須從 MAC OSX 上取得 FairPlay 播放 Apple 開發人員帳戶取得範例播放程式。 
 
##<a name="streaming-urls"></a>串流 Url

如果您的資產使用一個以上的 DRM 加密，您應該使用加密標籤串流的 URL: (格式 ='m3u8-aapl' 加密 = '上述 」)。

下列事項︰

- 可指定只有零或一個加密類型。
- 如果資產套用一加密，只在 url 中指定沒有加密類型。
- 加密類型是不區分大小寫。
- 您可以指定下列的加密類型︰  
    - **cenc**︰ 一般加密 （Playready 或 Widevine）
    - **cbcs aapl**: Fairplay
    - **cbc**: AES 信封加密。


##<a name="net-example"></a>.NET 範例


下列範例會示範功能中 Azure 媒體服務 SDK 介紹.net-版本 3.6.0 （能夠使用 Azure 媒體服務來傳送您的內容以 FairPlay 加密）。 安裝套件時，使用下列 Nuget 套件命令︰

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. 建立主控台專案。
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>後續步驟︰ 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
