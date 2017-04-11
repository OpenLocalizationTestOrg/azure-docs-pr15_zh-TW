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


#<a name="upgrade-procedures"></a>升級程序

如果您已經有整合我們 SDK 的舊版應用程式，您必須升級 SDK 時，請考慮下列幾點。

您可能必須追蹤多個程序，如果您未接來電 SDK 的多個版本。 如果您將移轉 1.4.0 到 1.6.0 您必須先依照 「 從至 1.5.0 1.4.0 」 的程序然後 」 從至 1.6.0 1.5.0 」 程序的範例。

若要取代您有任何您從，升級的版本`mobile-engagement-VERSION.jar`以新。

##<a name="from-420-to-421"></a>從 4.2.0 至 4.2.1

實際 SDK 的任何版本上完成這個步驟，安全性改進時整合達到活動。

您現在應該新增`exported="false"`所有範圍的活動。

達到活動現在看起來應該像這樣您`AndroidManifest.xml`:

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

##<a name="from-400-to-410"></a>從 4.0.0 至 4.1.0

SDK 現在控點新權限模型從 Android M。

如果您使用的位置功能或一窺全貌通知，請閱讀[本節](mobile-engagement-android-integrate-engagement.md#android-m-permissions)。

除了新的權限模型中，我們現在支援在執行階段設定的位置功能。
我們仍相容於位置資訊清單的參數，但現在已被取代。 若要使用執行階段設定，移除 [從下列各節將``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

也可以閱讀改用執行階段設定[此更新程序](mobile-engagement-android-integrate-engagement.md#location-reporting)。

##<a name="from-300-to-400"></a>從 3.0.0 至 4.0.0

### <a name="native-push"></a>原生推入

原生推入 (GCM/ADM) 現在也適用於應用程式通知中，您必須設定任何類型的推入行銷活動的原生推入認證。

如果尚未完成請遵循[此程序](mobile-engagement-android-integrate-engagement-reach.md#native-push)。

### <a name="androidmanifestxml"></a>AndroidManifest.xml

中已經修改的範圍整合``AndroidManifest.xml``。

取代這種情況︰

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

依

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

有可能是載入畫面現在當您按一下通知 （含文字/web 內容) 或投票。
您必須新增此 4.0.0 使用這些行銷活動︰

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>資源

內嵌的新`res/layout/engagement_loading.xml`檔案至您的專案。

##<a name="from-240-to-300"></a>從 2.4.0 至 3.0.0

以下說明如何從將由 Azure 行動互動應用程式提供 Capptain SA Capptain 服務移轉 SDK 整合。 如果您從舊版升級，請參閱 [第一次移轉到 2.4.0 Capptain 網站，然後套用 [下列程序。

>[AZURE.IMPORTANT] Capptain 及 Mobile 使用不相同的服務，如下所示的程序只會醒目提示要移轉的用戶端應用程式的方式。 移轉應用程式中的 SDK 會不到行動互動伺服器的從 Capptain 伺服器移轉您的資料。

### <a name="jar-file"></a>JAR 檔案

取代`capptain.jar`以`mobile-engagement-VERSION.jar`中您`libs`資料夾。

### <a name="resource-files"></a>資源檔案

我們提供的每個資源檔案 (前面加上`capptain_`) 具有要取代的新的文件 (在前面加上`engagement_`)。

如果您自訂的檔案，您必須重新套用您在新的檔案，**在資源檔案中的所有識別項也已重新命名**的自訂。

### <a name="application-id"></a>應用程式識別碼

現在互動使用連線字串設定 SDK 識別碼，例如應用程式識別碼。

您必須使用`EngagementAgent.init`方法在您啟動程式的活動像這樣︰

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Azure 入口網站上，會顯示您的應用程式的連接字串。

請先移除任何呼叫`CapptainAgent.configure`為`EngagementAgent.init`取代的方法。

`appId`可以不再使用來設定`AndroidManifest.xml`。

請移除 [此節從您`AndroidManifest.xml`如果您已經︰

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java API

每個呼叫我們 SDK 的任何 Java 類別具有要重新命名。例如，`CapptainAgent.getInstance(this)`必須重新命名`EngagementAgent.getInstance(this)`，`extends CapptainActivity`必須重新命名`extends EngagementActivity`等...

如果您已整合預設代理程式的喜好設定檔案，預設檔案名稱現在`engagement.agent`及索引鍵是`engagement:agent`。

在建立網站的相關公告，Javascript 文件夾現在`engagementReachContent`。

### <a name="androidmanifestxml"></a>AndroidManifest.xml

變更許多事，不會再，共用服務且接收器許多無法匯出不再。

服務宣告現在簡單。移除意圖的篩選和，內的所有中繼資料，並新增`exportable=false`。

此外，若要使用的互動重新命名的所有項目。

現在看起來像︰

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

當您想要啟用測試記錄時，現在已移至 [應用程式] 標籤中繼資料，並已重新命名︰

            <application>
            
              <meta-data android:name="engagement:log:test" android:value="true" />
            
              <service/>
            
            </application>

所有其他中繼資料，只要已經重新命名，方法如下的完整清單 （課程重新命名您使用的項目）︰

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
            
            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play 和 SmartAd 追蹤已從 SDK，您只需要移除這不移除︰

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

達到活動現在宣告像這樣︰

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            
如果您有自訂範圍活動，您需要只變更意圖的動作，以符合 [`com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT`或`com.microsoft.azure.engagement.reach.intent.action.POLL`。

廣播的接收器已經重新命名，以及我們立即新增`exported=false`。 以下是新規格 （的課程重新命名您使用的項目） 接收者的完整清單︰

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>
            
            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>
            
            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>
            
            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

追蹤收件者已移除，讓您移除此區段︰

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

請注意，在已變更的廣播的收件者**EngagementMessageReceiver**實作的宣告`AndroidManifest.xml`。 這是因為傳送及移除任意 XMPP 實體任意 XMPP 郵件 API，並傳送及接收郵件裝置之間 API 已被移除。 因此，您必須也下列回撥刪除**EngagementMessageReceiver**實作︰

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

與

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

刪除任何通話的**EngagementAgent**上︰

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

與

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard

Proguard 設定會受到 rebranding，例如現在尋找規則︰

            -dontwarn android.**
            -keep class android.support.v4.** { *; }
            
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }
 
