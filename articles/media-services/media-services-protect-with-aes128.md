<properties
    pageTitle="使用 AES 128 動態加密和金鑰遞送服務 |Microsoft Azure"
    description="Microsoft Azure 媒體服務可讓您傳送您 AES 128 位元加密金鑰以加密的內容。 媒體服務也會提供鍵傳送服務，提供加密金鑰授權的使用者。 本主題說明如何動態以 AES 128 加密，並使用金鑰傳遞服務。"
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>使用 AES 128 動態加密和金鑰遞送服務

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>概觀

>[AZURE.NOTE] 請參閱[此](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)視訊，概略瞭解如何保護您的媒體內容與 AES 加密。

Microsoft Azure 媒體服務可讓您進行 Http-Live-串流 (HLS) 和加密與進階加密標準 (AES) （使用的是 128 位元加密金鑰） 的平滑資料流。 媒體服務也會提供鍵傳送服務，提供加密金鑰授權的使用者。 如果您想要加密資產的媒體服務，您需要加密金鑰關聯資產也設定金鑰的授權原則。 要求資料流時，播放程式，媒體服務會使用指定的索引鍵動態加密您使用的 AES 加密的內容。 若要解密串流，播放程式會從主要傳遞服務要求索引鍵。 若要決定以取得金鑰授權的使用者，服務會評估您指定的金鑰的授權原則。

