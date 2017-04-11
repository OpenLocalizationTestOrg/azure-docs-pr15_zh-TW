<properties
    pageTitle="通知集線器-企業推入架構"
    description="企業環境中使用 Azure 通知集線器指南"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>企業推入建築的指導方針

企業今天是建立任何行動應用程式使用者 （外部） 或 （內部） 的員工逐漸移動。 他們擁有現有後端系統就地大型主機或必須整合到行動應用程式架構的一些 LoB 應用程式。 本指南將討論如何執行此整合常見的案例建議可行的解決方案。

經常需求做為感興趣的事件發生的後端系統中時，使用者可透過行動應用程式傳送推入通知。 例如︰ 銀行客戶她在 iPhone 上擁有銀行的銀行應用程式的使用者想要轉帳卡高於特定她的帳戶或內部網路的案例的財務部的員工具有預算核准應用程式，他的 Windows Phone 要便可以獲得核准要求時收到通知時收到通知。

銀行帳戶或核准程序有一些必須啟動發送給使用者的後端系統完成。 可能有多個所有必須建立相同的邏輯實作推入事件觸發通知時，例如後端系統。 整合與單一推入系統位置訂閱使用者可能會有不同的通知，甚至可能有多個行動應用程式，例如在內部網路的行動應用程式的情況下一個行動應用程式可能會想要從多個這類後端系統會收到通知的位置數個後端系統在於的複雜性。 不知道或需要知道的推入語意/技術，因此常見的解決方案傳統已介紹元件輪詢感興趣的任何事件的後端系統，並負責傳送推入郵件用戶端的後端系統。
以下我們會討論使用 Azure 服務匯流排主題/訂閱模型也會降低複雜度，同時進行方案可調整更好的解決方案。

以下是解決方案的一般架構，（通用使用多個行動應用程式但只有一個行動應用程式時的平均適用）

## <a name="architecture"></a>架構

![][1]

