<properties
    pageTitle="通知集線器本地化相關消息教學課程"
    description="瞭解如何使用 Azure 通知集線器傳送本地化的相關消息通知。"
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>使用通知集線器傳送本地化的相關消息

> [AZURE.SELECTOR]
- [Windows 市集 C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>概觀

本主題說明如何使用**範本**的功能 Azure 通知集線器廣播已經化語言和裝置的相關消息通知。 在本教學課程您開始使用 Windows 市集應用程式建立[使用通知集線器，傳送新消息]。 完成時，您可以註冊您感興趣的類別，指定要收到通知，並接收只選取類別的推入通知，該語言的語言。


有兩個部分這種情況︰

- Windows 市集應用程式可讓用戶端裝置指定語言]，並為不同的相關消息類別; 訂閱

- 後端廣播通知，並使用的 Azure 通知集線器**標記**及**範本**feautres。



##<a name="prerequisites"></a>必要條件

您必須已經完成[傳送相關消息使用通知集線器]教學課程和有可用的程式碼，因為此教學課程直接根據該程式碼。

您也需要 Visual Studio 2012 或更新版本。


##<a name="template-concepts"></a>範本的概念

[使用通知集線器傳送相關消息]建置即可訂閱通知不同新聞類別**標籤**的應用程式。
多個應用程式]，不過，多個市場為目標，並要求已進行本地化。 此選項表示本地化並傳送至正確設定的裝置的通知] 自己的內容。
本主題中，我們會顯示如何使用**範本**的功能通知集線器輕鬆進行本地化的相關消息通知。

附註︰ 傳送本地化的通知的其中一個方法是建立多個版本的每一個標籤。 舉例來說，以支援英文、 法文和中文，我們需要三個不同的標籤的全球消息: 「 world_en 「，」 world_fr 」，並 「 world_ch 」。 接著就必須這些標記的每個傳送當地語系化的版本的全球消息。 本主題中，我們會使用範本的標籤和傳送多封郵件的需求。

在高的層級，範本可指定如何在特定的裝置應收到通知。 範本會參照屬於您的應用程式後端所傳送的訊息的內容，來指定確切的內容格式。 在此例中，我們將傳送給包含所有支援的語言的地區設定無關訊息︰

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

然後，我們可確保裝置註冊指的是正確的屬性的範本。 例如，想要收到一則簡單的快顯訊息的 Windows 市集應用程式會註冊下列範本以任何對應的標籤︰

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



範本是您可以進一步瞭解我們的[範本](notification-hubs-templates-cross-platform-push-messages.md)文件中的強大功能。 


##<a name="the-app-user-interface"></a>應用程式使用者介面

現在，我們將修改中斷新聞應用程式在主題中[使用通知集線器傳送相關消息]傳送本地化的相關消息使用範本建立的。

在您的 Windows 市集應用程式︰

變更您要包含的地區設定下拉式方塊 MainPage.xaml:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>建立 Windows 市集用戶端應用程式

1. 在通知類別中，新增 [地區設定參數*StoreCategoriesAndSubscribe*和*SubscribeToCateories*方法。

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    請注意，而非呼叫*RegisterNativeAsync*方法我們呼叫*RegisterTemplateAsync*︰ 我們註冊的範本而定的地區設定為特定的通知格式。 我們也提供的範本 (「 localizedWNSTemplateExample 」) 的名稱，因為我們可能會想要註冊一個以上的範本 （例如一個快顯通知） 和一個磚，我們需要命名才能更新，或將它們刪除。

    請注意，是否裝置註冊相同標籤的多個範本，內送郵件目標標籤將會產生多個通知傳送至裝置 （一個的每個範本）。 這種行為時必須產生多個的畫面通知，例如在 Windows 市集應用程式中顯示徽章] 與 [快顯相同的邏輯訊息。

2. 新增下列方法來擷取已儲存的地區設定︰

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. 在您 MainPage.xaml.cs，更新您的按鈕來擷取目前地區設定下拉式方塊的值，以及其提供給通話通知類別中，按一下處理常式，如下圖所示︰

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. 最後，在您 App.xaml.cs 的檔案，請務必更新您`InitNotificationsAsync`來擷取地區設定，並使用訂閱時的方法︰

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>從您的後端傳送本地化的通知

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[使用通知集線器傳送相關消息]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
