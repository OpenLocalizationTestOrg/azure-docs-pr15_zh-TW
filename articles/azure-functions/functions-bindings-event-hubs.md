<properties
    pageTitle="Azure 函數事件中心繫結 |Microsoft Azure"
    description="瞭解如何使用 Azure 事件中心繫結 Azure 函數中。"
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Azure 函數事件中心連結

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定和 Azure 函數的程式碼[Azure 事件中心](../event-hubs/event-hubs-overview.md)連結。 Azure 函數支援觸發程序和輸出繫結 Azure 事件集線器。

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

如果您不熟悉 Azure 事件集線器，請參閱[Azure 事件中心概觀](../event-hubs/event-hubs-overview.md)。

## <a name="azure-event-hub-trigger-binding"></a>Azure 事件中心觸發繫結

Azure 事件中心觸發程序可用來回應事件傳送至事件中心事件資料流。 您必須設定 [觸發程序繫結 [事件] 中心內的 「 讀取 」 權限。

#### <a name="functionjson-for-event-hub-trigger-binding"></a>事件集線器 function.json 觸發繫結

Azure 事件中心觸發程序的*function.json*檔案指定下列屬性︰

- `type`︰ 必須設定為 [ *eventHubTrigger*。
- `name`︰ 用於函數的程式碼，事件中心訊息變數名稱。 
- `direction`︰ 必須設定為 [*中*。 
- `path`︰ [事件] 中心名稱。
- `consumerGroup`︰ 這是一個選擇性的屬性，用來設定[消費者群組](../event-hubs-overview.md#consumer-groups)用於訂閱在中樞的事件。 如果省略則為`$Default`使用消費者群組。 
- `connection`︰ 設定應用程式名稱包含 [事件] 中心所在的命名空間的連接字串。 命名空間，而不事件中心本身的 [**連線資訊**] 按鈕，即可複製此連線字串。  此連線字串必須至少有讀取權限，若要啟動觸發程序。

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Azure 事件中心觸發程序 C# 範例
 
使用上述範例 function.json，事件訊息的本文會記錄使用 C# 函數以下程式碼︰
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Azure 事件中心觸發程序 F # 範例

使用上述範例 function.json，事件訊息的本文會記錄使用 F # 函數以下程式碼︰

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Azure 事件中心觸發程序 Node.js 範例
 
使用上述範例 function.json，事件訊息的本文會記錄使用下列 Node.js 函數程式碼︰
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Azure 事件中心輸出繫結

Azure 事件中心輸出繫結用來撰寫事件事件中樞的事件資料流。 您必須事件中樞的 「 傳送 」 權限，寫入事件。 

#### <a name="functionjson-for-event-hub-output-binding"></a>function.json 事件中樞的輸出繫結

Azure 事件中樞的*function.json*檔輸出繫結指定下列屬性︰

- `type`︰ 必須設定為 [ *eventHub*。
- `name`︰ 用於函數的程式碼，事件中心訊息變數名稱。 
- `path`︰ [事件] 中心名稱。
- `connection`︰ 設定應用程式名稱包含 [事件] 中心所在的命名空間的連接字串。 命名空間，而不事件中心本身的 [**連線資訊**] 按鈕，即可複製此連線字串。  此連線字串必須事件中樞資料流傳送郵件的傳送權限。
- `direction`︰ 若要*查看*必須設定。 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Azure 事件中心 C# 程式碼範例輸出繫結
 
在下列 C# 程式函數碼示範事件中樞的事件資料流撰寫事件。 [事件] 中心輸出如上所示的繫結這個範例代表套用至 C# 計時器觸發程序。  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure 事件中心 F # 程式碼範例輸出繫結

下列 F # 範例函數程式碼示範撰寫事件事件中樞的事件資料流。 [事件] 中心輸出如上所示的繫結這個範例代表套用至 C# 計時器觸發程序。

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>輸出繫結的 azure 事件中心 Node.js 程式碼範例
 
下列 Node.js 函數程式碼範例示範事件中樞的事件資料流撰寫事件。 [事件] 中心輸出如上所示的繫結這個範例代表套用至 Node.js 計時器觸發程序。  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
