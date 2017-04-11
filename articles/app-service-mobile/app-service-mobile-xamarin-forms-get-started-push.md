<properties
    pageTitle="推播通知加入 Xamarin.Forms 應用程式 |Microsoft Azure"
    description="瞭解如何使用 Azure 服務傳送多平台推入通知給您 Xamarin.Forms 應用程式。"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>新增至您 Xamarin.Forms 應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>概觀

在本教學課程中，您可以新增推入通知的所有專案導因於[Xamarin.Forms 快速開始](app-service-mobile-xamarin-forms-get-started.md)，讓每次即會插入一筆記錄，將會傳送至所有跨平台用戶端的推入通知。

如果您不使用下載快速入門伺服器專案，您需要推播通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="prerequisites"></a>必要條件

* 針對 iOS，您將需要[Apple 開發人員程式成員資格](https://developer.apple.com/programs/ios/)的實體 iOS 裝置和因為[iOS 模擬器不支援推播通知](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。

##<a name="configure-hub"></a>設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>更新伺服器專案可傳送推入通知

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>（選用）設定並執行 Android 的專案

完成這個區段以啟用 Xamarin.Forms Droid 專案的 Android 版的推播通知。


###<a name="enable-firebase-cloud-messaging-fcm"></a>啟用 Firebase 雲端訊息 (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>設定行動應用程式後端傳送使用 FCM 推入要求

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>新增至 Android 專案的推播通知

使用以 FCM 設定後端，我們可以加入元件和註冊 FCM，用戶端註冊 Azure 通知集線器與透過行動應用程式後端的推入通知，並會收到通知。

1. 在**Droid**專案中，以滑鼠右鍵按一下 [**元件**] 資料夾**取得更多元件...**、 **Google 雲端訊息用戶端**元件的搜尋，然後按一下將其新增至專案。 此元件 Xamarin Android 專案支援推播通知。


2. 開啟 MainActivity.cs 專案檔案，並新增下列使用在檔案頂端的陳述式︰

        using Gcm.Client;

3. 新增下列程式碼**OnCreate**方法， **LoadApplication**呼叫之後︰

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. 加入新的**CreateAndShowDialog**協助程式方法，如下所示︰

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. 將下列程式碼新增至**MainActivity**類別︰

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    此公開目前**MainActivity**執行個體，因此我們可以在主要使用者介面執行緒上執行。

6. 初始化`instance`、 變數開頭**OnCreate**的方法，如下所示。

        // Set the current instance of MainActivity.
        instance = this;

2. 名為**Droid**專案中新增新的類別檔案`GcmService.cs`，並且確定下列**使用**陳述式簡報頂端的檔案︰

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. **使用**陳述式之後，以及**命名空間**宣告之前，請按一下頂端的檔案]，新增下列權限要求。

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. 新增下列類別定義為命名空間。 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]**< PROJECT_NUMBER >**換成您先前所述的專案數字。   

11. 空白的**GcmService**類別取代下列程式碼，使用新的廣播收件者︰

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. 新增下列程式碼會覆寫**OnRegistered**事件處理常式及實作**註冊**方法的**GcmService**類別。

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. 新增實作**OnMessage**的下列程式碼︰ 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    此處理內送的通知，並傳送給通知經理，以顯示。

14. **GcmServiceBase**也會要求您實作**OnUnRegistered**和**OnError**處理常式方法可執行，如下所示︰

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

現在，您就準備好測試推入通知，在應用程式在 Android 裝置或模擬器上執行。

###<a name="test-push-notifications-in-your-android-app"></a>Android 應用程式中的測試推入通知

測試模擬器上時，才，所需的前兩個步驟。

1. 請確定您是部署或偵錯時，如下所示，在 Android 虛擬裝置 (AVD) 管理員已設定為目標，Google Api 虛擬裝置上。

