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

#<a name="how-to-integrate-engagement-reach-on-android"></a>如何將整合在 Android 上的互動範圍

> [AZURE.IMPORTANT] 您必須遵循整合述的程序整合互動如何在 Android 的文件之前本指南。

##<a name="standard-integration"></a>標準整合

連絡 SDK 需要**Android 支援文件庫 (v4)**。

若要新增文件庫至您的專案中**蝕**最快的方法是`Right click on your project -> Android Tools -> Add Support Library...`。

如果您不是使用蝕，您可以閱讀指示[以下]。

複製您在 project 中 SDK 達到資源檔案︰

-   從檔案複製`res/layout`資料夾傳遞到 SDK 與`res/layout`應用程式的資料夾。
-   從檔案複製`res/drawable`資料夾傳遞到 SDK 與`res/drawable`應用程式的資料夾。

編輯您`AndroidManifest.xml`檔案︰

-   新增下一節 (之間`<application>`和`</application>`標籤):

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
              <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
              </intent-filter>
            </receiver>

-   您需要重新執行系統通知開機所未按下此權限 （否則，將會保留在磁碟上，但不會再顯示，您確實必須包含此）。

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   指定使用複製並編輯 [下一節 （兩者均在應用程式和系統檔案） 的通知圖示 (之間`<application>`和`</application>`標籤):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] 如果您打算使用系統通知，建立達到行銷活動時，此區段會**強制**。 Android 可防止系統通知圖示不顯示。 因此如果您省略此區段中，使用者會無法收到通知。

