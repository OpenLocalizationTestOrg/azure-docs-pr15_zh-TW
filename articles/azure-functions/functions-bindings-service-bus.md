<properties
    pageTitle="Azure 函數服務匯流排引動程序和繫結 |Microsoft Azure"
    description="瞭解如何使用 Azure 函數中的 Azure 服務匯流排引動程序及連結。"
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="azure 函數，如函數、 事件處理，動態計算，無架構"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure 函數服務匯流排引動程序和繫結佇列 」 和 「 主題

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定程式碼 Azure 服務匯流排引動程序和 Azure 函數中的連結。 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>服務匯流排佇列或主題觸發程序

#### <a name="functionjson"></a>function.json

*Function.json*檔案指定下列屬性。

- `name`︰ 在 [函數程式碼用於佇列中] 或 [主題中或 [佇列或主題的郵件變數名稱。 
- `queueName`︰ 佇列的觸發程序，以獲得佇列中的名稱。
- `topicName`︰ 主題的觸發程序，以獲得主題的名稱。
- `subscriptionName`︰ 主題的觸發程序、 訂閱名稱。
- `connection`︰ 設定應用程式名稱包含服務匯流排連線字串。 服務匯流排命名空間，不限於特定的佇列或主題必須連線字串。 如果連線字串不會有權限管理，請設定`accessRights`屬性。 如果您離開`connection`空白，觸發程序或繫結可搭配函數應用程式，AzureWebJobsServiceBus 應用程式設定所指定的預設服務匯流排連線字串。
- `accessRights`︰ 指定連線字串的存取權限。 預設值是`manage`。 若要設定`listen`如果您使用的不提供的連接字串管理權限。 執行階段可以嘗試的功能及無法執行要求的作業管理權限。
- `type`︰ 必須設定為 [ *serviceBusTrigger*。
- `direction`︰ 必須設定為 [*中*。 

範例*function.json*服務匯流排佇列中觸發程序︰

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>C# 程式碼範例，處理服務匯流排佇列中的訊息

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>F # 程式碼範例，處理服務匯流排佇列中的訊息

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Node.js 程序服務匯流排佇列中郵件的程式碼範例

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>支援的類型

下列類型的任何可還原序列化服務匯流排佇列訊息︰

* （從 JSON) 的物件
* 字串
* 位元組陣列 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>PeekLock 行為

函數執行階段接收訊息中的`PeekLock`模式和通話`Complete`函數成功，完成時，當訊息或來電`Abandon`如果功能將會失敗。 如果函數執行超過`PeekLock`逾時，鎖定就會自動更新。

#### <a id="sbpoison"></a>有害訊息處理

服務匯流排會處理哪些無法控制或 Azure 函數設定] 或 [程式碼中設定自己有害訊息。 

#### <a id="sbsinglethread"></a>單一執行緒

根據預設，函數執行階段會同時處理多個佇列中的郵件。 若要直接處理只有單一佇列或主題訊息一次執行階段，將`serviceBus.maxConcurrrentCalls`1 *host.json*檔案中。 如*host.json*檔案的相關資訊，請參閱在開發人員參考文章和[host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) WebJobs.Script 存放庫 wiki 中的[資料夾結構](functions-reference.md#folder-structure)。

## <a id="sboutput"></a>服務匯流排佇列或主題輸出繫結

#### <a name="functionjson"></a>function.json

*Function.json*檔案指定下列屬性。

- `name`︰ 在函數碼中使用的佇列或佇列中的訊息變數名稱。 
- `queueName`︰ 佇列的觸發程序，以獲得佇列中的名稱。
- `topicName`︰ 主題的觸發程序，以獲得主題的名稱。
- `subscriptionName`︰ 主題的觸發程序、 訂閱名稱。
- `connection`︰ 同服務匯流排觸發程序。
- `accessRights`︰ 指定連線字串的存取權限。 預設值是`manage`。 若要設定`send`如果您使用的不提供的連接字串管理權限。 執行階段可以嘗試的功能及無法執行要求的作業管理權限，例如建立佇列。
- `type`︰ 必須設定為 [ *serviceBus*。
- `direction`︰ 若要*查看*必須設定。 

若要撰寫郵件服務匯流排佇列使用計時器觸發程序的範例*function.json* :

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>支援的類型

Azure 函數可以從下列類型的任何建立服務匯流排佇列中郵件。

* 物件 （一律會建立 JSON 訊息，以 null 物件建立郵件，如果該值為 null 函數結束時）
* （如果該值為非 null 函數結束時，請建立郵件） 的字串
* 位元組陣列 （的運作方式字串） 
* `BrokeredMessage`(C# 的運作方式字串)

建立 C# 函數中的多封郵件，您可以使用`ICollector<T>`或`IAsyncCollector<T>`。 當您撥打時，建立一封郵件`Add`方法。

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>C# 程式碼範例，建立服務匯流排佇列中的郵件

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>F # 程式碼的範例會建立服務匯流排佇列中訊息

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>建立服務匯流排佇列中郵件的 Node.js 程式碼範例

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
