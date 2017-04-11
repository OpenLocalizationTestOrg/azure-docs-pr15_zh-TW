<properties 
    pageTitle="使用連線至媒體服務帳戶 REST API |Microsoft Azure" 
    description="本主題示範如何連線至媒體服務 uisng REST API。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>


# <a name="connecting-to-media-services-account-using-media-services-rest-api"></a>連線到使用媒體服務 REST API 媒體服務帳戶

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-connect-programmatically.md)
- [其餘](media-services-rest-connect-programmatically.md)

本主題說明如何在程式設計以媒體服務 REST API 時取得以程式設計方式連線至 Microsoft Azure 媒體服務。

存取 Microsoft Azure 媒體服務時，有需要兩件事︰ 存取權杖提供 Azure 存取控制服務 (ACS) 及 URI 媒體服務本身。 您可以使用任何建立這些要求，只要您指定正確的標頭中的值，並存取權杖中正確呼叫時，傳遞到媒體服務時所要的方式。

使用媒體服務 REST API 連線至媒體服務時，下列步驟會說明最常見的工作流程︰

1. 快速存取權杖 
2. 連線至媒體服務 URI 

    >[AZURE.NOTE] 成功連接之後要 https://media.windows.net，您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI。
您也可能會收到包含 ODATA API 中繼資料描述的 HTTP/1.1 200 回應。

3. 張貼後續 API 來電至新的 URL。 

    例如之後嘗試連線，您有下列︰

        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    您應該張貼 https://wamsbayclus001rest-hs.cloudapp.net/api/ 後續 API 來電。

##<a name="access-control-address"></a>存取控制位址

媒體服務存取控制位址是 https://wamsprodglobal001acs.accesscontrol.windows.net，除了北美 china （中國） 區域，則 https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn。

##<a name="getting-an-access-token"></a>快速存取權杖

若要透過 REST API 直接存取媒體服務，從 ACS 擷取存取權杖，並用您進行到服務每個 HTTP 要求期間。 此 token 加就像其他所提供的 ACS 根據存取宣告 HTTP 要求，並使用 OAuth v2 通訊協定的標頭中提供的 token。 您不需要任何其他的必要條件之前直接連接到媒體服務。

下列範例顯示的 HTTP 要求標題和本文用來擷取權杖。

**標題**︰

    POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Host: wamsprodglobal001acs.accesscontrol.windows.net
    Content-Length: 120
    Expect: 100-continue
    Connection: Keep-Alive
    Accept: application/json

    
**本文**︰

您需要以證明此邀請; 本文中的 [client_id 和 client_secret] 值client_id client_secret 對應至 [AccountName 和 AccountKey 值，分別。 這些值會為您提供媒體服務設定您的帳戶。 

請注意，您的媒體服務帳戶 AccountKey 必須 URL 編碼 （請參閱使用它為 client_secret 值在存取權杖邀請時[的百分比編碼](http://tools.ietf.org/html/rfc3986#section-2.1)。

    grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


例如︰ 

    grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


下列範例顯示 HTTP 回應包含存取權杖回應內容中。

    HTTP/1.1 200 OK
    Cache-Control: no-cache, no-store
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    Expires: -1
    request-id: a65150f5-2784-4a01-a4b7-33456187ad83
    X-Content-Type-Options: nosniff
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 15 Jan 2015 08:07:20 GMT
    Content-Length: 670
    
    {  
       "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
建議您快取 」 access_token 」 和 「 expires_in 」 值，外部的儲存空間。 權杖資料可能稍後可以擷取從儲存空間和媒體服務 REST API 來電中重複使用。 這是位置權杖能安全地共用多個程序或電腦的案例的尤其有用。

請務必監視 「 expires_in 」 的值的存取權杖，並視需要使用新的權杖更新 REST API 來電。

###<a name="connecting-to-the-media-services-uri"></a>連線至媒體服務 URI

根 URI 媒體服務是 https://media.windows.net/。 您應該先連線到此 URI，，如果您收到 301 重新導向回回應時，您應該都讓新 uri 的後續通話。 此外，請勿使用任何自動重新導向/遵循邏輯在邀請中。 將新 uri 轉送 HTTP 動詞和邀請內文。

請注意，根 URI 上傳和下載資產檔案是 https://yourstorageaccount.blob.core.windows.net/ 位置儲存體帳戶名稱就是您媒體服務帳戶安裝時所用的相同。

下列範例會示範 HTTP 要求媒體服務根 URI (https://media.windows.net/)。 要求取得 301 重新導向回回應。 後續要求使用新的 URI (https://wamsbayclus001rest-hs.cloudapp.net/api/)。     

**HTTP 要求**︰
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: media.windows.net


**HTTP 回應**︰
    
    HTTP/1.1 301 Moved Permanently
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
    Server: Microsoft-IIS/8.5
    request-id: 987d7652-497a-44e5-b815-f492e02aef97
    x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:53 GMT
    Content-Length: 164
    
    <html><head><title>Object moved</title></head><body>
    <h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
    </body></html>


**HTTP 要求**（使用新的 URI）︰
            
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f19258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
    x-ms-version: 2.11
    Accept: application/json
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 回應**︰
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1250
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sat, 17 Jan 2015 07:44:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
     


>[AZURE.NOTE] 當您收到新的 URI 時，這是 URI 應與媒體服務進行通訊。 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
