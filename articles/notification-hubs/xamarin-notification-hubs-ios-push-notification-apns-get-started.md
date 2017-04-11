<properties
    pageTitle="iOS 推播通知]，以通知集線器 Xamarin 應用程式 |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Azure 通知集線器推入通知傳送至 Xamarin iOS 應用程式。"
    services="notification-hubs"
    keywords="ios 推入通知，推入郵件推播通知、 發送訊息"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS 推播通知]，以通知集線器 Xamarin 應用程式

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀
> [AZURE.IMPORTANT] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。

本教學課程教您如何使用 Azure 通知集線器推入通知傳送至 iOS 應用程式。
您會建立空白的 Xamarin.iOS 應用程式會使用[Apple 推入通知服務 (Apn)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)接收推入通知。 完成後，您可以使用您的通知中心廣播執行您的應用程式的所有裝置的推播通知。 完成的程式碼是[NotificationHubs 應用程式]中可用[GitHub]範例。

本教學課程說明通知集線器簡單發送訊息廣播的案例。

##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ [Xcode 6.0][Install Xcode]
+ IOS 7.0 （或更新版本） 相容的裝置
+ iOS 開發人員程式成員資格
+ [Xamarin Studio]

   > [AZURE.NOTE] 由於設定需求的 iOS 推入通知，您必須部署及測試實體 iOS 裝置 （iPhone 或 iPad） 上的範例應用程式而不是在模擬器中。

完成此教學課程是所有其他通知集線器教學課程 Xamarin iOS 應用程式的必要條件。


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>設定您的通知中心

