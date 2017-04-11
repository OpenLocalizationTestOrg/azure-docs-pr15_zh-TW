<properties
   pageTitle="如何使用 Power BI 內嵌其餘 |Microsoft Azure"
   description="瞭解如何使用 Power BI 內嵌其餘 "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>如何使用 Power BI 內嵌其餘


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI 內嵌︰ 這是什麼和功能
Power BI 內嵌概略瞭解述正式[內嵌 Power BI 網站](https://azure.microsoft.com/services/power-bi-embedded/)中，但我們來看快速之前我們取得關於使用其他詳細資料。

它是相當簡單。 ISV 通常會想要在自己的應用程式中的[Power BI](https://powerbi.microsoft.com)的動態資料視覺效果作為 UI 建置組塊。

不過，您知道，請將您的網頁上內嵌 Power BI 報表或磚已可能沒有 Power BI 內嵌 Azure 服務，使用**Power BI API**。 當您想要共用您在相同組織中的報表時，您可以內嵌報表 Azure AD 驗證。 檢視報表的使用者必須使用自己的 Azure AD 帳戶登入。 當您想要共用報表 （包括外部使用者） 的所有使用者時，您可以直接內嵌以匿名存取。

您看到，這個簡單內嵌解決方案，但不完全符合 ISV 應用程式的需求。
大部分的 ISV 應用程式需要的資料傳送自己的客戶，不一定自己的組織中的使用者。 例如，如果您正在進行一些公司 A 和 B 公司兩者均適用的服務，公司中的使用者應該只看到資料為自己的公司 a就是多 tenancy 所需的傳送。

ISV 應用程式可能也會提供自己的驗證方法，例如表單驗證、 基本驗證等等... 然後，內嵌的解決方案必須與共同作業此現有的驗證方法安全地。 也是必要的使用者無法使用這些 ISV 應用程式，而不額外購買或 Power BI 訂閱的授權。

 **Power BI 內嵌**被專為確實這些類型的 ISV 案例。 因此我們已經有的簡單的方式，就讓我們開始到一些詳細資料

您可以使用.NET \(C#) 或 Node.js SDK，輕鬆地建立內嵌 Power BI 應用程式。 但本文中，我們會說明 HTTP 流程\(內含驗證) 的不 Sdk 的 Power BI。 了解此流程，您可以建立您的應用程式**使用任何程式設計語言**，您可以瞭解及深的 Power BI 內嵌。

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>建立 Power BI 工作區的集合，並取得便捷鍵\(提供)
Power BI 內嵌是一種 Azure 服務。 只使用 Azure 入口網站的 ISV 負責的使用狀況費用\(每小時使用者工作階段)，並檢視報表的使用者不會計入或甚至需要 Azure 訂閱。
在開始之前我們開發應用程式，我們必須使用 Azure 入口網站，以建立**Power BI 工作區集合**。

每個工作區的內嵌 Power BI 是每位客戶 （租用戶） 的工作區，我們可以新增多個工作區中每個工作區集合。 每個工作區集合中使用相同的便捷鍵。 在效果，工作區集合是 Power BI 內嵌的安全性界限。

![](media\power-bi-embedded-iframe\create-workspace.png)

完成我們建立的工作區的集合，請從 Azure 入口網站複製便捷鍵。

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] 我們也可以佈建的工作區的集合，並取得透過 REST API 便捷鍵。 若要深入瞭解，請參閱[Power BI 資源提供者 Api](https://msdn.microsoft.com/library/azure/mt712306.aspx)。

## <a name="create-pbix-file-with-power-bi-desktop"></a>使用 Power BI Desktop 中建立.pbix 檔案
接下來，我們必須建立資料連線和內嵌的報表。
此工作，沒有程式設計或程式碼。 我們只會使用 Power BI Desktop。
本文中，我們無法幫助瞭解如何使用 Power BI Desktop 的詳細資料。 如果您需要協助您在這裡，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)。 此範例中，我們只是要使用[零售分析範例](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/)。

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>建立 Power BI 工作區

現在，佈建全部完成後，我們開始吧 REST Api 透過工作區集合中建立客戶的工作區。 下列 HTTP 文章要求 （剩餘） 我們現有的工作區集合中建立新的工作區。 在此範例中，工作區集合名稱為**mypbiapp**。
我們只會設定為**AppKey**的便捷鍵，我們先前複製。 這是很簡單驗證 ！

**HTTP 要求**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 回應**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

傳回的**workspaceId**適用於下列後續的 API 呼叫。 我們的應用程式必須保留這個值。

## <a name="import-pbix-file-into-the-workspace"></a>將工作區.pbix 檔案匯入
每個工作區可以主控資料集的單一 Power BI Desktop 檔案\(包括資料來源設定) 和報表。 我們可以將我們的.pbix 檔案匯入至工作區，如以下的程式碼中所示。 如您所見，我們可以上傳 http 中使用 MIME 多重.pbix 檔案的二進位。

Uri 片段**32960a09-6366-4208-a8bb-9e0678cdbb9d** workspaceId，而查詢參數**datasetDisplayName**會建立的資料集名稱。 建立資料集保留.pbix 的成品檔案，例如相關的所有資料匯入的資料，資料來源]，將指標為等...

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

此匯入工作可能會執行一段時間。 完成時，我們的應用程式可以要求使用匯入識別碼的任務狀態。 在我們的範例，匯入 id 是**4eec64dd-533b-47c3-a72c-6508ad854659**。

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

以下要求使用此匯入 id 的狀態︰

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

若未完成工作，HTTP 回應可能會是像這樣︰

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

如果任務已完成，HTTP 回應可能是更多像這樣︰

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>資料來源連線\(和資料的多 tenancy)
匯入幾乎所有.pbix 檔案中的成品我們的工作區，而不是資料來源的認證。 如此一來，當您使用**DirectQuery 模式**，內嵌的報表無法顯示正確。 不過，當您使用 [**匯入模式**，我們可使用現有的匯入的資料的報表。 在這種情況下，我們必須設定認證使用其他通話透過下列步驟。

首先，我們必須取得閘道器的資料來源。 我們知道資料集**id**是先前所傳回的識別碼。

**HTTP 要求**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 回應**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

使用所傳回的閘道器識別碼] 及 [資料來源識別碼\(的上一個**gatewayId**和**id**在傳回的結果，請參閱)，我們可以變更此資料來源的認證，如下所示︰

**HTTP 要求**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**HTTP 回應**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

實際我們也可以設定不同的連接字串，每個工作區使用 REST API。 \(例如︰ 存在，我們可以將資料庫的每一個客戶。）

以下變更透過其餘的資料來源的連線字串。

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

或者，我們可以使用 [資料列層級安全性中內嵌 Power BI，我們可以將一份報表中的每個使用者的資料。 As a result，我們可以佈建相同.pbix 每個客戶報表\(UI，等等。)和不同資料來源。

> [AZURE.NOTE] 如果您使用**匯入模式**，而不**DirectQuery 模式**，有任何重新整理模型透過 API。 然後，透過 Power BI 閘道器的內部部署資料來源尚不支援的 Power BI 內嵌。 不過，會真的要留意[Power BI 部落格](https://powerbi.microsoft.com/blog/)的新功能和近期的未來版本。

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>驗證與裝載 （內嵌） 我們網頁中的報表

在先前的 REST API，我們可以為授權標頭使用便捷鍵**AppKey**本身。 可在後端伺服器端處理這些呼叫，因為它是安全。

但時我們我們網頁中內嵌報表，會使用 JavaScript 處理這種安全性資訊\(frontend)。 然後授權標頭值必須受到保護。 如果我們便捷鍵探索惡意使用者或惡意程式碼，他們可以打電話給任何使用此索引鍵的作業。

當我們我們網頁中內嵌報表時，我們就必須使用計算的權杖，而不是便捷鍵**AppKey**。 我們的應用程式必須建立 OAuth Json Web Token \(JWT) 其中包含的宣告和計算的數位簽章。 如下所示，此 OAuth JWT 是點分隔編碼的字串權杖。

![](media\power-bi-embedded-iframe\oauth-jwt.png)

首先，我們必須準備輸入的值，稍後再簽署。 此值是 base64 url 編碼 (rfc4648) 的字串下列 json，而且這些點以分隔\(。)字元。 更新版本中，我們會說明如何取得的報表識別碼。

> [AZURE.NOTE] 如果我們想要使用 Power BI 內嵌列層級安全性 (RLS)，我們也必須在宣告上指定**的使用者名稱**和**角色**。

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

接下來，我們必須建立 HMAC base64 編碼字串\(簽章) 與 SHA256 演算法。 此簽章的輸入的值為先前的字串。

最後，必須使用期間輸入的值] 和 [簽章字串結合\(。)字元。 已完成的字串是報表內嵌的應用程式 token。 即使的應用程式 token 發現惡意使用者時，他們無法存取原始便捷鍵。 這個應用程式權杖即將過期快速。

以下是 PHP 範例，這些步驟︰

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>最後，內嵌到網頁上的 [報表

內嵌我們的報表，我們必須取得內嵌 url，並報告使用下列 REST API 的**識別碼**。

**HTTP 要求**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**HTTP 回應**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

我們可以內嵌報表中使用的上一個應用程式 token 我們 web 應用程式。
如果我們查看的下一個範例程式碼，離職一部分是前一個範例相同。 在第二部分中，此範例顯示**embedUrl** \(查看一筆結果) 中的 iframe，將 iframe 張貼的應用程式 token。

> [AZURE.NOTE] 您需要變更您自己的其中一個報表識別碼值。 此外，我們的內容管理系統中的錯誤，因為程式碼範例中的 iframe 標記為唯讀如實。 如果您複製並貼上這個範例程式碼，則您可以移除標籤 capped 的文字。

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

然後，以下是我們的結果︰

![](media\power-bi-embedded-iframe\view-report.png)

此時，Power BI 內嵌僅顯示報表中的 iframe。 不過， [Power BI 部落格]()留意。 未來的改良功能無法使用新的用戶端 Api，就讓我們傳送將 iframe 資訊，以及取得資訊。 令人驚豔的檔案 ！


## <a name="see-also"></a>另請參閱
- [驗證並中內嵌 Power BI 授權](power-bi-embedded-app-token-flow.md)
