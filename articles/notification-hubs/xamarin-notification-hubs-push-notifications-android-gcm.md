<properties
    pageTitle="快速入門通知集線器 Xamarin.Android 應用程式 |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Azure 通知集線器推入通知傳送至 Xamarin Android 應用程式。"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>快速入門通知集線器 Xamarin 與 Android 版

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀

本教學課程教您如何使用 Azure 通知集線器推入通知傳送至 Xamarin.Android 應用程式。
您會建立空白的 Xamarin.Android 應用程式會使用 Google 雲端訊息 (GCM) 接收推入通知。 完成後，您可以使用您的通知中心廣播執行您的應用程式的所有裝置的推播通知。 完成的程式碼是[NotificationHubs 應用程式]中可用[GitHub]範例。

本教學課程說明在使用通知集線器簡單的廣播的案例。


## <a name="before-you-begin"></a>開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

您可以在 GitHub 找到已完成的程式碼，在此教學課程[此](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid)。



##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ Visual Studio 與 Xamarin 在 Windows 或 Mac OS X 完成的安裝指示上 Xamarin Studio 位於[設定與安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。
+ 作用中的 Google 帳戶
+ [Azure 訊息元件]
+ [Google 雲端訊息的用戶端元件]

完成此教學課程是所有其他通知集線器教學課程 Xamarin.Android 應用程式的必要條件。

> [AZURE.IMPORTANT] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)。

