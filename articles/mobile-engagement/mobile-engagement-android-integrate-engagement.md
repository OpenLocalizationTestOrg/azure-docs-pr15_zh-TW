<properties
    pageTitle="Azure 行動互動 Android SDK 整合"
    description="最新的更新和 Android SDK Azure 行動互動的程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-on-android"></a>如何將整合在 Android 上的互動

> [AZURE.SELECTOR]
- [Windows 標準](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

此程序說明最簡單的方式啟動互動的分析及監視 Android 應用程式中的函數。

> [AZURE.IMPORTANT] 您最小的 Android SDK API 層級必須 10 或更新版本 (Android 2.3.3 或更新版本)。

以下是足以啟動報表的計算有關使用者、 工作階段、 活動、 當機和 Technicals 所有統計資料所需的記錄。 使用手動互動 API 必須完成計算其他統計資料事件、 錯誤等工作所需的記錄檔的報表 （這些統計資料與應用程式有關之後，請參閱[如何使用進階的行動互動標記 API 的 Android](mobile-engagement-android-use-engagement-api.md) 。

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>將 Android 專案內嵌的互動 SDK 及服務

從[這裡](https://aka.ms/vq9mfn)開始下載 Android SDK `mobile-engagement-VERSION.jar` ，並將它們放`libs`Android 專案的資料夾 （如果尚不存在，請建立 [程式庫] 資料夾）。

> [AZURE.IMPORTANT]
> 如果您建置 ProGuard 與應用程式套件時，您需要讓某些類別。 您可以使用下列設定程式碼片段︰
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

您互動的連接字串，來指定啟動器活動呼叫下列方法︰

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Azure 入口網站上，會顯示您的應用程式的連接字串。

-   如果遺失，新增下列 Android 權限 (之前`<application>`標籤):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   新增下一節 (之間`<application>`和`</application>`標籤):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   變更`<Your application name>`應用程式的名稱。

> [AZURE.TIP] `android:label`屬性可讓您將會顯示給使用者在他們的電話的 「 執行中的服務] 畫面中，選擇互動服務的名稱。 建議您將此屬性設定為`"<Your application name>Service"`(例如`"AcmeFunGameService"`)。

指定`android:process`屬性可確保互動服務會執行，其本身 （為您的應用程式會使您的主要/UI 執行緒可能會降低回應，互動執行中的相同程序） 的程序中。

> [AZURE.NOTE] 您將在任何程式碼`Application.onCreate()`及其他應用程式回撥會執行的所有應用程式的處理程序，包括互動服務。 可能不想要的側邊效果 （例如，不需要的記憶體配置和對話中互動的程序，重複廣播的接收器或服務）。

如果您覆寫`Application.onCreate()`，建議您新增下列程式碼片段開頭您`Application.onCreate()`函數︰

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

您可以執行相同動作`Application.onTerminate()`，`Application.onLowMemory()`和`Application.onConfigurationChanged(...)`。

您也可以延伸`EngagementApplication`，而不是延伸`Application`: 回撥`Application.onCreate()`程序檢查和通話`Application.onApplicationProcessCreate()`僅其他的回撥如果目前的程序沒有裝載互動服務的項目，請套用相同的規則。

##<a name="basic-reporting"></a>基本報表

### <a name="recommended-method-overload-your-activity-classes"></a>建議使用的方法︰ 超載您`Activity`類別

若要啟動互動，來計算使用者、 工作階段、 活動、 當機和統計資料所需的所有記錄的報表，您只需所有您`*Activity`子類別繼承自對應`Engagement*Activity`類別 (例如如果您舊版的活動延伸`ListActivity`，讓延伸至`EngagementListActivity`)。

**不互動︰**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**使用互動︰**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] 使用時`EngagementListActivity`或`EngagementExpandableListActivity`，請確定任何撥`requestWindowFeature(...);`進行通話之前`super.onCreate(...);`，否則會當機。

