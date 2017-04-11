<properties
    pageTitle="Azure 函數 NodeJS 開發人員參考 |Microsoft Azure"
    description="瞭解如何開發使用 NodeJS Azure 函數。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
    ms.service="functions"
    ms.devlang="nodejs"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-nodejs-developer-reference"></a>Azure 函數 NodeJS 開發人員參考

> [AZURE.SELECTOR]
- [C# 指令碼](../articles/azure-functions/functions-reference-csharp.md)
- [F # 指令碼](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)

Azure 函數的節點/JavaScript 體驗輕鬆匯出函數會傳遞`context`物件與執行階段，通訊和接收及傳送透過繫結的資料。

本文假設您已讀取[Azure 函數開發人員參考](functions-reference.md)。

## <a name="exporting-a-function"></a>匯出函數

所有 JavaScript 函數必須都匯出單一`function`透過`module.exports`以尋找函數，並執行執行階段。 此函數一定要包含`context`物件。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

繫結的`direction === "in"`傳遞為函數的引數，這表示您可以使用[`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx)動態處理新的輸入 (例如，藉由使用`arguments.length`來逐一查看所有您輸入)。 這項功能很方便，如果您只需要觸發程序沒有其他的輸入與您預測可以存取您的觸發程序資料而不需要參考為您`context`物件。

傳遞引數永遠沿著函數順序發生*function.json*，即使您不在您匯出的陳述式中加以指定。 例如，如果您有`function(context, a, b)`並將其變更為`function(context, a)`，您仍然可以存取的值`b`函數所參照的程式碼中`arguments[3]`。

所有的繫結，無論方向，也傳遞`context`物件 （如下所示）。 

## <a name="context-object"></a>內容物件

執行階段使用`context`來傳送資料與您的函數，讓您執行階段與通訊的物件。

為內容物件一律函數的第一個參數，應該一律會包含因為它有方法，例如`context.done`和`context.log`其才能正確地使用執行階段。 您可以將物件命名任何您喜歡 (亦即`ctx`或`c`)。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>context.bindings

`context.bindings`物件收集所有您輸入與輸出資料。 將資料加入至`context.bindings`物件透過`name`繫結的屬性。 例如，指定下列繫結定義*function.json*中，您可以存取透過佇列中的內容`context.bindings.myInput`。 

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

`context.done`函數在您完成告知執行階段執行。 這是重要通話當您完成函數。如果沒有的話，執行階段就會仍不知道您函數完成。 

`context.done`函數可讓您傳遞給執行階段，以及屬性的屬性包將會覆寫內容上的使用者定義的錯誤回`context.bindings`物件。

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>context.log(message)

`context.log`方法可讓您輸出相互關聯放在一起，用於記錄的記錄檔陳述式。 如果您是使用`console.log`，您的訊息只會顯示的程序記錄層級，不會有用。

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

`context.log`方法支援節點[util.format 方法](https://nodejs.org/api/util.html#util_util_format_format)支援相同參數格式。 因此，例如 code 像這樣︰

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

可以撰寫像這樣︰

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>HTTP 引動程序︰ context.req 和 context.res

若是 HTTP 引動程序，因為它是使用的常見模式，例如`req`和`res`HTTP 要求與回應物件，我們會決定，使其更容易存取這些物件上，您不必使用完整`context.bindings.name`圖樣。

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>節點版本及封裝管理

節點版本目前鎖定狀態， `5.9.1`。 我們正在調查新增更多版本支援，並讓您可設定。

您可以將*package.json*檔案上傳至您的函數資料夾函數應用程式的檔案系統中，您函數中包含套件。 檔案上傳的指示，請參閱**如何更新函數應用程式檔案**的[Azure 函數開發人員參考主題](functions-reference.md#fileupdate)。 

您也可以使用`npm install`函數應用程式的 SCM (Kudu) 命令列介面中︰

1. 瀏覽至︰ `https://<function_app_name>.scm.azurewebsites.net`。

2. 按一下 [**偵錯主控台 > CMD**。

3. 瀏覽至`D:\home\site\wwwroot\<function_name>`。

4. 執行`npm install`。

您需要的套件的安裝後，您匯入其對函數平常的方式 (也就是透過`require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>環境變數

若要取得環境變數或應用程式設定值，使用`process.env`，如下列範例所示︰

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    
    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="typescriptcoffeescript-support"></a>TypeScript/CoffeeScript 支援

沒有，還自動編譯 TypeScript/CoffeeScript 透過執行階段，讓所有要的任何直接支援需要執行階段，外部處理部署次。 

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源︰

* [Azure 函數開發人員參考](functions-reference.md)
* [Azure 函數 C# 開發人員參考](functions-reference-csharp.md)
* [Azure 函數 F # 開發人員參考](functions-reference-fsharp.md)
* [Azure 函數引動程序及連結](functions-triggers-bindings.md)
