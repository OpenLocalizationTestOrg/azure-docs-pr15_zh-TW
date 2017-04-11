<properties
    pageTitle="Azure 行動互動 iOS SDK 概觀 |Microsoft Azure"
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

#<a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK Azure 行動互動

從這裡開始，以取得如何整合 Azure 行動互動 ios 應用程式的所有的詳細資料。 如果您想要嘗試一下第一次，請確定您執行我們[15 分鐘教學課程](mobile-engagement-ios-get-started.md)。

按一下以查看[SDK 內容](mobile-engagement-ios-sdk-content.md)

##<a name="integration-procedures"></a>整合程序
1. 從這裡開始︰[整合 iOS 應用程式中的行動互動的方式](mobile-engagement-ios-integrate-engagement.md)

2. 通知︰[如何將整合 iOS 應用程式中的範圍 （通知）](mobile-engagement-ios-integrate-engagement-reach.md)

3. 標記計劃實作︰[如何使用標記 API iOS 應用程式的進階的行動互動](mobile-engagement-ios-use-engagement-api.md)


##<a name="release-notes"></a>版本資訊

### <a name="400-09122016"></a>4.0.0 (09/12/2016)

-   在 iOS 10 裝置上不 actioned 固定的通知。
-   取代 XCode 7。

舊版，請參閱[完整版本資訊](mobile-engagement-ios-release-notes.md)

##<a name="upgrade-procedures"></a>升級程序

如果您已經有整合互動的舊版應用程式，您必須升級 SDK 時，請考慮下列幾點。

您可能必須追蹤多個程序，如果您未接的 SDK 請完成的[升級程序](mobile-engagement-ios-upgrade-procedure.md)多個版本。

每個新版本的 SDK，您必須先取代 （移除和重新匯入 xcode） [EngagementSDK 和 EngagementReach] 資料夾。

###<a name="from-300-to-400"></a>從 3.0.0 至 4.0.0

### <a name="xcode-8"></a>XCode 8
XCode 8 是強制從版本 4.0.0 SDK 的開始。

> [AZURE.NOTE] 如果您真的依賴 XCode 7，您可能會使用[iOS 互動 SDK v3.2.4](https://aka.ms/r6oouh)。 有已知的錯誤之前的版本的範圍模組 iOS 10 裝置上執行時︰ 系統通知沒有 actioned。 若要修正此您就必須執行已遭取代的 API`application:didReceiveRemoteNotification:`應用程式中代理人，如下所示︰

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **不建議使用此因應措施**這個問題，因為此 iOS API 功能已遭取代可以會變更任何即將來臨 （即使次要） 的 iOS 版升級。 您應該越快越切換 XCode 8。

#### <a name="usernotifications-framework"></a>UserNotifications 架構
您需要新增`UserNotifications`架構在您建立的階段。

在專案總管] 中開啟您的專案窗格，然後選取正確的目標。 然後，開啟**「 建立階段 」** ] 索引標籤，並在**「 連結二進位與文件庫]**功能表中，新增 [架構`UserNotifications.framework`-設定為連結`Optional`

#### <a name="application-push-capability"></a>應用程式推入功能
XCode 8 可能會重設您的應用程式推播功能，請在 [雙檢查其`capability`] 索引標籤的 [選取的目標。

#### <a name="add-the-new-ios-10-notification-registration-code"></a>加入新的 iOS 10 通知註冊程式碼
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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>如果您已經有自己的 UNUserNotificationCenterDelegate 實作

SDK 都有它自己的實作 UNUserNotificationCenterDelegate 通訊協定。 SDK 用於監控生命週期的 10 個或更大的 iOS 上執行的裝置上的互動通知。 如果 SDK 偵測到您的代理人，不會使用自己的實作因為可能只有一個 UNUserNotificationCenter 代理人，每個應用程式。 這表示您必須將互動邏輯新增至您的代理人。

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