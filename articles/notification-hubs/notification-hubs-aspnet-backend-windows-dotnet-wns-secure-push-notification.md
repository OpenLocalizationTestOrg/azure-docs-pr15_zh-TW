<properties
    pageTitle="推播通知 azure 集線器安全"
    description="瞭解如何傳送 Azure 中的 [安全的推播通知。 以 C# 使用.NET API 撰寫程式碼範例。"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>推播通知 azure 集線器安全

> [AZURE.SELECTOR]
- [Windows 標準](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>概觀

Microsoft Azure 中的推入通知支援可讓您存取大幅簡化的行動裝置平台的同時消費者版和企業版應用程式的推播通知方便使用多平台、 縮放出推入基礎結構。

由於法規或安全性的限制式，有時候應用程式可能會想要併入項目無法傳送透過標準推入通知基礎結構的通知。 本教學課程說明如何藉由傳送透過安全、 已驗證的用戶端裝置與應用程式後端之間連線的機密資訊達成相同的體驗。

在高的層級，流程如下︰

1. 應用程式後端︰
    - 儲存安全後端資料庫中的內容。
    - （不安全的資訊會傳送） 的裝置傳送此通知的識別碼。
2. 在裝置上時收到通知, 應用程式︰
    - 裝置連絡人後端要求安全的內容。
    - 應用程式通知，以在裝置上的方式顯示的內容。

請務必請注意，上述 flow 中 （和在本教學課程），我們假設的裝置會儲存驗證權杖在本機的儲存空間，在使用者登之後。 這可以確保完全順暢的使用體驗，如裝置可擷取的通知安全的內容，使用此 token 加。 如果您的應用程式不會儲存在裝置上，驗證的權杖，或如果可以是這些權杖，裝置應用程式時，在收到通知，應該顯示一般通知提示使用者以啟動應用程式。 然後，應用程式授權的使用者，並顯示通知內容。

本安全推入教學課程中會顯示如何安全地傳送推入通知。 教學課程是根據[通知使用者](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)教學課程中，因此您應該先完成步驟在教學課程中的第一次。

> [AZURE.NOTE] 本教學課程假設您建立，以及[快速入門通知集線器 （Windows 市集）](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)所述設定您的通知中心。
此外，請注意 Windows Phone 8.1 需要 Windows (不在 Windows Phone) 認證，並在背景的工作，請執行 Windows Phone 8.0 或 Silverlight 8.1 上無法運作。 Windows 市集應用程式，您會收到通知透過背景工作只有應用程式是鎖定畫面啟用 （按一下 Vappmanifest 中的核取方塊）。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>修改 Windows Phone 專案

1. 在**NotifyUserWindowsPhone**專案中，新增下列程式碼至 App.xaml.cs 登錄推入背景的工作。 新增下列程式碼的結尾`OnLaunched()`方法︰

        RegisterBackgroundTask();

2. 仍在 App.xaml.cs，新增下列程式碼之後，立即`OnLaunched()`方法︰

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. 新增下列`using`App.xaml.cs 檔案頂端的陳述式︰

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. 從 [Visual Studio 中的 [**檔案**] 功能表中，按一下 [**全部儲存**]。

## <a name="create-the-push-background-component"></a>建立推入背景元件

下一步是建立推入背景元件。

1. 方案總管] 中以滑鼠右鍵按一下最上層的節點的解決方案 (在本例中的**解決方案 SecurePush** )，然後按一下 [**新增**]，然後按一下 [**新專案**。

2. 展開**市集應用程式**，然後按一下**Windows Phone 應用程式**，然後按一下 [ **Windows 執行階段元件 (Windows Phone)**。 **PushBackgroundComponent**，為專案的名稱，然後按一下 [**確定**] 以建立專案。

    ![][12]

3. 在 [方案總管**PushBackgroundComponent (Windows Phone 8.1)**專案，以滑鼠右鍵按一下 [**新增**]，然後按一下 [**類別**]。 新的類別**PushBackgroundTask.cs**命名。 按一下 [**新增**]，以產生類別]。

4. 下列程式碼，取代版面配置區以取代**PushBackgroundComponent**命名空間定義的全部內容`{back-end endpoint}`與後端端點時部署後端取得︰

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. 在方案總管中，以滑鼠右鍵按一下**PushBackgroundComponent (Windows Phone 8.1)**專案，然後按一下**管理 NuGet 套件**。

6. 在左邊，按一下 [**線上**]。

7. 在 [**搜尋**] 方塊中，輸入**Http 用戶端**。

8. 在結果清單中，按一下**Microsoft HTTP 用戶端文件庫**，然後按一下 [**安裝**]。 完成安裝。

9. 回 NuGet**搜尋**方塊中，輸入**Json.net**。 安裝**Json.NET**套件，然後關閉 [NuGet 套件管理員] 視窗。

10. 新增下列`using` **PushBackgroundTask.cs**檔案頂端的陳述式︰

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. 在方案總管中，在**NotifyUserWindowsPhone (Windows Phone 8.1)**專案中，以滑鼠右鍵按一下 [**參考資料**，然後按一下 [**加入參考**。 在 [參照管理員] 對話方塊中，核取**PushBackgroundComponent**，，然後按一下**[確定]**。

12. 在方案總管中，按兩下 [ **Package.appxmanifest** **NotifyUserWindowsPhone (Windows Phone 8.1)**專案中。 在 [**通知**]，設定**簡訊快顯**為 [**是]**。

    ![][3]

13. 仍在**Package.appxmanifest**，按一下上方附近的 [**宣告**] 功能表。 在 [**可用的宣告**] 下拉式功能表中，按一下**背景的工作**]，然後按一下 [**新增**。

14. 在**Package.appxmanifest**，在 [**內容**] 核取 [**推播通知**。

15. 在**Package.appxmanifest**，在**應用程式設定**] 底下輸入**PushBackgroundComponent.PushBackgroundTask** **進入點**] 欄位中。

    ![][13]

16. 從 [**檔案**] 功能表中，按一下 [**全部儲存**]。

## <a name="run-the-application"></a>執行應用程式

若要執行應用程式，執行下列動作︰

1. 在 Visual Studio 中，執行**AppBackend** API Web 應用程式。 ASP.NET web 頁面會顯示。

2. 在 Visual Studio 中，執行**NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone 應用程式。 在 Windows Phone 模擬器執行，並會自動載入應用程式。

3. 在 [ **NotifyUserWindowsPhone**應用程式使用者介面，輸入使用者名稱和密碼。 這些可以是任何字串，但必須相同的值。

4. 在 [ **NotifyUserWindowsPhone**應用程式使用者介面，按一下 [**登入並註冊**。 然後按一下 [**傳送推入**]。

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