您可以找到這些類別中的`src`資料夾，並可以將其複製到您的專案。 類別，也會**JavaDoc**。

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>替代方法︰ 來電`startActivity()`和`endActivity()`手動

如果您無法或不希望超載您`Activity`課程]，您可以改為開始及結束您的活動，則可電話`EngagementAgent`的直接方法。

> [AZURE.IMPORTANT] Android SDK 永不通話`endActivity()`方法，即使關閉應用程式時 （在 Android 上的應用程式實際不關閉）。 因此，是*強烈*建議您撥打`startActivity()`中的方法`onResume`的*所有*的回撥您的活動，以及`endActivity()`中的方法`onPause()`的*所有*的回撥您的活動。 這是唯一的方法，確定不會洩漏工作階段。 如果遺漏工作階段，互動服務的互動後端永遠不會中斷連線，（因為服務保持連線，只要工作階段中）。

以下是範例︰

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

此範例中非常類似`EngagementActivity`類別和其變化，其原始程式碼所提供的`src`資料夾。

##<a name="test"></a>測試

現在請確認您的整合模擬器或裝置中執行您的行動應用程式，然後驗證註冊監視器] 索引標籤上的工作階段。

下一節中是選擇性的。

##<a name="location-reporting"></a>位置報告

如果您想要報告的位置，您需要新增幾行程式設定 (之間`<application>`和`</application>`標籤)。

### <a name="lazy-area-location-reporting"></a>延遲區域位置報告

延遲區域位置報告可讓報表的國家/地區、 區域和裝置與相關聯的位置。 此類型的位置報告只會使用網路位置 （根據儲存格 ID 或 WIFI）。 一次最多每個工作階段報告 [裝置] 區域。 GPS 從未使用過，，所以此類型的位置報表很少 （不以無） 電池的影響。

報告的區域用來計算使用者、 工作階段、 事件與錯誤地理統計資料。 他們也可做為準則範圍行銷活動中。

若要啟用麻煩區域位置報告，您可以使用此程序上述設定執行︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您也需要新增下列權限，如果遺失︰

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

您可以繼續使用或者``ACCESS_FINE_LOCATION``如果已在您的應用程式中使用。

### <a name="real-time-location-reporting"></a>即時位置報告

即時位置報告可讓報表緯度和經度相關聯的裝置。 根據預設，此類型的位置報告只使用網路位置 （根據儲存格 ID 或 WIFI），而且報表時，才使用中應用程式在前景執行 （也就是在工作階段中）。

即時位置都*無法*用來計算統計資料。 其唯一的目的是要允許即時地理圍欄使用\<抵達-對象-geofencing\>達到行銷活動中的準則。

若要啟用即時報告的位置，您可以使用此程序上述設定執行︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您也需要新增下列權限，如果遺失︰

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

您可以繼續使用或者``ACCESS_FINE_LOCATION``如果已在您的應用程式中使用。

#### <a name="gps-based-reporting"></a>GPS 基礎報告

根據預設，即時位置報告只會使用基礎網路位置。 若要啟用 GPS （這是最更精確地） 的位置，請使用 [設定] 物件︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

您也需要新增下列權限，如果遺失︰

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>背景報告

根據預設，即時位置報告只有作用中時，應用程式在前景執行 （也就是在工作階段中）。 若要啟用報告也背景中，使用 [設定] 物件︰

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] 當應用程式在背景執行，僅網路基礎位置報告，即使您啟用 GPS。

如果使用者重新啟動裝置，也會停止背景位置報表，您可以將其加入，使其自動重新啟動在啟動時︰

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

您也需要新增下列權限，如果遺失︰

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M 權限

開始使用 Android M，某些與權限在執行階段管理需要使用者核准。

如果您要在 Android API 層級 23 執行階段權限將會預設為新的應用程式安裝關閉。 否則，會開啟預設。

使用者可以啟用/停用裝置的 [設定] 功能表這些權限。 關閉 [系統] 功能表上的權限刪除背景處理程序的應用程式，這是系統行為，背景中接收推入功能造成任何影響。

