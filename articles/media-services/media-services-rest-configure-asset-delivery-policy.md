<properties 
    pageTitle="設定使用媒體服務 REST API 資產傳遞原則 |Microsoft Azure" 
    description="本主題說明如何設定不同的資產傳遞原則使用媒體服務 REST API。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>設定資產傳遞原則

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

如果您打算將動態加密的資產，其中一個步驟中的媒體服務內容傳遞工作流程正在設定資產的傳遞的原則。 資產傳遞原則會告訴媒體服務要用於您要傳送的資產的方式︰ 將應您的資產動態封裝 （如 MPEG 破折號、 HLS、 平滑串流、 或全部），無論是否要動態加密您的資產，以及如何哪些串流通訊協定 （信封或一般加密）。

本主題探討原因，以及如何建立及設定資產傳遞原則。

>[AZURE.NOTE]若要使用動態包裝和動態加密，您必須確定已至少有一個刻度] 單位 （也稱為串流單位）。 如需詳細資訊，請參閱[媒體服務不按比例縮放的方式](media-services-portal-manage-streaming-endpoints.md)。
>
>此外，您的資產必須包含一組調整位元率 MP4s 或調整位元率平滑串流檔案。

您無法將不同的原則套用至相同的資產。 例如，您可以套用 PlayReady 加密 MPEG 破折號和 HLS 平滑串流和信封 AES 加密。 傳遞原則中未定義的任何通訊協定 （例如，您新增單一原則，只有指定 HLS 通訊協定） 會從串流遭到封鎖。 此例外是如果您有定義完全沒有資產傳遞原則。 然後清除中所允許的所有通訊協定。

如果您想要進行儲存空間的加密的資產，您必須設定資產的傳遞原則。 可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則的內容。 例如，進行您進階加密標準 (AES) 信封加密金鑰以加密的資產，設原則類型**DynamicEnvelopeEncryption**。 若要移除儲存加密，並將串流中清除資產，將原則類型設為**NoDynamicEncryption**。 以下說明如何設定這些原則類型的範例。

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

- 您無法刪除 AssetDeliveryPolicy 資產與相關聯，並視需要 （串流） 定位器存在的資產。 若要刪除原則之前先移除資產的原則建議。
- 沒有資產傳遞原則設定時，無法建立串流的定位上儲存的加密資產。  如果資產不儲存加密，系統會讓您建立定位器及串流中沒有資產傳遞原則清除資產。
- 您可以有多個與單一的資產相關聯的資產傳遞原則，但您可以只指定一種方式處理指定的 AssetDeliveryProtocol。  這表示您嘗試連結兩個傳遞指定原則，因為系統不知道您想要套用當用戶端平滑串流要求，會導致錯誤的 AssetDeliveryProtocol.SmoothStreaming 通訊協定。
- 如果您有現有的資料流定位器資產，您無法連結資產的新原則、 取消資產，從現有的原則或更新任何相關聯的資產傳遞原則。  您必須先移除串流定位器，調整原則，然後重新建立串流定位器。  當您重新建立串流定位器，但您應該確認，不會造成問題的用戶端，因為原點或下游 cdn 到底快取內容時，您可以使用相同的 locatorId。

>[AZURE.NOTE] 使用媒體服務 REST API 工作時，下列事項︰
>
>當存取媒體服務中的項目，您必須在 HTTP 邀請中設定特定的標題欄位和值。 如需詳細資訊，請參閱[媒體服務 REST API 開發的設定](media-services-rest-how-to-use.md)。

>成功連接之後要 https://media.windows.net，您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI[連線至媒體服務使用 REST API](media-services-rest-connect-programmatically.md)所述。


##<a name="clear-asset-delivery-policy"></a>清除的資產傳遞原則

###<a id="create_asset_delivery_policy"></a>建立資產傳遞原則
下列 HTTP 要求建立資產傳遞原則，指定套用動態加密，並傳送資料流中的任何下列通訊協定︰ MPEG 破折號、 HLS 和平滑串流通訊協定。 

