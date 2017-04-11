<properties
    pageTitle="通知集線器相關消息教學課程-iOS"
    description="瞭解如何使用 Azure 服務匯流排通知集線器傳送 iOS 裝置相關消息通知。"
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>使用通知集線器傳送相關消息

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>概觀

本主題說明如何使用 Azure 通知集線器廣播 iOS 應用程式的相關消息通知。 完成時，您將無法註冊中斷您感興趣的新聞類別，並接收只推入通知這些類別。 這種情況下是許多應用程式的常見模式通知傳送至先前已經宣告它們，例如 RSS 讀取程式、 應用程式的音樂風扇等感興趣的使用者群組有的位置。

在 [通知] 中心建立註冊時，將一或多個_標籤_啟用廣播的案例。 當通知會傳送至標籤中時，所有的裝置註冊標記會收到通知。 標籤是只是字串，因為他們沒有事先佈建。 如需標記的詳細資訊，請參閱[通知集線器路由和標籤運算式](notification-hubs-tags-segment-push-message.md)。


##<a name="prerequisites"></a>必要條件

本主題是根據您在[開始使用通知集線器]建立的應用程式[get-started]。 在開始之前本教學課程中，您必須已經完成[快速入門通知集線器][get-started]。

##<a name="add-category-selection-to-the-app"></a>類別選取項目新增至應用程式

第一個步驟是將使用者介面元素新增至您現有的腳本，可讓使用者選取登錄的類別。 使用者選取類別會儲存在裝置上。 應用程式啟動時，裝置登錄中建立與所選的類別通知中心為標記。

1. 在您的 MainStoryboard_iPhone.storyboard 新增物件程式庫下列元件︰
    + 有 「 中斷新聞 」 文字的標籤
    + 「 世界 」、 「 政治 」、 「 公司 」、 「 技術 」、 「 科學 」、 「 運動 」] 類別的文字標籤
    + 六個參數，每個類別，一個設定每個參數依預設為**關閉****狀態**。
    + 一個按鈕標示為 「 訂閱 」

    您的腳本應如下所示︰

    ![][3]

2. 在 [小幫手編輯器建立的所有參數插座，撥打電話給 「 WorldSwitch 「，」 PoliticsSwitch 」、 「 BusinessSwitch 」、 「 TechnologySwitch 」、 「 ScienceSwitch 」、 「 SportsSwitch 」


3. 建立名為 「 訂閱] 按鈕的動作。 您 ViewController.h 應包含下列各項︰

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. 建立新的**Cocoa 觸控類別**稱為`Notifications`。 檔案 Notifications.h 介面一節中複製下列程式碼︰

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. 新增下列匯入指示詞 Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. 檔案 Notifications.m 實作一節中複製下列程式碼。

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    這個類別使用本機存放區來儲存和擷取會收到此裝置的新聞的類別。 此外，它包含註冊使用[範本](notification-hubs-templates-cross-platform-push-messages.md)登錄這些類別的方法。

7. 在 [AppDelegate.h 檔案，匯入陳述式的新增 Notifications.h，並新增通知類別的執行個體的屬性︰

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. 在**didFinishLaunchingWithOptions**方法 AppDelegate.m 中，新增初始化通知執行個體的開頭之方法的程式碼。  
 
    `HUBNAME`和`HUBLISTENACCESS`（hubinfo.h 中定義） 應該已經有`<hub name>`和`<connection string with listen access>`版面配置區取代您的通知中心名稱與連線字串*DefaultListenSharedAccessSignature*取得較舊版本

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] 因為隨附於用戶端應用程式的認證通常安全，，您只應該使用您的用戶端應用程式發佈接聽存取鍵。 聆聽存取可讓您的應用程式註冊通知，但現有登錄無法修改，且不傳送通知。 完整便捷鍵會傳送通知，及變更現有的登錄受到保護的後端服務中使用。


