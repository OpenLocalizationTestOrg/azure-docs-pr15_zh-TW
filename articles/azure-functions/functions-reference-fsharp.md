<properties
    pageTitle="Azure 函數 F # 開發人員參考 |Microsoft Azure"
    description="瞭解如何開發使用 F # Azure 函數。"
    services="functions"
    documentationCenter="fsharp"
    authors="sylvanc"
    manager="jbronsk"
    editor=""
    tags=""
    keywords="azure 函數、 函數、 事件處理 webhooks 動態計算、 無伺服器架構，F#"/>

<tags
    ms.service="functions"
    ms.devlang="fsharp"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/09/2016"
    ms.author="syclebsc"/>

# <a name="azure-functions-f-developer-reference"></a>Azure 函數的 F # 開發人員參考

> [AZURE.SELECTOR]
- [C# 指令碼](../articles/azure-functions/functions-reference-csharp.md)
- [F # 指令碼](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

F # Azure 函數是輕鬆地在雲端中執行的程式碼或 「 函數 」 的小型的解決方案。 透過 [函數引數您 F # 函數將資料流。 引數名稱以指定`function.json`，有預先定義的名稱，以存取項目，例如函數記錄器和取消權杖。

本文假設您已讀取[Azure 函數開發人員參考](functions-reference.md)。

## <a name="how-fsx-works"></a>.Fsx 的運作方式

`.fsx`檔案是 F # 指令碼。 它可以是想為 F # 專案所包含的單一檔案中。 檔案包含兩個程式 （在此例中，您 Azure 函數） 的程式碼和管理相依性的指示詞。

當您使用`.fsx`Azure 函數，通常所需的組件會自動包含您可讓您將焦點放在函數，而不是 「 使用 」 的程式碼。

## <a name="binding-to-arguments"></a>繫結至引數

每個繫結支援引數，詳述[Azure 函數引動程序與繫結開發人員參考](functions-triggers-bindings.md)的一些設定。 例如，其中一個支援 blob 觸發程序的引數繫結是 POCO，可以使用 F # 記錄來表示。 例如︰

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

F # Azure 函數將一或多個引數。 當討論 Azure 函數的引數時，我們會將_輸入_引數] 和 [_輸出_引數。 輸入引數完全是聽起來︰ 輸入您的 F # Azure 函數。 _輸出_引數是變動的資料或`byref<>`做為的方式來傳遞引數資料將_out_的函數。

在上述範例`blob`是輸入引數，並`output`輸出引數。 請注意，我們使用`byref<>`的`output`(不需要新增`[<Out>]`註釋)。 使用`byref<>`類型可讓您的函數，用於變更哪些記錄或參照引數的物件。

輸入類型使用的 F # 記錄時，必須與標記記錄定義`[<CLIMutable>]`才能允許適當地將記錄傳送給您函數之前，請先設定欄位的 Azure 函數架構。 顯示進階設定，`[<CLIMutable>]`產生 setter 記錄屬性。 例如︰

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: TraceWriter) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

F # 類別也可用於在和出引數。 類別，getter 和 setter，通常會需要屬性。 例如︰

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>記錄

若要登輸出您[串流記錄](../app-service-web/web-sites-streaming-logs-and-console.md)F # 中，您函數應該採取的引數的類型`TraceWriter`。 我們建議您的一致性，此引數名稱為`log`。 例如︰

```fsharp
let Run(blob: string, output: byref<string>, log: TraceWriter) =
    log.Verbose(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>非同步

`async`可以使用工作流程，但結果需要傳回`Task`。 這可以使用`Async.StartAsTask`，例如︰

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>取消權杖

如果您函數需要順利處理關機，您可以為它[`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx)引數。 這可以結合`async`，例如︰

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>匯入的命名空間

命名空間可開啟依照平常的方式︰

```fsharp
open System.Net
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

會自動開啟下列命名空間︰

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>參照外部的組件

同樣地，架構的組件新增參照`#r "AssemblyName"`指示詞。

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks

let Run(req: HttpRequestMessage, log: TraceWriter) =
    ...
