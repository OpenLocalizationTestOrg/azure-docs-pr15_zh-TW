<properties
    pageTitle="Azure 行動互動 iOS SDK 達到整合 |Microsoft Azure"
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

#<a name="how-to-integrate-engagement-reach-on-ios"></a>在 iOS 上整合互動連絡方式

您必須遵循整合程序之前本指南所述[iOS 文件的整合互動的方式](mobile-engagement-ios-integrate-engagement.md)。

這份文件需要 XCode 8。 如果您真的依賴 XCode 7，您可能會使用[iOS 互動 SDK v3.2.4](https://aka.ms/r6oouh)。 有已知的錯誤之前的版本 iOS 10 裝置上執行時︰ 系統通知沒有 actioned。 若要修正此您就必須執行已遭取代的 API`application:didReceiveRemoteNotification:`應用程式中代理人，如下所示︰

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **不建議使用此因應措施**這個問題，因為此 iOS API 功能已遭取代可以會變更任何即將來臨 （即使次要） 的 iOS 版升級。 您應該越快越切換 XCode 8。

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>啟用您的應用程式收到無推播通知

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>整合步驟

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>互動達到 SDK 內嵌 iOS 專案

-   新增達到 sdk Xcode 專案中。 在 Xcode，移至 [**專案\>新增至專案**選擇`EngagementReach`資料夾。

### <a name="modify-your-application-delegate"></a>修改您的應用程式的代理人

-   在實作檔案頂端，匯入的互動達到模組︰

        [...]
        #import "AEReachModule.h"

-   方法內`applicationDidFinishLaunching:`或`application:didFinishLaunchingWithOptions:`、 建立達到模組，並將它傳遞給您現有的互動初始化行︰

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   修改您要為您的通知圖示的影像名稱**「 icon.png 」**字串。
-   如果您想要使用達到行銷活動的選項*更新徽章值*，或如果您想要使用原生推入\<SaaS/達到 API 行銷活動格式/原生推入\>行銷活動，您必須讓的範圍模組管理徽章圖示本身 （它會自動清除 [應用程式徽章和也重設互動所儲存的每次應用程式的開始或 foregrounded 值）。 這是達到模組初始化後新增的下列行︰

        [reach setAutoBadgeEnabled:YES];

-   如果您想要處理達到資料推入，您必須讓符合您的應用程式代理人`AEReachDataPushDelegate`通訊協定。 在達到模組初始化後新增的下列行︰

        [reach setDataPushDelegate:self];

-   然後您可以實作方法`onDataPushStringReceived:`和`onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:`在您的應用程式的代理人︰

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>類別

類別參數是選擇性的當您建立資料推播行銷活動時，可讓您用來篩選資料推入。 如果您想要推入不同類型的`Base64`資料，如果想来識別其類型剖析它們之前。

**您的應用程式現在已準備就緒接收，並顯示達到內容的 ！**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>如何接收宣告] 與 [隨時投票

互動可以傳送達到通知給您的使用者隨時使用 Apple 推入通知服務。

若要啟用此功能，您必須準備您的應用程式的 Apple 推入通知，及修改您的應用程式的代理人。

### <a name="prepare-your-application-for-apple-push-notifications"></a>準備您的應用程式 Apple 推播通知

請依照快速入門︰[如何準備您的應用程式 Apple 推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>新增必要的用戶端程式碼

*現在您的應用程式應該互動主選單中有註冊的 Apple 推播憑證。*

如果不已完成它，您需要註冊您的應用程式，來接收推入通知。

* 匯入`User Notification`架構︰

        #import <UserNotifications/UserNotifications.h>

* 您的應用程式啟動時新增的下列行 (通常是在`application:didFinishLaunchingWithOptions:`):

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

然後，您需要提供給 Apple 伺服器所傳回的裝置 token 的互動。 這是在名為方法`application:didRegisterForRemoteNotificationsWithDeviceToken:`在您的應用程式的代理人︰

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

最後，您必須通知互動 SDK，當您的應用程式收到遠端通知。 若要執行這項作業，呼叫方法`applicationDidReceiveRemoteNotification:fetchCompletionHandler:`在您的應用程式的代理人︰

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] 上述的方法被採用 iOS 7。 如果您的目標 iOS < 7，請務必實作方法`application:didReceiveRemoteNotification:`應用程式代理人與通話`applicationDidReceiveRemoteNotification`上傳墩，而不是 EngagementAgent`handler`引數︰

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] 根據預設，互動達到控制項 completionHandler。 如果您想要手動回應`handler`封鎖程式碼中，您可以將墩傳遞`handler`引數，並控制完成封鎖自己。 請參閱`UIBackgroundFetchResult`可能值的清單類型。


### <a name="full-example"></a>完整的範例

以下是整合完整的範例︰

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>如果您有自己的 UNUserNotificationCenterDelegate 實作

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

##<a name="how-to-customize-campaigns"></a>如何自訂行銷活動

### <a name="notifications"></a>通知

有兩種類型的通知︰ 系統和應用程式中的通知。

系統通知由 iOS，且無法自訂。

在 [應用程式通知所做的動態會新增至目前的應用程式視窗的檢視。 這稱為通知覆疊。 通知覆疊非常適合在快速的整合，因為他們不需要您修改您的應用程式中的任何檢視。

#### <a name="layout"></a>版面配置

若要修改您的應用程式通知的外觀，只需修改檔案`AENotificationView.xib`至您的需求，只要您保留標記值及現有 subviews 的類型。

根據預設，在應用程式通知會顯示在畫面底部。 如果您想要顯示在畫面頂端，編輯提供`AENotificationView.xib`變更`AutoSizing`主檢視，讓它可以保留其超級檢視表頂端的屬性。

#### <a name="categories"></a>類別

當您修改提供的版面配置時，您可以修改您的通知的外觀。 類別可讓您定義通知的各種不同的目標看起來 （可能是行為）。 當您建立達到行銷活動時，您可以指定一個類別。 請記住，類別也可讓您自訂的相關公告和輪詢的回覆，這份文件的更新版本中所述。

若要註冊您的通知的類別處理常式，您需要後初始化達到模組中加入通話。

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`必須符合的通訊協定物件的執行個體`AENotifier`。

您可以自行實作通訊協定方法，或者您可以選擇重新實作現有類別`AEDefaultNotifier`已執行大部分的工作。

例如，如果您想要重新定義在特定類別的 [通知] 檢視，您可以依照本範例︰

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

此範例中的類別假設您有一個名為檔案`MyNotificationView.xib`在您的主要應用程式套件。 如果無法找到對應方法`.xib`、 將不會顯示通知，並且互動輸出主控台中檢視郵件。

提供的 nib 檔案應該尊重下列規則︰

-   只包含單一檢視。
-   Subviews 應該會在提供的 nib 檔案的檔案類型的名稱加以命名`AENotificationView.xib`
-   Subviews 應有內所提供的項目為相同的標籤名稱為 nib 檔案`AENotificationView.xib`

> [AZURE.TIP] 只複製提供的 nib 檔案，名為`AENotificationView.xib`，並使用從這裡開始。 但小心，在此 nib 檔案內檢視是關聯類別`AENotificationView`。 此課程重新定義方法`layoutSubViews`移動和重新調整其 subviews 根據內容而來。 您可能會想要取代`UIView`或您的自訂檢視類別。

如果您需要更深入的通知您的自訂 (如果您想要執行個體載入您的檢視，直接從程式碼)，建議您提供的來源碼和類別文件，查看`Protocol ReferencesDefaultNotifier`和`AENotifier`。

請注意，您可以使用相同的上線多個類別。

您也可以重新定義預設上線，像這樣︰

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>通知的處理

使用時的預設類別，在呼叫一些生命週期方法`AEReachContent`物件報表統計資料，並更新行銷活動狀態︰

-   當應用程式中，不會顯示通知`displayNotification`方法稱為 （此報告統計） 來`AEReachModule`如果`handleNotification:`會傳回`YES`。
-   關閉通知，如果`exitNotification`呼叫方法，報告統計和下一個行銷活動可以立即處理。
-   按一下通知時，如果`actionNotification`是一部分，報告統計資料，並執行相關聯的動作。

如果您實作`AENotifier`略過的預設行為，您必須自行撥生命週期種方法。 下列範例說明部分會在略過的預設行為的情況下︰

-   您不會延伸`AEDefaultNotifier`，例如您實作從頭類別處理。
-   您覆蓋`prepareNotificationView:forContent:`，請務必對應至少`onNotificationActioned`或`onNotificationExited`其中一個您 U.I 控制項。

> [AZURE.WARNING] 如果`handleNotification:`擲回例外狀況，內容會刪除與`drop`是呼叫，這報告中，而現在可以處理下一個活動。

#### <a name="include-notification-as-part-of-an-existing-view"></a>包含屬於現有檢視的通知

覆疊適合用來快速的整合，但可以有時不方便，或可能會有不想要的側邊效果。

如果您不滿意您檢視的部分覆疊系統，則您可以自訂，這些檢視。

您可以決定要在現有檢視中包含我們通知版面配置。 若要這麼做，有兩個實作樣式︰

1.  新增使用介面建立器的 [通知] 檢視

    -   開啟*介面建立器*
    -   將 320 x 60 （或 768 x 60，如果您是在 iPad 上）`UIView`您要顯示的通知
    -   設定此檢視的標籤值︰ **36822491**

2.  以程式設計方式將 [通知] 檢視。 當您檢視已初始化時，只要新增下列程式碼︰

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`巨集，請參閱`AEDefaultNotifier.h`。

> [AZURE.NOTE] 預設上線會自動偵測通知版面配置會包含在此檢視中，並為它不會加入重疊。

### <a name="announcements-and-polls"></a>宣告和輪詢的回覆

#### <a name="layouts"></a>版面配置

您可以修改檔案`AEDefaultAnnouncementView.xib`和`AEDefaultPollView.xib`，只要您保留標記值及現有 subviews 的類型。

#### <a name="categories"></a>類別

##### <a name="alternate-layouts"></a>替代的版面配置

通知，例如行銷活動的類別可以用於有替代的版面配置的相關公告和輪詢的回覆。

若要建立類別通知，您必須延伸**AEAnnouncementViewController**並註冊後初始化達到模組︰

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] 每次使用者會在與類別的公告的通知按一下 「 我\_類別 」，您註冊的檢視控制器 (在此情況下`MyCustomAnnouncementViewController`) 將初始化呼叫方法`initWithAnnouncement:`和檢視將會新增至目前的應用程式視窗。

在您實作`AEAnnouncementViewController`類別，則必須以讀取屬性`announcement`初始化您 subviews。 請考慮下列範例，其中兩個標籤都使用初始化`title`和`body`的屬性`AEReachAnnouncement`類別︰

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

如果您不想要自行載入您的檢視，但您只想要重複使用的預設公告檢視版面配置，您可以只是讓您的自訂檢視控制器延伸提供的課程`AEDefaultAnnouncementViewController`。 在此情況下，複製 [nib 檔案`AEDefaultAnnouncementView.xib`並將其重新命名，就可以由您的自訂檢視控制器載入 (名為控制站`CustomAnnouncementViewController`，您應致電 nib 檔案`CustomAnnouncementView.xib`)。

若要取代預設的類別的相關公告，只要註冊您的自訂檢視控制器類別中所定義`kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

投票可以進行自訂，以相同的方式︰

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

這次，提供`MyCustomPollViewController`必須擴充`AEPollViewController`。 您可以選擇從預設的控制站︰ `AEDefaultPollViewController`。

> [AZURE.IMPORTANT] 別忘了呼叫`action`(`submitAnswers:`自訂投票檢視控制站) 或`exit`之前關閉檢視控制站的方法。 否則，統計資料不會傳送 （也就是在行銷活動沒有分析），並重新啟動應用程式程序之前，就不會通知其他重要的是下一個活動。

##### <a name="implementation-example"></a>實作範例

在此實作自訂公告檢視會載入從外部 xib 檔案。

等進階的通知自訂建議您要的標準實作程式。

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
