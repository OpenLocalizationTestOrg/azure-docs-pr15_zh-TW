<properties
    pageTitle="推播通知傳送至 Azure 通知集線器 Android |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Azure 通知集線器推入通知到 Android 的裝置。"
    services="notification-hubs"
    documentationCenter="android"
    keywords="推入通知，推入通知，android 推入通知"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/05/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>可在 Android 上使用 Azure 通知集線器傳送推入通知

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀

> [AZURE.IMPORTANT] 本主題示範推入通知使用 Google 雲端訊息 (GCM)。 如果您使用的 Google 的 Firebase 雲端訊息 (FCM)，請參閱[Azure 通知集線器和 FCM Android 傳送推入通知](notification-hubs-android-push-notification-google-fcm-get-started.md)。

本教學課程教您如何使用 Azure 通知集線器推入通知傳送至 Android 應用程式。
您會建立空白的 Android 應用程式會使用 Google 雲端訊息 (GCM) 接收推入通知。

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以從 GitHub 下載已完成的程式碼，在此教學課程[此](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted)。


##<a name="prerequisites"></a>必要條件

> [AZURE.IMPORTANT] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)。

除了前述作用中 Azure 帳戶，請在此教學課程，只需要[Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797)的最新版本。

完成此教學課程是 Android 應用程式的所有其他通知集線器教學課程的必要條件。

