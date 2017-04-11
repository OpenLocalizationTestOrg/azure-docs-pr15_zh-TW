<properties 
    pageTitle="加密使用反飛彈系統 REST API 儲存加密的內容" 
    description="瞭解如何以使用反飛彈系統 REST Api 儲存加密加密您的內容。" 
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
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="encrypting-your-content-with-storage-encryption-using-ams-rest-api"></a>加密使用反飛彈系統 REST API 儲存加密的內容

強烈建議加密本機使用 AES 256 元加密內容，然後上傳至 Azure 儲存體會儲存在其他加密。

本文提供反飛彈系統儲存加密的概觀，並說明如何上傳加密的儲存空間內容︰

- 建立內容索引鍵。
- 建立資產。 建立資產時，則您可以將 StorageEncryption AssetCreationOption。

     加密的資產一定要與內容索引鍵相關聯。
- 連結資產的內容索引鍵。  
- 將加密設定相關 AssetFile 實體參數。
 
>[AZURE.NOTE]如果您想要進行儲存加密的資產，您必須設定資產的傳遞原則。 可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則的內容。 如需詳細資訊，請參閱[設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)。


>[AZURE.NOTE] 使用媒體服務 REST API 工作時，下列事項︰
>
>當存取媒體服務中的項目，您必須在 HTTP 邀請中設定特定的標題欄位和值。 如需詳細資訊，請參閱[媒體服務 REST API 開發的設定](media-services-rest-how-to-use.md)。

>成功連接之後要 https://media.windows.net，您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI[連線至媒體服務使用 REST API](media-services-rest-connect-programmatically.md)所述。 

##<a name="storage-encryption-overview"></a>儲存加密概觀 