```

下列組件會自動新增主控環境 Azure 函數︰

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

此外，下列是選擇性的大小寫，可能會 simplename 參考 (例如`#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

如果您需要參考私人的組件，您可以上傳到的組件檔`bin`相對於其使用的檔案名稱 （例如您函數與參照的資料夾 `#r "MyAssembly.dll"`). 如何上傳檔案至您的 [函數] 資料夾的相關資訊，請參閱套件管理的下一節。

## <a name="editor-prelude"></a>編輯器 Prelude

支援 F # 編譯器服務的編輯器不會知道命名空間和 Azure 函數會自動包含的組件。 因此，可以包含可協助編輯器] 中尋找您使用的組件 prelude，並且明確開啟命名空間。 例如︰

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open Sytem
open Microsoft.Azure.WebJobs.Host

let Run(blob: string, output: byref<string>, log: TraceWriter) =
    ...
```

當 Azure 函數執行程式碼時，它會處理與來源`COMPILED`定義，因此編輯器 prelude 也會忽略。

## <a name="package-management"></a>封裝管理

若要在 F # 函數中使用 NuGet 封包，新增`project.json`檔案至函數應用程式的檔案系統中的函數的資料夾。 以下是範例`project.json`加入 NuGet 套件的參考的檔案`Microsoft.ProjectOxford.Face`1.1.0 版︰

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

僅限.NET Framework 4.6 受到支援，因此請確定的您`project.json`檔案指定`net46`如下所示。

當您上傳`project.json`檔案、 執行階段取得套件，並會自動新增套件組件的參考。 您不需要新增`#r "AssemblyName"`指示詞。 只要在新增所需`open`陳述式以您`.fsx`檔案。

您可能會想要放在您編輯器 prelude，以改善您的編輯器互動 F # 編譯服務自動參照組件。

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>如何新增`project.json`檔案給您 Azure 函數

1. 開始，以確保您函數的應用程式會執行，而您可以透過 Azure 入口網站中開啟您的函數。 這也可讓存取串流記錄套件安裝輸出顯示的位置。

2. 若要上傳`project.json`檔案，請使用其中一種方法說明[如何更新函數應用程式的檔案](functions-reference.md#fileupdate)。 如果您使用的[Azure 函數的連續部署](functions-continuous-deployment.md)，您可以新增`project.json`到您暫存的分支，才能加入您的部署分支之前實驗的檔案。

3. 之後`project.json`新增檔案時，您會看到類似以下範例將函數中的輸出的串流記錄︰

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>環境變數

若要取得環境變數或應用程式設定值，使用`System.Environment.GetEnvironmentVariable`，例如︰

```fsharp
open System.Environment

let Run(timer: TimerInfo, log: TraceWriter) =
    log.Info("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.Info("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>重複使用.fsx 程式碼

您可以使用來自其他程式碼`.fsx`使用檔案`#load`指示詞。 例如︰

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: TraceWriter) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: TraceWriter, text: string) =
    log.Verbose(text);
```

若要提供的路徑`#load`相對於的位置的指示詞是您`.fsx`檔案。

* `#load "logger.fsx"`載入位於 [函數] 資料夾中的檔案。

* `#load "package\logger.fsx"`載入檔案位於`package`[函數] 資料夾中的資料夾。

* `#load "..\shared\mylogger.fsx"`載入檔案位於`shared`為 [函數] 資料夾，也就是說，相同層級的資料夾正下方`wwwroot`。

`#load`指示詞只適用於`.fsx`（F # 指令碼） 檔案，而不是與`.fs`檔案。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源︰

* [F # 指南](https://docs.microsoft.com/en-us/dotnet/articles/fsharp/index)
* [Azure 函數開發人員參考](functions-reference.md)
* [Azure 函數 C# 開發人員參考](functions-reference-csharp.md)
* [Azure 函數 NodeJS 開發人員參考](functions-reference-node.md)
* [Azure 函數引動程序及連結](functions-triggers-bindings.md)
* [測試 azure 函數](functions-test-a-function.md)
* [按比例縮放的 azure 函數](functions-scale.md)
