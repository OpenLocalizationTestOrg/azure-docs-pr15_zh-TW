在此區段中，您可以更新程式碼中現有的行動應用程式後端專案可傳送推入通知，每次加入新的項目。 這被可通知集線器[範本](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)」 功能，以便跨平台推入。 推播通知使用的範本，登錄各種用戶端和單一通用推入能存取所有的用戶端平台。

選擇下列程序符合您的後端專案類型&mdash; [.NET 後端](#dotnet)或[Node.js 後端](#nodejs)。

### <a name="dotnet"></a>.NET 後端專案
1. 在 Visual Studio 中，以滑鼠右鍵按一下伺服器專案並按一下 [**管理 NuGet 套件**，搜尋`Microsoft.Azure.NotificationHubs`，然後按一下 [**安裝**]。 這會安裝傳送通知您的後端的通知集線器文件庫。

3. 在伺服器專案中，開啟 [**控制器** > **TodoItemController.cs**，並新增下列使用陳述式︰

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. 在**PostTodoItem**的方法，請之後**InsertAsync**通話中加入下列程式碼︰  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    這會傳送之項目的範本通知。插入新的項目時的文字。

4. 重新發佈伺服器專案。 

### <a name="nodejs"></a>Node.js 後端專案

1. 如果您還沒有這麼做，[下載快速入門後端專案](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)，或是使用[Azure 入口網站中的線上編輯器](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)。

2. 現有的程式碼中 todoitem.js 取代下列動作︰

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
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

    這會傳送插入新的項目時包含 item.text 範本通知。

2. 編輯您的本機電腦上的檔案，請重新發佈伺服器專案。