在行動互動的內容，是在執行階段核准的權限︰

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`（僅限時選取目標針對此項目 Android API 層級 23）

外部儲存僅用於達到一窺全貌功能。 如果您發現要求使用者為破壞此權限，您可以移除，如果您是使用其僅適用於行動互動，但犧牲停用一窺全貌 」 功能。

位置功能，您應該要求權限給使用者使用標準系統] 對話方塊。 如果使用者核准，您需要判斷``EngagementAgent``才能至帳戶的變更即時 （否則，變更將會處理使用者啟動的應用程式在下一次）。

以下是使用應用程式的活動要求權限及轉寄結果，如果是正數至程式碼範例``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>進階報告

（選擇性） 如果您要回報的應用程式的特定事件、 錯誤和工作，您需要使用互動 API 經由的方法`EngagementAgent`類別。 此類別的物件可抓取呼叫`EngagementAgent.getInstance()`靜態方法。

互動 API 使用所有互動的進階功能可都讓和詳細說明如何在 Android 上使用互動 API (也在的技術文件中`EngagementAgent`類別)。

##<a name="advanced-configuration-in-androidmanifestxml"></a>進階的設定 （在 AndroidManifest.xml)

### <a name="wake-locks"></a>喚醒鎖定

如果您想要確定統計資料，都會傳送即時使用 Wifi 時，或螢幕關閉時，新增下列選用的權限︰

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>當機報表

如果您想要停用當機報表，則新增這個 (之間`<application>`和`</application>`標籤):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>尖峰閥值

根據預設，互動服務報表中進行即時記錄。 如果您的應用程式經常報告記錄，但最好緩衝記錄，以及報告它們，一次一般時間基底 （又稱為 「 突發模式 」）。 若要這麼做，請新增此 (之間`<application>`和`</application>`標籤):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

尖峰模式稍微增加電池，但會影響互動監視器︰ 所有的工作階段及工作期間會捨入至突發臨界值 （因此，工作階段及工作短比突發閥值可能無法看到）。 建議使用不超過 30000 （30 秒） 的突發閾值。

### <a name="session-timeout"></a>工作階段逾時

根據預設的結束的 10s 結束之後，其最後一個活動 （通常會發生按下 [常用] 或 [回鍵、 設定電話閒置或跳到另一個應用程式）。 這是為了避免工作階段分割每次使用者結束並快速返回應用程式 （這可以發生時他回答圖像，檢查通知等等。）。 若要修改此參數。 若要這麼做，請新增此 (之間`<application>`和`</application>`標籤):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>停用日誌報告

### <a name="using-a-method-call"></a>使用方法呼叫

如果您想要停止傳送記錄檔的互動，您可以撥打︰

            EngagementAgent.getInstance(context).setEnabled(false);

這個呼叫是持續︰ 使用共用的偏好設定檔案。

如果互動是作用中，當您撥打此函數時，可能需要停止服務 1 分鐘。 不過，就不會啟動服務在下次啟動應用程式。

您可以啟用記錄報告再次呼叫的同一個函數`true`。

### <a name="integration-in-your-own-preferenceactivity"></a>在您自己的整合`PreferenceActivity`

而非呼叫此函數，您也可以整合這項設定直接在現有的`PreferenceActivity`。

您可以設定以使用您的喜好設定檔案 （含所需的模式） 互動`AndroidManifest.xml`檔案與`application meta-data`:

-   `engagement:agent:settings:name`索引鍵用來定義共用的喜好設定檔的名稱。
-   `engagement:agent:settings:mode`索引鍵用來定義共用的喜好設定檔的模式，您應該使用相同的模式中為您`PreferenceActivity`。 必須為的數字傳遞模式︰ 如果您在程式碼中使用常數旗標組合，檢查總計的值。

永遠互動使用`engagement:key`布林值的索引鍵，在 [管理此設定的喜好設定檔中。

下列範例`AndroidManifest.xml`顯示的預設值︰

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

然後您可以新增`CheckBoxPreference`下列所示的喜好設定版面配置︰

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
