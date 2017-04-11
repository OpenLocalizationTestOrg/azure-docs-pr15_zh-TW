<properties 
    pageTitle="快速入門發表內容，視需要使用其餘 |Microsoft Azure" 
    description="本教學課程中會引導您執行上視需要內容傳遞應用程式與使用 REST API Azure 媒體服務的步驟。" 
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
    ms.date="10/11/2016" 
    ms.author="juliako"/>

#<a name="get-started-with-delivering-content-on-demand-using-rest"></a>開始使用視需要使用其餘發表內容 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> 若要完成此教學課程中，您需要 Azure 帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](/pricing/free-trial/?WT.mc_id=A261C142F)。 


此快速入門會引導您執行 Video-on-Demand (VoD) 內容傳遞的應用程式，使用 Azure 媒體服務 （反飛彈系統） REST Api 的步驟。 

教學課程介紹基本的媒體服務工作流程的最常見的程式設計物件及媒體服務開發所需的工作。 在完成教學課程中，您可以將串流或漸進下載的範例媒體檔案上傳、 編碼，及下載。  

## <a name="prerequisites"></a>必要條件
下列先決條件，才能開始開發 REST Api 與媒體服務使用。

- 瞭解如何使用媒體服務 REST API 開發。 如需詳細資訊，請參閱[媒體服務-其餘概觀](http://msdn.microsoft.com/library/azure/hh973616.aspx)。
- 可以傳送 HTTP 邀請和回應您所選擇的應用程式。 本教學課程中使用[Fiddler](http://www.telerik.com/download/fiddler)。 

下列工作會顯示這個快速入門。

1.  建立媒體服務帳戶 （使用 Azure 入口網站）。
2.  設定串流端點 （使用 Azure 入口網站）。
1.  連線至以 REST API 媒體服務帳戶。
1.  建立新的資產上, 傳的視訊檔案，以 REST API。
1.  設定 REST API 串流單位。
2.  將一組調整位元率 MP4 檔案以 REST API 編碼來源檔案。
1.  發佈資產取得串流和以 REST API 漸進下載 Url。 
1.  播放您的內容。 

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>建立使用 Azure 入口網站 Azure 媒體服務帳戶

本節中的步驟會顯示如何建立反飛彈系統帳戶。

1. 在[Azure 入口網站](https://portal.azure.com/)登入。
2. 按一下**[+ 新增** > **媒體 + CDN** > **媒體服務**。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. 在**建立媒體服務帳戶**輸入必要的值。

    ![建立媒體服務](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. 在 [**帳戶名稱**] 中，輸入新的反飛彈系統帳戶的名稱。 媒體服務帳戶名稱是全部小寫的數字或字母不加空格，，3 至 24 個字元的長度。
    2. 在訂閱中，選取 [之間的不同的 Azure 訂閱，您有權存取]。
    
    2. 在 [**資源] 群組**中，選取新的或現有的資源。  資源群組是共用生命週期、 權限及原則的資源的集合。 深入瞭解[以下](azure-resource-manager/resource-group-overview.md#resource-groups)。
    3. 在 [**位置**]，選取地理區域用來儲存您的媒體服務帳戶的媒體及中繼資料記錄。 此區域用來處理和串流媒體。 使用媒體服務區域會出現在下拉式清單方塊中。 
    
    3. 在**儲存帳戶**，選取儲存的帳戶提供媒體內容媒體服務帳戶的 blob 儲存體。 您可以選取現有的儲存空間帳戶中相同的地理區域，為您的媒體服務帳戶，或者您也可以建立儲存帳戶。 在同一個區域中建立新的儲存空間帳戶。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。

        進一步瞭解儲存空間[以下](storage-introduction.md)。

    4. 選取 [**固定至儀表板**，若要查看帳戶部署的進度。
    
7. 按一下 [**建立**底部的表單]。

    帳戶已成功建立之後，狀態會變更為**執行**。 

    ![媒體服務設定](./media/media-services-portal-vod-get-started/media-services-settings.png)

    若要管理您的反飛彈系統帳戶 （例如上, 傳視訊編碼資產、 監控工作進度） 使用 [**設定**] 視窗。

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>使用 [Azure 入口網站的串流端點的設定

使用其中一個最常見的案例會將透過調整的位元率串流視訊至您的用戶端 Azure 媒體服務。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。

媒體服務會提供可讓您在發表您調整的位元率串流媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 只的時間，您不必儲存的每個串流格式預先封裝的版本支援的格式編碼 MP4 內容的動態包裝。

若要充分利用動態包裝，您需要執行下列動作︰

- 將一組調整位元率 MP4 檔案 （示範稍後在本教學課程編碼步驟） 編碼 mezzanine （來源） 檔案。  
- 建立至少有一個串流單位*串流端點*從您打算傳送您的內容。 下列步驟說明如何變更串流單位數量。

動態包裝，您只需要儲存及支付中單一儲存格式的檔案與媒體服務建置，並提供適當的回應，根據用戶端的要求。

若要建立並變更數串流保留的單位，請執行下列動作︰


1. 在 [**設定**] 視窗中，按一下 [**串流結束點**]。 

2. 按一下 [預設串流結束點]。 

    **預設串流端點詳細資料**視窗隨即出現。

3. 若要指定串流單位數量，請將滑**串流單位**。

    ![串流單位](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. 按一下 [**儲存**] 按鈕，以儲存變更]。

    >[AZURE.NOTE]分派的任何新的單位花費 20 分鐘才能完成。

## <a id="connect"></a>連線至 REST API 媒體服務帳戶

存取 Azure 媒體服務時，有需要兩件事︰ 存取權杖提供 Azure 存取控制服務 (ACS) 及 URI 媒體服務本身。 您可以使用任何建立這些要求，只要您指定正確的標頭中的值，並存取權杖中正確呼叫時，傳遞到媒體服務時所要的方式。

使用媒體服務 REST API 連線至媒體服務時，下列步驟會說明最常見的工作流程︰

1. 快速存取權杖。 
2. 連線到媒體服務 URI。  

    請記住之後成功連線至 https://media.windows.net，, 您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI。 您也可能會收到包含 ODATA API 中繼資料描述的 HTTP/1.1 200 回應。
3. 張貼後續 API 來電至新的 URL。 
    
    例如之後嘗試連線，您有下列︰
        
        HTTP/1.1 301 Moved Permanently
        Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

    您應該張貼 https://wamsbayclus001rest-hs.cloudapp.net/api/ 後續 API 來電。

###<a name="getting-an-access-token"></a>快速存取權杖

若要透過 REST API 直接存取媒體服務，從 ACS 擷取存取權杖，並用您進行到服務每個 HTTP 要求期間。 您不需要任何其他的必要條件之前直接連接到媒體服務。

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

您需要證明此邀請; 本文中的 [client_id 和 client_secret] 值client_id client_secret 對應至 [AccountName 和 AccountKey 值，分別。 這些值會為您提供媒體服務設定您的帳戶。 

媒體服務帳戶 AccountKey URL 編碼時必須做為 client_secret 值存取權杖邀請中使用。

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
       "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
       "expires_in":"21600",
       "scope":"urn:WindowsAzureMediaServices"
    }
    

>[AZURE.NOTE]
建議您快取 」 access_token 」 和 「 expires_in 」 值，外部的儲存空間。 權杖資料可能稍後可以擷取從儲存空間和媒體服務 REST API 來電中重複使用。 這是位置權杖能安全地共用多個程序或電腦的案例的尤其有用。

請務必監視 「 expires_in 」 的值的存取權杖，並視需要使用新的權杖更新 REST API 來電。

###<a name="connecting-to-the-media-services-uri"></a>連線至媒體服務 URI

根 URI 媒體服務是 https://media.windows.net/。 您應該先連線到此 URI，，如果您收到 301 重新導向回回應時，您應該都讓新 uri 的後續通話。 此外，請勿使用任何自動重新導向/遵循邏輯在邀請中。 將新 uri 轉送 HTTP 動詞和邀請內文。

根 URI 上傳和下載資產檔案是 https://yourstorageaccount.blob.core.windows.net/ 位置儲存體帳戶名稱媒體服務帳戶安裝時您用的相同。

下列範例會示範 HTTP 要求媒體服務根 URI (https://media.windows.net/)。 要求取得 301 重新導向回回應。 後續要求使用新的 URI (https://wamsbayclus001rest-hs.cloudapp.net/api/)。     

**HTTP 要求**︰
    
    GET https://media.windows.net/ HTTP/1.1
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
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
     


>[AZURE.NOTE] 現在新 URI 會在此教學課程中使用。

## <a id="upload"></a>建立新的資產，然後上傳的視訊檔案，以 REST API

在媒體服務中，您上將數位檔案傳到資產。 **資產**實體可以包含視訊、 音訊、 圖像、 縮圖的集合，文字追蹤和字幕檔案 （以及這些檔案的中繼資料。） 一旦將檔案上傳到資產，您的內容是安全地儲存在雲端進一步處理和串流。 

其中一個值，您必須建立資產時提供是資產建立選項。 [**選項**] 屬性是列舉值，說明您可以使用建立資產的加密選項。 有效的值是其中一個下方此清單中的值組合的清單中的值︰

 
- **無** = 會使用**0**不加密。 使用此選項時，您的內容未受到保護傳送或儲存區中的其餘部分。
    如果您打算將 MP4 使用漸進下載時，請使用這個選項。 
- **StorageEncrypted** = **1** -加密本機使用 AES 256 元加密清除內容，然後將它上載至 Azure 儲存體將其儲存在其他加密。 受到保護的儲存空間加密的資產會自動加密放在之前編碼，加密的檔案系統中和您也可以選擇重新加密之前上傳回到為新的輸出資產。 儲存加密的主要的使用情況時，您要保護磁碟上的使用加密靜止高品質輸入的媒體檔案。
- **CommonEncryptionProtected** = **2** -使用這個選項，如果您要上傳的已加密並以一般加密或 PlayReady DRM （例如，平滑串流保護與 PlayReady DRM） 保護內容。
- **EnvelopeEncryptionProtected** = **4** – 使用這個選項，如果您要上傳 HLS 以 AES 加密。 檔案必須已編碼及加密轉換的管理員。

### <a name="create-an-asset"></a>建立資產

資產是容器的多個類型的媒體服務，包括視訊、 音訊、 圖像、 縮圖的集合，文字追蹤及字幕檔案中的物件。 在 REST API，建立資產需要媒體服務傳送文章要求與邀請內文中放在您的資產的任何屬性資訊。

下列範例會示範如何建立資產。

**HTTP 要求**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45
    
    {"Name":"BigBuckBunny.mp4", "Options":"0"}
    

**HTTP 回應**

如果成功，則會傳回下列動作︰
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
### <a name="create-an-assetfile"></a>建立 AssetFile

[AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx)實體代表 blob 容器中所儲存的視訊或音訊檔案。 資產檔案一律資產，與相關聯，而資產可能包含一或多個 AssetFiles。 媒體服務編碼器工作失敗，如果資產檔案物件無法與 blob 容器中的數位檔案相關聯。

您可將數位媒體檔案上傳到 blob 容器之後，您可以使用**合併**HTTP 要求以您的媒體檔案 （如下所示的主題稍後的） 的相關資訊更新 AssetFile。 

**HTTP 要求**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP 回應**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>建立 AccessPolicy 寫入] 權限。 

將任何檔案上傳到 blob 儲存體前,，請先設定存取原則撰寫資產的權限。 若要執行這項作業，張貼到 AccessPolicies 實體組 HTTP 要求。 定義在建立 DurationInMinutes 值或是您收到 500 內部伺服器的錯誤訊息回回應。 如需有關 AccessPolicies 的詳細資訊，請參閱[AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx)。

下列範例會示範如何建立 AccessPolicy:
        
**HTTP 要求**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP 回應**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>取得上傳 URL

若要收到實際的上傳 URL，請建立 SA 定位器。 定位器定義要存取檔案資產的用戶端的開始時間] 和 [連線的結束點類型。 您可以建立指定 AccessPolicy 和資產組不同的用戶端要求和需求的多個定位項目。 每個這些定位器使用 [開始時間] 的值加上 AccessPolicy DurationInMinutes 值來判斷的 URL 可用的時間長度。 如需詳細資訊，請參閱[定位器](http://msdn.microsoft.com/library/azure/hh974308.aspx)。


SA URL 會包含下列格式︰

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

一些事項︰

- 您不能超過五個唯一的定位器一次指定的資產與相關聯。 如需詳細資訊，請參閱定位器。
- 如果您需要立即上傳檔案，您應該將您要的開始時間值，為 5 分鐘左右，再目前的時間。 這是因為可能會有時鐘 skew 用戶端電腦與媒體服務。 此外，您要開始時間的值必須是下列 DateTime 格式︰ YYYY-MM-DDTHH:mm:ssZ (例如，「 2014年-05-23T17:53:50Z 」)。   
- 可能有 30 40 秒延遲後定位器建立時，就可供使用。 若要同時 SA URL] 與 [Origin 定位器會發生這個問題。

下列範例會顯示如何建立 SA URL 定位器，所定義的 (如 SA 定位器為 「 1 」) 和 「 2 」 點播原點定位器邀請內文中的 [類型] 屬性。 傳回**路徑**屬性會包含您必須使用上傳檔案的 URL。
    
**HTTP 要求**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178
    
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP 回應**

如果成功，則會傳回下列回應︰
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>將檔案上傳到 blob 儲存體容器
    
一旦您有 AccessPolicy 和定位器設定時，會實際的檔案上傳到使用 Azure 儲存體 REST Api Azure Blob 儲存體容器。 您可以在頁面中上傳或封鎖二進位大型物件。 

>[AZURE.NOTE] 您必須新增您要上傳至定位器**路徑**所收到的值在前一節中的檔案的檔案名稱。 例如，https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

如需有關如何使用儲存空間 Azure blob 的詳細資訊，請參閱[Blob 服務 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)。


### <a name="update-the-assetfile"></a>更新 AssetFile 

既然您已上傳檔案，請更新 FileAsset 大小 （及其他） 資訊。 例如︰
    
    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP 回應**

如果成功，下列會傳回︰ HTTP/1.1 204 沒有內容

## <a name="delete-the-locator-and-accesspolicy"></a>刪除定位器及 AccessPolicy 

**HTTP 要求**


    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

    
**HTTP 回應**

如果成功，則會傳回下列動作︰

    HTTP/1.1 204 No Content 
    ...

**HTTP 要求**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 回應**

如果成功，則會傳回下列動作︰

    HTTP/1.1 204 No Content 
    ...

 
## <a id="configure_streaming_units"></a>設定 REST API 串流單位

使用其中一個最常見的案例進行調整的位元率串流至您的用戶端 Azure 媒體服務。 使用自動調整位元率串流、 用戶端可以切換到高或較低的位元率串流視訊會顯示根據目前的網路頻寬、 CPU 使用率和其他因素。 媒體服務支援下列調整位元率串流技術︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。 

媒體服務會提供動態包裝，可讓您進行調整位元率 MP4 或平滑串流編碼內容在資料流中支援的媒體服務 （MPEG 虛線 HLS、 平滑串流、 HDS） 格式，您不必重新封裝將這些串流格式。 

若要充分利用動態包裝，您需要執行下列動作︰

- 取得至少有一個串流單位**串流端點**您打算發表內容 （如本節所述）。
- 編碼或轉換成一組調整位元率 MP4 檔案或調整位元率平滑串流的檔案 （示範稍後在本教學課程編碼步驟），您 mezzanine （來源） 檔案  

動態包裝，您只需要儲存及支付中單一儲存格式的檔案與媒體服務建置，並提供適當的回應，根據用戶端的要求。 


>[AZURE.NOTE] 定價詳細資料的相關資訊，請參閱[媒體服務價格詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。

若要變更的串流保留的單位的數字，請執行下列動作︰
    
### <a name="get-the-streaming-endpoint-you-want-to-update"></a>取得您想要更新的串流端點

例如，在您的帳戶 （您可以有兩個串流端點，同時執行狀態）。 就讓我們開始的第一個資料流端點

**HTTP 要求**︰

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 回應**
    
如果成功，則會傳回下列動作︰
    
    HTTP/1.1 200 OK
    . . . 
    
### <a name="scale-the-streaming-endpoint"></a>不按比例縮放的串流端點
 
**HTTP 要求**︰

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
    Content-Type: application/json;odata=verbose
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    Host: wamsbayclus001rest-hs.cloudapp.net
    
    {"scaleUnits":1}

**HTTP 回應**

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
    request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:16:43 GMT
    Content-Length: 0

    
### <a id="long_running_op_status"></a>查看較長時間執行作業的狀態

分派的任何新的單位需要大約 20 分鐘才能完成。 若要檢查作業使用的**作業**方式的狀態，以及指定作業的識別碼。 作業**比例**要求回應中傳回識別碼。

    operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**HTTP 要求**︰
    
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    
**HTTP 回應**
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 515
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
    request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Fri, 16 Jan 2015 22:57:39 GMT
    
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
          },
          "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
          "State":"Succeeded",
          "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
          "ErrorCode":null,
          "ErrorMessage":null
       }
    }


## <a id="encode"></a>將一組調整位元率 MP4 檔案編碼來源檔案

之後 ingesting 可以編碼將媒體服務，媒體資產，transmuxed 的浮水印，以及等等，之前它傳送至用戶端。 這些活動的排程，並針對多個背景角色執行個體，以確保高效能與可用性執行。 下列活動稱為工作，每項[工作](http://msdn.microsoft.com/library/azure/hh974289.aspx)組成原子執行資產檔案的實際工時的任務。 

上述，當 Azure 媒體服務其中一個最常見的案例使用會將調整串流至您的用戶端的位元率。 媒體服務可以動態封裝成下列格式的其中一個的一組調整位元率 MP4 檔案︰ HTTP Live 串流 (HLS)、 平滑串流、 MPEG 破折號和 HDS （適用於只使用 Adobe 寶貴時間/存取授權）。 

若要充分利用動態包裝，您需要執行下列動作︰

- 編碼或轉換成一組調整位元率 MP4 檔案或調整位元率平滑串流檔案，您 mezzanine （來源） 檔案  
- 取得您要傳送您的內容串流端點至少有一個串流單位。 

[動作] 區段會顯示如何建立包含一個編碼工作的工作。 指定轉碼任務為一組調整位元率 MP4s 使用**Media Encoder 標準**mezzanine 檔案。 區段也會顯示如何監視處理進度的工作。 在工作完成時，您都可以建立定位器所需存取您的資產。 

### <a name="get-a-media-processor"></a>取得媒體處理器

在媒體服務媒體處理器會是處理特定處理的工作，例如編碼，格式轉換，加密，或解密媒體內容的元件。 針對編碼工作顯示在本教學課程中，我們可以使用媒體 Encoder 標準。

下列程式碼要求編碼器的識別碼。 

**HTTP 要求**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**HTTP 回應**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>建立工作

每個工作只能有一或多個工作，視您想要完成的處理的類型而定。 透過 REST API，您可以工作和其相關的工作中建立兩種方法之一︰ 工作可透過工作實體上的 [工作導覽] 屬性或 OData 批次處理內嵌定義。 媒體服務 SDK 使用批次處理。 不過，本主題中的程式碼範例的可讀性，任務是內嵌的定義。 批次處理資訊，請參閱[開放式資料通訊協定 (OData) 批次處理](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)。

下列範例會顯示如何建立和張貼使用任務設定為編碼在特定的解析度和品質視訊的工作。 下列文件區段包含所有[任務的預設](http://msdn.microsoft.com/library/mt269960)Media Encoder 標準處理器支援的清單。  

**HTTP 要求**
    
    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482
    
    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP 回應**

如果成功，則會傳回下列回應︰

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  
    
             ]
          }
       }
    }


有附註在任何工作要求的幾個重要事項︰

- TaskBody 屬性必須使用常值的 XML 定義的輸入或輸出資產所使用的工作。 任務主題包含 XML XML 結構描述定義。
- 在 TaskBody 定義中，每個內部值的<inputAsset>和<outputAsset>必須設定為 JobInputAsset(value) 或 JobOutputAsset(value)。
- 工作可以有多個輸出資產。 為工作中的工作成果只使用一次一個 JobOutputAsset(x)。
- 您可以指定 JobInputAsset 或 JobOutputAsset 列為輸入任務的資產。
- 工作必須並未構成循環圖。
- 您傳送至 JobInputAsset 或 JobOutputAsset 值參數代表資產索引值。 實際資產都定義在 InputMediaAssets 和 OutputMediaAssets 導覽上的內容工作實體定義。 

>[AZURE.NOTE] 透過媒體服務內建在 OData v3，因為參照 InputMediaAssets 和 OutputMediaAssets 導覽屬性集合中的個別資產 」 __metadata: uri 「 名稱 / 值組。 

- InputMediaAssets 會對應到媒體服務中建立的一個或多個資產。 系統會建立 OutputMediaAssets。 未參考現有的資產。
- OutputMediaAssets 可以使用 assetName 屬性來命名。 如果此屬性不存在，那麼 OutputMediaAsset 名稱就是任何內部文字的值<outputAsset>項目是含後稱謂的工作名稱] 的值或作業識別碼中的值 （不在已定義名稱屬性大小寫）。 例如，如果您設定 assetName 」 範例 」 的值，然後 OutputMediaAsset Name 屬性則會設定 「 範例 」。 不過，如果您未設定 assetName 的值，但未設定 「 NewJob 」 的工作名稱，然後 OutputMediaAsset 名稱就是 「 JobOutputAsset （值） _NewJob 」。 

    下列範例會顯示如何設定 assetName 屬性︰
    
        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- 若要啟用鏈結工作︰

    - 必須至少為兩個工作的工作。
    - 必須至少有一個其輸入是輸出的工作中的另一個任務的任務。

如需詳細資訊，請參閱[建立與媒體服務 REST API 編碼的作業](http://msdn.microsoft.com/library/azure/jj129574.aspx)。

### <a name="monitor-processing-progress"></a>處理進度的監視器

下列範例所示，您可以使用 [狀態] 屬性中，擷取工作狀態。 

**HTTP 要求**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP 回應**

如果成功，則會傳回下列回應︰

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT
    
    {"d":{"State":2}}


### <a name="cancel-a-job"></a>取消工作

媒體服務可讓您取消透過 CancelJob 函數執行工作。 這個呼叫傳回 400 錯誤程式碼，如果您嘗試取消工作取消其狀態時，才能取消、 錯誤，或完成。

下列範例會示範如何呼叫 CancelJob。


**HTTP 要求**


    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
    Host: wamsbayclus001rest-hs.net


如果成功，204 回應程式碼會傳回不郵件本文。

>[AZURE.NOTE] 您必須進行 URL 編碼的作業識別碼 (通常 nb:jid:UUID: somevalue) 將它做為參數傳遞 CancelJob。


### <a name="get-the-output-asset"></a>取得輸出資產 

下列程式碼示範如何要求輸出資產識別碼。 


**HTTP 要求**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
    x-ms-version: 2.11
    Host: wamsbayclus001rest-hs.cloudapp.net
    

**HTTP 回應**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT
        
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }



## <a id="publish_get_urls"></a>發佈資產取得串流和漸進下載 Url 以 REST API

若要將串流或下載資產，您必須 「 發佈 」，您可以建立定位器。 定位器提供存取資產中所包含的檔案。 媒體服務支援兩種類型的定位器︰ OnDemandOrigin 定位器，用來串流媒體 （例如，MPEG 破折號、 HLS 或平滑串流） 和存取簽章 (SA) 定位器用來下載媒體檔案。

一旦您建立定位器，您可以建立可用於串流或下載檔案的 Url。 


帶有平滑串流串流 URL 具有下列格式︰

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 串流 URL 具有下列格式︰

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG 虛線串流 URL 具有下列格式︰

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


下載檔案 SA URL 具有下列格式︰

    {blob container name}/{asset name}/{file name}/{SAS signature}

本節說明如何執行下列工作所需的 「 發佈 」 的資產。  

- 建立 AccessPolicy 讀取權限 
- 建立 SA URL 以下載內容 
- 建立資料流內容的原始 URL 

###<a name="creating-the-accesspolicy-with-read-permission"></a>建立 AccessPolicy 讀取權限

下載或之前串流任何媒體內容，請先定義 AccessPolicy 有讀取權限，然後建立的適當的定位器實體，指定您想要啟用您的用戶端傳送機制的類型。 如需可用的屬性的詳細資訊，請參閱[AccessPolicy 實體屬性](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties)。

下列範例會示範如何指定 AccessPolicy 的指定的資產的讀取權限。

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue
    
    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

如果成功，201 成功的程式碼會傳回描述您所建立的 AccessPolicy 實體。 然後，您會使用包含您想要建立定位器實體發表 （例如資產輸出） 的檔案的資產資產識別碼以及 AccessPolicy 識別碼。

>[AZURE.NOTE]
這項基本的工作流程是相同上傳檔案時 ingesting 資產 （如已之前在本主題中所述）。 此外，例如上傳的檔案，如果您 （或您的用戶端） 要立即存取您的檔案，請您開始時間值設定為 5 分鐘左右，再目前的時間。 這個動作，因為可能有時鐘 skew 用戶端和媒體服務之間。 [開始時間] 的值必須以下列 DateTime 格式︰ YYYY-MM-DDTHH:mm:ssZ (例如，「 2014年-05-23T17:53:50Z 」)。


###<a name="creating-a-sas-url-for-downloading-content"></a>建立 SA URL 以下載內容 

下列程式碼會顯示如何取得可以用來下載建立並上傳先前的媒體檔案的 URL。 AccessPolicy 有讀取權限設定，並定位器路徑參考 SA 下載 URL。

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

如果成功，則會傳回下列回應︰

    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }


傳回的**路徑**屬性會包含 SA URL。

>[AZURE.NOTE]
如果您要下載加密的儲存空間的內容，必須手動解密之前，呈現，或使用儲存空間解密 MediaProcessor 處理任務輸出 OutputAsset 清除處理的檔案，然後從該資產下載。 如需處理的詳細資訊，請參閱媒體服務 REST API 建立編碼的工作。 此外，SA URL 定位器無法更新後所建立。 例如，您無法重複使用相同的定位器更新的開始時間值。 這是因為建立 SA Url 的方式。 如果您想要存取下載的資產定位器過期之後，您必須建立一個新的新的開始時間。

###<a name="download-files"></a>下載檔案

一旦您擁有 AccessPolicy 和定位器設定，您可以下載使用 Azure 儲存體 REST Api 的檔案。  

>[AZURE.NOTE] 您必須新增您想要在前一節中收到的定位器**路徑**值下載檔案的檔案名稱。 例如，https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

如需有關如何使用儲存空間 Azure blob 的詳細資訊，請參閱[Blob 服務 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)。

當做執行舊版 （編碼調整 MP4 設定成） 編碼工作，您有多個 MP4 檔案，您可以逐步下載。 例如︰    
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
    
    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### <a name="creating-a-streaming-url-for-streaming-content"></a>建立串流 URL 以資料流內容


下列程式碼會顯示如何建立串流的 URL 定位器︰

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue
    
    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

如果成功，則會傳回下列回應︰

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT
        
    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

若要串流平滑串流來源中的 URL 串流媒體播放，您必須附加屬性名稱的平滑串流資訊清單檔案，後面再加上 「 / 資訊清單 」 的路徑。

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

若要將串流 HLS，附加 (格式 = m3u8 aapl) 之後 」 / 資訊清單 」。

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

若要將串流 MPEG 虛線，附加 (格式 = mpd-時間-csf) 之後 」 / 資訊清單 」。

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>播放您的內容  

若要串流視訊，使用[Azure 媒體服務播放程式](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。

若要測試漸進下載，將 URL 貼到瀏覽器 （例如，IE、 Chrome、 Safari）。


##<a name="next-steps-media-services-learning-paths"></a>後續步驟︰ 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="looking-for-something-else"></a>想找其他項目？

如果本主題未包含什麼您所預期，就會缺少項目，或其他方式不符合您的需求，請提供我們使用 Disqus 執行緒下您的意見反應。