反飛彈系統儲存加密適用於整個檔案**AES CTR**模式加密。  AES CTR 模式會的區塊密碼，可以將加密的任意長度的資料，而不需要使用與邊框距離。 加密與 AES 演算法然後 XOR-ing AES 的輸出資料加密或解密計數器區塊的作用。  使用 [計數器封鎖建構 InitializationVector 的值，複製到 [計數器值 0 到 7 位元組，到 15 8 位元組計數器值皆設定為零。 16 位元計數器封鎖的位元組 8 到 15 （亦即最不重要的位元組） 會使用簡單的 64 位元正負號的整數，就會增加 1 的每個資料的後續區塊處理，並且會保留在網路位元組順序。 請注意，如果此整數達到最大值 (0xFFFFFFFFFFFFFFFF) 然後遞增重設為零 （位元組 8 到 15） 封鎖計數器而不會影響其他 64 位元的計數器 （也就是 0 到 7 個位元組）。   若要維持 AES CTR 模式加密的安全性，請指定的金鑰識別項，每個內容索引鍵的 InitializationVector 值應該是唯一的每個檔案和檔案必須小於 2 ^64 區塊的長度。  這是為了確保計數器值永遠不會使用指定的金鑰重複使用。 如需有關 CTR 模式的詳細資訊，請參閱[此 wiki 頁面](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR)（wiki 文章所使用的字詞，而不是 「 InitializationVector 」 的 「 Nonce 」）。

使用**儲存加密**加密本機使用 AES 256 元加密清除內容，然後上傳至 Azure 儲存體將其儲存在其他加密。 受到保護的儲存空間加密的資產會自動加密放在之前編碼，加密的檔案系統中和您也可以選擇重新加密之前上傳回到為新的輸出資產。 儲存加密的主要的使用情況時，您要保護高品質輸入的媒體檔案與在其他加密磁碟上。

才能進行儲存空間的加密的資產，您必須設定資產的傳遞原則，讓媒體服務可讓您知道您要傳送您的內容的方式。 可以傳送您的資產之前，串流伺服器移除儲存加密，並會傳送到您使用指定的傳遞原則 （例如，AES、 一般加密或不加密） 的內容。

##<a name="create-contentkeys-used-for-encryption"></a>建立用於加密 ContentKeys

加密的資產有儲存加密金鑰與相關聯。 您必須建立要用於建立資產檔案之前，先加密的內容索引鍵。 本節說明如何建立內容索引鍵。

以下是產生您將會與您想要加密的資產建立關聯的內容索引鍵的一般步驟。 

1. 儲存加密的隨機產生 32 位元 AES 金鑰。 

    這是您的資產，這表示該資產相關聯的所有檔案都必須使用相同的內容索引鍵解密期間的內容索引鍵。 
2.  呼叫[GetProtectionKeyId](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkeyid)和[GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey)方法以取得正確的 X.509 憑證必須使用加密您內容的金鑰。
3.  加密您內容的金鑰以公開金鑰 X.509 憑證。 

    媒體服務.NET SDK 使用 RSA OAEP 時執行加密。  您可以看到[EncryptSymmetricKeyData 函數](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)中的.NET 範例。
4.  建立導出使用 [重要的識別碼] 與 [內容索引鍵的總和。 下列.NET 範例計算使用 GUID 組件的金鑰識別碼和清除內容索引鍵的總和。
    

        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
        {
            const int ChecksumLength = 8;
            const int KeyIdLength = 16;

            byte[] encryptedKeyId = null;

            // Checksum is computed by AES-ECB encrypting the KID
            // with the content key.
            using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
            {
                rijndael.Mode = CipherMode.ECB;
                rijndael.Key = contentKey;
                rijndael.Padding = PaddingMode.None;

                ICryptoTransform encryptor = rijndael.CreateEncryptor();
                encryptedKeyId = new byte[KeyIdLength];
                encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
            }

            byte[] retVal = new byte[ChecksumLength];
            Array.Copy(encryptedKeyId, retVal, ChecksumLength);

            return Convert.ToBase64String(retVal);
        }


5. 建立內容索引鍵的**EncryptedContentKey** （轉換為 base64 編碼字串）、 **ProtectionKeyId**、 **ProtectionKeyType**、 **ContentKeyType**，和您在上述步驟中收到的**檢查值**值。

    
    儲存加密的下列屬性應該包含在邀請內文中。
     
    邀請本文屬性   | 描述
    ---|---
    識別碼 | 我們會產生自己 ContentKey 識別碼使用下列的格式，「 nb:kid:UUID:<NEW GUID>」。
    ContentKeyType | 這是整數，此內容索引鍵的重要的內容類型。 我們傳遞儲存加密的值 1。
    EncryptedContentKey | 我們建立一個新內容索引鍵值 256 位元 （32 位元組） 值。 使用我們來擷取從 Microsoft Azure 媒體服務 GetProtectionKeyId 和 GetProtectionKey 方法執行 HTTP GET 要求儲存加密 X.509 憑證來加密金鑰。 例如，請參閱下列.NET 程式碼︰ 定義的**EncryptSymmetricKeyData**方法[以下](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)。
    ProtectionKeyId | 這是用來加密我們內容索引鍵的儲存空間加密 X.509 憑證的保護識別碼。
    ProtectionKeyType | 這是用來加密內容索引鍵的保護索引鍵的加密類型。 此值是 StorageEncryption(1) 我們的範例。
    檢查值 |計算的 md5 內容索引鍵。 它會計算加密內容識別碼與內容索引鍵。 在 [程式碼範例會示範如何計算總和。
    

###<a name="retrieve-the-protectionkeyid"></a>擷取 ProtectionKeyId 
 

下列範例會示範如何擷取 ProtectionKeyId，認證指紋，您必須使用加密您內容的識別碼憑證。 請執行此步驟中，請確定您的電腦上已經有適當的憑證。


要求︰
    
    
    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    

回應︰
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

###<a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>擷取 ProtectionKeyId ProtectionKey

下列範例會示範如何擷取您在上一個步驟中收到的 X.509 憑證使用 ProtectionKeyId。

要求︰
        
    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net
    


回應︰
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>建立內容索引鍵 

您有擷取的 X.509 憑證，並用於其公開金鑰加密您內容的金鑰之後，請建立**ContentKey**實體，然後依此設定其屬性的值。

其中一個值，您必須將何時建立內容索引鍵是類型。 若儲存加密值是 '1'。 

下列範例顯示如何建立**ContentKey**以**ContentKeyType**儲存加密 (「 1 」)，以表示保護金鑰 Id 是 X.509 憑證指紋**ProtectionKeyType**設定為 「 0 」。  


要求

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }


回應︰
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>建立資產

下列範例會示範如何建立資產。

**HTTP 要求**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny" "Options":1}


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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
##<a name="associate-the-contentkey-with-an-asset"></a>建立 ContentKey 與資產的關聯

建立之後 ContentKey，其關聯資產使用 $links 作業，如下列範例所示︰
    
要求︰
    
    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

回應︰

    HTTP/1.1 204 No Content 

##<a name="create-an-assetfile"></a>建立 AssetFile

[AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx)實體代表 blob 容器中所儲存的視訊或音訊檔案。 資產檔案一律資產，與相關聯，而資產可能包含一或多個資產的檔案。 媒體服務編碼器工作失敗，如果資產檔案物件無法與 blob 容器中的數位檔案相關聯。

請注意， **AssetFile**執行個體和實際的媒體檔案兩個不同的物件。 媒體檔案包含實際的媒體內容時，AssetFile 執行個體包含媒體檔案相關的中繼資料。

您可將數位媒體檔案上傳到 blob 容器之後，您會使用**合併**HTTP 要求以您 （在本主題中未顯示） 的媒體檔案的相關資訊更新 AssetFile。 

**HTTP 要求**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