-   如果您使用一窺全貌系統通知建立行銷活動，您需要新增下列權限 (之後`</application>`標記) 如果遺失︰

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   在 Android M，如果您的應用程式的目標 Android API 層級 23 或更大，``WRITE_EXTERNAL_STORAGE``權限要求使用者核准。 請閱讀[本節](mobile-engagement-android-integrate-engagement.md#android-m-permissions)。

-   系統通知您也可以指定在達到行銷活動如果裝置應該撥打及/或震動。 您必須確定宣告下列權限，即可 (之後`</application>`標籤):

            <uses-permission android:name="android.permission.VIBRATE" />

    沒有此權限，Android 可防止系統通知的顯示檢查撥打] 或 [達到行銷活動管理員中的 [vibrate] 選項。

-   如果您在建立使用**ProGuard**應用程式，並錯誤相關的 Android 支援文件庫或互動 jar，新增下列幾行，您`proguard.cfg`檔案︰

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>原生推入

現在您設定範圍模組，您需要設定都能收到在裝置上的行銷活動的原生推入。

我們支援在 Android 上的兩個服務︰

  - Google Play 裝置︰ 使用依照[如何互動輔助線的整合 GCM](mobile-engagement-android-gcm-integrate.md)指南 [ [Google 雲端訊息]。
  - Amazon 裝置︰ 使用依照[如何互動輔助線的整合 ADM](mobile-engagement-android-adm-integrate.md)指南 [ [Amazon 裝置訊息]。

如果您想要目標 Amazon 和 Google Play 裝置，可以有 1 AndroidManifest.xml/APK 開發內的所有項目。 但如果他們找出 GCM 程式碼時送出到 Amazon，它們可能拒絕您的應用程式。

您應該在此情況下使用多個 APKs。

**您的應用程式現在已準備就緒接收及顯示達到行銷活動的 ！**

##<a name="how-to-handle-data-push"></a>如何處理資料推入

### <a name="integration"></a>整合

如果您想要接收達到資料推入您的應用程式，您必須建立子類別的`com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver`及參照中的`AndroidManifest.xml`檔案 (之間`<application>`及/或`</application>`標籤):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

然後您可以覆寫`onDataPushStringReceived`和`onDataPushBase64Received`回撥。 以下是範例︰

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>類別

類別參數是選擇性的當您建立資料推播行銷活動時，可讓您用來篩選資料推入。 如果您有多個廣播的接收器處理不同類型的資料推入，或如果您想要推入不同類型的`Base64`資料，如果想来識別其類型剖析它們之前。

### <a name="callbacks-return-parameter"></a>傳回回撥的參數

以下是一些可正確處理的傳回參數的指示`onDataPushStringReceived`和`onDataPushBase64Received`:

-   廣播的收件者應傳回`null`中回撥如果不知道如何處理資料推入。 您應該使用類別來判斷您廣播的收件者是否應該處理資料推入。
-   廣播的收件者的其中一個應傳回`true`中回撥如果它接受資料推入。
-   廣播的收件者的其中一個應傳回`false`中回撥如果認為資料推入，但是它捨棄基於任何原因。 例如，傳回`false`時收到的資料不正確。
-   如果其中一個廣播收件者會傳回`true`時另一個傳回`false`相同的資料推入，會定義行為，您應該永遠不會執行這項作業。

傳回類型僅用於達到統計資料︰

-   `Replied`如果其中一個廣播接收器傳回 [遞增`true`或`false`。
-   `Actioned`只有當其中一個廣播接收器傳回就會增加`true`。

##<a name="how-to-customize-campaigns"></a>如何自訂行銷活動

若要自訂行銷活動，您可以修改達到 SDK 中提供的版面配置。

您應該保留所有使用版面配置中的識別項，保留，用於識別項，尤其是文字檢視及圖像的檢視類型]。 某些檢視只會用來隱藏或顯示區域，因此可能會變更其類型。 請如果您想要變更的檢視中提供的版面配置類型，檢查程式碼。

### <a name="notifications"></a>通知

有兩種類型的通知︰ 系統和應用程式中使用不同的版面配置檔案的通知。

#### <a name="system-notifications"></a>系統通知

若要自訂系統告知您需要使用**類別**。 您可以跳到[類別](#categories)。

#### <a name="in-app-notifications"></a>在 [應用程式通知

根據預設，在應用程式通知是動態會新增至目前活動的使用者介面獎狀 Android 方法檢視`addContentView()`。 這稱為通知覆疊。 通知覆疊非常適合在快速的整合，因為它們不需要您修改您的應用程式中的任何版面配置。

若要修改您的通知覆疊的外觀，只需修改檔案`engagement_notification_area.xml`至您的需求。

> [AZURE.NOTE] 檔案`engagement_notification_overlay.xml`是用來建立一個通知覆疊，其中包括檔案`engagement_notification_area.xml`。 您也可以自訂以符合您的需求 （例如通知區域內重疊的位置）。

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>包含的活動版面配置一部分的通知版面配置

覆疊適合用來快速的整合，但可以有所不便或有側效果，在特殊的情況下。 在活動層級，方便您避免影響特殊的活動，您可以自訂覆疊系統。

您可以決定要併入您現有的版面配置**包含**Android 陳述式獎狀我們通知版面配置。 以下是範例修改`ListActivity`版面配置只包含`ListView`。

**前互動整合︰**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**之後互動整合︰**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

在此範例中我們新增上層容器，因為原始的版面配置會使用清單檢視的最上層的項目。 我們也加入`android:layout_weight="1"`能夠將清單檢視設定] 下方的檢視新增`android:layout_height="fill_parent"`。

互動達到 SDK 會自動偵測通知版面配置包含在此活動，並不會將新增此活動重疊。

> [AZURE.TIP] 如果您在應用程式中使用 ListActivity，顯示的範圍覆疊您將無法從反應再按一下 [清單] 檢視中的項目。 這是已知的問題。 若要暫時解決這個問題建議您自己清單活動中的版面配置等前一個範例中內嵌的通知版面配置。

##### <a name="disabling-application-notification-per-activity"></a>停用每活動的應用程式通知

如果您不想要新增至您的活動，重疊，而且如果您沒有在自己的版面配置中包含通知版面配置，您可以停用此活動中的覆疊`AndroidManifest.xml`藉由新增`meta-data`區段像在下列範例中︰

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>類別

當您修改提供的版面配置時，您可以修改您的通知的外觀。 類別可讓您定義通知的各種不同的目標看起來 （可能是行為）。 當您建立達到行銷活動時，您可以指定一個類別。 請記住，類別也可讓您自訂的相關公告和輪詢的回覆，這份文件的更新版本中所述。

若要註冊您的通知的類別處理常式，您需要初始化應用程式時加入通話。

> [AZURE.IMPORTANT] 請閱讀關於 android︰ 程序屬性的警告\<android sdk 互動-流程圖\>中整合 Android 主題之前的互動方式。

下列範例假設您確認先前的警告，並使用的子類別`EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier`物件是通知類別處理常式的實作。 其中一個是實作`EngagementNotifier`介面或預設實作子類別︰ `EngagementDefaultNotifier`。

請注意，相同的上線可以處理數種類別，您可以將其註冊像這樣︰

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

若要取代預設的類別實作，您可以在下列範例中註冊您的實作，例如︰

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

目前在處理常式中使用的類別以在大部分的方法，您可以在覆寫參數傳遞`EngagementDefaultNotifier`。

傳送以`String`參數或間接在`EngagementReachContent`物件`getCategory()`方法。

您可以變更的通知建立程序大部分重新方法定義在`EngagementDefaultNotifier`，更進階的自訂隨意看技術文件和程式碼。

##### <a name="in-app-notifications"></a>在 [應用程式通知

如果您只想要用於在特定類別的替代的版面配置，您可以執行此，如下列範例所示︰

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**範例`my_notification_overlay.xml`:**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

如您所見，重疊檢視識別碼是不同的標準的項目。 請務必每個版面配置，使用覆疊的唯一識別碼。

**範例`my_notification_area.xml`:**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

如您所見，通知區域檢視識別碼是不同的標準的項目。 請務必每個版面配置，使用之通知區域的唯一識別碼。

此範例中的類別可顯示在畫面頂端的應用程式 （或在 [應用程式） 通知。 我們不會變更使用本身通知區域中的標準識別項。

如果您想要的變更，您必須重新定義`EngagementDefaultNotifier.prepareInAppArea`方法。 如果您要在技術文件和來源程式碼，建議`EngagementNotifier`和`EngagementDefaultNotifier`如果您要的層級的進階自訂。

##### <a name="system-notifications"></a>系統通知

延長`EngagementDefaultNotifier`，您可以覆寫`onNotificationPrepared`變更預設實作準備的通知。

例如︰

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

此範例可讓您內容，顯示為 [進行中事件 」 進行中的 「 類別使用系統的通知。

如果您想要建立`Notification`物件中從頭開始，您可以返回`false`方法與通話`notify`自己上`NotificationManager`。 在此情況下很重要您保持`contentIntent`、`deleteIntent`和使用通知識別項`EngagementReachReceiver`。

以下是這種實作正確的範例︰

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>通知只公告

按一下 [僅限公告可以進行自訂，覆寫通知的管理`EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared`修改準備`Intent`。 使用此方法可讓您輕鬆地微調標幟。

若要新增的範例`SINGLE_TOP`標幟︰

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

針對舊版的互動使用者，請注意，系統不需要動作的通知 URL 立即啟動應用程式是否它是在背景，讓這種方式可與不動作 URL 的公告。 自訂目的時，您應該考慮的。

您也可以實作`EngagementNotifier.executeNotifAnnouncementAction`從頭。

##### <a name="notification-life-cycle"></a>通知生命週期

使用時的預設類別，在呼叫一些生命週期方法`EngagementReachInteractiveContent`物件報表統計資料，並更新行銷活動狀態︰

-   當通知會顯示在應用程式，或放在狀態列中，`displayNotification`方法稱為 （此報告統計） 來`EngagementReachAgent`如果`handleNotification`傳回`true`。
-   關閉通知，如果`exitNotification`呼叫方法，報告統計和下一個行銷活動可以立即處理。
-   按一下通知時，如果`actionNotification`是，報告統計資料，並啟動的相關聯的目的。

如果您實作`EngagementNotifier`略過的預設行為，您必須自行撥生命週期種方法。 下列範例說明部分會在略過的預設行為的情況下︰

-   您不會延伸`EngagementDefaultNotifier`，例如您實作從頭類別處理。
-   系統通知覆蓋`onNotificationPrepared`您修改`contentIntent`或`deleteIntent`中`Notification`物件。
-   您的應用程式通知，覆蓋`prepareInAppArea`，請務必對應至少`actionNotification`其中一個您 U.I 控制項。

> [AZURE.NOTE] 如果`handleNotification`擲回例外狀況，內容會刪除與`dropContent`稱為。 這報告中，可以立即處理下一個活動。

### <a name="announcements-and-polls"></a>宣告和輪詢的回覆

#### <a name="layouts"></a>版面配置

您可以修改`engagement_text_announcement.xml`，`engagement_web_announcement.xml`和`engagement_poll.xml`檔案以自訂文字的相關公告、 網頁的相關公告和輪詢的回覆。

這些檔案共用標題區域和按鈕區域的兩個常見的版面配置。 標題版面配置`engagement_content_title.xml`，且使用得名 drawable 檔案的背景。 動作] 和 [結束] 按鈕的版面配置是`engagement_button_bar.xml`，且使用得名 drawable 檔案的背景。

在投票問題版面配置和其選項會動態膨脹使用數次`engagement_question.xml`版面配置的問題的檔案和`engagement_choice.xml`檔案的選項。

#### <a name="categories"></a>類別

##### <a name="alternate-layouts"></a>替代的版面配置

通知，例如行銷活動的類別可以用於有替代的版面配置的相關公告和輪詢的回覆。

例如，若要建立文字公告類別，您可以延伸`EngagementTextAnnouncementActivity`並參考它`AndroidManifest.xml`檔案︰

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

請注意，目的在類別篩選用來進行含預設公告活動的差異。

連絡 SDK 使用意圖系統解決在特定類別的右活動，並會回頭預設分類如果失敗。

然後，您必須實作`MyCustomTextAnnouncementActivity`，如果您只想要變更版面配置 （但保留相同的檢視識別碼），您只需在下列範例中定義的類別，例如︰

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

若要取代文字的相關公告的預設分類，只需取代`android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"`您實作。

可以自訂網站的相關公告和輪詢的回覆，以類似的方式。

您可以擴充 web 宣告`EngagementWebAnnouncementActivity`並將您的活動`AndroidManifest.xml`像在下列範例中︰

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

您可以擴充的投票`EngagementPollActivity`宣告及您在`AndroidManifest.xml`像在下列範例中︰

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>實作中從頭開始

您可以公告 （和投票） 活動的實作類別，而不用延伸下列其中一項`Engagement*Activity`達到 SDK 所提供的類別。 這是很有用，例如您是否要定義為標準版面配置未使用相同的檢視的版面配置。

等進階的通知自訂建議您要的標準實作程式。

以下是要記住的一些事項︰ 達到便會啟動 （相對應意圖篩選） 特定用途的活動，再加上額外的參數是內容的識別碼。

若要擷取的內容的物件，其中包含您指定在網站上建立行銷活動的欄位您可以︰

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

統計資料，您應該報告的內容會顯示在`onResume`事件︰

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

然後，別忘了呼叫`actionContent(this)`或`exitContent(this)`內容的物件之前活動進入背景上。

如果您不可以呼叫`actionContent`或`exitContent`，統計資料不會傳送 （也就是在行銷活動沒有分析） 和更重要的是下一個活動不會通知必須重新啟動應用程式程序。

方向或變更其他設定可能會使程式碼難以判斷是否活動進入背景，標準實作可確保內容報告為退出當使用者活動 (藉由按`HOME`或`BACK`)，但不如果方向會變更。

以下是實作感興趣的一部分︰

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

您可以看到，如果您呼叫`actionContent(this)`完成活動，然後`exitContent(this)`可安全地呼叫而不需要任何效果。

[以下是]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google 雲端訊息]:http://developer.android.com/guide/google/gcm/index.html
[Amazon 裝置訊息]:https://developer.amazon.com/sdk/adm.html