##<a name="enable-google-cloud-messaging"></a>啟用 Google 雲端訊息

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>設定您的通知中心

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>按一下頂端的 [<b>設定</b>] 索引標籤，輸入您在前一節中，取得<b>API 金鑰</b>值，再按一下 [<b>儲存</b>。</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


您的通知中心正在設定以使用 GCM，且您擁有以上兩者的連接字串註冊您的應用程式會收到通知，並傳送推入通知。

##<a name="connect-your-app-to-the-notification-hub"></a>將您的應用程式連線到 [通知] 中心

###<a name="create-a-new-project"></a>建立新專案

1. 在 Xamarin Studio 中，按一下**新的方案** **Android 應用程式**，然後按一下**下一步**。

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. 輸入您的**應用程式的名稱**及**識別碼**。 按一下您想要支援，然後按一下 [**下一步**] 和 [ **Create****目標 Plaforms** 。

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    這樣會建立新的 Android 專案。

2. 開啟專案的屬性，只要以滑鼠右鍵按一下 [解決方案] 檢視中的進行新專案，並選擇**選項**。 在 [**建立**] 區段中，選取**Android 應用程式**項目。

    請確定您的**套件名稱**的第一個字母小寫。

    > [AZURE.IMPORTANT] 第一個字母的套件名稱必須小寫。 否則，您會收到應用程式顯示錯誤，當您註冊您的**BroadcastReceiver**和**IntentFilter**推入通知] 下方。

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. 您也可以將**最小值 Android 版**至另一個的 API 層級。

4. （選擇性） 到您要將另一個 API 版本設定**目標 Android 版本**（必須是 API 層級 8 或更新版本）。

按一下**[確定]** ，然後關閉 [專案選項] 對話方塊。


###<a name="add-the-required-components-to-your-project"></a>新增所需的元件至專案

Google 雲端訊息上的用戶端可以使用 Xamarin 元件存放區簡化 Xamarin.Android 支援推入通知程序。

1. 以滑鼠右鍵按一下 Xamarin.Android 應用程式中的 [元件] 資料夾，然後選擇 [**更多取得元件**。

2. 搜尋**Azure 訊息**元件，並將其新增至專案。

3. 搜尋**Google 雲端訊息用戶端**元件，並將其新增至專案。


###<a name="set-up-notification-hubs-in-your-project"></a>設定您專案中的通知集線器

1. 收集 Android 應用程式和通知中心的下列資訊︰

    - **GoogleProjectNumber**︰ 從您的 Google 開發人員入口網站上的應用程式概觀取得此專案的數字的值。 您在入口網站上建立應用程式時，您所做此值較舊版本的記事。
    - **聆聽連線字串**︰ 在[Azure 傳統入口網站]中的儀表板，按一下 [**檢視連接字串**。 複製這個值*DefaultListenSharedAccessSignature*連線字串。
    - **中心名稱**︰ 這是您從[Azure 傳統入口網站]中樞的名稱。 例如， *mynotificationhub2*。

    建立 Xamarin 專案**Constants.cs**類別，類別中定義的下列常數值。 以您的值取代預留位置。

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. 新增下列使用陳述式以**MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. 新增至執行個體變數`MainActivity`將用來執行應用程式時，顯示通知] 對話方塊的類別︰

        public static MainActivity instance;


3. **MainActivity**類別中建立下列方法︰

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. 在 [`OnCreate`的**MainActivity.cs**，方法初始化`instance`變數和加入通話`RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. 建立新的類別， **MyBroadcastReceiver**。

    > [AZURE.NOTE] 我們會逐步從下列從頭開始建立**BroadcastReceiver**課程。 不過，以手動方式建立**MyBroadcastReceiver.cs**快速替代方案是參照到[NotificationHubs 範例]隨附的範例 Xamarin.Android 專案中找到的**GcmService.cs**檔案[GitHub]。 複製**GcmService.cs**和變更類別名稱可以以及開始的絕佳位置。

5. 新增下列使用陳述式以**MyBroadcastReceiver.cs** （參照的元件和您先前新增的組件）︰

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. 在**MyBroadcastReceiver.cs**，新增下列權限要求的**使用**陳述式之間的**命名空間**宣告︰

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. 在**MyBroadcastReceiver.cs**，變更**MyBroadcastReceiver**類別，以符合下列動作︰

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. 名為**PushHandlerService**，衍生自**GcmServiceBase** **MyBroadcastReceiver.cs**中新增另一個類別。 請確認**服務**屬性套用至類別︰

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase**實作**OnRegistered()**、 **OnUnRegistered()**、 **OnMessage()**、 **OnRecoverableError()**及**OnError()**的方法。 **PushHandlerService**實作類別必須覆寫這些方法，而這些方法會引發互動通知中心來回應。


9. 使用下列程式碼，覆寫**PushHandlerService**的**OnRegistered()**方法︰

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] 在上述**OnRegistered()**代碼中，您應該注意指定標籤以註冊特定訊息的頻道。

10. 使用下列程式碼，覆寫**PushHandlerService**的**OnMessage**方法︰

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. 新增至**PushHandlerService**下列**createNotification**和**dialogNotify**方法通知使用者時收到通知。

    >[AZURE.NOTE] 在 Android 版 5.0 及更新版本中的通知設計代表嚴重的出發從先前的版本。 如果您測試 Android 5.0 或更新版本，應用程式必須執行收到通知。 如需詳細資訊，請參閱[Android 通知](http://go.microsoft.com/fwlink/?LinkId=615880)。

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. 覆寫抽象成員**OnUnRegistered()** **OnRecoverableError()**，與**OnError()** ，以便編譯程式碼︰

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>在模擬器中執行您的應用程式

如果您在模擬器中執行此應用程式，請確定您使用 Android 虛擬裝置 (AVD) 支援 Google Api 的。

> [AZURE.IMPORTANT] 若要接收推入通知，您必須設定 Google 帳戶虛擬 Android 裝置上。 （在模擬器中瀏覽至 [**設定]**然後按一下 [**新增帳戶**）。此外，請確定模擬器已連線至網際網路。

>[AZURE.NOTE] 在 Android 版 5.0 及更新版本中的通知設計代表嚴重的出發從先前的版本。 如需詳細資訊，請參閱[Android 通知](http://go.microsoft.com/fwlink/?LinkId=615880)。


1. 從 [**工具**] 中，按一下 [**開啟 Android 模擬器管理員**、 選取您的裝置，，然後按一下 [**編輯**。

    ![][18]

2. 選取**Google Api** **目標**，然後按一下**[確定]**。

    ![][19]

3. 在上方工具列中，按一下 [**執行**]，然後選取您的應用程式。 這會啟動模擬器，並執行應用程式。

  應用程式從 GCM 擷取*registrationId* ，並登錄的通知中心。

##<a name="send-notifications-from-your-backend"></a>從您的後端傳送通知


您可以測試收到通知您的應用程式中，在 [偵錯] 索引標籤上 [通知] 中心內，透過[Azure 傳統入口網站]，傳送通知以下畫面所示。

![][30]


推播通知會以正常方式傳送後端服務例如行動電話服務或 ASP.NET 透過相容的文件庫中。 您也可以使用 REST API 直接移到文件庫不適用於您的後端傳送通知訊息。

以下是您可能會想要檢閱傳送通知的一些其他教學課程的清單︰

- ASP.NET︰ 請參閱[使用通知集線器推入通知給使用者]。
- Azure 通知集線器 Java SDK︰ 請參閱[如何使用從 Java 通知集線器](notification-hubs-java-push-notification-tutorial.md)從 Java 傳送通知。 此已經過測試中蝕 Android 開發。
- PHP︰ 請參閱[如何使用從 PHP 通知集線器](notification-hubs-php-push-notification-tutorial.md)。


教學課程的下一個小節，您會傳送通知，使用.NET 主控台應用程式，並使用透過節點指令碼行動訊息服務。

####<a name="optional-send-notifications-by-using-a-net-app"></a>（選用）使用.NET 的應用程式傳送通知

在此區段中，我們會傳送通知給使用.NET 主控台應用程式

1. 建立新的 Visual C# 主控台應用︰

    ![][20]

2. 在 Visual Studio 中，按一下 [**工具]**、 按一下**NuGet 封裝管理員**]，然後按一下**封裝管理員主控台**。

    隨後便會顯示在 Visual Studio 中封裝管理員主控台

3. 在 [封裝管理員主控台] 視窗中，設定**預設專案**至新主控台應用程式專案，然後在 [主控台] 視窗中，執行下列命令︰

        Install-Package Microsoft.Azure.NotificationHubs

    如此會將 Azure 通知集線器 SDK 使用<a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 集線器 NuGet 套件</a>的參考。

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. 開啟 Program.cs 檔案，並將以下`using`陳述式︰

        using Microsoft.Azure.NotificationHubs;

5. 在您`Program`課程，請新增下列方法。 更新您*DefaultFullSharedAccessSignature*連線字串，以及中樞的名稱從[Azure 傳統入口網站]的版面配置區文字。

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. 在您的**主要**方法中加入下列行︰

         SendNotificationAsync();
         Console.ReadLine();

7. 按 F5 鍵以執行應用程式。 應用程式中，您應該會收到通知。

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>（選用）使用行動訊息服務傳送通知

1. 請遵循[開始使用行動電話服務]。

1. 登入至[Azure 傳統入口網站]，然後選取您的行動訊息服務。

2. 選取頂端的 [**排程**] 索引標籤。

    ![][22]

3. 建立新的排程的工作，請插入名稱，然後選取**On demand**。

    ![][23]

4. 建立工作時，按一下 [工作名稱]。 然後按一下 [在頂端列的 [**指令碼**] 索引標籤。

5. 插入您的排程器函數下列指令碼。 請務必為*DefaultFullSharedAccessSignature*取得較舊版本的取代您的通知中心名稱與連線字串的版面配置區。 按一下 [**儲存**]。

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. 按一下**執行一次**下方列]。 您應該會收到快顯通知。

##<a name="next-steps"></a>後續步驟

在此範例中，您通知傳送到該網域您所有的 Android 裝置。 若要針對特定的使用者，請參閱教學課程[使用通知集線器推入通知給使用者]。 如果您想要區段您感興趣的群組的使用者，您可以瞭解[使用通知集線器，傳送新消息]。 進一步瞭解如何使用通知集線器[通知集線器]指南和[通知集線器使用方法 Android 版]。

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動電話服務]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure 傳統入口網站]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[通知集線器指南]: http://msdn.microsoft.com/library/jj927170.aspx
[Android 版通知集線器使用方法]: http://msdn.microsoft.com/library/dn282661.aspx

[使用通知集線器給使用者的推播通知]: /manage/services/notification-hubs/notify-users-aspnet
[使用通知集線器傳送相關消息]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Google 雲端訊息的用戶端元件]: http://components.xamarin.com/view/GCMClient/
[Azure 訊息元件]: http://components.xamarin.com/view/azure-messaging
