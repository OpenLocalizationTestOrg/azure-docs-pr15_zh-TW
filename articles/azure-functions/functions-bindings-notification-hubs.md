<properties
    pageTitle="Azure 函數通知中心繫結 |Microsoft Azure"
    description="瞭解如何使用 Azure 通知中心繫結 Azure 函數中。"
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
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Azure 函數通知中心輸出繫結

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文說明如何設定和 Azure 函數中的 [程式碼 Azure 通知中心繫結。 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

您函數可以傳送推入通知設定的 Azure 通知中樞使用幾行程式碼。 不過，Azure 通知中心必須設定的平台通知服務 (PNS) 您想要使用。 如需有關設定 Azure 通知中心，以及開發註冊接收通知用戶端應用程式的詳細資訊，請參閱[快速入門通知集線器](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)，然後按一下您的目標用戶端平台頂端。

原生通知或範本通知，可使用您所傳送的通知。 原生通知平特定通知為中設定`platform`的輸出繫結的屬性。 目標多種平台可範本通知。   

## <a name="notification-hub-output-binding-properties"></a>通知中心輸出繫結屬性

Function.json 檔案會提供下列屬性︰

- `name`︰ 在 [函數程式碼中用於通知中心訊息變數的名稱。
- `type`︰ 必須設定為*「 notificationHub 」*。
- `tagExpression`︰ 標記運算式可讓您指定的傳送通知給一組人員會收到通知，符合標記運算式已註冊的裝置。  如需詳細資訊，請參閱[路由及標籤的運算式](../notification-hubs/notification-hubs-tags-segment-push-message.md)。
- `hubName`: Azure 入口網站中的通知中心資源名稱。
- `connection`︰ 這個連接字串必須是設定為您的通知中心*DefaultFullSharedAccessSignature*值的**應用程式設定**連線字串。
- `direction`︰ 必須設定為*「 取出 」*。 
- `platform`︰ 平台屬性會指出您通知目標通知平台。 必須為下列值︰
    - `template`︰ 如果省略輸出繫結的平台屬性，這是預設平台。 針對任何在 Azure 通知中心上設定的平台可範本通知。 如需有關如何使用範本的一般傳送跨平台與 Azure 通知中心的通知的詳細資訊，請參閱[範本](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。
    - `apns`: Apple 推入通知服務。 如需設定 APN 和用戶端應用程式中收到通知的 [通知] 中心請參閱[傳送推入通知，可在 iOS 上使用 Azure 通知集線器](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Amazon 裝置訊息](https://developer.amazon.com/device-messaging)。 設定通知中心 ADM 與 Kindle 應用程式中收到通知的詳細資訊，請參閱[Kindle 應用程式通知集線器快速入門](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Google 雲端訊息](https://developers.google.com/cloud-messaging/)。 Firebase 雲端訊息，也就是新版本的 GCM，也有支援。 如需設定通知中樞 GCM/FCM 和接收通知 Android 的用戶端應用程式，請參閱[傳送推入通知，可在 Android 上使用 Azure 通知集線器](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`︰ 針對選取目標 Windows 平台版的[Windows 推入通知服務](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。 WNS 也支援 Windows Phone 8.1 及更新版本。 如需有關設定 [通知] 中心 WNS 和接收通知通用 Windows 平台 (UWP) 應用程式中，請參閱[快速入門通知集線器的 Windows 通用平台應用程式](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Microsoft 推入通知服務](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx)。 這個平台支援 Windows Phone 8 和舊版的 Windows Phone 平台。 如需設定 MPNS 和 Windows Phone 應用程式中收到通知的 [通知] 中心請參閱[Azure 通知集線器 Windows Phone 上使用的傳送推入通知](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
範例 function.json:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>通知中心連線字串設定

若要使用繫結的通知中心輸出，您必須設定中樞的連接字串。 進行*整合*] 索引標籤上選取您的通知中心或建立新的項目。 

您可以手動新增現有的中樞的連接字串， *DefaultFullSharedAccessSignature*連線字串加入您的通知中心。 此連線字串提供您函數的存取權限傳送通知訊息。 從主要的刀通知中心資源 Azure 入口網站中的 [**索引鍵**] 按鈕可存取*DefaultFullSharedAccessSignature*連接字串值。 若要手動新增的中心連接字串，請使用下列步驟︰ 

1. 在 [Azure 入口網站**函數應用程式**刀，按一下 [**函數應用程式設定 > 移至 [應用程式服務設定**。

2. 在**設定**刀中，按一下 [**應用程式設定**]。

3. [**連線字串**] 區段中，往下捲動，然後新增您的通知中心*DefaultFullSharedAccessSignature*值的命名項目。 變更**自訂**類型。
4. 參照的輸出繫結連線字串名稱。 類似**MyHubConnectionString**上述範例中使用。

## <a name="native-notification-examples"></a>原生通知範例

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>與 C# 佇列中引動程序的 APN 原生通知

此範例會示範如何使用[Microsoft Azure 通知集線器文件庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型傳送原生 APNS 通知。 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>GCM 原生通知與 C# 佇列中引動程序

此範例會示範如何使用[Microsoft Azure 通知集線器文件庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型傳送原生 GCM 通知。 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>WNS 原生通知與 C# 佇列中引動程序

此範例會示範如何使用[Microsoft Azure 通知集線器文件庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型傳送原生 WNS 快顯通知。 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>範本通知範例

#### <a name="template-example-for-nodejs-timer-triggers"></a>範本範例 Node.js 計時器觸發程序 

此範例會傳送通知包含[範本登錄](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)`location`和`message`。

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>範本範例 F # 計時器觸發程序

此範例會傳送通知包含[範本登錄](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)`location`和`message`。

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>範本範例使用 out 參數 

此範例會傳送通知包含[範本登錄](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)`message`範本中的預留位置。

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>範本範例使用非同步函數

如果您使用的非同步的程式碼，查看參數不允許。 在此情況下使用`IAsyncCollector`回到您的範本通知。 下列程式碼是非同步上述程式碼範例。 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>範本範例使用 JSON 

此範例會傳送通知包含[範本登錄](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)`message`範本使用有效的 JSON 字串中的預留位置。

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>範本範例使用通知集線器文件庫類型

此範例會示範如何使用[Microsoft Azure 通知集線器文件庫](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中定義的類型。 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
