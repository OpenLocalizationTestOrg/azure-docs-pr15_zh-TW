<properties
    pageTitle="傳送 Azure 通知集線器和 Node.js 推入通知"
    description="瞭解如何使用通知集線器推入通知傳送 Node.js 應用程式。"
    keywords="推入通知，推入 notifications,node.js 推入，ios 推入"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>傳送 Azure 通知集線器和 Node.js 推入通知
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>概觀

> [AZURE.IMPORTANT] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)。

本指南會示範如何傳送推入通知協助的 Azure 通知集線器直接從 Node.js 應用程式。 

涵蓋的案例包含下列的平台上的應用程式傳送推入通知︰

* Android
* iOS
* 在 Windows Phone
* 通用 Windows 平台 

如需有關通知集線器的詳細資訊，請參閱[下一步](#next)] 區段。

##<a name="what-are-notification-hubs"></a>通知集線器是什麼？

Azure 通知集線器的推入通知傳送到行動裝置提供容易使用多平台、 調整基礎結構。 服務基礎結構的詳細資訊，請參閱[Azure 通知集線器](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx)頁面。

##<a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

本教學課程中的第一個步驟就建立新的空白 Node.js 應用程式。 如需建立 Node.js 應用程式的指示，請參閱[建立並部署 Node.js 應用程式至 Azure 網站][nodejswebsite]， [Node.js 雲端服務][Node.js Cloud Service]使用 Windows PowerShell 或[WebMatrix 的網站]。

##<a name="configure-your-application-to-use-notification-hubs"></a>設定您的應用程式使用通知集線器

若要使用 Azure 通知集線器，您需要下載並使用 Node.js [azure 套件](https://www.npmjs.com/package/azure)，其中包含一組內建協助文件庫與推入通知其他服務。

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>若要取得套件使用節點套件管理員 (NPM)

1.  使用命令列**PowerShell** (Windows)、**終端機**(Mac)，或**被**(Linux) 等介面，並瀏覽至您用來建立空白的應用程式的資料夾。

2.  在 [命令] 視窗中，輸入**npm 安裝 azure sb** 。

3.  您可以手動執行**1**或**dir**命令，以確認**節點\_模組**建立資料夾。 在該資料夾中，尋找**azure**套件，其中包含您要存取 [通知] 中心內的文件庫。

>[AZURE.NOTE] 您可以進一步瞭解安裝 NPM 正式[NPM 部落格](http://blog.npmjs.org/post/85484771375/how-to-install-npm)。 

### <a name="import-the-module"></a>匯入模組

使用文字編輯器， **server.js**檔案的應用程式的頂端新增下列動作︰

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>設定 Azure 通知中心連線

**NotificationHubService**物件可讓您與通知集線器搭配使用。 下列程式碼會建立名為**hubname**nofication 中心**NotificationHubService**物件。 將其新增靠近頂端的**server.js**的檔案，匯入 azure 模組陳述式之後︰

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

連線的**連接字串**值可以取得從[Azure 入口網站]，藉由執行下列步驟︰

1. 在左側的功能窗格中，按一下 [**瀏覽**]。

2. 選取**通知集線器**，然後找出您想要使用的樣本中心]。 如果您需要建立新的通知中心協助，您可以參照至[Windows 市集開始教學課程](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)。

3. 選取 [**設定**]。

4. 按一下 [上**存取原則**。 您會看到兩個共用和完整存取連接字串。

![Azure 入口網站-通知集線器](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] 您也可以擷取連接字串使用提供的[PowerShell 的 Azure](../powershell-install-configure.md)或**azure sb 命名空間顯示**命令[Azure 命令列介面 (Azure CLI)](../xplat-cli-install.md)**取得 AzureSbNamespace**指令程式。

##<a name="general-architecture"></a>一般的結構

**NotificationHubService**物件公開特定裝置和應用程式傳送推入通知的下列物件執行個體︰

* **Android** -使用**GcmService**物件，也就是在**notificationHubService.gcm**
* **iOS** -使用**ApnsService**物件，可在**notificationHubService.apns**存取
* **在 Windows Phone** -使用**MpnsService**物件，也就是在**notificationHubService.mpns**
* **通用 Windows 平台**-使用**WnsService**物件，也就是在**notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>如何︰ 在 Android 應用程式的傳送推入通知

**GcmService**物件提供**傳送**的方法，可以用來傳送推入通知 Android 應用程式。 **傳送**方法接受下列參數︰

* **標籤**的標籤識別碼。 如果提供沒有標記，則會傳送通知 al 用戶端。
* **裝載**-訊息的 JSON 或原始的字串裝載。
* **回撥**-回撥函數。

在格式] 內容的詳細資訊，請參閱[實作 GCM 伺服器](http://developer.android.com/google/gcm/server.html#payload)文件中的 [**內容**] 區段。

下列程式碼會使用公開**NotificationHubService** **GcmService**執行個體來傳送推入通知給所有已註冊的用戶端。

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>如何︰ iOS 應用程式的傳送推入通知

就跟 Android 應用程式，上述相同， **ApnsService**物件提供**傳送**的方法，可以用來傳送推入通知 iOS 應用程式。 **傳送**方法接受下列參數︰

* **標籤**的標籤識別碼。 如果提供沒有標記，則會傳送通知給所有的用戶端。
* **裝載**-訊息的 JSON 或字串裝載。
* **回撥**-回撥函數。

內容格式的詳細資訊，請參閱[本機和推播通知程式設計指南](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)文件的**通知內容**一節。

下列程式碼會使用公開**NotificationHubService** **ApnsService**執行個體來傳送給所有的用戶端的提醒訊息︰

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>如何︰ 在 Windows Phone 應用程式的傳送推入通知

**MpnsService**物件提供可用來在 Windows Phone 應用程式傳送推入通知**傳送**的方法。 **傳送**方法接受下列參數︰

* **標籤**的標籤識別碼。 如果提供沒有標記，則會傳送通知給所有的用戶端。
* **裝載**-訊息的 XML 內容。
* **TargetName**  -  `toast`快顯通知]。 `token`並排顯示通知]。
* **通知類別**-通知的優先順序。 請參閱**HTTP 標題元素**的[推播通知從伺服器](http://msdn.microsoft.com/library/hh221551.aspx)文件中的正確的值。
* **選項**-選擇性要求標頭。
* **回撥**-回撥函數。

有效的**TargetName**、**通知類別**和標題選項的清單，請參閱[推入通知，從伺服器](http://msdn.microsoft.com/library/hh221551.aspx)的頁面。

下列範例會使用公開**NotificationHubService** **MpnsService**執行個體來傳送快顯推入通知︰

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>如何︰ 通用 Windows 平台 (UWP) 應用程式的傳送推入通知

**WnsService**物件提供**傳送**的方法，可以用來傳送推入通知通用 Windows 平台應用程式。  **傳送**方法接受下列參數︰

* **標籤**的標籤識別碼。 如果提供沒有標記，則會傳送通知給所有已註冊的用戶端。
* **裝載**-XML 訊息內容。
* **類型**-通知類型。
* **選項**-選擇性要求標頭。
* **回撥**-回撥函數。

有效的類型與要求標頭的清單，請參閱 [[推入通知服務要求與回應標頭](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx)]。

下列程式碼會使用公開**NotificationHubService** **WnsService**執行個體來傳送 UWP 應用程式的快顯推入通知︰

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>後續步驟

上述範例程式碼片段可讓您輕鬆地建立各種不同的裝置傳送推入通知服務基礎結構。 現在，您學到的通知集線器使用 node.js 基本概念，請遵循這些連結，深入瞭解如何將延伸進一步這些功能。

-   [Azure 通知](https://msdn.microsoft.com/library/azure/jj927170.aspx)集線器，請參閱 MSDN 參考。
-   請造訪 GitHub[節點 Azure SDK]存放庫更多範例與實作詳細資料。

  [Azure SDK 節點]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [具有 WebMatrix 的網站]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Azure 入口網站]: https://portal.azure.com
