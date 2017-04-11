<properties
    pageTitle="Azure 函數 HTTP 和 webhook 繫結 |Microsoft Azure"
    description="瞭解如何使用 HTTP 和 webhook 引動程序和繫結 Azure 函數中。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande"/>

# <a name="azure-functions-http-and-webhook-bindings"></a>Azure 函數 HTTP 和 webhook 繫結

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定和程式碼 HTTP 和 webhook 引動程序和 Azure 函數中的連結。 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-http-and-webhook-bindings"></a>對 HTTP 和 webhook 繫結 function.json

*Function.json*檔案提供適用於要求並回應的屬性。

空的的內容︰

- `name`︰ 在 [函數程式碼中用於要求物件 （或如果 Node.js 函數邀請內文） 變數的名稱。
- `type`︰ 必須設定為 [ *httpTrigger*。
- `direction`︰ 必須設定為 [*中*。 
- `webHookType`︰ 如 WebHook 引動程序，有效值*github*、*可寬延時間*，以及*genericJson*。 為 HTTP 不 WebHook 的觸發程序會將此屬性設定為空字串。 如需有關 WebHooks 的詳細資訊，請參閱下[WebHook 引動程序](#webhook-triggers)一節。
- `authLevel`︰ 不適用於 WebHook 引動程序。 設定為 「 函數 」 需要 API 金鑰 」 匿名 」 首 API 主要的需求，或 「 系統 」，以要求主要 API 金鑰。 如需詳細資訊，請參閱下方的[API 金鑰](#apikeys)。

HTTP 回應的屬性︰

- `name`︰ 在 [函數程式碼用於回應物件變數的名稱。
- `type`︰ 必須設定為*http*。
- `direction`︰ 若要*查看*必須設定。 
 
範例*function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="webhook-triggers"></a>WebHook 引動程序

WebHook 觸發程序是專為 WebHooks 下列功能在 HTTP 觸發程序︰

* 特定 WebHook 提供者 （目前 GitHub 可寬延時間支援和） 函數執行階段驗證提供者的簽章。
* 針對 Node.js 函數函數執行階段會提供，而不是要求物件邀請內文。 沒有任何特殊的處理 C# 函數，因為您控制指定參數類型所提供的內容。 如果您指定`HttpRequestMessage`您收到要求物件。 如果您指定 POCO 類型時，函數執行階段會嘗試剖析 JSON 物件填入物件屬性邀請內文中。
* 若要觸發 WebHook 函數 HTTP 要求必須包含 API 金鑰。 非 WebHook HTTP 引動程序，這項需求是選擇性的。

如需如何設定 GitHub WebHook 相關資訊，請參閱[GitHub 開發人員-建立 WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)。

## <a name="url-to-trigger-the-function"></a>若要觸發函數的 URL

若要觸發函數，您可以傳送 HTTP 要求函數應用程式 URL] 與 [在函數名稱的組合的 url:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## <a name="api-keys"></a>API 金鑰

根據預設，必須包含要觸發在 HTTP 或 WebHook 函數 HTTP 要求的 API 金鑰。 索引鍵可以包含一個名為查詢字串變數中`code`，或會包含在`x-functions-key`HTTP 標頭。 非 WebHook 函數，您可以指示設定並不需要的 API 金鑰`authLevel`「 具名 」 *function.json*檔案中的屬性。

您可以找到 API 關鍵值函式應用程式的檔案系統中的 [ *D:\home\data\Functions\secrets* ] 資料夾中。  主索引鍵和功能鍵在中設定*host.json*檔案，在此範例中所示。 

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

功能鍵，從*host.json*可以用來觸發的任何函數，但不會觸發停用的函數。 主索引鍵可以用來觸發的任何函數，將會觸發函數，即使已停用。 您可以設定為需要主索引鍵，藉由設定函數`authLevel`屬性設定為 「 系統 」。 

如果 [*機密*] 資料夾包含函數，相同名稱的 JSON 檔案`key`該檔案中的屬性也可用來觸發函數、 與此按鍵將只會使用所參照的函數。 例如，命名的函數的 API 金鑰`HttpTrigger` *HttpTrigger.json*在 [*密碼*] 資料夾中指定。 以下是範例︰

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] 當您正在設定 WebHook 觸發程序時，不要共用 WebHook 提供者的主索引鍵。 使用僅適用於處理 WebHook 函數的金鑰。  主索引鍵可以用來觸發任何函數，甚至停用函數。

## <a name="example-c-code-for-an-http-trigger-function"></a>HTTP 觸發程序函數的範例 C# 程式碼 

在 [程式碼範例看起來`name`參數查詢字串或 HTTP 邀請內文。

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

## <a name="example-f-code-for-an-http-trigger-function"></a>HTTP 觸發程序函數的範例 F # 程式碼

在 [程式碼範例看起來`name`參數查詢字串或 HTTP 邀請內文。

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

您將需要`project.json`參考使用 NuGet`FSharp.Interop.Dynamic`和`Dynamitey`組件，就像這樣︰

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

這會使用 NuGet 擷取相依性，並將指令碼參考。

## <a name="example-nodejs-code-for-an-http-trigger-function"></a>HTTP 觸發程序函數的範例 Node.js 程式碼 

此範例程式碼會尋找`name`參數查詢字串或 HTTP 邀請內文。

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

## <a name="example-c-code-for-a-github-webhook-function"></a>GitHub WebHook 函數的範例 C# 程式碼 

此範例程式碼會記錄 GitHub 問題註解。

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## <a name="example-f-code-for-a-github-webhook-function"></a>GitHub WebHook 函數的範例 F # 程式碼

此範例程式碼記錄 GitHub 問題註解。

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## <a name="example-nodejs-code-for-a-github-webhook-function"></a>GitHub WebHook 函數的範例 Node.js 程式碼 

此範例程式碼記錄 GitHub 問題註解。

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