9. 在**didRegisterForRemoteNotificationsWithDeviceToken**方法 AppDelegate.m 中，用下列程式碼將裝置權杖傳遞到通知類別來取代方法中的程式碼。 通知課程會執行登錄與類別的通知。 如果使用者變更類別選取項目，我們稱為 「`subscribeWithCategories`方法以回應 [**訂閱**] 按鈕以進行更新。

    > [AZURE.NOTE] 因為指派的 Apple 推入通知服務 (APNS) 的裝置 token 可以隨時將，您應該註冊經常為避免通知失敗的通知。 此範例中登錄應用程式啟動每次通知。 經常執行的應用程式，超過一天，您可以可能略過註冊，若要保留的頻寬，如果小於某一天已經過了後的前一個註冊。

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    請注意，現在應該會沒有其他程式碼**didRegisterForRemoteNotificationsWithDeviceToken**方法。

10. 以下兩種方法已應該出現在 AppDelegate.m 完成的[快速入門通知集線器][get-started]教學課程。  如果沒有的話，將其新增。

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    這種方式處理顯示簡單的**UIAlert**執行應用程式時收到通知。

11. 在 ViewController.m，匯入陳述式的新增 AppDelegate.h，並將下列程式碼複製到 XCode 產生**訂閱**的方法。 將此程式碼會更新以使用新的類別標籤使用者選擇使用者介面中的通知註冊。

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    這個方法所建立的類別**NSMutableArray**使用**通知**類別來儲存在本機存放區中的清單，並使用您的通知中心註冊的相對應的標籤。 變更類別時, 註冊將重新建立新的類別。

12. 在 ViewController.m，新增下列程式碼**viewDidLoad**方法若要設定使用者介面根據先前儲存的類別中。


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



應用程式現在可以存放裝置本機存放區用來與通知中樞登錄，只要啟動應用程式中的類別的一組。  使用者可以變更的類別在執行階段選取範圍，然後按一下 [更新裝置註冊的**訂閱**方式。 接下來，您將會更新應用程式中的應用程式本身的直接傳送給相關消息通知。


##<a name="optional-sending-tagged-notifications"></a>（選用）傳送的標記的通知

如果您沒有安裝 Visual Studio 存取，您可以直接跳到下一節，並從 [應用程式本身傳送通知。 您也可以傳送適當的範本通知從[Azure 傳統入口網站]的通知中心中使用 [偵錯] 索引標籤。 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>（選用）從裝置傳送通知

通常會傳送通知後端服務，但您可以直接從應用程式傳送相關消息通知。 若要這麼做我們將會更新`SendNotificationRESTAPI`方法，我們定義中的[快速入門通知集線器][get-started]教學課程。


1. 在 ViewController.m 更新`SendNotificationRESTAPI`方法追蹤接受的類別標籤的參數，使其傳送給適當的[範本](notification-hubs-templates-cross-platform-push-messages.md)通知。

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

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



2. ViewController.m 更新**傳送通知**動作的程式碼，如下所示。 使其將會傳送通知個別使用每一個標籤，並傳送給多個平台。



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. 重建專案，並確定您有沒有建置錯誤。


##<a name="run-the-app-and-generate-notifications"></a>執行應用程式，並產生通知

1. 按下建立專案並啟動應用程式的 [執行] 按鈕。 選取相關消息選項訂閱，然後按下 [**訂閱**] 按鈕。 您應該會看到一個對話方塊，表示已訂閱的通知。

    ![][1]

    當您選擇**訂閱**時，應用程式就會將所選的類別轉換成標籤，然後從 [通知] 中心要求，選取標籤的新裝置註冊。

2. 輸入訊息以傳送為相關消息，然後按下 [**傳送通知**] 按鈕。 或者，執行.NET console 應用程式來產生通知。

    ![][2]


3. 每個訂閱最新消息的裝置會收到您傳送相關消息通知。



## <a name="next-steps"></a>後續步驟

在本教學課程中，我們學會如何依類別廣播相關消息。 請考慮完成下列教學課程醒目提示其他進階的通知集線器案例的其中一項︰

+ **[使用通知集線器廣播本地化的相關消息]**

    瞭解如何展開相關消息應用程式，才能傳送的本地化的通知。





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[使用通知集線器廣播本地化的相關消息]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure 傳統入口網站]: https://manage.windowsazure.com
