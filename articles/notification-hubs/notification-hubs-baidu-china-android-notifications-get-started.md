<properties
    pageTitle="開始使用 Azure 通知集線器使用 Baidu |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Android 裝置使用 Baidu 推入通知 Azure 通知集線器。"
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>使用 Baidu 通知集線器快速入門

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀

Baidu 雲端發送是中文雲端服務，您可以使用推入通知傳送到行動裝置。 這項服務是 china （中國），可在 Android 上發表推播通知在哪裡複雜因為不同的應用程式存放區 」 及 「 推入服務，除了通常一定要連線到 GCM （Google 雲端訊息） 的 Android 裝置可使用目前狀態特別有用。

##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ Android （我們假設您將使用蝕） sdk，您可以從<a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android 的網站</a>，您可以下載的
+ [行動服務 Android SDK]
+ [Baidu 推入 Android SDK]

>[AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F)。


##<a name="create-a-baidu-account"></a>建立 Baidu 帳戶

若要使用 Baidu，您必須 Baidu 帳戶。 如果您已經有一個，請登入[Baidu 入口網站]，並直接跳到下一個步驟。 如何建立 Baidu 帳戶，否則請參閱下方的指示進行。  

1. 移至 [ [Baidu 入口網站]，然後按一下**登录**(**登入**) 連結。 按一下 [**立即注册**開始帳戶註冊程序。

    ![][1]

2. 輸入所需的詳細資料，電話/電子郵件地址、 密碼和驗證的程式碼，然後按一下 [**註冊**。

    ![][2]

3. 您將會將電子郵件傳送您輸入的連結，啟動 Baidu 帳戶的電子郵件地址。

    ![][3]

4. 登入您的電子郵件帳戶開啟 Baidu 啟動郵件]，然後按一下 [啟動] 連結，啟動您 Baidu 帳戶。

    ![][4]

啟動的 Baidu 帳戶之後，登入[Baidu 入口網站]。

##<a name="register-as-a-baidu-developer"></a>註冊為 Baidu 開發人員

1. 一旦您已登入[Baidu 入口網站]，按一下 [**更多 >>** （**更多**）。

    ![][5]

2. 在**站长与开发者服务 （網站管理員和開發人員服務）**區段中，向下捲動，然後按一下 [**百度开放云平台**（**Baidu 開啟雲端平台**）。

    ![][6]

3. 在下一個頁面上，按一下 [**开发者服务**(**開發人員服務**) 在右上角。

    ![][7]

4. 在下一個頁面上，按一下 [從右上角的功能表**注册开发者**（**註冊的開發人員**）。

    ![][8]

5. 收到的驗證文字訊息中，輸入您的名稱、 描述和行動電話號碼，然後按一下 [**送验证码**（**傳送驗證碼**）。 請注意，取得國際電話號碼，您需要以括弧括住國家/地區碼。 例如，美國數字，它位於**(1) 1234567890**。

    ![][9]

6. 然後您應該與驗證數字，接收文字訊息，如下列範例所示︰

    ![][10]

7. 從**验证码**（**確認程式碼**）] 中的訊息輸入驗證碼。

8. 最後，完成開發人員註冊接受 Baidu 合約，按一下 [**提交**（**提交**）。 註冊順利完成時，您會看到下列頁面︰

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>建立 Baidu 雲端推入專案

當您建立 Baidu 雲端發送專案時，您會收到您的應用程式識別碼 API 金鑰]，然後金鑰。

1. 一旦您已登入[Baidu 入口網站]，按一下 [**更多 >>** （**更多**）。

    ![][5]

2. 在**站长与开发者服务**（**網站管理員及開發人員服務**） 區段中，向下捲動，然後按一下 [**百度开放云平台**（**Baidu 開啟雲端平台**）。

    ![][6]

3. 在下一個頁面上，按一下 [**开发者服务**(**開發人員服務**) 在右上角。

    ![][7]

4. 在下一個頁面上，按一下 [**云推送**(**雲端推入**) 從**云服务**(**雲端服務**) 區段。

    ![][12]

5. 當您註冊開發人員，您會看到**管理控制台**（**管理主控台**） 在頂端的功能表。 按一下 [**开发者服务管理**（**開發人員服務管理**）。

    ![][13]

6. 在下一個頁面上，按一下 [**创建工程**（**建立專案**）。

    ![][14]

7. 輸入應用程式名稱，然後按一下 [**创建**（**建立**）。

    ![][15]

8. 在 Baidu 雲端推入專案的成功建立，您會看到**AppID**、 **API 金鑰**與**密碼金鑰**頁面。 請記下的 API 金鑰及更新版本，我們將使用中的密碼金鑰。

    ![][16]

9. 設定推入通知的專案，即可**云推送**（**雲端推入**） 在左窗格。

    ![][31]

10. 在下一個頁面上，按一下 [**推送设置**(**推入設定**)] 按鈕。

    ![][32]  

11. 在 [設定] 頁面上新增 [套件名稱，您的會使用 Android**应用包名**（**應用程式套件**）] 欄位中，在專案中，然後按一下 [**保存设置**（**儲存**）。  

    ![][33]

您會看到**保存成功 ！** (**已成功儲存 ！**) 訊息。

##<a name="configure-your-notification-hub"></a>設定您的通知中心

1. [Azure 傳統入口網站]中，登入，然後按一下 [ **+ 新增**在畫面底部。

2. 按一下 [**應用程式服務**按一下**服務匯流排**、 按一下 [**通知] 中心內**，，再按一下 [**快速建立**。

3. 提供您的**通知中心**的名稱，請選取 [**地區**和**命名空間**此通知中心建立的位置，然後按一下 [**建立新的通知中心**。  

    ![][17]

4. 按一下您要在其中建立您的通知中心內，命名空間，然後按一下**通知集線器**頂端。

    ![][18]

5. 選取 [通知] 中心內的您所建立的然後從上方的功能表，再按一下 [**設定**]。

    ![][19]

6. 捲動到 [ **baidu 通知設定**] 區段，然後輸入 API 金鑰與您先前取得從 Baidu 主控台 Baidu 雲端推入專案的金鑰。 按一下 [**儲存**]。

    ![][20]

7. 按一下頂端的 [通知] 中心的 [**儀表板**] 索引標籤，然後按一下 [**檢視連接字串**。

    ![][21]

8. 記下**DefaultListenSharedAccessSignature**和**DefaultFullSharedAccessSignature**從**Access 連線資訊**] 視窗。

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連線到 [通知] 中心

1. 在蝕 ADT，建立一個新的 Android 專案 (**檔案** > **新增** > **Android 應用程式專案**)。

    ![][23]

2. 輸入 [**應用程式名稱**，並確保的**最小值所需 SDK**版本設定**API 16: Android 4.1**。

    ![][24]

3. 按一下 [**下一步**，繼續精靈的指示進行，直到**建立活動**視窗隨即出現。 請確認已選取 [**空白的活動**，及最後，請選取 [**完成**] 以建立新的 Android 應用程式。

    ![][25]

4. 請確定已正確設定**專案建立目標**。

    ![][26]

5. 下載通知-集線器-0.4.jar 檔案的[通知-集線器-Android-SDK Bintray 上](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4)的 [**檔案**] 索引標籤。 將檔案新增至 [**程式庫**] 資料夾的蝕專案，並重新整理 [*程式庫*] 資料夾。

6. 下載並解壓縮[Baidu 推播 Android SDK]，開啟 [**程式庫**] 資料夾中，然後複製**pushservice x.y.z** jar 檔案與**armeabi** & **mips** Android 應用程式的 [**程式庫**] 資料夾中的資料夾。

7. 開啟您的 Android 專案**AndroidManifest.xml**檔案，並新增所需的 Baidu SDK 的權限。

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. 新增**android: name**屬性時，您**的應用程式**中的項目**AndroidManifest.xml**，取代*yourprojectname* (例如**com.example.BaiduTest**)。 請確定此專案名稱符合您在 Baidu 主控台中設定的。

        <application android:name="yourprojectname.DemoApplication"

9. 新增的應用程式項目內之後**下列設定。MainActivity**活動項目，取代*yourprojectname* (例如**com.example.BaiduTest**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. 新增到專案中稱為**ConfigurationSettings.java**的新類別。

    ![][28]

    ![][29]

10. 將下列程式碼新增至其︰

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    設定**API_KEY**使用您從 Baidu 雲端專案之前，使用您從 Azure 傳統入口網站的通知中心名稱**NotificationHubName**和**NotificationHubConnectionString**與 DefaultListenSharedAccessSignature 從 Azure 傳統入口網站中所擷取的值。

11. 加入新的類別，稱為**DemoApplication.java**，並為其新增下列程式碼︰

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. 新增另一個新的類別，稱為**MyPushMessageReceiver.java**，並為其新增下列程式碼。 這是處理從 Baidu 推入伺服器接收推入通知的類別。

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. 開啟**MainActivity.java**，並新增下列**onCreate**方法︰

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. 開啟下列匯入陳述式頂端︰

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>傳送通知給您的應用程式


您可以快速測試您的應用程式中收到通知，藉由以下畫面所示，傳送通知在[Azure 入口網站](https://portal.azure.com/)上 [通知] 中心內，使用**測試傳送**] 按鈕。

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

推播通知會以正常方式傳送後端服務，例如行動電話服務或 ASP.NET 使用相容的文件庫中。 您也可以使用 REST API 直接移到文件庫不適用於您的後端傳送通知訊息。

在本教學課程中，我們會保持簡單，並只將示範測試您的用戶端應用程式傳送通知集線器主控台應用程式，而不是在後端服務的使用.NET SDK 的通知。 建議[使用通知集線器推入通知使用者](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)教學課程為從 ASP.NET 後端傳送通知的下一個步驟。 不過，可以使用下列方法，傳送通知︰

* **其餘介面**︰ 您可以使用[其餘介面](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)任何後端平台支援通知。

* **Microsoft Azure 通知集線器.NET SDK**: Nuget 套件管理員中的 Visual Studio 中，執行[安裝套件 Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

* **Node.js** ︰[如何使用從 Node.js 通知集線器](notification-hubs-nodejs-push-notification-tutorial.md)。

* **行動應用程式**︰ 如需如何從通知集線器整合 Azure 應用程式服務行動應用程式後端傳送通知的範例，請參閱[新增至您的行動應用程式的推播通知](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)。

* <b0>Java / PHP</b0>︰ 如需如何使用 REST Api 來傳送通知的範例，請參閱 < 如何使用從 Java/PHP 通知集線器 」 (<b1>Java</b1><b2></b2><b3>PHP</b3>)。

##<a name="optional-send-notifications-from-a-net-console-app"></a>（選用）從.NET 主控台應用程式會傳送通知。

在此區段中，我們會示範傳送通知使用.NET 主控台應用程式。

1. 建立新的 Visual C# 主控台應用︰

    ![][30]

2. 在 [封裝管理員主控台] 視窗中，設定**預設專案**至新主控台應用程式專案，然後在 [主控台] 視窗中，執行下列命令︰

        Install-Package Microsoft.Azure.NotificationHubs

    如此會將 Azure 通知集線器 SDK 使用<a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 集線器 NuGet 套件</a>的參考。

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. 開啟檔案**Program.cs** ，並新增下列使用陳述式︰

        using Microsoft.Azure.NotificationHubs;

4. 在您`Program`類別、 加入下列方法並使用您的值取代*DefaultFullSharedAccessSignatureSASConnectionString*及*NotificationHubName* 。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. 在您的**主要**方法中加入下列行︰

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>測試您的應用程式

若要測試此應用程式，與實際的電話，只要將電話連接到您的電腦使用 USB 纜線。 這會載入到附加 phone 應用程式。

要測試此應用程式與模擬器，蝕上方工具列上，按一下 [**執行**]，然後選取您的應用程式。 這會啟動模擬器，然後載入並執行應用程式。

應用程式從 Baidu 推入通知服務中擷取 '使用者識別碼 」 和 「 channelId 」，並登錄的通知中心。

若要傳送測試通知中，您可以使用 Azure 傳統入口網站中的 [偵錯] 索引標籤。 如果您內建的 Visual Studio.NET 主控台應用程式，只要按 F5 鍵在 Visual Studio 中執行應用程式。 應用程式會傳送通知，會出現在您的裝置或模擬器的上方的通知區域。


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[行動服務 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu 推入 Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[Baidu 入口網站]: http://www.baidu.com/
