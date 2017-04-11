<properties
    pageTitle="推播通知加入通用 Windows 平台 (UWP) 應用程式 |Azure 行動應用程式"
    description="瞭解如何使用 Azure 應用程式服務行動應用程式和 Azure 通知集線器推入通知傳送至通用 Windows 平台 (UWP) 應用程式。"
    services="app-service\mobile,notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-windows-app"></a>新增至您的 Windows 應用程式的推播通知

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>概觀

在本教學課程中，讓每次即會插入一筆記錄，將會傳送至裝置的推入通知 」，推入通知新增至[快速的 Windows [開始](app-service-mobile-windows-store-dotnet-get-started.md)專案。

如果您不使用下載快速入門伺服器專案，您必須推入通知擴充套件。 如需詳細資訊，請參閱[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

##<a name="configure-hub"></a>設定通知中心

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="register-your-app-for-push-notifications"></a>註冊您的應用程式推播通知

您需要提交您的應用程式至 Windows 市集]，然後設定您的伺服器專案整合與 Windows 通知服務 (WNS) 傳送推入。

1. Visual Studio 方案總管] 中以滑鼠右鍵按一下 UWP 應用程式專案，請按一下 [**市集** > **建立關聯的應用程式存放區...**。 

    ![與 Windows 市集應用程式](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. 在精靈中，按一下 [**下一步**、 使用您的 Microsoft 帳戶登入、 輸入您的應用程式的名稱中**保留新的應用程式名稱**，然後按一下 [**保留**。

3. 應用程式註冊已成功建立之後，請選取新的應用程式名稱，按一下 [**下一步**，，然後按一下**建立關聯**。 如此會將所需的 Windows 市集註冊資訊應用程式資訊清單。  

7. 瀏覽至[Windows 開發人員中心](https://dev.windows.com/en-us/overview)中，使用您的 Microsoft 帳戶登入，按一下 [新增應用程式註冊在**我的應用程式**，然後展開**服務** > **推播通知**。 

8. 在 [**推播通知**] 頁面上，按一下 [ **Microsoft Azure 行動服務**] 底下的 [ **Live 服務網站**]。

9. 在 [註冊] 頁面上，記下**應用程式密碼**與**套件 SID**您接下來要用來設定您的行動應用程式後端] 下的值。 

    ![與 Windows 市集應用程式](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] [用戶端密碼] 和 [套件 SID 是重要的安全性憑證。 不使用任何人共用這些值或發佈您的應用程式。 **應用程式識別碼**與密碼用於設定的 Microsoft 帳戶驗證。

##<a name="configure-the-backend-to-send-push-notifications"></a>設定後端傳送推入通知

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

##<a id="update-service"></a>更新伺服器傳送推入通知

使用下列程序符合您的後端專案類型&mdash; [.NET 後端](#dotnet)或[Node.js 後端](#nodejs)。

### <a name="dotnet"></a>.NET 後端專案

1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [**管理 NuGet 套件**、 搜尋 Microsoft.Azure.NotificationHubs，然後按一下 [**安裝**]。 這會安裝通知集線器用戶端文件庫。

2. 展開 [**控制器**、 開啟 TodoItemController.cs，以及新增下列使用陳述式︰

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. 在**PostTodoItem**的方法，請之後**InsertAsync**通話中加入下列程式碼︰

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    將此程式碼會告訴 [通知] 中心傳送推入通知後的新項目插入。

4. 重新發佈伺服器專案。

### <a name="nodejs"></a>Node.js 後端專案

1. 如果您還沒有這麼做，[下載快速入門專案](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)，或是使用[Azure 入口網站中的線上編輯器](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。

2. 取代現有 todoitem.js 檔案中使用下列步驟︰

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    這會傳送插入新的 todo 項目時包含 item.text WNS 快顯通知。

2. 編輯您的本機電腦上的檔案，請重新發佈伺服器專案。

##<a id="update-app"></a>新增至您的應用程式的 [推入通知

接下來，您的應用程式必須註冊啟動時的推播通知。 當您已啟用驗證時，請確定的使用者符號增益集之前，先註冊推播通知。

1. 開啟**App.xaml.cs**專案檔案，並將以下`using`陳述式︰

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. 在相同的檔案，新增下列**InitNotificationsAsync**方法定義到的**應用程式**類別︰

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    將此程式碼會從 WNS，擷取 ChannelURI 應用程式，並使用您的應用程式服務行動應用程式，然後註冊該 ChannelURI。

3. 在**App.xaml.cs** **OnLaunched**事件處理常式頂端新增**非同步**輔助按鍵方法定義，新增下列呼叫新的**InitNotificationsAsync**方法，如下列範例所示︰

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    如此一來，可確保短暫 ChannelURI 已註冊每次啟動應用程式。

4. 重建 UWP 應用程式專案。 您的應用程式現在就會收到快顯通知。

##<a id="test"></a>測試推播通知在您的應用程式

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>後續步驟

深入瞭解推入通知︰

* [如何使用受管理的用戶端 Azure Mobile 應用程式](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
範本會提供您傳送跨平台推入和本地化推入的彈性。 瞭解如何註冊範本。

* [診斷推入通知問題](../notification-hubs/notification-hubs-push-notification-fixer.md)  
有通知可能卸除或裝置上不會出現各種不同的原因。 本主題說明如何分析及找出推入通知失敗的原因。 

請考慮繼續執行下列教學課程其中一項︰

+ [將驗證新增至您的應用程式](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  瞭解如何將項目會驗證您的應用程式與身分識別提供者的使用者。

+ [啟用離線同步處理應用程式](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  瞭解如何新增離線支援您使用行動應用程式後端的應用程式。 離線同步處理可讓使用者能在行動應用程式與&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

