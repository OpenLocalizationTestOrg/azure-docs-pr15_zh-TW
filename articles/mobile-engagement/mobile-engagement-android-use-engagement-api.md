<properties
    pageTitle="如何在 Android 上使用互動 API"
    description="最新的 Android SDK-如何在 Android 上使用互動 API"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>如何在 Android 上使用互動 API

這份文件是[在 Android 行動互動 SDK 的進階報告選項](mobile-engagement-android-advanced-reporting.md)的文件的增益集。 提供有關如何使用互動 API 報告您的應用程式統計資料深度詳細資料。

請記住，如果您只想參與報告您的應用程式工作階段、 活動、 當機和技術資訊，請再最簡單的方法是所有您`Activity`子類別繼承自對應`EngagementActivity`類別。

如果您想要執行其他工作，例如，如果您需要報告應用程式特定事件與錯誤的工作，或如果您使用不同的方式中的應用程式的活動報表超過一個實作於`EngagementActivity`類別，然後您需要使用互動 API。

互動 API 提供的`EngagementAgent`類別。 這個類別的執行個體，則可電話擷取`EngagementAgent.getInstance(Context)`靜態方法 (請注意，`EngagementAgent`傳回物件是單一)。

##<a name="engagement-concepts"></a>互動的概念

下列組件來調整常見[行動互動的概念](mobile-engagement-concepts.md)，Android 的平台。

### <a name="session-and-activity"></a>`Session`與`Activity`

如果使用者皆會保持閒置兩個*活動*之間的多個幾秒鐘，在兩個不同*的工作階段*中分割他們的*活動*的順序。 下列幾秒鐘稱為 「 工作階段逾時 」。

*活動*是通常相關聯的應用程式，一個畫面也就是說*活動*的開始畫面會顯示，並停駐點螢幕關閉時︰ 這是大小寫時使用整合互動 SDK`EngagementActivity`類別。

但*活動*可以也手動控制使用互動 API。 這個選項可讓分割指定的螢幕幾個 sub 部分，以取得更多詳細資料 （例如以已知頻率，並多久對話方塊內使用，則此畫面） 這個畫面的使用方式。

##<a name="reporting-activities"></a>報表的活動

> [AZURE.IMPORTANT] 您不需要的報表活動等本節所述，如果您使用的`EngagementActivity`類別和其所述整合互動如何在 Android 的文件上的變異字。

### <a name="user-starts-a-new-activity"></a>使用者啟動新的活動

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

您需要呼叫`startActivity()`每次使用者活動的變更。 此函數的第一個呼叫開始新的使用者工作階段。

最好呼叫此函數會在每一個活動`onResume`回撥。

### <a name="user-ends-his-current-activity"></a>使用者結束他目前活動

            EngagementAgent.getInstance(this).endActivity();

您需要呼叫`endActivity()`至少一次當使用者結束他的最後一個活動。 這會通知互動 SDK 使用者目前閒置，而且使用者工作階段，必須先關閉一次工作階段逾時便會到期 (如果您呼叫`startActivity()`工作階段的工作階段逾時到期之前，只要繼續)。

最好呼叫此函數會在每一個活動`onPause`回撥。

##<a name="reporting-events"></a>報告的事件

### <a name="session-events"></a>工作階段事件

工作階段事件通常用來報告使用者在他們的工作階段期間執行的動作。

**不含額外資料的範例︰**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**額外資料的範例︰**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>獨立的事件

相反工作階段事件獨立事件發生以外的工作階段的內容。

**範例︰**

假設您想要報告的事件發生時，就會觸發廣播的收件者︰

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>報表錯誤

### <a name="session-errors"></a>工作階段錯誤

工作階段錯誤通常用來報告他的工作階段期間影響使用者的錯誤。

**範例︰**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>獨立的錯誤

相反工作階段的錯誤，獨立錯誤可能會發生以外的工作階段的內容。

**範例︰**

下列範例會示範如何報告錯誤，只要將記憶體不足手機應用程式程序正在執行時。

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>報告工作

### <a name="example"></a>範例

假設您要報告您的登入程序的工期︰

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>工作期間的報表錯誤

若要執行的工作，而不是在目前的使用者工作階段的相關關聯錯誤。

**範例︰**

假設您想要的錯誤，在您登入程序的報表︰

[...]公用 void 登入 （內容的內容，...）{

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>報告工作期間的事件

執行中的工作，而不是在目前的使用者工作階段的相關可以相關聯的事件。

**範例︰**

假設我們有社交網路，我們使用報告工作的使用者連線至伺服器的總時間。 使用者可以保持連線偶數背景中當他使用的另一個應用程式，或手機在休眠狀態，因此沒有工作階段。

使用者可以從其朋友接收郵件，這是工作的事件。

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>額外的參數

可附加任意資料事件、 錯誤、 活動和工作。

可以結構化資料，它會使用 Android 的套件類別 （實際，其運作方式如同額外的參數，在 Android 對應方式）。 請注意，套件可以包含陣列或另一個套件的執行個體。

> [AZURE.IMPORTANT] 如果您將在 parcelable 或序列化參數，請確定其`toString()`方式執行以傳回閱讀字串。 當您將會撥打時包含非暫時的欄位不是序列化序列化課程會使 Android 當機`bundle.putSerializable("key",value);`

> [AZURE.WARNING] 不支援疏鬆陣列中額外的參數，也就是它不會序列化以陣列方式。 您應該將它們轉換成標準陣列額外的參數中使用之前，先。

### <a name="example"></a>範例

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>限制

#### <a name="keys"></a>索引鍵

在每個機碼`Bundle`必須符合下列規則運算式︰

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示開始鍵時，必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

Extras 限於**1024年**個字元，每個呼叫 （一次以編碼 JSON 互動服務）。

在上一個範例中，傳送至伺服器 JSON 是 58 字元︰

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>報告應用程式的資訊

您可以手動在報告追蹤資訊 （或任何其他應用程式特定資訊） 使用`sendAppInfo()`函數。

請注意，這些資訊可以傳送從屬參照︰ 只指定索引鍵的最新值將會保留為指定的裝置。

事件 extras]，例如套件類別用來擷取應用程式的資訊，請注意，陣列或子配搭會被視為一般的字串 （使用 JSON 序列化）。

### <a name="example"></a>範例

以下是要傳送使用者性別及 birthdate 的程式碼範例︰

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>限制

#### <a name="keys"></a>索引鍵

在每個機碼`Bundle`必須符合下列規則運算式︰

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示開始鍵時，必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

應用程式的資訊僅限於**1024年**個字元，每個呼叫 （一次以編碼 JSON 互動服務）。

在上一個範例中，傳送至伺服器 JSON 是 44 字元︰

            {"expiration":"2016-12-07","status":"premium"}
