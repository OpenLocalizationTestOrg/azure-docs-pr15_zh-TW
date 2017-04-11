<properties
    pageTitle="Azure 函數 Twilio 繫結 |Microsoft Azure"
    description="瞭解如何使用 Twilio 繫結 Azure 函數。"
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Azure 函數 Twilio 輸出繫結

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定和使用 Azure 函數 Twilio 繫結。 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Azure 函數支援 Twilio 輸出繫結若要啟用您要傳送的幾行程式碼和[Twilio](https://www.twilio.com/)帳戶的簡訊文字函數。 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>Azure 通知集線器 function.json 輸出繫結

Function.json 檔案會提供下列屬性︰

- `name`︰ 在 [函數程式碼用於 Twilio SMS 文字訊息變數的名稱。
- `type`︰ 必須設定為*「 twilioSms 」*。
- `accountSid`︰ 此值必須設為保留您 Twilio 帳戶 Sid 應用程式設定的名稱。
- `authToken`︰ 此值必須設為保留您 Twilio 驗證的權杖應用程式設定的名稱。
- `to`︰ 此值設定為簡訊文字會傳送至的電話號碼。
- `from`︰ 電話號碼會傳送簡訊文字的設定這個值。
- `direction`︰ 必須設定為*「 取出 」*。
- `body`︰ 此值可以用於硬式編碼 SMS 文字訊息，如果您不需要動態函數的程式碼設定。 

 
範例 function.json:

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>範例 C# 佇列中的觸發程序 Twilio 與輸出繫結

#### <a name="synchronous"></a>同步

此同步範例的程式碼 Azure 儲存體佇列中的觸發程序使用 out 參數簡訊傳送到客戶訂單。

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>非同步

此非同步範例的程式碼 Azure 儲存體佇列中的觸發程序會將簡訊傳送到客戶訂單。

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>範例 Node.js 佇列中的觸發程序 Twilio 與輸出繫結

此 Node.js 範例將傳送簡訊給客戶訂單。

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
