<properties
    pageTitle="快速入門 Azure 通知集線器 Kindle 應用程式 |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Azure 通知集線器推入通知傳送至 Kindle 應用程式。"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>快速入門通知集線器 Kindle 應用程式

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀

本教學課程教您如何使用 Azure 通知集線器推入通知傳送至 Kindle 應用程式。
您會建立空白的 Kindle 應用程式會使用 Amazon 裝置訊息 (ADM) 接收推入通知。

##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ 從<a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android 網站</a>取得 Android SDK （我們假設您將使用蝕）。
+ 依照<a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">您的開發環境設定] 設定</a>為 Kindle 設定您的開發環境中的步驟。

##<a name="add-a-new-app-to-the-developer-portal"></a>將新的應用程式新增至開發人員入口網站

1. 在[Amazon 開發人員入口網站]，首先，建立應用程式。

    ![][0]

2. 複製該**應用程式鍵**。

    ![][1]

3. 在入口網站中，按一下您的應用程式的名稱，然後按一下 [**裝置訊息**] 索引標籤。

    ![][2]

4. 按一下 [**建立新的安全性設定檔**]，然後建立新的安全性設定檔 (例如， **TestAdm 安全性設定檔**)。 然後按一下 [**儲存**]。

    ![][3]

5. 按一下 [**安全性設定檔**若要檢視您剛剛建立的安全性設定檔]。 複製**用戶端識別碼**] 和 [**用戶端私人**的值，以供日後使用。

    ![][4]

## <a name="create-an-api-key"></a>建立 API 金鑰

1. 開啟命令提示字元具有管理員權限。
2. 瀏覽至 Android SDK 資料夾。
3. 輸入下列命令︰

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  **採用**密碼中，輸入**android**。

5.  複製**MD5**指紋的步驟。
6.  回開發人員入口網站，在 [**訊息**] 索引標籤上按一下**Android/Kindle**並輸入您的應用程式 (例如， **com.sample.notificationhubtest**) 及**MD5**值，套件的名稱，然後按一下**產生 API 金鑰**。

## <a name="add-credentials-to-the-hub"></a>新增認證集線器

在入口網站中，新增至您的通知中心的 [**設定**] 索引標籤的 [用戶端密碼] 和 [用戶端識別碼。

## <a name="set-up-your-application"></a>設定您的應用程式

> [AZURE.NOTE] 當您要建立應用程式時，使用 [至少 API 層級 17。

新增 ADM 文件庫至蝕專案︰

1. 若要取得 ADM 文件庫，[下載 SDK]。 展開的 SDK zip 檔案。
2. 在蝕，以滑鼠右鍵按一下您的專案，然後再按一下 [**內容**。 在左側，選取**Java 建立路徑**，然後選取 [頂端的 [**文件庫**] 索引標籤。 按一下 [**新增外部 Jar**，然後選取檔案`\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar`從您可以在其中解壓縮 Amazon SDK 的目錄。
3. 下載 NotificationHubs Android SDK （連結）。
4. 解壓縮套件，並將檔案`notification-hubs-sdk.jar`到`libs`蝕中的資料夾。

編輯您的應用程式資訊清單，以支援 ADM:

1. 新增 Amazon 命名空間根資訊清單項目︰


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. 新增資訊清單項目下的第一個項目權限。 取代**[您套件名稱]**使用您用來建立您的應用程式套件。

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. 為第一個項目的子項應用程式中插入下列項目。 請記住，來取代**[您的服務名稱]**的 ADM 郵件處理常式 （包括封裝） 下一節，您建立的名稱，以建立您的應用程式套件名稱取代**[您套件名稱]** 。

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>建立您的 ADM 郵件處理常式

1. 建立新的類別繼承自`com.amazon.device.messaging.ADMMessageHandlerBase`並將其命名`MyADMMessageHandler`，如下圖所示︰

    ![][6]

2. 新增下列`import`陳述式︰

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. 在您建立的類別中新增下列程式碼。 請記得要取代的中心名稱和連線字串 （聆聽）︰

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. 新增下列程式碼，`OnMessage()`方法︰

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. 新增下列程式碼，`OnRegistered`方法︰

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  新增下列程式碼，`OnUnregistered`方法︰

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. 在 [`MainActivity`方法，新增下列匯入陳述式︰

        import com.amazon.device.messaging.ADM;

8. 新增下列程式碼的結尾`OnCreate`方法︰

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>將您的 API 金鑰新增至您的應用程式

1. 在蝕，建立名為**api_key.txt**目錄資產的專案中的新檔案。
2. 開啟檔案，然後複製您產生 Amazon 開發人員入口網站中的 API 金鑰。

## <a name="run-the-app"></a>執行應用程式

1. 啟動模擬器。
2. 在模擬器中從頂端撥動並按一下 [**設定**]，然後按一下 [**我的帳號**並登錄有效 Amazon 帳戶。
3. 在 [蝕，執行應用程式。

> [AZURE.NOTE] 如果發生問題，請核取模擬器 （或裝置） 的時間。 時間值必須是正確的。 若要變更的 Kindle 模擬器時間，您可以從您的 Android SDK 平台工具目錄來執行下列命令︰

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>傳送郵件

若要使用.NET 傳送郵件︰

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon 開發人員入口網站]: https://developer.amazon.com/home.html
[下載 SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
