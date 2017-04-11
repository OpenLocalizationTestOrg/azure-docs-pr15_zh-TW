<properties
    pageTitle="共用 Access 簽章概觀 |Microsoft Azure"
    description="什麼是共用的 Access 簽章，它們是如何運作，以及如何使用這些節點、 PHP，及 C#。"
    services="service-bus"
    documentationCenter="na"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/02/2016"
    ms.author="darosa;sethm"/>

# <a name="shared-access-signatures"></a>共用的 Access 簽章

*共用 Access 簽章*(SA) 主要安全性機制的服務，包括事件集線器代理訊息 （佇列及主題），而且轉送訊息。 本文將告訴您共用的 Access 簽章、 其的運作方式，以及如何使用適用於多種平台的方式。

## <a name="overview-of-sas"></a>SA 的概觀

共用的 Access 簽章會根據 sha-256 安全雜湊或 Uri 驗證機制。 SA 是很強大的機制所使用的所有服務匯流排服務。 在 [實際使用，SA 有兩個元件︰*共用存取原則*] 和 [*共用的 Access 簽章*（通常稱為 「*權杖*）。

您可以尋找[共用的 Access 簽章驗證服務匯流排](service-bus-shared-access-signature-authentication.md)關於共用與服務匯流排存取簽章的詳細的資訊。

## <a name="shared-access-policy"></a>共用的 Access 原則

若要瞭解有關 SA 重要的是全部開始與原則]。 針對每個原則，您決定使用哪種資訊的三種︰**名稱**、**範圍**、 和**權限**。 **名稱**是指;在該範圍內的唯一名稱。 範圍很容易︰ 是問題資源 URI。 服務匯流排命名空間，範圍的完整的網域名稱 (FQDN)，例如`https://<yournamespace>.servicebus.windows.net/`。

可用的權限原則的是主要自我闡明︰

  + 傳送
  + 聆聽
  + 管理

建立原則之後，會被指派*主索引鍵*和*次要索引鍵*。 這些是強式密碼編譯的按鍵。 不在其遺失或遺漏這些-它們會永遠可以在[Azure 入口網站][]。 您可以使用其中一個產生的索引鍵，並隨時都可以產生。 不過，如果您重新產生，或變更原則的主索引鍵，將失效它建立任何共用 Access 簽章。

當您建立的服務匯流排命名空間時，整個命名空間稱為**RootManageSharedAccessKey**，會自動建立原則，而且此原則有所有的權限。 您沒有登入**根**，因此很適合的理由不使用此原則。 命名空間入口網站中的 [**設定**] 索引標籤中，您可以建立其他的原則。 請務必以單一樹狀目錄中服務匯流排層級的附註 (命名空間，佇列中，[事件] 中心內，等) 只能有最多 12 原則附加。

## <a name="shared-access-signature-token"></a>共用的 Access 簽章 （權杖）

