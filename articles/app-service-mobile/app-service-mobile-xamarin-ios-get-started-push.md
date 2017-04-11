<properties
    pageTitle="推播通知加入 Azure 應用程式服務 Xamarin.iOS 應用程式"
    description="瞭解如何使用 Azure 應用程式服務傳送推入通知給您 Xamarin.iOS 應用程式"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>新增至您 Xamarin.iOS 應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>概觀

在本教學課程中，您專案中新增推入通知[Xamarin.iOS 快速開始](app-service-mobile-xamarin-ios-get-started.md)，讓每次即會插入一筆記錄，將會傳送至裝置的推入通知。

如果您不使用下載快速入門伺服器專案，您必須推入通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="prerequisites"></a>必要條件

* 完成[Xamarin.iOS 快速入門](app-service-mobile-xamarin-ios-get-started.md)教學課程。

* 實體 iOS 裝置。 IOS 模擬器不支援推播通知。

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>註冊推播通知在 Apple 的開發人員入口網站上的應用程式

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>設定您的行動應用程式傳送推入通知

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>更新伺服器專案可傳送推入通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>設定 Xamarin.iOS 專案

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>新增至您的應用程式的 [推入通知

1. 讓**AppDelegate**可以取得行動用戶端，請在**QSTodoService**，新增下列屬性︰

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. 新增下列`using` **AppDelegate.cs**檔案頂端的陳述式。

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. 在**AppDelegate**，會覆寫**FinishedLaunching**事件︰

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 在相同的檔案，會覆寫**RegisteredForRemoteNotifications**事件。 將此程式碼中您要註冊簡單範本通知伺服器傳送所有支援的平台上的。

    如需有關通知集線器範本的詳細資訊，請參閱[範本](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)。


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. 然後，覆寫**DidReceivedRemoteNotification**事件︰

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

支援推入通知，現在已經更新您的應用程式。

## <a name="test"></a>測試推播通知在您的應用程式

1. 按下建立專案並啟動應用程式在 iOS 簡訊裝置中的 [**執行**] 按鈕，然後按一下**[確定**] 接受推入通知]。

    > [AZURE.NOTE] 您必須明確接受推入通知，從您的應用程式。 這項要求才會發生的應用程式執行第一次。

2. 在應用程式中，輸入任務，然後按一下加號 (**+**) 圖示。

3. 請確認會收到通知，然後按一下**[確定**] 關閉通知。

4. 重複步驟 2 立即關閉應用程式，然後確認已顯示通知]。

您已順利完成本教學課程。

<!-- Images. -->

<!-- URLs. -->



