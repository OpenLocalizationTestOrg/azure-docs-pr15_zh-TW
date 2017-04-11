<properties
    pageTitle="快速入門 Azure 行動互動中快速 iOS 版 |Microsoft Azure"
    description="瞭解如何使用 Azure 行動互動分析及推入通知的 iOS 應用程式。"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>快速入門 Azure 行動互動的 iOS 中快速應用程式

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主題說明如何使用 Azure 行動互動，瞭解您的應用程式使用狀況，並將推入通知傳送至 [區段 iOS 應用程式的使用者。
在本教學課程中，您可以建立一空白 iOS 應用程式，收集基本的資料，並會接收推入通知使用 Apple 推入通知系統 (APNS)。

本教學課程的需求如下︰

+ XCode 8，您可以從您的 MAC App Store 安裝
+ [行動互動 iOS SDK]
+ 您可以在您的 Apple 開發人員中心取得的推入通知憑證 (.p12)

> [AZURE.NOTE] 本教學課程中使用快速 3.0 版使用。 

完成此教學課程是所有其他行動互動的教學課程 iOS 應用程式的必要條件。

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started)。

##<a id="setup-azme"></a>IOS 應用程式的安裝行動互動

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>將您的應用程式連線到行動互動後端

本教學課程中會顯示 「 基本整合 」，這是收集資料，並傳送推入通知所需的最小集合。 完成的整合文件，請參閱[行動互動 iOS SDK 整合](mobile-engagement-ios-sdk-overview.md)

我們會示範整合 XCode 與建立基本的應用程式︰

###<a name="create-a-new-ios-project"></a>建立新的 iOS 專案

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>將您的應用程式連線到行動互動後端

1. 下載[行動互動 iOS SDK]
2. 擷取。 tar.gz 檔案到您的電腦中的資料夾
3. 以滑鼠右鍵按一下專案，然後選取 [」 將檔案新增至 … 」

    ![][1]

4. 瀏覽至您解壓縮 SDK 並選取的資料夾`EngagementSDK`資料夾，然後按 [確定]。

    ![][2]

5. 開啟`Build Phases`] 索引標籤以及在`Link Binary With Libraries`功能表新增架構，如下所示︰

    ![][3]

8. 建立可以選擇 [檔案] 以使用 SDK 的目標 C Api 橋接頁首 > 新增 > 檔案 > iOS > 來源 > 頁首檔案。

    ![][4]

9. 編輯橋接標頭檔公開行動互動目標 C 迅速程式碼的程式碼，新增下列匯入︰

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. 建立設定] 下確認下快速編譯器的程式碼產生設定 [目標 C 橋頁首建立具有此標題的路徑。 以下是路徑範例︰ **$(（根據路徑） 的 SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h**

    ![][6]

11. 回到您的應用程式*連線資訊*] 頁面的 Azure 入口網站，並複製連線字串

    ![][5]

12. 現在貼上中的連接字串`didFinishLaunchingWithOptions`代理人

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>啟用即時監視

若要開始傳送資料，並確保作用中使用者，請您必須行動互動後端傳送至少有一個畫面 （活動）。

1. 開啟**ViewController.swift**檔案，並取代為**EngagementViewController** **ViewController**的基底類別︰

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>使用即時監視連線應用程式

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>啟用推入通知和訊息的應用程式

行動互動可讓您互動，以及連絡您的使用者推入通知與行銷活動的內容中的應用程式訊息。 本單元稱為行動互動入口網站中的範圍。
下列各節會設定您的應用程式會收到通知。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>啟用您的應用程式收到無推播通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>取得文件庫新增至專案

1. 以滑鼠右鍵按一下您的專案
2. 選取`Add file to ...`
3. 瀏覽至您解壓縮 SDK 的資料夾
4. 選取 [`EngagementReach`資料夾
5. 按一下 [新增]
6. 編輯橋接的標頭檔案，以公開行動互動目標 C 達到標題，並新增下列匯入︰

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>修改您的應用程式的代理人

1. 內`didFinishLaunchingWithOptions`-建立達到模組，並將它傳遞給您現有的互動初始化行︰

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>啟用您的應用程式收到 APNS 推播通知
1. 新增至下行`didFinishLaunchingWithOptions`方法︰

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. 新增`didRegisterForRemoteNotificationsWithDeviceToken`方法，如下所示︰

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. 新增`didReceiveRemoteNotification:fetchCompletionHandler:`方法，如下所示︰

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[行動互動 iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