##<a name="creating-a-project-that-supports-google-cloud-messaging"></a>建立專案支援 Google 雲端訊息

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>設定新的通知中心


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6.在中**設定**刀中，選取 [**通知服務**]，然後按一下 [ **Google (GCM)**。 輸入 API 金鑰，然後按一下 [**儲存**]。

&emsp;&emsp;![Azure 通知集線器-Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

您的通知中心正在設定以使用 GCM，且您擁有以上兩者的連接字串註冊您的應用程式，來接收及傳送推入通知。

##<a id="connecting-app"></a>將您的應用程式連線到 [通知] 中心

###<a name="create-a-new-android-project"></a>建立新的 Android 專案

1. 在 Android Studio 中，開始新的 Android Studio 專案。

    ![Android Studio-新專案][13]

2. 選擇 [**手機和平板電腦**的表單係數和**最小值 SDK** ，您想要支援。 然後按一下 [**下一步**。

    ![Android Studio-專案建立工作流程][14]

3. 選擇主活動**空白的活動**，按 [**下一步**，，再按一下 [**完成]**。

###<a name="add-google-play-services-to-the-project"></a>新增 Google Play 服務至專案

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>新增 Azure 通知集線器文件庫


1. 在 [ `Build.Gradle` **應用程式**的檔案，請將下列幾行**相依性**] 區段中。

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. 新增下列存放庫後的**相依性**區段。

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>更新 AndroidManifest.xml。


1. 若要支援 GCM，必須在我們程式碼中用來[取得註冊權杖](https://developers.google.com/cloud-messaging/android/client#sample-register)使用[Google 的執行個體識別碼 API](https://developers.google.com/instance-id/)實作執行個體識別碼接聽服務。 在本教學課程中，我們會命名類別`MyInstanceIDService`。 
 
    檔案中加入下列服務定義 AndroidManifest.xml 內部`<application>`標籤。 取代`<your package>`版面配置區與您在頂端顯示的實際套件名稱`AndroidManifest.xml`檔案。

        <service android:name="<your package>.MyInstanceIDService" android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID"/>
            </intent-filter>
        </service>


2. 一旦我們收到我們 GCM 註冊憑證的執行個體識別碼 API，我們會註冊[Azure 通知中心](notification-hubs-push-notification-registration-management.md)中使用它。 我們會在背景使用支援此登錄`IntentService`名為`RegistrationIntentService`。 這項服務，也會負責[重新整理我們 GCM 註冊憑證](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)。
 
    檔案中加入下列服務定義 AndroidManifest.xml 內部`<application>`標籤。 取代`<your package>`版面配置區與您在頂端顯示的實際套件名稱`AndroidManifest.xml`檔案。 

        <service
            android:name="<your package>.RegistrationIntentService"
            android:exported="false">
        </service>



3. 我們也會定義要收到通知收件者。 檔案中加入下列收件者定義 AndroidManifest.xml 內部`<application>`標籤。 取代`<your package>`版面配置區與您在頂端顯示的實際套件名稱`AndroidManifest.xml`檔案。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. 新增下列必要 GCM 相關權限下`</application>`標籤。 請務必將`<your package>`套件名稱顯示在頂端`AndroidManifest.xml`檔案。

    如需有關這些權限的詳細資訊，請參閱[安裝 Android 適用的 GCM 用戶端應用程式](https://developers.google.com/cloud-messaging/android/client#manifest)。

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>


### <a name="adding-code"></a>新增程式碼


1. 在 [專案] 檢視中，展開 [**應用程式** > **src** > **主** > **java**。 以滑鼠右鍵按一下下**java**您套件的資料夾，按一下 [**新增**]，再按一下 [ **Java 類別**。 加入新的類別名稱為`NotificationSettings`。 

    ![Android Studio-新 Java 類別][6]

    請務必更新下列三個版面配置區中的下列程式碼`NotificationSettings`類別︰
    * **寄件者識別碼**︰ 您之前在[Google 雲端主控台](http://cloud.google.com/console)中取得專案數字。
    * **HubListenConnectionString**︰ 您中樞的**DefaultListenAccessSignature**連接字串。 您可以在上的 [**存取原則****設定**刀中心[Azure 入口網站]上的複製連線字串。
    * **HubName**︰ 使用的通知中心出現在中樞刀在[Azure 入口網站]中的名稱。

    `NotificationSettings`程式碼︰

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Your default listen connection string>";
        }

2. 使用上述步驟，新增另一個新的類別名稱為`MyInstanceIDService`。 這是我們的執行個體識別碼接聽程式服務實作。

    此類別的程式碼會呼叫我們`IntentService`背景中的 [[重新整理的 GCM token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) 。

        import android.content.Intent;
        import android.util.Log;
        import com.google.android.gms.iid.InstanceIDListenerService;
        
        
        public class MyInstanceIDService extends InstanceIDListenerService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.i(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. 將另一個新的類別新增至您的專案名稱， `RegistrationIntentService`。 這將成為的實作我們`IntentService`的會處理[重新整理 GCM 權杖](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)，以及[使用 [通知] 中心註冊](notification-hubs-push-notification-registration-management.md)。

    此課程，請使用下列程式碼。

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;
        
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.google.android.gms.iid.InstanceID;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {      
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
        
                try {
                    InstanceID instanceID = InstanceID.getInstance(this);
                    String token = instanceID.getToken(NotificationSettings.SenderId,
                            GoogleCloudMessaging.INSTANCE_ID_SCOPE);        
                    Log.i(TAG, "Got GCM Registration Token: " + token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if ((regID=sharedPreferences.getString("registrationID", null)) == null) {      
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.i(TAG, "Attempting to register with NH using token : " + token);

                        regID = hub.register(token).getRegistrationId();

                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                        resultString = "Registered Successfully - RegId : " + regID;
                        Log.i(TAG, resultString);       
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                    } else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete token refresh", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. 在您`MainActivity`課程，請新增下列`import`類別宣告上方的陳述式。

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. 在類別的頂端新增下列私人成員。 我們會使用下列[檢查 Google 播放服務 Google 根據建議](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)。

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private GoogleCloudMessaging gcm;
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. 在您`MainActivity`課程，將以下方法新增至 [Google 播放服務的可用性。 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. 在您`MainActivity`課程，請新增下列程式碼的 Google 播放服務檢查呼叫之前您`IntentService`取得 GCM 註冊權杖，並登錄通知中心。

        public void registerWithNotificationHubs()
        {
            Log.i(TAG, " Registering with Notification Hubs");
    
            if (checkPlayServices()) {
                // Start IntentService to register this application with GCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. 在 [`OnCreate`的方法`MainActivity`課程，請新增下列程式碼，建立活動時開始註冊程序。

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. 新增這些其他方法來`MainActivity`以驗證您的應用程式中的應用程式的狀態和報表狀態。

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. `ToastNotify`方法使用*「 Hello World 」* `TextView`控制項狀態報告，並持續的應用程式中的通知。 在您 activity_main.xml 版面配置]，新增該控制項的下列識別碼。

        android:id="@+id/text_hello"

11. 我們會將子類別加入我們在 AndroidManifest.xml 我們所定義的收件者的下一步。 名為您專案中新增另一個新的類別`MyHandler`。

12. 在頂端新增下列匯入陳述式`MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. 新增下列程式碼`MyHandler`類別，讓它的子類別`com.microsoft.windowsazure.notifications.NotificationsHandler`。

    將此程式碼會覆寫`OnReceive`方法，讓這個處理常式會報告都會收到的通知。 處理常式也會傳送推入通知到 Android 的通知管理員使用`sendNotification()`方法。 `sendNotification()`應該執行方法，當未執行應用程式，並收到通知。

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. 在 Android Studio 在功能表列中，按一下 [**建立** > **重建專案**，請確定沒有錯誤是出現在您的程式碼。

##<a name="sending-push-notifications"></a>傳送推入通知

您可以測試接收推播通知在您的應用程式中，傳送透過[Azure 入口網站]的外觀的 [**疑難排解**] 區段中中心刀中，如下所示。

![Azure 通知集線器-測試傳送](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>（選用）直接從應用程式傳送推入通知

一般而言，您會傳送通知使用的後端伺服器。 在某些情況下，您可能會想要可以直接從用戶端應用程式傳送推入通知。 本節說明如何從用戶端使用[Azure 通知中心 REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx)傳送通知。

1. 在 Android Studio 專案檢視] 中，展開 [**應用程式** > **src** > **主** > **解析度** > **版面配置**。 開啟`activity_main.xml`版面配置的檔案，然後按一下 [**文字**] 索引標籤，更新檔案的文字內容。 更新下列程式碼新增加`Button`和`EditText`控制項將推入通知訊息傳送至 [通知] 中心。 將此程式碼之前新增的底端， `</RelativeLayout>`。

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. 在 Android Studio 專案檢視] 中，展開 [**應用程式** > **src** > **主** > **解析度** > **值**。 開啟`strings.xml`檔案，並加入參照的新字串值`Button`和`EditText`控制項。 新增這些底部的檔案]，之前`</resources>`。

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. 在您`NotificationSetting.java`檔案中加入下列設定來`NotificationSettings`類別。

    更新`HubFullAccess` **DefaultFullSharedAccessSignature**連接字串的中心。 此連線字串複製從[Azure 入口網站]上的 [**存取原則****設定**刀的通知中心。

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. 在您`MainActivity.java`檔案中加入下列`import`陳述式上述`MainActivity`類別。

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. 在您`MainActivity.java`檔案，在頂端新增下列成員的`MainActivity`類別。  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. 您必須建立驗證傳送郵件給您的通知中心文章要求的軟體存取簽章 (Sa) 權杖。 這是由剖析從連接字串索引鍵的資料，並建立 Sa 權杖，[常見的概念](http://msdn.microsoft.com/library/azure/dn495627.aspx)REST API 參照所述。 下列程式碼是執行範例。

    在 [ `MainActivity.java`，新增下列方法以`MainActivity`類別，以分析您的連線字串。

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. 在 [ `MainActivity.java`，新增下列方法以`MainActivity`類別，以建立 Sa 驗證權杖。

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. 在 [ `MainActivity.java`，新增下列方法以`MainActivity`類別來處理**傳送通知**] 按鈕按一下和使用內建的 REST API 來傳送到中樞的推入通知訊息。

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>測試您的應用程式

####<a name="push-notifications-in-the-emulator"></a>推播通知在模擬器中

如果您想要測試模擬器內的推入通知，請確定您模擬器支援您選擇的應用程式的 Google API 層級。 如果您的圖像並不支援原生 Google Api，您會得到**服務\_不\_可用**例外狀況。

除了以上，請確定您已在 [**設定**底下執行模擬器新增您的 Google 帳戶 > **帳戶**。 您嘗試登錄 GCM 可能會導致否則**驗證\_失敗**例外狀況。

####<a name="running-the-application"></a>執行應用程式

1. 執行應用程式，並注意成功的登錄的報告註冊 ID。

    ![在 Android-通道註冊測試][18]

2. 輸入要傳送給所有的 Android 裝置註冊與中樞通知訊息。

    ![在 Android 上測試-傳送郵件][19]

3. 按下**傳送通知**。 有應用程式執行任何裝置將會顯示`AlertDialog`推入通知訊息的執行個體。 沒有執行應用程式，但已登錄推播通知將會收到通知 Android 通知管理員中。 可以檢視的左上角的向下撥動。

    ![在 Android-通知上測試][21]

##<a name="next-steps"></a>後續步驟

建議[使用通知集線器推入通知使用者]教學課程的下一個步驟。 隨即會顯示您如何針對特定的使用者使用標籤 ASP.NET 後端傳送通知。

如果您想要區段您感興趣的群組的使用者，請參閱[使用通知集線器傳送相關消息]教學課程。

若要瞭解通知集線器的其他一般資訊，請參閱我們[通知集線器指引]。

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[通知集線器指南]: http://msdn.microsoft.com/library/jj927170.aspx
[使用通知集線器給使用者的推播通知]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[使用通知集線器傳送相關消息]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure 入口網站]: https://portal.azure.com