媒體服務支援多種方式可以驗證使用者進行重要的要求。 內容索引鍵的授權原則可能會有一或多個授權限制︰ 開啟或權杖限制。 權杖限制的原則必須伴隨的權杖發出的安全性 Token 服務 (STS)。 媒體服務支援權杖的[簡單 Web 權杖](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(SWT) 格式和[JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 的格式。 如需詳細資訊，請參閱[設定內容金鑰的授權原則](media-services-protect-with-aes128.md#configure_key_auth_policy)。

若要充分利用動態加密，必須有包含一組多重位元率 MP4 檔案或多重位元率平滑串流來源檔案的資產。 您也需要設定傳遞原則資產 （本主題稍後的描述）。 然後，根據串流 URL 中指定的格式，點播串流伺服器，可確保資料流已傳送中您所選擇的通訊協定。 如此一來，您只需要儲存及支付中單一儲存格式的檔案和媒體服務會建立並提供適當的回應，根據用戶端的要求。

本主題會處理傳遞受保護的媒體的應用程式的開發人員有用。 主題說明如何使用授權原則設定主要傳遞服務，以便僅授權的用戶端，可能會收到加密金鑰。 也會顯示如何使用動態加密。

>[AZURE.NOTE]若要開始使用動態加密，您必須先取得至少有一個刻度] 單位 （也稱為串流單位）。 如需詳細資訊，請參閱[媒體服務不按比例縮放的方式](media-services-portal-manage-streaming-endpoints.md)。

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES 128 動態加密和主要傳遞服務工作流程

以下是您所需執行加密您資產與 AES，使用媒體服務主要傳遞服務，並可使用 [動態加密時的一般步驟。

1. [建立資產及上傳到資產的檔案](media-services-protect-with-aes128.md#create_asset)。
1. [編碼包含要調整的位元率 MP4 設定的檔案的資產](media-services-protect-with-aes128.md#encode_asset)。
1. [建立內容索引鍵和關聯編碼的資產](media-services-protect-with-aes128.md#create_contentkey)。 媒體服務中的內容索引鍵包含資產的加密金鑰。
1. [設定內容金鑰的授權原則](media-services-protect-with-aes128.md#configure_key_auth_policy)。 內容索引鍵的授權原則必須由您與符合的用戶端的內容索引鍵順序傳遞至用戶端。
1. [設定資產的傳遞原則](media-services-protect-with-aes128.md#configure_asset_delivery_policy)。 傳遞原則設定包含︰ 關鍵取得 URL 和初始化向量 (IV) （AES 128 需要加密和解密時提供相同的 IV），傳送通訊協定 （例如，MPEG 破折號、 HLS、 HDS、 平滑串流或全部） 中，輸入的動態加密 （例如，信封或不動態加密）。

您可以套用不同的原則，每個通訊協定上相同的資產。 例如，您可以套用 PlayReady 加密平滑/破折號和 HLS AES 信封。 傳遞原則中未定義的任何通訊協定 （例如，您新增單一原則，只有指定 HLS 通訊協定） 會從串流遭到封鎖。 此例外是如果您有定義完全沒有資產傳遞原則。 然後清除中所允許的所有通訊協定。

1. 若要取得串流 URL[建立視需要定位器](media-services-protect-with-aes128.md#create_locator)。

主題也會顯示[在用戶端應用程式可以列印文件的要求，請從主要傳遞服務的金鑰](media-services-protect-with-aes128.md#client_request)。

您會看到完整的.NET[範例](media-services-protect-with-aes128.md#example)主題的結尾。

下圖示範上述工作流程。 以下使用權杖進行驗證。

![使用 AES 128 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

本主題其餘提供詳細的說明與程式碼範例，告訴您如何達到上述工作的主題的連結。

##<a name="current-limitations"></a>目前的限制

如果您新增或更新您的資產傳遞原則，您必須刪除現有的定位器 （如果有的話），並建立新的搜尋。

##<a id="create_asset"></a>建立資產，並將檔案上傳到資產

才能管理、 編碼，以及串流視訊，您必須先將 Microsoft Azure 媒體服務上載您的內容。 上傳後您的內容是安全地儲存在雲端進一步處理和資料流。 

如詳細資訊，請參閱[上傳檔案至媒體服務帳戶](media-services-dotnet-upload-files.md)。

##<a id="encode_asset"></a>編碼資產包含要調整的位元率 MP4 設定的檔案

使用動態加密您只需要是建立包含一組多重位元率 MP4 檔案或多重位元率平滑串流來源檔案的資產。 然後，根據指定的格式，在資訊清單或片段邀請中，點播串流伺服器會確保您收到資料流中您所選擇的通訊協定。 如此一來，您只需要儲存及支付中單一儲存格式的檔案和媒體服務會建立並提供適當的回應，根據用戶端的要求。 如需詳細資訊，請參閱[動態包裝概觀](media-services-dynamic-packaging-overview.md)主題。

瞭解如何編碼，請參閱[如何編碼資產使用 Media Encoder 標準](media-services-dotnet-encode-with-media-encoder-standard.md)的相關指示。

##<a id="create_contentkey"></a>建立內容索引鍵和關聯編碼資產

媒體服務中的內容索引鍵會包含您想要加密與資產的金鑰。

如詳細資訊，請參閱[建立內容索引鍵](media-services-dotnet-create-contentkey.md)。

##<a id="configure_key_auth_policy"></a>設定內容金鑰的授權原則

媒體服務支援多種方式可以驗證使用者進行重要的要求。 內容索引鍵的授權原則必須由您與傳遞至用戶端鍵順序符合用戶端 （播放程式）。 內容索引鍵的授權原則可能會有一或多個授權限制︰ 開啟、 權杖限制或 IP 限制。

如詳細資訊，請參閱[設定內容的金鑰授權原則](media-services-dotnet-configure-content-key-auth-policy.md)。

##<a id="configure_asset_delivery_policy"></a>設定資產傳遞原則 

設定您的資產的傳遞原則。 資產傳遞原則設定包含的一些事項︰

- 索引鍵擷取 URL。 
- 初始化向量 (IV) 用於信封加密。 AES 128 需要加密和解密時提供相同的 IV。 
- 資產傳送通訊協定 （例如，MPEG 破折號、 HLS、 HDS、 平滑串流或全部）。
- 輸入的動態加密 （例如，AES 信封） 或沒有動態加密。 

詳細資訊，請參閱[設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)。

##<a id="create_locator"></a>建立若要取得串流的 URL 串流定位器視需要

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

##<a id="client_request"></a>如何能您的用戶端要求鍵主要傳遞服務？

在上一個步驟中，您建立的資訊清單檔案的 URL。 您的用戶端必須從串流資訊清單檔案中擷取所需的資訊，才能進行重要傳遞服務的要求。

###<a name="manifest-files"></a>資訊清單的檔案

若要擷取的 URL (也包含內容金鑰識別碼 （孩子）） 必須用戶端資訊清單檔案的值。 用戶端然後會嘗試從主要傳遞服務取得加密金鑰。 用戶端也必須擷取 IV 值和使用，請執行解密資料流。下列程式碼片段顯示<Protection>平滑串流資訊清單的項目。

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

若是 HLS 根資訊清單分成區段檔案。 

例如，根資訊清單是︰ http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl)，而且包含區段檔案名稱的清單。
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

如果您的其中一個區段檔案中開啟文字編輯器 (例如，http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels （514369)/Manifest(video,format=m3u8-aapl)，它應該包含 #EXT X-鍵表示檔案已加密。
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>從主要傳遞服務要求索引鍵

下列程式碼示範如何傳送要求給媒體服務主要傳遞服務使用金鑰送達 Uri （的擷取資訊清單） 和 （本主題會不會說明如何從安全的權杖服務取得簡單的 Web 權杖） 的權杖。

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>範例

1. 建立新的主控台專案。
1. 使用 NuGet 安裝並新增 Azure 媒體服務.NET SDK 擴充功能。 安裝此套件，也會安裝媒體服務.NET SDK 並新增所有其他必要的相依性。
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

1. 使用此區段中所顯示的程式碼會覆寫 Program.cs 檔案中的程式碼。
    
    請務必一併更新為指向輸入的檔案的所在位置的資料夾的變數。
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
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
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
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
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
                    };
        
                    IAssetDeliveryPolicy assetDeliveryPolicy =
                        _context.AssetDeliveryPolicies.Create(
                                    "AssetDeliveryPolicy",
                                    AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                                    AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                                    assetDeliveryPolicyConfiguration);
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
                    Console.WriteLine();
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