本節會引導您建立新的通知中心，並以使用您所建立的**.p12**推播憑證的 APN 設定驗證。 如果您想要使用您已經建立通知中心，您可以跳至步驟 5。

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>如要設定 APN 連線，Azure 入口網站中，開啟您的通知中心設定、 ande<b>通知服務</b>] 下，按一下，然後再按一下 [ <b>Apple (APNS)</b>中的項目清單。 完成時，按一下 [<b>上傳</b>的憑證，然後選取您要匯出更早版本，以及憑證的密碼<b>.p12</b>憑證。</p>
<p>請務必選取<b>沙箱</b>模式，因為您會在開發環境中傳送推入訊息。 如果您想要將推入通知傳送給您的應用程式從商店購買的使用者，僅使用<b>生產</b>設定。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


您的通知中心正在設定以使用 APNS，且您擁有註冊您的應用程式，並傳送推入通知的連線字串。


##<a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連線到 [通知] 中心

#### <a name="create-a-new-project"></a>建立新專案

1. 在 Xamarin Studio 中，建立新的 iOS 專案，然後選取 [**整合 API** > **單一檢視應用程式**範本。

    ![Xamarin Studio-選取應用程式類型][31]

2. 新增至 Azure 訊息元件的參照。 在 [方案] 檢視中，以滑鼠右鍵按一下您專案的**元件**資料夾並選擇**取得更多元件**。 搜尋**Azure 訊息**元件，並新增至專案的元件。

3. 在**AppDelegate.cs**，新增下列使用陳述式︰

        using WindowsAzure.Messaging;

4. 宣告**SBNotificationHub**執行個體︰

        private SBNotificationHub Hub { get; set; }

5. 使用下列變數建立**Constants.cs**類別︰

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. 在**AppDelegate.cs**，更新**FinishedLaunching()**以符合下列動作︰

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. 覆寫**AppDelegate.cs**的**RegisteredForRemoteNotifications()**方法︰

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. 覆寫**AppDelegate.cs**的**ReceivedRemoteNotification()**方法︰

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. 建立**AppDelegate.cs**下列**ProcessNotification()**方法︰

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] 您可以選擇覆寫**FailedToRegisterForRemoteNotifications()**處理情況，例如沒有網路連線。 位置使用者可能會在離線模式 （例如飛機） 啟動應用程式，而且您想要處理發送訊息應用程式的特定案例，這是特別重要。


10. 在您的裝置上執行應用程式。


## <a name="sending-push-notifications"></a>傳送推入通知


您可以測試接收推播通知在您的應用程式中，傳送通知給**測試傳送****疑難排解**工具組中的功能，在 [通知] 中心] 頁面的權限透過[Azure 入口網站]中以下畫面所示。

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

推播通知通常會傳送到後端服務，例如行動電話服務或 ASP.NET 使用相容的文件庫。 您也可以使用 REST API 直接來傳送發送訊息，如果文件庫不適用於您的狀況。 

在本教學課程中，我們會保持簡單，並只將示範測試您的用戶端應用程式傳送通知集線器主控台應用程式，而不是在後端服務的使用.NET SDK 的通知。 建議[使用通知集線器推入通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教學課程為從 ASP.NET 後端傳送通知的下一個步驟。 不過，可以使用下列方法，傳送通知︰

* **其餘介面**︰ 您可以使用[其餘介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)任何後端平台支援推入通知。

* **Microsoft Azure 通知集線器.NET SDK**: Nuget 套件管理員中的 Visual Studio 中，執行[安裝套件 Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js** ︰[如何使用從 Node.js 通知集線器](notification-hubs-nodejs-push-notification-tutorial.md)。

**行動應用程式**︰ 如需如何從通知集線器整合 Azure 應用程式服務行動應用程式後端傳送通知的範例，請參閱[新增至您的行動應用程式的推播通知](../app-service-mobile/app-service-mobile-ios-get-started-push.md)。

* <b0>Java / PHP</b0>︰ 如需如何使用 REST Api 來傳送推入通知的範例，請參閱 < 如何使用從 Java/PHP 通知集線器 」 (<b1>Java</b1><b2></b2><b3>PHP</b3>)。


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>（選用）從.NET 主控台應用程式傳送推入通知

在此區段中，我們會使用簡單的.NET 主控台應用程式傳送推入通知。 為了此範例中，我們會切換至具有已安裝的 Visual Studio Windows 的開發環境。

1. 在 Visual Studio 中，建立新的 Visual C# 主控台應用︰

    ![Visual Studio-建立新的主控台應用程式][213]

2. 在 Visual Studio 中，按一下 [**工具]**、 按一下**NuGet 封裝管理員**]，然後按一下**封裝管理員主控台**。

    封裝管理員主控台應該固定至您的 Visual Studio 工作區的底部會出現。

3. 在 [封裝管理員主控台] 視窗中，設定**預設專案**至新主控台應用程式專案，然後在 [主控台] 視窗中，執行下列命令︰

        Install-Package Microsoft.Azure.NotificationHubs

    如此會將 Azure 通知集線器 SDK 使用<a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 集線器 NuGet 套件</a>的參考。

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. 開啟`Program.cs`檔案，並新增下列`using`陳述式中，確保我們可以使用 Azure 類別和您的主要類別中的函數︰

        using Microsoft.Azure.NotificationHubs;

3. 在您`Program`課程，請新增下列方法 （別忘了要取代的**連接字串**和**中心名稱**）︰

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. 新增下列幾行中的您`Main`方法︰

         SendNotificationAsync();
         Console.ReadLine();

5. 按 F5 鍵以執行應用程式。 在秒內，您應該會看到推入通知，會出現在您的裝置上。 無論您使用 Wi-fi 或行動電話資料網路，請確定您在裝置上有網際網路連線。

您可以找到所有可能的裝載在 Apple[本機和推播通知程式設計指南]。

####<a name="optional-send-notifications-from-a-mobile-service"></a>（選用）從行動訊息服務傳送通知

在此區段中，我們會傳送給使用透過節點指令碼行動訊息服務的推播通知。

若要使用行動訊息服務傳送通知，請依照下列[開始使用行動電話服務]，然後︰

1. 登入至[Azure 傳統入口網站]，然後選取您的行動訊息服務。

2. 選取頂端的 [**排程**] 索引標籤。

    ![Azure 傳統入口網站的排程器][215]

3. 建立新的排程的工作，請插入名稱，然後選取**On demand**。

    ![Azure 傳統的入口網站-建立新的工作][216]

4. 建立工作時，按一下 [工作名稱]。 然後按一下 [在頂端列的 [**指令碼**] 索引標籤。

5. 插入您的排程器函數下列指令碼。 請務必為*DefaultFullSharedAccessSignature*取得較舊版本的取代您的通知中心名稱與連線字串的版面配置區。 按一下 [**儲存**]。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. 按一下**執行一次**下方列]。 您應該會在您的裝置上收到通知。

##<a name="next-steps"></a>後續步驟

在此範例中，您推入通知傳送到該網域所有 iOS 裝置。 若要針對特定的使用者，請參閱教學課程[使用通知集線器推入通知給使用者]。 如果您想要區段您感興趣的群組的使用者，您可以瞭解[使用通知集線器，傳送新消息]。 進一步瞭解如何使用通知集線器[通知集線器]指南和[通知集線器使用方法 iOS 版]。


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[開始使用行動電話服務]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[通知集線器指南]: http://msdn.microsoft.com/library/jj927170.aspx
[通知集線器使用方法 iOS 版]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[使用通知集線器給使用者的推播通知]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知集線器傳送相關消息]: /manage/services/notification-hubs/breaking-news-dotnet

[本機和推播通知程式設計指南]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure 入口網站]: https://portal.azure.com
