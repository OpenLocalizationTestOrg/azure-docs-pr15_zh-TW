<properties
    pageTitle="快速入門 Azure 行動互動目標 c iOS 版 |Microsoft Azure"
    description="瞭解如何使用狀況分析及推入通知中的 Azure 行動互動，iOS 應用程式。"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>快速入門 Azure 行動互動的目標 c iOS 應用程式

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，瞭解您的應用程式使用狀況，並將推入通知傳送至 [區段 iOS 應用程式的使用者。
在本教學課程中，您可以建立一空白 iOS 應用程式，收集基本的資料，並會接收推入通知使用 Apple 推入通知系統 (APNS)。

本教學課程的需求如下︰

+ XCode 8，您可以從您的 MAC App Store 安裝
+ [行動互動 iOS SDK]

完成此教學課程是所有其他行動互動的教學課程 iOS 應用程式的必要條件。

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started)。

##<a id="setup-azme"></a>IOS 應用程式的安裝行動互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合 」，這是收集資料，並傳送推入通知所需的最小集合。 完成的整合文件，請參閱[行動互動 iOS SDK 整合](mobile-engagement-ios-sdk-overview.md)

我們會示範整合 XCode 與建立基本的應用程式。

###<a name="create-a-new-ios-project"></a>建立新的 iOS 專案

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 下載[行動互動 iOS SDK]。
2. 擷取。 tar.gz 檔案到您的電腦中的資料夾。
3. Project 中，以滑鼠右鍵按一下，然後選取 [**新增檔案**。

    ![][1]

4. 瀏覽至您解壓縮 sdk，您可以選取的資料夾`EngagementSDK`資料夾，，然後按**[確定]**。

    ![][2]

5. 開啟 [**建立階段**] 索引標籤，並在 [**連結二進位與文件庫**] 功能表中，加入架構，如下所示︰

    ![][3]

6. 回到您的應用程式**連線資訊**] 頁面的 Azure 入口網站，並複製連線字串。

    ![][4]

7. 新增下列程式碼**AppDelegate.m**檔案中。

        #import "EngagementAgent.h"

8. 現在貼上中的連接字串`didFinishLaunchingWithOptions`委派。

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`是選擇性的陳述式，這可讓您可以找出問題 SDK 記錄。 

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料，並確保作用中使用者，請您必須行動互動後端傳送至少有一個畫面 （活動）。

1. 開啟**ViewController.h**檔案，並匯入**EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. 現在取代**ViewController**介面的進階類別`EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您與您的使用者進行互動，抵達推入通知與的應用程式行銷活動的內容中的訊息。 本單元稱為行動互動入口網站中的範圍。
下列各節會接收設定您的應用程式。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>啟用您的應用程式收到無推播通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>取得文件庫新增至專案

1. 以滑鼠右鍵按一下您的專案。
2. 選取**要新增的檔案**。
3. 瀏覽至您解壓縮 SDK 的資料夾。
4. 選取 [`EngagementReach`資料夾。
5. 按一下 [**新增**]。

### <a name="modify-your-application-delegate"></a>修改您的應用程式的代理人

1. 回**AppDeletegate.m**檔案匯入的互動達到模組。

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. 內`application:didFinishLaunchingWithOptions`方法，建立達到模組，並將它傳遞給您現有的互動初始化行︰

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>啟用您的應用程式收到 APNS 推播通知

1. 新增至下行`application:didFinishLaunchingWithOptions`方法︰

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. 新增`application:didRegisterForRemoteNotificationsWithDeviceToken`方法，如下所示︰

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. 新增`didFailToRegisterForRemoteNotificationsWithError`方法，如下所示︰

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. 新增`didReceiveRemoteNotification:fetchCompletionHandler`方法，如下所示︰

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[行動互動 iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

