<properties
    pageTitle="使用通知集線器傳送相關消息 （Windows 通用）"
    description="使用標籤在登錄中的 Azure 通知集線器傳送至通用的 Windows 應用程式的相關消息。"
    services="notification-hubs"
    documentationCenter="windows"
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

# <a name="use-notification-hubs-to-send-breaking-news"></a>使用通知集線器傳送相關消息


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>概觀

本主題說明如何使用 Azure 通知集線器廣播至 Windows 市集或 Windows Phone 8.1 (非 Silverlight) 應用程式的相關消息通知。 如果您的目標在 Windows Phone 8.1 Silverlight，請參閱[在 Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)版。 完成時，您將無法註冊中斷您感興趣的新聞類別，並接收只推入通知這些類別。 這種情況下是許多應用程式的常見模式通知傳送至先前已經宣告它們，例如 RSS 讀取程式，如音樂風扇應用程式的興趣等使用者群組有的位置。 

在 [通知] 中心建立註冊時，將一或多個_標籤_啟用廣播的案例。 當通知會傳送至標籤中時，所有的裝置註冊標記會收到通知。 標籤是只是字串，因為他們沒有事先佈建。 如需標記的詳細資訊，請參閱[通知集線器路由和標籤運算式](notification-hubs-tags-segment-push-message.md)。

##<a name="prerequisites"></a>必要條件

本主題是根據您在[開始使用通知集線器]建立的應用程式[get-started]。 在開始之前本教學課程中，您必須已經完成[快速入門通知集線器][get-started]。

##<a name="add-category-selection-to-the-app"></a>類別選取項目新增至應用程式

第一步是新增至現有主頁面可讓使用者選取類別註冊的使用者介面元素。 使用者選取類別會儲存在裝置上。 應用程式啟動時，就會在您的通知中心內，選取類別與裝置註冊建立為標記。

1. 開啟 MainPage.xaml 專案檔案，然後複製下列程式碼中的**格線**項目︰

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. 以滑鼠右鍵按一下**共用**專案，並加入新的類別名稱為**通知**，**公用**輔助按鍵加入類別定義中，然後將下列**使用**陳述式新增至新的程式碼檔案︰

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. 下列程式碼，複製到新的**通知**類別︰

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    此課程會使用本機存放區來儲存此裝置已接收新聞的類別。 請注意，而非呼叫*RegisterNativeAsync*方法我們呼叫*RegisterTemplateAsync*註冊使用範本登錄的類別。 
    
    我們也提供的範本 (「 simpleWNSTemplateExample 」) 的名稱，因為我們可能會想要註冊一個以上的範本 （例如一個快顯通知） 和一個磚，我們需要命名才能更新，或將它們刪除。

    請注意，是否裝置註冊相同標籤的多個範本，內送郵件目標標籤將會產生多個通知傳送至裝置 （一個的每個範本）。 這種行為時必須產生多個的畫面通知，例如在 Windows 市集應用程式中顯示徽章] 與 [快顯相同的邏輯訊息。

    如需有關範本的詳細資訊，請參閱[範本](notification-hubs-templates-cross-platform-push-messages.md)。




4. App.xaml.cs 專案檔案中加入**應用程式**類別中的下列屬性︰

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    這個屬性會用來建立及存取**通知**執行個體。

    將上述的程式碼，取代`<hub name>`和`<connection string with listen access>`版面配置區，您的通知中心名稱與*DefaultListenSharedAccessSignature*取得較舊版本的連接字串。

    > [AZURE.NOTE] 因為隨附於用戶端應用程式的認證通常安全，，您只應該使用您的用戶端應用程式發佈接聽存取鍵。 聆聽存取可讓您的應用程式註冊通知，但現有登錄無法修改，且不傳送通知。 完整便捷鍵會傳送通知，及變更現有的登錄受到保護的後端服務中使用。

5. 在您 MainPage.xaml.cs，加上的下列行︰

        using Windows.UI.Popups;

6. 在 [MainPage.xaml.cs 專案檔案中，新增下列方法︰

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    這個方法所建立的類別清單，並使用儲存在本機存放區中的清單，並登錄的相對應的標籤通知中心的**通知**類別。 變更類別時, 註冊將重新建立新的類別。

您的應用程式現在能夠將類別的一組儲存在本機裝置上的儲存空間，並登錄 [通知] 中心每次使用者變更的類別選取範圍。

##<a name="register-for-notifications"></a>註冊通知

在啟動時使用的類別已經儲存在本機存放區中的 [通知] 中心登錄這些步驟。

> [AZURE.NOTE] 因為通道 URI 指派 Windows 通知服務 (WNS) 可以隨時變更，您應該註冊經常為避免通知失敗的通知。 此範例中登錄應用程式啟動每次通知。 經常執行的應用程式，超過一天，您可以可能略過註冊，若要保留的頻寬，如果小於某一天已經過了後的前一個註冊。

1. 開啟 App.xaml.cs 檔案，並更新要使用的**InitNotificationsAsync**方法`notifications`訂閱課程根據類別。

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    如此可確保的每次應用程式啟動時，擷取類別從本機存放區，並要求 registeration 這些類別。 **InitNotificationsAsync**方法建立一部分的[快速入門通知集線器][get-started]教學課程。

3. 在 MainPage.xaml.cs 專案檔案中加入下列程式碼*OnNavigatedTo*方法︰

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
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

    ![][19]

4. 傳送新的通知後端的下列方法之一︰

    + **主控台應用程式︰**啟動主控台應用程式。

    + **Java/PHP:**執行您的應用程式/指令碼。

    選取類別的通知會顯示為快顯通知。

    ![][14]

##<a name="next-steps"></a>後續步驟

在本教學課程中，我們學會如何依類別廣播相關消息。 請考慮完成下列教學課程醒目提示其他進階的通知集線器案例的其中一項︰

+ [使用通知集線器廣播本地化的相關消息]

    瞭解如何展開相關消息應用程式，才能傳送的本地化的通知。



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[使用通知集線器廣播本地化的相關消息]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
