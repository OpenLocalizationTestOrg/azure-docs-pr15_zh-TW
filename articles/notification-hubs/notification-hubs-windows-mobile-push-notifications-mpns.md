<properties
    pageTitle="在 Windows Phone 上傳送推入通知與 Azure 通知集線器 |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Windows Phone 8 或 Windows Phone 8.1 Silverlight 應用程式的推入通知 Azure 通知集線器。"
    services="notification-hubs"
    documentationCenter="windows"
    keywords="推播通知、 推入通知，windows phone 推入"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>使用 Windows Phone 上 Azure 通知集線器傳送推入通知

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)。

本教學課程教您如何使用 Azure 通知集線器推入通知傳送至 Windows Phone 8 或 Windows Phone 8.1 Silverlight 應用程式。 如果您的目標 Windows Phone 8.1 (非 Silverlight)，然後請參閱[Windows 通用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)的版本。
在此教學課程中，您可以建立空白的 Windows Phone 8 應用程式會使用 Microsoft 推入通知服務 (MPNS) 接收推入通知。 完成後，您可以使用您的通知中心廣播執行您的應用程式的所有裝置的推播通知。

> [AZURE.NOTE] 通知集線器 Windows Phone SDK 不支援使用 Windows Phone 8.1 Silverlight 應用程式中的 Windows 推入通知服務 (WNS)。 若要使用 Windows Phone 8.1 Silverlight 應用程式中使用 WNS （而非 MPNS)，請依照下列[通知集線器-Windows Phone Silverlight 教學課程中]，使用 REST Api。

教學課程示範使用通知集線器的簡單廣播的案例。

##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ [Visual Studio 2012 Express for Windows Phone]或更新版本。

完成此教學課程是 Windows Phone 8 應用程式的所有其他通知集線器教學課程的必要條件。

##<a name="create-your-notification-hub"></a>建立您的通知中心

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>按一下 （<i>設定</i>） 內的 [<b>通知服務</b>] 區段，按一下在<b>Windows Phone (MPNS)</b> ，然後按一下 [<b>啟用未經認證的推入</b>] 核取方塊。</p>
</li>
</ol>

