<properties
    pageTitle="Azure 函數開發人員參考 |Microsoft Azure"
    description="瞭解如何開發使用 C# Azure 函數。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
    ms.service="functions"
    ms.devlang="dotnet"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-c-developer-reference"></a>Azure 函數 C# 開發人員參考

> [AZURE.SELECTOR]
- [C# 指令碼](../articles/azure-functions/functions-reference-csharp.md)
- [F # 指令碼](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)
 
Azure 函數的 C# 體驗根據 Azure WebJobs SDK。 資料會接續到 C# 函數透過方法引數。 引數名稱以指定`function.json`，有預先定義的名稱，以存取項目，例如函數記錄器和取消權杖。

本文假設您已讀取[Azure 函數開發人員參考](functions-reference.md)。

## <a name="how-csx-works"></a>.Csx 的運作方式

`.csx`格式可讓撰寫較少的重複 「 使用 」，並將焦點集中在撰寫只 C# 函數。 Azure 函數，您只包含任何組件參考和命名空間您需要設定上方，如同以往，，而非文繞圖的所有項目中的命名空間和類別，您可以只定義您`Run`方法。 如果您需要執行個體包含任何課程]，若要定義 POCO 物件，您可以包含類別內相同的檔案。

## <a name="binding-to-arguments"></a>繫結至引數

各種繫結繫結至 C# 函數透過`name` *function.json*設定中的屬性。 每個繫結有自己的每一個繫結; 記錄支援的類型例如，字串、 POCO 或數個其他類型，可支援 blob 觸發程序。 您可以使用最適合您需求的類型。 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>記錄

若要登輸出 C# 程式串流記錄，您可以包含`TraceWriter`輸入引數。 我們建議您將其命名`log`。 我們建議您避免`Console.Write`Azure 函數中。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>非同步

若要讓函數非同步，使用`async`關鍵字並返回`Task`物件。

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>取消權杖

在某些情況下，您可能會區分被關閉作業。 雖然最好撰寫程式碼，它可以處理損毀，在您要的情況下處理正常的關閉要求，您可以在定義[`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx)輸入引數。  A`CancellationToken`將會提供如果觸發主機關閉。 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>匯入的命名空間

如果您要匯入命名空間，您可以執行，因此為主要，與`using`子句。

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

下列命名空間自動匯入，因此可省略︰

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>參照外部的組件

對於架構組件，請使用，參照新增`#r "AssemblyName"`指示詞。

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
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
* `Microsoft.AspNEt.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

如果您需要參考私人的組件，您可以上傳到的組件檔`bin`相對於其使用的檔案名稱 （例如您函數與參照的資料夾 `#r "MyAssembly.dll"`). 如何上傳檔案至您的 [函數] 資料夾的相關資訊，請參閱套件管理的下一節。

## <a name="package-management"></a>封裝管理

若要在 C# 函數中使用 NuGet 套件，將檔案上傳*project.json*至函數應用程式的檔案系統中的函數的資料夾。 以下是新增 Microsoft.ProjectOxford.Face 1.1.0 版的參照的範例*project.json*檔案︰

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

僅限.NET Framework 4.6 受到支援，因此請確定您*project.json*檔案指定`net46`如下所示。

當您上傳*project.json*檔案時，執行階段取得套件，並會自動新增套件組件的參考。 您不需要新增`#r "AssemblyName"`指示詞。 只要在新增所需`using`陳述式以*run.csx*檔案時，要使用的 NuGet 套件中定義的類型。


### <a name="how-to-upload-a-projectjson-file"></a>如何上傳 project.json 檔案

1. 開始，以確保您函數的應用程式會執行，而您可以透過 Azure 入口網站中開啟您的函數。 

    這也可讓存取串流記錄套件安裝輸出顯示的位置。 

2. 若要將檔案上傳 project.json，使用其中一種方法的[Azure 函數開發人員參考主題](functions-reference.md#fileupdate)**如何更新函數應用程式檔案**節所述。 

3. *Project.json*檔案上傳後，您會看到以下範例將函數中的輸出的串流記錄︰

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

若要取得環境變數或應用程式設定值，使用`System.Environment.GetEnvironmentVariable`，如下列範例所示︰

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>重複使用.csx 程式碼

您可以使用類別和*run.csx*檔案中的其他*.csx*檔案中所定義的方法。 若要這樣做，請使用`#load` *run.csx*檔案，如下圖所示，在下列範例中的指示詞。

範例*run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

範例*mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

您可以使用的相對路徑`#load`指示詞︰

* `#load "mylogger.csx"`載入位於 [函數] 資料夾中的檔案。

* `#load "loadedfiles\mylogger.csx"`載入 [函數] 資料夾中的資料夾中的檔案。

* `#load "..\shared\mylogger.csx"`載入*wwwroot*下直接位於 [函數] 資料夾，也就是，為相同層級的資料夾中的檔案。
 
`#load`指示詞運作，只使用*.csx* （C# 指令碼） 檔案，而不使用*.cs*檔案。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源︰

* [Azure 函數開發人員參考](functions-reference.md)
* [Azure 函數 F # 開發人員參考](functions-reference-fsharp.md)
* [Azure 函數 NodeJS 開發人員參考](functions-reference-node.md)
* [Azure 函數引動程序及連結](functions-triggers-bindings.md)