原則本身無法存取權杖的服務。 就從存取權杖會產生-使用 [主要或次要鍵的物件。 產生的權杖謹慎製作以下列格式的字串︰

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

位置`signature-string`是 sha-256 雜湊範圍的權杖 （**範圍**上一節所述） 的附加 CRLF 與的到期時間 (秒數期間自︰`00:00:00 UTC`上 1 年 1 月 1970年)。

雜湊看起來就像下列虛擬程式碼，並傳回 32 位元。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

非雜湊值是**SharedAccessSignature**字串中，讓收件者可以計算雜湊使用相同的參數，以確定會傳回相同的結果。 URI 指定範圍，並機碼名稱識別要用來計算雜湊原則。 這是從安全性的觀點來看重要。 如果簽章不符，它會計算收件者 （服務匯流排），然後存取。 現在您可以確定寄件者有權存取的索引鍵，並應授與權限原則中指定。

## <a name="generating-a-signature-from-a-policy"></a>產生簽章的原則

您如何實際進行此程式碼？ 讓我們來看看以下幾個。

### <a name="nodejs"></a>NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### <a name="java"></a>Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### <a name="php"></a>PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time();  
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256',             
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
return $token; 
}
```
 
### <a name="c35"></a>C & #35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="using-the-shared-access-signature-at-http-level"></a>使用共用的 Access 簽章 （在 HTTP 層級）
 
現在您已經知道如何建立共用的 Access 簽名服務匯流排中的任何實體，是準備好執行 HTTP 文章︰

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
    
請記住，這適用於所有項目。 您可以建立 SA 佇列中、 主題、 訂閱、 事件] 中心內，或轉送。 如果您使用 publisher 每個身分識別的事件集線器時，您只要新增`/publishers/< publisherid>`。

如果您允許的寄件者或用戶端的 SA 權杖，對方沒有金鑰直接，及他們無法回復的取得方式雜湊。 因此，您可以控制在他們可以存取的內容，以及如何 long。 記住重要的是如果您變更原則的主索引鍵，將會失效它建立任何共用 Access 簽章。

## <a name="using-the-shared-access-signature-at-amqp-level"></a>使用共用的 Access 簽章 （在 AMQP 層級）

在上一節中，您會看到如何使用 SA 權杖 HTTP 文章要求傳送資料至服務匯流排。 您知道，您可以存取服務匯流排使用進階訊息佇列通訊協定 (AMQP) 的效能，在許多情況下使用慣用的通訊協定。 在文件中的工作草稿自 2013年，但完善支援 Azure 今天[AMQP Claim-Based 安全性 1.0 版](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)說明 AMQP SA token 用法。

在開始之前傳送資料至服務匯流排，publisher 必須明確定義的 AMQP 節點，名為**$cbs**傳送 AMQP 郵件的 SA token （您可以看到它為 「 特殊 」 佇列服務用來擷取和驗證所有 SA 權杖）。 Publisher 必須指定**ReplyTo**欄位內 AMQP 訊息。這是服務回覆結果的權杖驗證 （publisher 和服務之間的簡單的要求回覆模式） publisher 中的節點。 建立這個回覆節點 」 上即時，「 講述 AMQP 1.0 規格所述的 「 動態建立遠端節點 」。 檢查後的 SA token 無效，publisher 可以往前，並開始傳送資料至服務。

下列步驟說明如何使用 AMQP 通訊協定使用[AMQP.Net 精簡](https://github.com/Azure/amqpnetlite)的文件庫傳送 SA 權杖。 如果您不能使用正式的服務匯流排 SDK (例如在 WinRT，.Net 精簡架構，.Net 微 Framework 與單聲道) 開發 C\#。當然，此文件庫是很有用，有助於了解如何宣告式安全性層級 AMQP，運作，如您所見 HTTP 層級 （與 HTTP 文章邀請傳送 「 授權 」 標頭內的 SA token） 的運作方式。 如果您不需要 AMQP 這類深度的知識，您可以使用正式的服務匯流排 SDK.Net Framework 應用程式，它為您進行。

### <a name="c35"></a>C & #35;

```
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()`方法接收*連線*（AMQP 連線類別執行個體為提供[AMQP.NET 精簡的文件庫](https://github.com/Azure/amqpnetlite)），表示 TCP 連線到服務和*sasToken*參數所傳送的 SA token。 

> [AZURE.NOTE] 請務必連線會建立與**SASL 驗證機制設定為外部**（不使用使用者名稱和密碼，您不必傳送 SA 權杖時使用的預設純）。

接下來，publisher 建立兩個 AMQP 連結傳送 SA 權杖和接收來自服務的回覆 （權杖驗證結果）。

AMQP 郵件包含一組的內容，以及比簡單訊息的詳細資訊。 SA 權杖是 （使用其建構函式） 在郵件本文。 **「 ReplyTo]**屬性設定為接收驗證結果 （如果可以變更其名稱，而且它將會建立動態服務） 的 [收件者] 連結的節點名稱。 服務會使用的最後三個應用程式/自訂屬性，表示有執行哪一類的作業。 依所述 CBS 草稿規格，他們必須**作業名稱**（「 放入權杖 」），**輸入的權杖**（在此例中 「 servicebus.windows.net:sastoken 」），然後權杖套用 （整個實體） 的**對象的 「 名稱 」** 。

傳送 [寄件者] 連結的 SA 權杖之後, publisher 必須讀取回覆 [收件者] 連結。 回覆的簡單 AMQP 訊息與應用程式屬性名為**「 狀態碼 」**可以包含 HTTP 狀態碼為相同的值。 

## <a name="next-steps"></a>後續步驟

如需有關如何使用這些 SA 權杖[服務匯流排 REST API 參考](https://msdn.microsoft.com/library/azure/hh780717.aspx)，請參閱。

如需有關服務匯流排驗證的詳細資訊，請參閱[服務匯流排驗證和授權](service-bus-authentication-and-authorization.md)。 

更多範例在 C# 和 Java 指令碼 SA 位於[此部落格文章](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)。

[Azure 入口網站]: https://portal.azure.com