此結構的圖表中的重要項是 Azure 服務匯流排提供程式設計模型 （詳細說明，在[服務匯流排發行/子程式設計]） 主題/訂閱。 收件者，在這個案例中，是 [行動裝置的後端 （通常是[Azure 行動訊息服務]，就會啟動推入行動應用程式） 不會直接從後端系統接收郵件，但改為有提供[Azure 服務匯流排]讓從一或多個後端系統接收訊息的行動後端中繼抽象層。 需要建立的每個的後端系統例如帳戶 HR，也就是基本上 」 主題 」，這會啟動，才能為推入通知傳送郵件感興趣的財務服務匯流排主題。 後端系統會傳送郵件給這些主題。 行動後端可以藉由建立服務匯流排訂閱訂閱一或多個主題。 這會贊成行動的後端，若要從的對應的後端系統會收到通知。 行動裝置的後端會繼續接聽訊息訂閱，而且當郵件送達時，將其重新開啟，並將其傳送為其通知中心的通知。 通知集線器會最後傳遞訊息的行動應用程式。 因此摘要的主要元件，我們有︰

1. 後端系統 （LoB/舊版系統）
    - 建立服務匯流排主題
    - 傳送訊息
2. 行動裝置的後端
    - 建立服務訂閱
    - （從後端系統） 接收郵件
    - 傳送通知給用戶端 （透過 Azure 通知中心）
3. 行動應用程式
    - 接收並顯示通知

###<a name="benefits"></a>優點︰

1. 收件者 （行動應用程式/透過通知中心服務） 和寄件者 （後端系統） 之間聯繫，可讓其他的後端系統與最小變更正在整合。
2. 這也讓的多個行動應用程式，可以接收來自一或多個後端系統事件的案例。  

## <a name="sample"></a>範例︰

###<a name="prerequisites"></a>必要條件
您應該先完成下列的教學課程熟悉的概念，以及常見建立及設定步驟︰

1. [服務匯流排發行/子程式設計]-此說明的詳細資料的工作與服務匯流排主題/訂閱，如何建立包含主題/訂閱、 命名空間如何傳送和接收他們的郵件。
2. [通知集線器-Windows 通用教學課程]-此說明如何設定 Windows 市集應用程式，並用通知集線器註冊，然後收到通知。

###<a name="sample-code"></a>程式碼範例

完整的範例程式碼的[通知中心範例]。 分成三個元件︰

1. **EnterprisePushBackendSystem**

    。 此專案使用*WindowsAzure.ServiceBus* Nuget 套件，並根據[服務匯流排發行/子程式設計]。

    b。 這是簡單 C# 主控台應用程式以模擬 LoB 系統會傳送到行動應用程式的訊息。

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c。 `CreateTopic`用來建立服務匯流排主題我們會傳送郵件的位置。

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d。 `SendMessage`用來傳送郵件給本服務匯流排主題。 以下我們只要傳送一組隨機訊息定期以樣本主題。 通常會在事件發生時，會傳送郵件的後端系統。

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    。 此專案使用*WindowsAzure.ServiceBus*和*Microsoft.Web.WebJobs.Publish* Nuget 套件，並根據[服務匯流排發行/子程式設計]。

    b。 這是另一個 C# console 應用程式因為它沒有持續執行接聽來自 LoB/後端系統，我們會執行為[Azure WebJob] 。 這是您行動裝置的後端的部分。

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c。 `CreateSubscription`用來建立主題的服務匯流排訂閱後端系統會傳送郵件的位置。 此元件會根據商務案例中，建立一或多個訂閱 （例如一些可能會收到的郵件從 HR 系統，部分從財務系統等等） 對應的主題

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d。 ReceiveMessageAndSendNotification 用來使用其訂閱的主題閱讀的訊息，並成功閱讀時然後會通知 （在此範例案例 Windows 原生的快顯通知） 製作傳送到使用 Azure 通知集線器的行動應用程式。

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    e。 發佈**WebJob**，以滑鼠右鍵按一下在 Visual Studio 方案，並選取 [**發佈為 WebJob**

    ![][2]

    f。 選取您發佈的設定檔，然後建立新的 Azure 網站，如果不存在已其會裝載此 WebJob 和有 [網站] 和 [**發佈**]。

    ![][3]

    g。 設定為 「 持續執行 」，讓您登入[Azure 傳統入口網站]時您應該會看到類似以下工作︰

    ![][4]


3. **EnterprisePushMobileApp**

    。 這是在 Windows 市集應用程式將會收到來自執行屬於您行動裝置的後端 WebJob 的快顯通知，並將其顯示。 這根據[通知集線器-Windows 通用教學課程]。  

    b。 確定您的應用程式已啟用接收快顯通知。

    c。 確定下列的通知集線器註冊程式碼呼叫應用程式在啟動 (之後取代*HubName*和*DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>執行範例︰

1. 確定您 WebJob 順利執行，並排定 「 持續執行 」。
2. 執行**EnterprisePushMobileApp**開始 Windows 市集應用程式。
3. 執行**EnterprisePushBackendSystem**主控台應用程式，以模擬 LoB 後端，並會開始傳送郵件，您應該會看到如下所示出現的快顯通知︰

    ![][5]

4. 這監視服務匯流排訂閱 Web 工作中的服務匯流排主題原本傳送所有郵件。 後收到的郵件，通知所建立並傳送到行動應用程式。 您可以查看 WebJob 記錄檔，以確認處理程序，當您移至您的 Web 工作[Azure 傳統入口網站]中的記錄連結︰

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[通知中心範例]: https://github.com/Azure/azure-notificationhubs-samples
[Azure 行動訊息服務]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure 服務匯流排]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[服務匯流排發行/子程式設計]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[通知集線器-Windows 通用教學課程]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure 傳統入口網站]: https://manage.windowsazure.com/