2. 按一下 [**應用程式**，將 Google 帳戶新增至 Android 裝置 > **設定** > **新增帳戶**]，然後遵循提示，使用 [新增現有的 Google 帳戶至裝置，建立新的範本。

1. 在 Visual Studio 或 Xamarin Studio 中，以滑鼠右鍵按一下**Droid**專案，然後按一下 [**設定為啟動專案**。

2. 按下建立專案並啟動應用程式在 Android 裝置或模擬器上的 [**執行**] 按鈕。

3. 在應用程式中，輸入任務，然後按一下加號 (**+**) 圖示。

4. 確認 [新增項目時收到的通知]。


##<a name="optional-configure-and-run-the-ios-project"></a>（選用）設定並執行 iOS 的專案

本節適用於執行 iOS 裝置的 Xamarin iOS 專案。 如果您不使用 iOS 裝置，您可以略過此節。

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>設定 APN 通知中心

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

接下來，您將 Xamarin Studio 或 Visual Studio 中設定 iOS 專案設定。

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>新增您的 iOS 應用程式的推播通知

1. 在**iOS**專案中，開啟 [AppDelegate.cs 的程式碼檔案頂端新增下列**使用**陳述式。

        using Newtonsoft.Json.Linq;

4. 在**AppDelegate**類別中，新增 [覆寫**RegisteredForRemoteNotifications**事件註冊通知︰

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. 在**AppDelegate**，也加入**DidReceivedRemoteNotification**事件處理常式的下列覆寫︰

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    應用程式正在執行時，這種方式處理內送的通知。

2. 在**AppDelegate**類別中，新增下列程式碼**FinishedLaunching**方法︰ 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    這可讓支援遠端通知，並要求推播註冊。

支援推入通知，現在已經更新您的應用程式。

####<a name="test-push-notifications-in-your-ios-app"></a>在 iOS 應用程式中的測試推入通知

1. 以滑鼠右鍵按一下 iOS 專案，然後按一下 [**設定為 StartPp 專案**。

2. 在 Visual Studio 中建立專案並啟動應用程式在 iOS 裝置，請按**[執行**] 按鈕或**F5** ，然後按一下**[確定**] 接受推入通知]。

    > [AZURE.NOTE] 您必須明確接受推入通知，從您的應用程式。 這項要求才會發生的應用程式執行第一次。

3. 在應用程式中，輸入任務，然後按一下加號 (**+**) 圖示。

4. 請確認會收到通知，然後按一下**[確定**] 關閉通知。


##<a name="optional-configure-and-run-the-windows-projects"></a>（選用）設定和執行 Windows 專案

本節適用於執行 Xamarin.Forms WinApp 和適用於 Windows 裝置 WinPhone81 專案。 這些步驟也支援通用 Windows 平台 (UWP) 專案。 如果您不使用 Windows 裝置，您可以略過此節。


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>以 WNS 註冊您的 Windows 應用程式推播通知

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>針對 WNS 設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>新增至您的 Windows 應用程式的推播通知

1. 在 Visual Studio 中，開啟 [Windows 專案中的 [ **App.xaml.cs**並新增下列**使用**陳述式。

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    取代`<your_TodoItemManager_portable_class_namespace>`與可攜式專案包含的命名空間`TodoItemManager`類別。
 

2. 在 App.xaml.cs 中加入下列**InitNotificationsAsync**方法︰ 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    這種方式取得推入通知通道，並登錄從您的通知中心接收範本通知範本。 支援*messageParam*範本通知會傳遞給此用戶端。

3. 在 App.xaml.cs，藉由新增更新**OnLaunched**事件處理常式方法定義`async`輔助按鍵，然後將下列程式碼新增結尾的方法︰ 

        await InitNotificationsAsync();

    如此可確保推入通知註冊建立，或重新整理每次啟動應用程式。 請務必執行此保證 WNS 推入頻道一律是作用中。  

4. Visual studio 方案總管] 中開啟**Package.appxmanifest**檔案並將**簡訊快顯**設定為 [ **** ]**通知**。

5. 建立應用程式，並確認您有沒有錯誤。  您用戶端應用程式現在應該註冊 Mobile 應用程式後端範本通知。 在您的方案中每個 Windows 專案重複此區段。


####<a name="test-push-notifications-in-your-windows-app"></a>測試您的 Windows 應用程式的推播通知

1. 在 Visual Studio 中，以滑鼠右鍵按一下 Windows 專案，然後按一下 [**設定為啟動專案**。

2. 按下建立專案並啟動應用程式的 [**執行**] 按鈕。

3. 在應用程式中，輸入新的 todoitem 的名稱，然後按一下加號 (**+**) 圖示，將它加入。

4. 確認 [新增項目時收到的通知]。

##<a name="next-steps"></a>後續步驟

深入瞭解推入通知︰

* [診斷推入通知問題](../notification-hubs/notification-hubs-push-notification-fixer.md)  
有通知可能卸除或裝置上不會出現各種不同的原因。 本主題說明如何分析及找出推入通知失敗的原因。 

請考慮繼續執行下列教學課程其中一項︰

* [將驗證新增至您的應用程式](app-service-mobile-xamarin-forms-get-started-users.md)  
瞭解如何將項目會驗證您的應用程式與身分識別提供者的使用者。

* [啟用離線同步處理應用程式](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  瞭解如何新增離線支援您使用行動應用程式後端的應用程式。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

