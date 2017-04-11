<properties 
    pageTitle="使用.NET SDK 設定資產傳遞原則 |Microsoft Azure" 
    description="本主題說明如何使用 Azure 媒體服務.NET SDK 設定不同的資產傳遞原則。" 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>使用.NET SDK 設定資產傳遞原則
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>概觀

如果您打算傳送加密的資產，其中一個步驟中的媒體服務內容傳遞工作流程正在設定資產的傳遞的原則。 資產傳遞原則會告訴媒體服務要用於您要傳送的資產的方式︰ 將應您資產動態封裝 （如 MPEG 破折號、 HLS、 平滑串流、 或全部），無論是否要動態加密您資產，以及如何哪些串流通訊協定 （信封或一般加密）。

本主題探討原因，以及如何建立及設定資產傳遞原則。

>[AZURE.NOTE]若要使用動態包裝和動態加密，您必須確定已至少有一個刻度] 單位 （也稱為串流單位）。 如需詳細資訊，請參閱[媒體服務不按比例縮放的方式](media-services-portal-manage-streaming-endpoints.md)。
>
>此外，您的資產必須包含一組調整位元率 MP4s 或調整位元率平滑串流檔案。

您無法將不同的原則套用至相同的資產。 例如，您可以套用 PlayReady 加密 MPEG 破折號和 HLS 平滑串流和信封 AES 加密。 傳遞原則中未定義的任何通訊協定 （例如，您新增單一原則，只有指定 HLS 通訊協定） 會從串流遭到封鎖。 此例外是如果您有定義完全沒有資產傳遞原則。 然後清除中所允許的所有通訊協定。

如果您想要進行儲存加密的資產，您必須設定資產的傳遞原則。 可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則的內容。 例如，進行您進階加密標準 (AES) 信封加密金鑰以加密的資產，設原則類型**DynamicEnvelopeEncryption**。 若要移除儲存加密，並將串流中清除資產，將原則類型設為**NoDynamicEncryption**。 以下說明如何設定這些原則類型的範例。

根據您設定的資產傳遞原則您能夠動態封裝、 動態加密，並將串流下列串流通訊協定︰ 平滑串流、 HLS、 MPEG 破折號和 HDS 資料流。

下列清單會顯示您使用串流平滑、 HLS、 破折號和 HDS 格式。

帶有平滑串流︰

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG 破折號

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HDS

{串流結束點名稱媒體服務帳戶 name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

如需如何發佈資產，並建立串流 URL 的指示，請參閱[建立串流的 URL](media-services-deliver-streaming-content.md)。

##<a name="considerations"></a>考量

- 您無法刪除與資產相關聯，並視需要 （串流） 定位器存在的資產 AssetDeliveryPolicy。 若要刪除原則之前先移除資產的原則建議。
- 沒有資產傳遞原則設定時，無法建立串流的定位上儲存的加密資產。  如果資產不儲存加密，系統會讓您建立定位器及串流中沒有資產傳遞原則清除資產。
- 您可以有多個與單一的資產相關聯的資產傳遞原則，但您可以只指定一種方式處理指定的 AssetDeliveryProtocol。  這表示您嘗試連結兩個傳遞指定原則，因為系統不知道您想要套用當用戶端平滑串流要求，會導致錯誤的 AssetDeliveryProtocol.SmoothStreaming 通訊協定。
- 如果您有現有的資料流定位器資產，您無法連結新原則資產 （您可以取消資產，從現有的原則或更新任何相關聯的資產傳遞原則）。  您必須先移除串流定位器，調整原則，然後重新建立串流定位器。  當您重新建立串流定位器，但您應該確認，不會造成問題的用戶端，因為原點或下游 cdn 到底快取內容時，您可以使用相同的 locatorId。


##<a name="clear-asset-delivery-policy"></a>清除的資產傳遞原則

下列**ConfigureClearAssetDeliveryPolicy**方法指定套用動態加密，並傳送資料流中的任何下列通訊協定︰ MPEG 破折號、 HLS 和平滑串流通訊協定。 您可能會想要將此原則套用至您的加密的儲存空間資產。

哪些值的資訊建立 AssetDeliveryPolicy 時，您可以指定，請參閱[定義 AssetDeliveryPolicy 時，使用類型](#types)] 區段。

靜態公用 void ConfigureClearAssetDeliveryPolicy （IAsset 資產） {IAssetDeliveryPolicy 原則 = _context。AssetDeliveryPolicies.Create (「 清除原則 」，AssetDeliveryPolicyType.NoDynamicEncryption，AssetDeliveryProtocol.HLS |AssetDeliveryProtocol.SmoothStreaming |AssetDeliveryProtocol.Dash，null);

資產。DeliveryPolicies.Add(policy);}

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 資產傳遞原則


下列**CreateAssetDeliveryPolicy**方法會設定為套用動態一般加密 (**DynamicCommonEncryption**) **AssetDeliveryPolicy**建立平滑串流通訊協定 （從串流會封鎖其他通訊協定）。 這個方法需要兩個參數︰**資產**（您要套用傳遞原則資產） 和**IContentKey** (的內容索引鍵**CommonEncryption**類型，如需詳細資訊，請參閱︰[建立內容索引鍵](media-services-dotnet-create-contentkey.md#common_contentkey))。

哪些值的資訊建立 AssetDeliveryPolicy 時，您可以指定，請參閱[定義 AssetDeliveryPolicy 時，使用類型](#types)] 區段。


靜態公用的 void CreateAssetDeliveryPolicy （IAsset 資產，IContentKey 鍵） {Uri acquisitionUrl = 鍵。GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

字典 < AssetDeliveryPolicyConfigurationKey、 字串 > assetDeliveryPolicyConfiguration = 新字典 < AssetDeliveryPolicyConfigurationKey 字串 > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl、 acquisitionUrl.ToString()}};

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure 媒體服務也可讓您新增 Widevine 加密。 下列範例會示範 PlayReady 和 Widevine 加入至資產傳遞原則。


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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]加密與 Widevine，您就只能發表使用虛線。 請確認指定虛線資產傳送通訊協定。


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 資產傳遞原則 

下列**CreateAssetDeliveryPolicy**方法建立**AssetDeliveryPolicy**設定為將動態信封加密 (**DynamicEnvelopeEncryption**) 套用至平滑串流、 HLS 和虛線通訊協定 （如果您決定不指定某些通訊協定，他們會遭到封鎖從串流）。 這個方法需要兩個參數︰**資產**（您要套用傳遞原則資產） 和**IContentKey** (的內容索引鍵**EnvelopeEncryption**類型，如需詳細資訊，請參閱︰[建立內容索引鍵](media-services-dotnet-create-contentkey.md#envelope_contentkey))。


哪些值的資訊建立 AssetDeliveryPolicy 時，您可以指定，請參閱[定義 AssetDeliveryPolicy 時，使用類型](#types)] 區段。   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
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
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>定義 AssetDeliveryPolicy 時所使用的類型

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


