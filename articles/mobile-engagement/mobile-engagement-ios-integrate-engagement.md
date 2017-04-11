<properties
    pageTitle="Azure 行動互動 iOS SDK 整合 |Microsoft Azure"
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

#<a name="how-to-integrate-engagement-on-ios"></a>在 iOS 上整合互動的方式

> [AZURE.SELECTOR]
- [Windows 標準](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

此程序說明最簡單的方式啟動互動的分析及監視 iOS 應用程式中的函數。

互動 SDK 需要 iOS6 + 和 Xcode 8︰ 部署目標應用程式必須至少 iOS 6。

> [AZURE.NOTE]
> 如果您真的依賴 XCode 7，您可能會使用[iOS 互動 SDK v3.2.4](https://aka.ms/r6oouh)。 有位於已知的錯誤之前的版本的範圍模組時執行 iOS 10 裝置上的，請參閱 「[達到模組整合](mobile-engagement-ios-integrate-engagement-reach.md)，如需詳細資訊。 如果您選擇使用 SDK v3.2.4，然後直接略過`UserNotifications.framework`匯入下一個步驟。

以下是足以啟動報表的計算有關使用者、 工作階段、 活動、 當機和 Technicals 所有統計資料所需的記錄。 使用手動互動 API 必須完成計算其他統計資料事件、 錯誤等工作所需的記錄檔的報表 （這些統計資料與應用程式有關之後，請參閱[如何使用標記 API iOS 應用程式的進階的行動互動](mobile-engagement-ios-use-engagement-api.md)。

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>互動 SDK 內嵌 iOS 專案

- 從 [[在這裡](http://aka.ms/qk2rnj)下載 iOS SDK。

- 新增互動 SDK 至 iOS 專案︰ 在 Xcode，以滑鼠右鍵按一下您的專案及選取**[...新增檔案]**並選擇`EngagementSDK`資料夾。

- 互動需要額外架構工作︰ 在專案總管] 中開啟您的專案窗格，然後選取正確的目標。 然後，開啟**「 建立階段 」** ] 索引標籤，並在**「 連結二進位與文件庫]**功能表中，新增這些架構︰

    -   `UserNotifications.framework`-設定為連結`Optional`
    -   `AdSupport.framework`-設定為連結`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] 您可以移除 AdSupport 架構。 互動必須收集 IDFA 此架構。 不過，可以停用 IDFA 集合\<ios sdk-互動 idfa\>遵守新 Apple 原則有關此識別碼。

##<a name="initialize-the-engagement-sdk"></a>初始化 SDK 的互動

您需要修改您的應用程式的代理人︰

-   在實作檔案頂端，匯入的互動代理程式︰

        [...]
        #import "EngagementAgent.h"

-   初始化互動的方法內 '**applicationDidFinishLaunching:**'**應用程式︰ didFinishLaunchingWithOptions:**」:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>基本報表

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>建議使用的方法︰ 超載您`UIViewController`類別

若要啟動計算使用者、 工作階段、 活動、 當機和統計資料互動所需的所有記錄的報表，只要進行所有您`UIViewController`子類別繼承自`EngagementViewController`類別 (相同的規則`UITableViewController`  - \> `EngagementTableViewController`)。

**不互動︰**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**使用互動︰**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>替代方法︰ 來電`startActivity()`手動

如果您無法或不希望超載您`UIViewController`課程]，您可以改為啟動您的活動，則可電話`EngagementAgent`的直接方法。

> [AZURE.IMPORTANT] 自動通話 SDK iOS`endActivity()`關閉應用程式時的方法。 因此，是*強烈*建議您撥打`startActivity`方法每活動使用者的變更，請為*永不*呼叫`endActivity`方法，因為呼叫這個方法強制結束工作流程目前的工作階段。

##<a name="location-reporting"></a>位置報告

Apple 服務條款不允許應用程式使用的統計資料用途只追蹤的位置。 因此，建議您啟用位置報表，只有在您的應用程式也會使用另一個原因所追蹤的位置。

開始使用 iOS 8，您必須提供您的應用程式設定您的應用程式 Info.plist 檔案的金鑰[NSLocationWhenInUseUsageDescription]或[NSLocationAlwaysUsageDescription]的字串所使用的位置服務的描述。 如果您要在背景與互動的報告位置時，請新增金鑰 NSLocationAlwaysUsageDescription。 在所有其他的情況下，加入 NSLocationWhenInUseUsageDescription 的金鑰。

### <a name="lazy-area-location-reporting"></a>延遲區域位置報告

延遲區域位置報告可讓報表的國家/地區、 區域和裝置與相關聯的位置。 此類型的位置報告只會使用網路位置 （根據儲存格 ID 或 WIFI）。 一次最多每個工作階段報告 [裝置] 區域。 GPS 從未使用過，，所以此類型的位置報表很少 （不以無） 電池的影響。

報告的區域用來計算使用者、 工作階段、 事件與錯誤地理統計資料。 他們也可做為準則範圍行銷活動中。 獎狀[裝置 API]擷取裝置的相關報告的最後一個已知的區域。

若要啟用麻煩區域位置報告，請後初始化互動代理程式新增的下列行︰

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>即時位置報告

即時位置報告可讓報表緯度和經度相關聯的裝置。 根據預設，此類型的位置報告只使用網路位置 （根據儲存格 ID 或 WIFI），而且報表時，才使用中應用程式在前景執行 （也就是在工作階段中）。

即時位置都*無法*用來計算統計資料。 其唯一的目的是要允許即時地理圍欄使用\<抵達-對象-geofencing\>達到行銷活動中的準則。

若要啟用即時位置報告，請後初始化互動代理程式新增的下列行︰

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS 基礎報告

根據預設，即時位置報告只會使用基礎網路位置。 若要啟用 GPS （這是最更精確地） 的位置，請新增︰

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>背景報告

根據預設，即時位置報告只有作用中時，應用程式在前景執行 （也就是在工作階段中）。 若要啟用報告也背景中，加入︰

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] 當應用程式在背景執行，僅網路基礎位置報告，即使您啟用 GPS。

實作此函數會呼叫[startMonitoringSignificantLocationChanges] ，當您的應用程式會進入背景。 請注意，它會自動重新啟動您的應用程式在背景如果新的位置事件。

##<a name="advanced-reporting"></a>進階報告

（選擇性） 如果您要回報的應用程式的特定事件、 錯誤和工作，您需要使用互動 API 經由的方法`EngagementAgent`類別。 此類別的物件，則可電話擷取`[EngagementAgent shared]`靜態方法。

互動 API 使用所有互動的進階功能可都讓和詳細說明如何使用的互動 API iOS 上 (在的技術文件中也`EngagementAgent`類別)。

##<a name="disable-idfa-collection"></a>停用 IDFA 集合

根據預設，互動會使用[IDFA]來識別使用者。 但如果您不使用廣告應用程式中的其他位置，您可能會被拒絕 App Store 檢閱程序。 IDFA 集合可以藉由新增前置處理器巨集已停用`ENGAGEMENT_DISABLE_IDFA`pch 檔案中 (或在`Build Settings`應用程式)。 這樣就能確定沒有任何參照`ASIdentifierManager`，`advertisingIdentifier`或`isAdvertisingTrackingEnabled`在應用程式建立。

**Prefix.pch**檔案中的整合︰

    #define ENGAGEMENT_DISABLE_IDFA
    ...

您可以驗證 IDFA 集合正確停用應用程式中，請核取 [互動測試記錄。 請參閱整合測試\<ios-sdk-互動-測試-idfa\>文件的其他相關資訊。

##<a name="disable-log-reporting"></a>停用日誌報告

### <a name="using-a-method-call"></a>使用方法呼叫

如果您想要停止傳送記錄檔的互動，您可以撥打︰

    [[EngagementAgent shared] setEnabled:NO];

這個呼叫是持續︰ 使用`NSUserDefaults`以儲存資訊。

您可以啟用記錄報告再次呼叫的同一個函數`YES`。

### <a name="integration-in-your-settings-bundle"></a>您設定套件中整合

而非呼叫此函數，您也可以整合這項設定直接在現有的`Settings.bundle`檔案。 字串`engagement_agent_enabled`做為喜好設定識別碼，而且必須相關切換 (`PSToggleSwitchSpecifier`)。

下列範例`Settings.bundle`顯示實作的方式︰

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[裝置 API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
