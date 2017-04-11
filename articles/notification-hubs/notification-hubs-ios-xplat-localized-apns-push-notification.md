<properties
    pageTitle="通知集線器本地化中斷新聞教學課程 iOS 版"
    description="瞭解如何使用 Azure 服務匯流排通知集線器傳送本地化的相關消息通知 (iOS)。"
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>使用通知集線器傳送 iOS 裝置本地化的相關消息

> [AZURE.SELECTOR]
- [Windows 市集 C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>概觀

本主題說明如何使用[範本](notification-hubs-templates-cross-platform-push-messages.md)的功能 Azure 通知集線器廣播已經化語言和裝置的相關消息通知。 在本教學課程開始 iOS 應用程式建立[使用通知集線器，傳送新消息]。 完成時，您可以註冊您感興趣的類別，指定要收到通知，並接收只選取類別的推入通知，該語言的語言。


有兩個部分這種情況︰

- iOS 應用程式可讓用戶端裝置指定語言]，並為不同的相關消息類別; 訂閱

- 後端廣播通知，並使用的 Azure 通知集線器**標記**及**範本**feautres。



##<a name="prerequisites"></a>必要條件

您必須已經完成[傳送相關消息使用通知集線器]教學課程和有可用的程式碼，因為此教學課程直接根據該程式碼。

Visual Studio 2012 或更新版本是選擇性的。



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

然後，我們可確保裝置註冊指的是正確的屬性的範本。 舉例來說，想要註冊法文新聞 iOS 應用程式會註冊下列各項︰

    {
        aps:{
            alert: "$(News_French)"
        }
    }

範本是您可以進一步瞭解我們的[範本](notification-hubs-templates-cross-platform-push-messages.md)文件中的強大功能。

##<a name="the-app-user-interface"></a>應用程式使用者介面

現在，我們將修改中斷新聞應用程式在主題中[使用通知集線器傳送相關消息]傳送本地化的相關消息使用範本建立的。


在您 MainStoryboard_iPhone.storyboard，新增 [區段控制項我們將會支援三種語言︰ 英文、 法文和中文。

![][13]

請確認在您 ViewController.h 新增 IBOutlet，如下所示︰

![][14]

##<a name="building-the-ios-app"></a>建置 iOS 應用程式


1. 在您 Notification.h 新增*retrieveLocale*的方法，並修改存放區與訂閱的方法如下所示︰

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    在您的 Notification.m 修改*storeCategoriesAndSubscribe*的方法，新增地區設定參數，並將其儲存在使用者的預設值︰

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    然後修改*訂閱*方法，以包含地區設定︰

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    請注意如何立即使用方法*registerTemplateWithDeviceToken*，而不*registerNativeWithDeviceToken*。 我們註冊範本時必須提供 json 範本，並也範本的名稱 （我們應用程式可能會想要註冊不同的範本）。 務必註冊的類別標籤中，我們要確認接收這些新聞 notifciations。

    新增使用者的預設設定從擷取地區設定的方法︰

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. 現在，我們修改通知類別，我們已確認我們 ViewController 會用到新的 UISegmentControl。 新增*viewDidLoad*方法，請確定顯示目前選取的地區設定中的下列行︰

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    然後，如果在您*訂閱*的方法，您可以變更您的來電至*storeCategoriesAndSubscribe*下列︰

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. 最後，您必須更新您的 AppDelegate.m *didRegisterForRemoteNotificationsWithDeviceToken*方法，讓您可以正確重新整理您的註冊您的應用程式啟動時。 變更您*訂閱*的通知，以下列方法的來電︰

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>（選用）從.NET 主控台應用程式傳送本地化的範本通知。

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>（選用）從裝置傳送本地化的範本通知

如果您不可以存取 Visual Studio 中，或只是想要測試直接從裝置上的應用程式傳送本地化的範本通知。  您可以簡單加入的本地化的範本參數`SendNotificationRESTAPI`在先前教學課程中所定義的方法。

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>後續步驟

如需有關如何使用範本的詳細資訊，請參閱︰

- [通知使用者，通知集線器︰ ASP.NET]
- [通知使用者，通知集線器︰ 行動服務]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[使用通知集線器傳送相關消息]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[通知使用者，通知集線器︰ ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[通知使用者，通知集線器︰ 行動服務]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
