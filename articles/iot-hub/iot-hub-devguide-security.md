<properties
 pageTitle="開發人員指南-IoT 中心來控制存取 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-如何控制 IoT 中樞的存取及管理安全性"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>控制存取權 IoT 中心

## <a name="overview"></a>概觀

本文將說明保護 IoT 中心的選項。 IoT 中心使用*權限*授與存取權每個 IoT 中心結束點。 權限限制的存取權依據功能 IoT 中心。

本文將說明︰

- 您可以授與裝置或後端的應用程式，來存取您的 IoT 集散地到不同的權限。
- 驗證程序，它會使用以驗證權限的權杖。
- 如何設定範圍的認證以限制存取特定的資源。
- IoT 中心支援的 X.509 憑證。
- 使用現有的裝置身分識別登錄或驗證配置的自訂裝置驗證機制。

### <a name="when-to-use"></a>使用時機

您必須有適當的權限存取的任何 IoT 中心端點。 例如，在裝置必須包含包含安全性憑證，以及每一封郵件會傳送到 IoT 集線器的權杖。

## <a name="access-control-and-permissions"></a>存取控制及權限

您可以透過下列方式來授與[權限](#iot-hub-permissions)︰

* **中樞層級共用存取原則**。 共用的 access 原則可以授與[權限](#iot-hub-permissions)的任何組合。 您可以在[Azure 入口網站]定義原則[lnk-management-portal]，或以程式設計方式使用[IoT 中心資源提供者 REST Api][lnk-resource-provider-apis]。 新建立的 IoT 中心具有預設的原則︰

    - **iothubowner**︰ 所有的權限原則。
    - **服務**︰ ServiceConnect 權限原則。
    - **裝置**︰ DeviceConnect 權限原則。
    - **registryRead**: RegistryRead 權限原則。
    - **registryReadWrite**︰ 以 RegistryRead 和 RegistryWrite 權限的原則。


* **每個裝置安全性認證**。 每個 IoT 中心包含[裝置身分識別登錄][lnk-identity-registry]。 針對此登錄中每個裝置，您可以設定授與**DeviceConnect**權限範圍的相對應的裝置端點的安全性憑證。

例如，在一般 IoT 方案︰

- 裝置管理元件使用*registryReadWrite*原則。
- 事件處理器元件要使用的*服務*原則。
- 執行階段裝置商務邏輯元件使用的*服務*原則。
- 使用 IoT 中樞的身分識別登錄中儲存的認證連線個別的裝置。

## <a name="authentication"></a>驗證

Azure IoT 中心授與存取權結束點，以驗證的權杖針對共用的存取原則和裝置身分識別登錄安全性認證。

安全性憑證，例如對稱金鑰，不會傳送網路連線。

> [AZURE.NOTE] Azure IoT 中心資源提供者受到保護 Azure 訂閱，透過[Azure 資源管理員]中的所有提供者所[lnk-azure-resource-manager]。

如需有關如何建立和使用安全性權杖的詳細資訊，請參閱[IoT 中心安全性權杖][lnk-sas-tokens]。

### <a name="protocol-specifics"></a>通訊協定細節

每個支援的通訊協定，例如 MQTT、 AMQP 及 HTTP]，以不同的方式傳送權杖。

連線封包時使用 MQTT，具有 ClientId，{iothubhostname} deviceId / {deviceId} 中的使用者名稱] 欄位中，並在 [密碼] 欄位中的 SA 權杖。 {iothubhostname} 應該 IoT 中心 (例如，devices.net contoso.azure) 的完整 CName。

使用[AMQP]時[lnk-amqp]，IoT 中心支援[SASL 純][lnk-sasl-plain]和[AMQP 宣告--安全性][lnk-cbs]。

如果您使用 AMQP 宣告--安全性，標準會指定如何傳送這些權杖。

針對 SASL 純，可以使用**的使用者名稱**︰

* `{policyName}@sas.root.{iothubName}`如果使用的中心層級權杖。
* `{deviceId}@sas.{iothubname}`如果使用的裝置範圍權杖。

在這兩種情況下，[密碼] 欄位包含權杖， [IoT 中心安全性權杖]所述[lnk-sas-tokens]。

