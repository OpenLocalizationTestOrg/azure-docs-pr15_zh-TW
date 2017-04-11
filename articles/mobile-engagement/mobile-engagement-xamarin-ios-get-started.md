<properties
    pageTitle="快速入門的 Xamarin.iOS Azure 行動互動"
    description="瞭解如何使用 Azure 行動互動分析及推入通知 Xamarin.iOS 應用程式。"
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>開始使用 Azure 行動已 Xamarin.iOS 應用程式的互動

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，以瞭解您的應用程式使用狀況，並在 Xamarin.iOS 應用程式中的區段使用者傳送推入通知。
在本教學課程中，您可以建立空白的 Xamarin.iOS 應用程式的會收集基本的資料，並會接收推入通知使用 Apple 推入通知系統 (APNS)。

本教學課程的需求如下︰

+ [Xamarin Studio](http://xamarin.com/studio)。 您也可以使用 Visual Studio 與 Xamarin 但本教學課程使用 Xamarin Studio。 如需安裝指示，請參閱[設定與安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。 
+ [行動裝置的互動 Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started)。

##<a id="setup-azme"></a>IOS 應用程式的安裝行動互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合 」 這是最小設定才能收集資料，並傳送推入通知。

我們會示範整合 Xamarin 與建立基本的應用程式︰

###<a name="create-a-new-xamarinios-project"></a>建立新的 Xamarin.iOS 專案

1. 啟動 Xamarin Studio。 移至 [**檔案** -> **新** -> **解決方案** 

    ![][1]

2. 選取**單一檢視應用程式**，請確定所選的語言是**C#** ，再按一下 [**下一步**。

    ![][2]

3. 填寫**應用程式名稱**] 和 [**組織識別碼**，然後按一下 [**下一步**。 

    ![][3]

    > [AZURE.IMPORTANT] 確認發佈您最後使用部署 iOS 應用程式的設定檔使用應用程式識別碼符合完全與您在這裡有套件識別碼。 

4. 更新的**專案名稱**、**方案名稱**和**位置**，如有必要，並按一下 [**建立**]。

    ![][4]
 
Xamarin Studio 將會建立示範應用程式中，我們會整合行動互動。 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 以滑鼠右鍵按一下 [解決方案] 視窗中的 [**套件**] 資料夾，然後選取 [**新增封包**

    ![][5]

2. **Microsoft Azure 行動互動 Xamarin SDK**搜尋，並將其新增至您的方案。  

    ![][6]
   
3. 開啟**AppDelegate.cs**並新增下列使用陳述式︰

        using Microsoft.Azure.Engagement.Xamarin;

4. 在**FinishedLaunching**方法中，新增下列初始化與行動互動後端的連線。 請務必先新增您的**連接字串**。 將此程式碼也會使用 dummy **NotificationIcon**加入，您可能會想要取代行動互動 SDK。 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料，並確保作用中使用者，請您必須行動互動後端傳送至少有一個畫面。

1. 開啟**ViewController.cs**並新增下列使用陳述式︰

        using Microsoft.Azure.Engagement.Xamarin;

2. 取代的類別`ViewController`繼承自`UIViewController`至`EngagementViewController`。 

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您與您的使用者進行互動，抵達推入通知與的應用程式行銷活動的內容中的訊息。 本單元稱為行動互動入口網站中的範圍。
下列各節會接收設定您的應用程式。

### <a name="modify-your-application-delegate"></a>修改您的應用程式的代理人

1. 開啟**AppDelegate.cs**並新增下列使用陳述式︰

        using System; 

2. 現在內`FinishedLaunching`方法，新增下列註冊推入郵件後`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. 最後的更新或新增下列方法︰

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. 在方案中您**Info.plist**檔案，請確認**套件識別碼**符合您有佈建設定檔中 Apple 開發人員中心中的**應用程式識別碼**。 

    ![][7]

5. 在相同的**Info.plist**檔案，請確定您已核取**啟用背景模式**和**遠端通知**。 

    ![][8]

6. 您有此發佈的設定檔與相關的裝置上執行應用程式。 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