哪些值的資訊建立 AssetDeliveryPolicy 時，您可以指定，請參閱[定義 AssetDeliveryPolicy 時，使用類型](#types)] 區段。   


要求︰
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

回應︰
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>連結資產資產傳遞原則

下列 HTTP 要求連結資產傳遞原則，以指定的資產。

要求︰

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

回應︰

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 資產傳遞原則 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>建立 EnvelopeEncryption 類型的內容索引鍵，並將其連結至資產

指定時 DynamicEnvelopeEncryption 傳遞原則，必須先確定您的資產連結至 EnvelopeEncryption 類型的內容索引鍵。 如需詳細資訊，請參閱︰[建立內容索引鍵](media-services-rest-create-contentkey.md))。


###<a id="get_delivery_url"></a>取得送達 URL

傳遞 URL 取得指定的傳遞的方法在上一個步驟中建立的內容索引鍵。 用戶端 AES 金鑰使用傳回的 URL 或 PlayReady 授權順序播放受保護的內容。

指定在 HTTP 邀請內文中的 URL 的類型。 如果您要保護您的內容與 PlayReady，要求媒體服務 PlayReady 授權擷取 URL 使用 keyDeliveryType 1: {「 keyDeliveryType 」: 1}。 如果您要保護您的內容與信封加密，要求鍵擷取 URL 指定 2 keyDeliveryType: {「 keyDeliveryType 」: 2}。

要求︰
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

回應︰
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>建立資產傳遞原則

下列 HTTP 要求建立**AssetDeliveryPolicy**設定為將動態信封加密 (**DynamicEnvelopeEncryption**) 套用至**HLS**通訊協定 （在此範例中，其他通訊協定會遭到封鎖從串流）。 


哪些值的資訊建立 AssetDeliveryPolicy 時，您可以指定，請參閱[定義 AssetDeliveryPolicy 時，使用類型](#types)] 區段。   

要求︰

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
回應︰
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>連結資產資產傳遞原則

請參閱[連結資產資產傳遞原則](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 資產傳遞原則 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>建立 CommonEncryption 類型的內容索引鍵，並將其連結至資產

指定時 DynamicCommonEncryption 傳遞原則，必須先確定您的資產連結至 CommonEncryption 類型的內容索引鍵。 如需詳細資訊，請參閱︰[建立內容索引鍵](media-services-rest-create-contentkey.md))。


###<a name="get-delivery-url"></a>取得送達 URL

傳遞 URL 取得 PlayReady 傳遞的方法在上一個步驟中建立的內容索引鍵。 用戶端使用傳回的 URL 要求 PlayReady 授權順序播放受保護的內容。 如需詳細資訊，請參閱[取得傳遞 URL](#get_delivery_url)。

###<a name="create-asset-delivery-policy"></a>建立資產傳遞原則

下列 HTTP 要求建立**AssetDeliveryPolicy**設定為將動態一般加密 (**DynamicCommonEncryption**) 套用至**平滑串流**通訊協定 （在此範例中，其他通訊協定會遭到封鎖從串流）。 

哪些值的資訊建立 AssetDeliveryPolicy 時，您可以指定，請參閱[定義 AssetDeliveryPolicy 時，使用類型](#types)] 區段。   


要求︰

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


如果您想要保護您使用 Widevine DRM 的內容，請更新 AssetDeliveryConfiguration 值，使用 WidevineLicenseAcquisitionUrl （其 7 的值），並指定授權服務的 URL。 您可以使用下列反飛彈系統合作夥伴以協助您進行 Widevine 授權︰ [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)， [EZDRM](http://ezdrm.com/)， [castLabs](http://castlabs.com/company/partners/azure/)。

例如︰ 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]加密與 Widevine，您就只能發表使用虛線。 請確定資產傳送通訊協定中指定虛線 (2)。
  
###<a name="link-asset-with-asset-delivery-policy"></a>連結資產資產傳遞原則

請參閱[連結資產資產傳遞原則](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>定義 AssetDeliveryPolicy 時所使用的類型

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

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

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

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

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

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