HTTP 實作驗證的方式，包括**授權**要求標頭的有效的權杖。

#### <a name="example"></a>範例

使用者名稱 （DeviceId 是區分大小寫）︰`iothubname.azure-devices.net/DeviceId`

密碼 （產生裝置的檔案總管 SA）︰`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT 中心 Sdk] [lnk-sdks]連線至服務時，會自動產生權杖。 在某些情況下，Sdk 不支援所有的通訊協定或所有驗證方法。

### <a name="special-considerations-for-sasl-plain"></a>SASL 純的特殊考量

時使用 AMQP SASL 純，連線到 IoT 中樞的用戶端可以使用單一針對每一個 TCP 連線。 權杖到期、 TCP 連線中斷與服務的連線，觸發重新連線。 這個問題，請時不有問題的應用程式後端元件，是非常損壞的裝置端應用程式的原因如下︰

*  閘道器通常連線代表許多裝置。 在使用 SASL 純時，他們擁有建立不同的 TCP 連接的每個裝置連接到 IoT 中心。 這種情況相當增加消耗和網路資源，並增加的每一種裝置連線的延遲。
* 具有資源限制式裝置影響所使用的每個 token 到期後重新連接的資源增加。

## <a name="scope-hub-level-credentials"></a>範圍中心層級的認證

您可以使用受限制的資源 URI 建立權杖範圍中心層級安全性原則。 例如，從裝置傳送裝置至雲端郵件端點是**/devices/ {deviceId} / 郵件/事件**。 您也可以使用具有**DeviceConnect**權限的中心層級共用的存取原則，登入其 resourceURI 是**/devices/ {deviceId}**的權杖。 這種方式建立只有在傳送郵件代表裝置**deviceId**可用的權杖。

這個機制很類似[事件集線器 publisher 原則][lnk-event-hubs-publisher-policy]，並可讓您實作自訂的驗證方法。

## <a name="security-tokens"></a>安全性權杖

IoT 中心使用安全性權杖驗證裝置和服務，避免傳送的網路上的按鍵。 此外，安全性權杖僅限於有效時間及範圍。 [Azure IoT 中心 Sdk] [lnk-sdks]自動產生權杖，而不需要任何特殊設定。 某些情況下，不過，要求使用者產生，並直接使用安全性權杖。 這些包括 MQTT、 AMQP 或 HTTP 表面，直接使用或權杖服務圖樣實作[自訂裝置驗證]所述[lnk-custom-auth]。

IoT 中心也可讓裝置 IoT 中樞的驗證使用[X.509 憑證][lnk-x509]。 

### <a name="security-token-structure"></a>安全性權杖結構
您可以使用安全性權杖授與時間限制存取的裝置和服務 IoT 中心中的特定功能。 若要確保只有授權的裝置和服務可以連線，必須與共用的存取原則鍵或儲存在登錄中識別裝置身分識別與對稱金鑰登安全性權杖。

權杖簽署具有相關聯的共用的存取原則權限的所有功能共用的 access 原則鍵授與存取權。 另一方面，裝置身分識別的對稱金鑰簽署的權杖只授與相關聯的裝置身分識別的**DeviceConnect**權限。

安全性權杖具有下列格式︰

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

以下是預期的值︰

| 值 | 描述 |
| ----- | ----------- |
| {簽章} | 表單的 HMAC SHA256 簽章字串︰ `{URL-encoded-resourceURI} + "\n" + expiry`。 **重要事項**︰ 會從 base64 解碼索引鍵，並將其用於鍵執行 HMAC SHA256 計算。 |
| {resourceURI} | URI 前置詞 （依區段） 的可存取此 token 加，主機名稱的 IoT 中心 （沒有通訊協定） 以開始與結束點。 例如，`myHub.azure-devices.net/devices/device1` |
| {到期} | UTF8 字串自上 1 年 1 月 1970年的期間 00:00:00 UTC 的秒數。 |
| {URL-編碼-resourceURI} | 較低種情況下 URL 編碼的小寫資源 URI |
| {包含} | 共用的 access 原則此 token 加所參照的名稱。 如果參照裝置登錄認證的權杖不存在。 |

**前置詞的相關附註**︰ URI 前置詞計算區段而不是字元。 例如`/a/b`的首`/a/b/c`而不是用於`/a/bc`。

下列 Node.js 片段顯示稱為**generateSasToken**計算從輸入權杖函數`resourceUri, signingKey, policyName, expiresInMins`。 下一節中詳細說明如何初始化不同的權杖使用案例的不同輸入。

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

比較，以產生安全性 token 相當於 Python 程式碼是︰

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] 由於 IoT 中心機器上驗證的權杖有效，請務必產生的權杖之電腦的時鐘積雪是最小化。

### <a name="use-sas-tokens-in-a-device-client"></a>在 [裝置用戶端中使用 SA 權杖

有兩種方式取得**DeviceConnect**權限與安全性權杖 IoT 中心︰ 使用[從裝置身分識別登錄對稱裝置鍵](#use-a-symmetric-key-in-the-identity-registry)，或使用[共用的 access 原則鍵](#use-a-shared-access-policy)。

請記住，可從裝置存取的所有功能，都公開設計與前置詞的端點上`/devices/{deviceId}`。

> [AZURE.IMPORTANT] IoT 中心送給特定裝置的唯一方式使用的裝置身分識別對稱金鑰。 在情況下時的共用的存取原則會用來存取裝置的功能，方案必須考慮發出安全性 token，為受信任的子元件的元件。

裝置具端點是 （不管資料通訊協定）︰

| 結束點 | 功能 |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | 傳送裝置至雲端的郵件。 |
| `{iot hub host name}/devices/{deviceId}/devicebound` | 收到的雲端-裝置郵件。 |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>使用 [身分識別登錄中的 [對稱金鑰

使用裝置身分識別的對稱金鑰產生的權杖包含時 (`skn`) 省略權杖的項目。

例如，若要存取所有裝置功能建立的權杖應有下列參數︰

* 資源 URI: `{IoT hub name}.azure-devices.net/devices/{device id}`，
* 登入索引鍵︰ 任何對稱金鑰`{device id}`身分識別
* 沒有原則名稱，
* 任何到期時間。

會使用上述節點函數的範例︰

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

結果，授與存取 device1 的所有功能，就會︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] 可以產生的安全使用.NET 工具[裝置 Explorer]的權杖[lnk-device-explorer]。

### <a name="use-a-shared-access-policy"></a>使用共用的 access 原則

建立與共用的存取原則的權杖，當原則名稱時欄位`skn`必須設定為使用原則的名稱。 也需要原則授與**DeviceConnect**權限。

使用共用的 access 原則存取裝置功能的兩個主要的情況如下︰

* [雲端通訊協定閘道][lnk-endpoints]，
* [權杖服務][lnk-custom-auth]用來執行自訂驗證配置。

由於共用的存取原則可能可以授與存取權以任何裝置連線，請務必建立安全性權杖時，請使用正確的資源 URI。 這是特別重要的權杖服務必須範圍使用資源 URI 特定裝置的 token。 這是較不相關的通訊協定閘道器時它們會已經交通量的所有裝置。

作為範例，token 服務使用**的裝置**預先建立的共用的存取原則會建立權杖，使用下列參數︰

* 資源 URI: `{IoT hub name}.azure-devices.net/devices/{device id}`，
* 登入索引鍵︰ 索引鍵的其中一項`device`原則，
* 原則名稱︰ `device`，
* 任何到期時間。

會使用上述節點函數的範例︰

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

結果，授與存取 device1 的所有功能，就會︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

通訊協定的閘道器可以使用相同的權杖的所有裝置，只要設定資源 URI 至`myhub.azure-devices.net/devices`。

### <a name="use-security-tokens-from-service-components"></a>使用安全性權杖，從服務元件

服務元件只能產生安全性權杖使用共用的存取權授與適當的權限，先前所述的原則。

以下是公開的端點上服務功能︰

| 結束點 | 功能 |
| ----- | ----------- |
| `{iot hub host name}/devices` | 建立、 更新、 擷取和刪除裝置身分識別。 |
| `{iot hub host name}/messages/events` | 收到裝置至雲端的訊息。 |
| `{iot hub host name}/servicebound/feedback` | 接收雲端-裝置郵件的意見反應。 |
| `{iot hub host name}/devicebound` | 傳送雲端至裝置的郵件。 |

例如，服務，產生使用**registryRead**預先建立的共用的存取原則會建立權杖下列參數︰

* 資源 URI: `{IoT hub name}.azure-devices.net/devices`，
* 登入索引鍵︰ 索引鍵的其中一項`registryRead`原則，
* 原則名稱︰ `registryRead`，
* 任何到期時間。

    var 端點 = 「 myhub.azure-devices.net/devices 」;  var 包含 = '裝置 」;  var policyKey =...]。

    var 權杖 = generateSasToken 端點、 policyKey (包含，60）;

結果，想要授與存取權讀取所有裝置身分識別，是︰

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>支援的 X.509 憑證

您可以使用任何的 X.509 憑證驗證 IoT 中樞的裝置。 包括︰

-   **現有的 X.509 憑證**。 在裝置可能已有與其相關聯的 X.509 憑證。 裝置可以使用這個憑證驗證 IoT 中心。

-   **自動產生，自我簽署 X-509 憑證**。 裝置製造商或內部部署者可以產生下列憑證，並儲存在裝置上的對應私密金鑰 （和憑證）。 您可以使用工具，例如[OpenSSL] [lnk-openssl]和[Windows SelfSignedCertificate] [lnk-selfsigned]公用程式的意思。

-   **CA 簽章的 X.509 憑證**。 您也可以使用 X.509 憑證產生與簽署的憑證授權單位 (CA) 來識別裝置及驗證 IoT 中樞的裝置。

在裝置可能會使用 X.509 憑證或安全性憑證驗證]，但是不能同時。

### <a name="register-an-x509-client-certificate-for-a-device"></a>註冊裝置的相關 X.509 用戶端憑證

[Azure IoT 服務 SDK C#] [ lnk-service-sdk] (版本 1.0.8+) 支援註冊使用 X.509 用戶端憑證驗證的裝置。 匯入/匯出的裝置等其他 Api 也支援的用戶端 X.509 憑證。

### <a name="c-support"></a>C\#支援

**RegistryManager**課程提供註冊裝置程式設計的方式。 特別的**AddDeviceAsync**和**UpdateDeviceAsync**方法可讓使用者以登錄及更新 Iot 中心裝置身分識別登錄中的裝置。 這兩種方法輸入會**裝置**執行個體。 **裝置**類別包含**驗證**屬性可讓使用者指定主要和次要 X.509 憑證憑證碼。 指紋代表 sha-1 雜湊 X.509 憑證 （使用二進位 DER 編碼儲存）。 使用者可以選擇指定主要指紋或次要指紋或兩者。 若要處理憑證變換案例支援主要和次要憑證碼。

> [AZURE.NOTE] 不需要或儲存的整個 X.509 用戶端憑證，只指紋 IoT 中心。

以下是範例 C\#註冊使用 X.509 用戶端憑證的裝置的程式碼片段︰

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>在執行階段作業期間使用 X.509 用戶端憑證

[Azure IoT 裝置 SDK.NET] [ lnk-client-sdk] (版本 1.0.11+) 支援的用戶端的 X.509 憑證使用。

### <a name="c-support"></a>C\#支援

課程**DeviceAuthenticationWithX509Certificate**支援建立使用 X.509 用戶端憑證 **DeviceClient**執行個體。

以下是範例程式碼片段︰

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>自訂裝置驗證

您可以使用 IoT 中心[裝置身分識別登錄][lnk-identity-registry]設定每個裝置安全性認證，並存取控制使用[權杖][lnk-sas-tokens]。 不過，如果 IoT 解決方案已經有大量的投資，自訂裝置的身分識別登錄及/或驗證配置中，您可以整合現有的基礎結構與 IoT 中心來建立*權杖服務*。 如此一來，您可以使用其他 IoT 功能在您的方案中。

權杖服務是自訂的雲端服務。 它使用 IoT 中心*共用的存取原則* **DeviceConnect**權限來建立*裝置範圍*權杖。 這些權杖啟用裝置連接到您的 IoT 集散地。

  ![權杖服務模式中的步驟][img-tokenservice]

權杖服務模式的主要步驟如下︰

1. 建立共用的 IoT 中心存取原則**DeviceConnect**權 IoT 中心。 您可以在[Azure 入口網站]中建立此原則[lnk-management-portal]或以程式設計方式。 權杖服務會用來登入，它會建立權杖的此原則。
2. 裝置需要存取 IoT 中心，它會要求的已簽署的權杖 token 服務。 裝置可以使用您自訂的裝置身分識別登錄/驗證配置來識別 token 服務用來建立權杖裝置身分驗證。
3. 權杖服務會傳回一個權杖。 使用建立權杖`/devices/{deviceId}`為`resourceURI`，與`deviceId`為通過驗證的裝置。 權杖服務使用共用的 access 原則，來建構權杖。
4. 裝置使用權杖，直接使用 [IoT 中心。

> [AZURE.NOTE] 您可以使用.NET 類別[SharedAccessSignatureBuilder] [lnk-dotnet-sas]或 Java 類別[IotHubServiceSasToken] [lnk-java-sas]建立權杖，在您的權杖服務。

權杖服務可以視需要設定 token 到期日。 當權杖到期時，[IoT 中心伺服器裝置連線。 然後，裝置必須從 token 服務要求新的憑證。 如果您使用的簡短的到期時間，這個動作會增加權杖服務和裝置上的載入。

連線到您的中樞的裝置，您必須仍將其新增至 IoT 中心裝置身分識別登錄機碼，即使裝置使用權杖並不是裝置索引鍵連線。 因此，您可以繼續使用啟用或停用裝置身分識別[IoT 中心身分識別登錄]中的每個裝置存取控制[lnk-identity-registry]裝置時驗證的權杖。 如此可減輕風險的權杖使用完整的到期時間。

### <a name="comparison-with-a-custom-gateway"></a>自訂的閘道器的比較

權杖服務模式是實作自訂的身分識別登錄/驗證配置與 IoT 中樞的建議的方式。 建議因為 IoT 中心會繼續處理大部分的解決方案流量。 然而，有位置自訂驗證配置已因此涉及服務處理所有流量 （*自訂的閘道*） 是必要的通訊協定的情況。 此範例是[傳輸層安全性 (TLS) 與預先共用索引鍵 (PSKs)][lnk-tls-psk]。 如需詳細資訊，請參閱[通訊協定閘道器][lnk-protocols]主題。

## <a name="reference-topics"></a>參考主題︰

下列主題提供關於控制存取權 IoT 中心的詳細資訊。

## <a name="iot-hub-permissions"></a>IoT 中心權限

下表列出可用來控制您 IoT 中樞的存取權限。

| 權限            | 備忘稿 |
| --------------------- | ----- |
| **RegistryRead**      | 授與讀取裝置身分識別登錄的存取權。 如需詳細資訊，請參閱[裝置身分識別登錄][lnk-identity-registry]。 |
| **RegistryReadWrite** | 讀取和寫入存取權的裝置身分識別登錄機碼。 如需詳細資訊，請參閱[裝置身分識別登錄][lnk-identity-registry]。 |
| **ServiceConnect**    | 授與存取雲端服務具通訊和監視結束點。 例如，授與權限後端雲端服務接收裝置至雲端郵件、 傳送雲端-裝置訊息，並擷取相對應的遞送通知。 |
| **DeviceConnect**     | 授與存取裝置具通訊結束點。 例如，授與權限可傳送裝置至雲端郵件並接收雲端-裝置訊息。 裝置使用此權限。 |

## <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已經學會如何控制 IoT 中樞的存取，您可能會感興趣的下列開發人員指南主題︰

- [使用同步處理狀態和設定裝置盡相同][lnk-devguide-device-twins]
- [叫用的裝置上直接方法][lnk-devguide-directmethods]
- [多個裝置上的排程工作][lnk-devguide-jobs]

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [開始使用 Azure IoT 中心][lnk-getstarted-tutorial]
- [如何傳送郵件雲端-裝置 IoT 中心][lnk-c2d-tutorial]
- [如何處理 IoT 中心裝置至雲端的訊息][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
