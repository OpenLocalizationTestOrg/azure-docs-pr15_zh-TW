<properties
    pageTitle="跨平台通知傳送給使用者的通知集線器 (ASP.NET)"
    description="瞭解如何使用通知集線器範本傳送，請在單一邀請中，針對所有的平台適用於多種平台的通知。"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>傳送跨平台通知給使用者，通知集線器


在先前教學課程[通知集線器通知使用者]，您學到如何登錄特定已驗證的使用者的所有裝置推播通知。 在此教學課程中，在多個要求才能傳送通知給每個支援的用戶端平台。 通知集線器支援範本，可讓您指定如何在特定的裝置想要收到通知。 這可簡化傳送跨平台通知。 本主題將示範如何利用範本，以傳送，請在單一邀請中，針對所有的平台適用於多種平台的通知。 如需詳細範本的相關資訊，請參閱[Azure 通知集線器概觀][Templates]。

> [AZURE.NOTE] 通知集線器可讓您的裝置註冊相同標籤的多個範本。 在此情況下，指定該標籤的內送郵件產生多個通知傳送至裝置，其中一個的每個範本。 這可讓您在多個視覺通知的詳細資訊，例如兩者徽章，而是在 Windows 市集應用程式中的快顯通知顯示相同的郵件。

完成下列步驟，以傳送給使用範本的跨平台通知︰

1. 在方案總管 Visual Studio 中，展開**控制站**資料夾，然後開啟 RegisterController.cs 檔案。

2. 在 [建立新的註冊取代的**文章**方法中找出程式碼的區塊`switch`內容與下列程式碼︰

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    將此程式碼通話建立範本註冊，而不是原生註冊的平台特定方式。 需要修改現有的登錄，因為範本登錄衍生自原生登錄。

3. [**通知**] 控制器用下列程式碼來取代**sendNotification**方法︰

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    同時，而不需要指定的原生的內容，將此程式碼會傳送通知給所有的平台。 通知集線器建置，並提供的_標籤_值，指定登錄範本的每個裝置提供正確的內容。

4. 重新發佈 WebApi 後端專案。

5. 再次執行用戶端應用程式，並確認 [註冊成功。

6. （選用）用戶端應用程式部署至第二個的裝置，然後執行應用程式]。

    請注意，在每個裝置上顯示的通知。

## <a name="next-steps"></a>後續步驟

現在，您必須完成本教學課程，瞭解更多有關通知集線器與這些主題中的範本︰

+ **[使用通知集線器傳送相關消息]** <br/>示範使用範本的另一種情況

+  **[Azure 通知集線器概觀][Templates]**<br/>概觀主題有更多的詳細資訊的範本。


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[使用通知集線器傳送相關消息]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[向使用者通知集線器通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
