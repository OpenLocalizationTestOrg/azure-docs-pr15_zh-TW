<properties
    pageTitle="Azure 行動互動 iOS SDK 升級程序 |Microsoft Azure"
    description="最新的更新和 iOS SDK Azure 行動互動的程序"
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
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="upgrade-procedures"></a>升級程序

如果您已經有整合互動的舊版應用程式，您必須升級 SDK 時，請考慮下列幾點。

每個新版本的 SDK，您必須先取代 （移除和重新匯入 xcode） [EngagementSDK 和 EngagementReach] 資料夾。

##<a name="from-300-to-400"></a>從 3.0.0 至 4.0.0

### <a name="xcode-8"></a>XCode 8
XCode 8 是強制從版本 4.0.0 SDK 的開始。

> [AZURE.NOTE] 如果您真的依賴 XCode 7，您可能會使用[iOS 互動 SDK v3.2.4](https://aka.ms/r6oouh)。 有已知的錯誤之前的版本的範圍模組 iOS 10 裝置上執行時︰ 系統通知沒有 actioned。 若要修正此您就必須執行已遭取代的 API`application:didReceiveRemoteNotification:`應用程式中代理人，如下所示︰

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **不建議使用此因應措施**這個問題，因為此 iOS API 功能已遭取代可以會變更任何即將來臨 （即使次要） 的 iOS 版升級。 您應該越快越切換 XCode 8。

### <a name="usernotifications-framework"></a>UserNotifications 架構
您需要新增`UserNotifications`架構在您建立的階段。

在專案總管] 中開啟您的專案窗格，然後選取正確的目標。 然後，開啟**「 建立階段 」** ] 索引標籤，並在**「 連結二進位與文件庫]**功能表中，新增 [架構`UserNotifications.framework`-設定為連結`Optional`

### <a name="application-push-capability"></a>應用程式推入功能
XCode 8 可能會重設您的應用程式推播功能，請在 [雙檢查其`capability`] 索引標籤的 [選取的目標。

### <a name="add-the-new-ios-10-notification-registration-code"></a>加入新的 iOS 10 通知註冊程式碼
登錄應用程式通知較舊的程式碼片段仍然運作，但遭取代之的 Api 使用 iOS 10 上執行時。

匯入`User Notification`架構︰

        #import <UserNotifications/UserNotifications.h> 

在您的應用程式委派`application:didFinishLaunchingWithOptions`方法取代︰

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

以︰

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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>如果您已經有自己的 UNUserNotificationCenterDelegate 實作

SDK 也會有自己的實作 UNUserNotificationCenterDelegate 通訊協定。 SDK 用於監控生命週期的 10 個或更大的 iOS 上執行的裝置上的互動通知。 如果 SDK 偵測到您的代理人，不會使用自己的實作因為可能只有一個 UNUserNotificationCenter 代理人，每個應用程式。 這表示您必須將互動邏輯新增至您的代理人。

有兩種方法達到此目標。

只要轉接您的代理人呼叫 sdk 的變更︰

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

或繼承自`AEUserNotificationHandler`類別

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] 您可以決定是否通知隨附的互動或不傳送其`userInfo`代理程式的字典`isEngagementPushPayload:`課程方法。

##<a name="from-200-to-300"></a>從 2.0.0 至 3.0.0
卸除 iOS 版支援 4.X。 從使用此版本部署目標應用程式必須至少 iOS 6。

如果您使用應用程式中的範圍，您必須新增`remote-notification`值`UIBackgroundModes`Info.plist 檔案才會收到遠端通知中的陣列。

方法`application:didReceiveRemoteNotification:`必須要取代的`application:didReceiveRemoteNotification:fetchCompletionHandler:`在您的應用程式委派。

「 AEPushDelegate.h 」 功能已遭取代介面，您需要移除所有參照。 這包含移除`[[EngagementAgent shared] setPushDelegate:self]`和代理人方法，從您的應用程式的代理人︰

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>從 1.16.0 至 2.0.0
以下說明如何從將由 Azure 行動互動應用程式提供 Capptain SA Capptain 服務移轉 SDK 整合。
如果您從舊版升級，請參閱 Capptain 網站，以第一次移轉到 1.16，然後套用下列程序。

>[AZURE.IMPORTANT] Capptain 及 Mobile 使用不相同的服務，如下所示的程序只會醒目提示要移轉的用戶端應用程式的方式。 移轉應用程式中的 SDK 不會移轉您的資料從 Capptain 伺服器到行動互動伺服器

### <a name="agent"></a>代理程式

方法`registerApp:`取代了新的方法`init:`。 您的應用程式的代理人必須隨之更新，並使用連線字串︰

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

從您只需要移除的所有執行個體的 SDK 已移除 SmartAd 追蹤`AETrackModule`類別

### <a name="class-name-changes"></a>變更類別名稱

屬於 rebranding，有幾個必須變更的類別/檔案名稱。

加上 「 CP 」 的所有類別是以 「 AE] 首碼重新都命名。

範例︰

-   `CPModule.h`重新命名為`AEModule.h`。

加上 「 Capptain 」 的所有類別是以 「 參與 」 首碼重新都命名。

範例︰

-   在課程`CapptainAgent`重新命名為`EngagementAgent`。
-   在課程`CapptainTableViewController`重新命名為`EngagementTableViewController`。
-   在課程`CapptainUtils`重新命名為`EngagementUtils`。
-   在課程`CapptainViewController`重新命名為`EngagementViewController`。
