<properties
    pageTitle="使用通知集線器傳送相關消息 (Windows Phone)"
    description="使用 Azure 通知集線器標籤在登錄中傳送至 Windows Phone 應用程式的相關消息。"
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>使用通知集線器傳送相關消息

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>概觀

本主題說明如何使用 Azure 通知集線器廣播至 Windows Phone 8.0/8.1 Silverlight 應用程式的相關消息通知。 如果您的目標 Windows 市集或 Windows Phone 8.1 版應用程式，請參閱為[Windows 通用](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)的版本。 完成時，您將無法註冊中斷您感興趣的新聞類別，並接收只推入通知這些類別。 這種情況下是許多應用程式的常見模式通知傳送至先前已經宣告它們，例如 RSS 讀取程式、 應用程式的音樂風扇等感興趣的使用者群組有的位置。

在 [通知] 中心建立註冊時，將一或多個_標籤_啟用廣播的案例。 當通知會傳送至標籤中時，所有的裝置註冊標記會收到通知。 標籤是只是字串，因為他們沒有事先佈建。 如需標記的詳細資訊，請參閱[通知集線器路由和標籤運算式](notification-hubs-tags-segment-push-message.md)。

##<a name="prerequisites"></a>必要條件

本主題是根據您在[開始使用通知集線器]建立的應用程式。 在開始之前本教學課程中，您必須已經完成[通知集線器快速入門]。

##<a name="add-category-selection-to-the-app"></a>類別選取項目新增至應用程式

第一步是新增至現有主頁面可讓使用者選取類別註冊的使用者介面元素。 使用者選取類別會儲存在裝置上。 應用程式啟動時，就會在您的通知中心內，選取類別與裝置註冊建立為標記。

1. 開啟 MainPage.xaml 專案檔案，然後取代名為**格線**元素`TitlePanel`和`ContentPanel`下列程式碼︰

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. 在專案中，類別定義中，建立新類別名為**通知**，新增**公用**輔助按鍵，然後將下列**使用**陳述式新增至新的程式碼檔案︰

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. 下列程式碼，複製到新的**通知**類別︰

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    此課程使用隔離的儲存空間儲存新聞會收到此裝置的類別。 同時也包含註冊使用[範本](notification-hubs-templates-cross-platform-push-messages.md)通知登錄這些類別的方法。


4. 在 App.xaml.cs 專案檔案中，新增下列屬性的**應用程式**類別。 取代`<hub name>`和`<connection string with listen access>`版面配置區，您的通知中心名稱與*DefaultListenSharedAccessSignature*取得較舊版本的連接字串。

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] 因為隨附於用戶端應用程式的認證通常安全，，您只應該使用您的用戶端應用程式發佈接聽存取鍵。 聆聽存取可讓您的應用程式註冊通知，但現有登錄無法修改，且不傳送通知。 完整便捷鍵會傳送通知，及變更現有的登錄受到保護的後端服務中使用。

5. 在您 MainPage.xaml.cs，加上的下列行︰

        using Windows.UI.Popups;

6. 在 [MainPage.xaml.cs 專案檔案中，新增下列方法︰

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    這個方法所建立的類別清單，並使用儲存在本機存放區中的清單，並登錄的相對應的標籤通知中心的**通知**類別。 變更類別時, 註冊將重新建立新的類別。

您的應用程式現在能夠將類別的一組儲存在本機裝置上的儲存空間，並登錄 [通知] 中心每次使用者變更的類別選取範圍。

##<a name="register-for-notifications"></a>註冊通知

在啟動時使用的類別已經儲存在本機存放區中的 [通知] 中心登錄這些步驟。

> [AZURE.NOTE] 因為通道 URI 指派由 Microsoft 推入通知服務 (MPNS) 可以隨時變更，您應該註冊經常為避免通知失敗的通知。 此範例中註冊通知每次啟動應用程式。 經常執行的應用程式，超過一天，您可以可能略過註冊，若要保留的頻寬，如果小於某一天已經過了後的前一個註冊。


1. 開啟 App.xaml.cs 檔案加入**非同步**輔助按鍵**Application_Launching**方法和取代通知集線器註冊程式碼加入下列程式碼[通知集線器快速入門]︰

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    如此可確保的每次應用程式啟動時類別從本機存放區，並且要求登錄這些類別。

2. 在 MainPage.xaml.cs 專案檔案中，新增下列程式碼實作**OnNavigatedTo**方法︰

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
        }

    這會更新主頁面根據先前儲存的類別的狀態。

應用程式現已完成，可以將類別的一組儲存在每次使用者變更的類別選取 [通知] 中心註冊使用的裝置本機儲存區。 接下來，我們會定義後端可以將類別通知傳送到此應用程式。

##<a name="sending-tagged-notifications"></a>傳送的標記的通知

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>執行應用程式，並產生通知

1. 在 Visual Studio 中，請按 F5 編譯和啟動應用程式。

    ![][1]

    附註的應用程式使用者介面，提供一組切換，可讓您選擇要訂閱的類別。

2. 啟用一或多個類別切換，然後按一下 [**訂閱**]。

    應用程式會將所選的類別轉換成標籤，然後從 [通知] 中心要求，選取標籤的新裝置註冊。 註冊的類別會傳回，並顯示於] 對話方塊。

    ![][2]

3. 收到確認的類別已完成的訂閱之後, 執行主控台應用程式傳送的每個類別的通知。 請確認您只會收到通知您訂閱的類別。

    ![][3]

您已經完成本主題。

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[通知集線器快速入門]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