&emsp;&emsp;![Azure 入口網站-啟用 unathenticated 推入通知](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

現在建立並傳送未經認證的通知，Windows Phone 版設定中心。

> [AZURE.NOTE] 本教學課程中使用 MPNS 在未驗證模式。 MPNS 未驗證的模式隨附限制您可以傳送給每個頻道的通知。 通知集線器支援[MPNS 驗證模式](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx)可以讓您上傳您的憑證。

##<a name="connecting-your-app-to-the-notification-hub"></a>連線到 [通知] 中心的 [應用程式

1. 在 Visual Studio 中，建立新的 Windows Phone 8 應用程式。

    ![Visual Studio 新專案的 Windows Phone 應用程式][13]

    在 Visual Studio 2013 更新 2 或更新版本，您會改為建立的 Windows Phone Silverlight 應用程式。

    ![Visual Studio 新專案的空白應用程式-Windows Phone Silverlight][11]

2. 在 Visual Studio 中，以滑鼠右鍵按一下方案，然後按一下**管理 NuGet 套件**。

    隨後便會顯示 [**管理 NuGet 套件**] 對話方塊。

3. 搜尋`WindowsAzure.Messaging.Managed`並按一下 [**安裝**]，再按一下 [接受使用規定。

    ![Visual Studio-NuGet 封裝管理員][20]

    這會下載、 安裝，並將 Azure 訊息的文件庫的參照在 Windows 版新增使用<a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet 套件</a>。

4. 開啟檔案 App.xaml.cs，並將以下`using`陳述式︰

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. 在 App.xaml.cs **Application_Launching**方法頂端新增下列程式碼︰

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] 值**MyPushChannel**是用來查詢現有的頻道中的[HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx)集合索引。 如果沒有一個有，建立新的項目名稱。
    
    請確定插入集線器及前一節中名為**DefaultListenSharedAccessSignature**取得連線字串的名稱。
    將此程式碼會從 MPNS，擷取通道 URI 應用程式，並使用您的通知中心，然後註冊頻道 URI。 也可以保證的頻道 URI 中註冊您的通知中心每次啟動應用程式。

    >[AZURE.NOTE]本教學課程中會傳送至裝置的快顯通知。 當您傳送磚通知時，您必須在通道，改為呼叫**BindToShellTile**方法。 若要支援這兩種快顯並磚通知，來電同時**BindToShellTile**和**BindToShellToast**。

6. 在方案總管中，展開 [**內容**]，開啟`WMAppManifest.xml`檔案，按一下 [**功能**] 索引標籤，請確定已核取 [ **ID_CAP_PUSH_NOTIFICATION**功能。

    ![Visual Studio-Windows Phone 應用程式功能][14]

    如此一來，可確保您的應用程式可接收推入通知。 而任何嘗試推入通知傳送至應用程式將會失敗。

7. 按下`F5`鍵以執行應用程式。

    註冊訊息會顯示在應用程式。

8. 關閉應用程式。  

   >[AZURE.NOTE] 若要接收快顯推入通知，應用程式不必須執行在前景中。

##<a name="send-push-notifications-from-your-backend"></a>從您的後端傳送推入通知

您可以從任何後端公用<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">其餘介面</a>透過使用通知集線器傳送推入通知。 在本教學課程中，您可以傳送使用.NET console 應用程式的推播通知。 

如需如何從通知集線器整合 ASP.NET WebAPI 後端傳送推入通知的範例，請參閱[Azure 通知集線器通知使用者，.NET 後端](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)。  

如需如何使用[REST Api](https://msdn.microsoft.com/library/azure/dn223264.aspx)來傳送推入通知，請參閱[如何使用從 Java 通知集線器](./notification-hubs-java-push-notification-tutorial.md)，以及[如何使用從 PHP 通知集線器](./notification-hubs-php-push-notification-tutorial.md)的範例。

1. 以滑鼠右鍵按一下方案**新增**和**新的專案]**，然後**Visual C#**下的 [ **Windows**和**主控台應用程式**，，然後選取按一下**[確定]**。

    ![Visual Studio 新專案-主控台應用程式][6]

    如此會將新 Visual C# 主控台應用程式至的方案。 您也可以執行這個動作在不同的方案。

4. 按一下 [**工具]**，按一下 [**文件庫封裝管理員**]，再按一下 [**封裝管理員主控台**。

    隨後便會顯示封裝管理員主控台。

5.  在 [**封裝管理員主控台**] 視窗中，設定**預設專案**至新主控台應用程式專案，然後在 [主控台] 視窗中，執行下列命令︰

        Install-Package Microsoft.Azure.NotificationHubs

    如此會將 Azure 通知集線器 SDK 使用<a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification 集線器 NuGet 套件</a>的參考。

6. 開啟`Program.cs`檔案，並新增下列`using`陳述式︰

        using Microsoft.Azure.NotificationHubs;

6. 在 [`Program`課程，請新增下列方法︰

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    請務必將`<hub name>`版面配置區出現在入口網站中的 [通知] 中心內的名稱。 此外，取代 「 設定您的通知中心 」 區段中，名為 [ **DefaultFullSharedAccessSignature**取得連線字串中的連線字串版面配置區

    >[AZURE.NOTE]請確定您的**完整**存取權，未**接聽**的 access 使用連接字串。 接聽存取字串沒有權限可傳送推入通知。

4. 新增中的下列行您`Main`方法︰

         SendNotificationAsync();
         Console.ReadLine();

5. 您執行的 Windows Phone 模擬器及關閉應用程式，請將主控台應用程式專案設定為預設啟動專案，然後按下`F5`鍵以執行應用程式。

    您會收到快顯推入通知。 點選快顯橫幅載入應用程式。

您可以在 MSDN 上的[快顯目錄]] 和 [[目錄] 磚]的主題中尋找所有可能的內容。

##<a name="next-steps"></a>後續步驟

在此範例中，您推入通知傳送到該網域所有的 Windows Phone 8 裝置。 

若要針對特定的使用者，請參閱[推入通知給使用者使用通知集線器]教學課程。 

如果您想要區段您感興趣的群組的使用者，您可以瞭解[使用通知集線器，傳送新消息]。 

進一步瞭解如何使用通知集線器[通知集線器]指引。



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Windows Phone 版的 visual Studio 2012 Express]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[通知集線器指南]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[使用通知集線器給使用者的推播通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[使用通知集線器傳送相關消息]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[快顯目錄]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[並排顯示目錄]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[通知集線器-Windows Phone Silverlight 教學課程]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

