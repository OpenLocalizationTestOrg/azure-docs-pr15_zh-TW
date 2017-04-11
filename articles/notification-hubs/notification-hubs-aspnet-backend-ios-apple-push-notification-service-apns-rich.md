<properties
    pageTitle="Azure 通知集線器 Rtf 推入"
    description="瞭解如何從 Azure 將豐富的推入通知傳送至 iOS 應用程式。 撰寫目標 C 及 C# 程式碼範例。"
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Azure 通知集線器 Rtf 推入


##<a name="overview"></a>概觀

若要加入立即 rtf 內容的使用者，應用程式可能會想推播純文字以外。 這些通知升階使用者互動及呈現的內容，例如 url、 音效、 圖像/優待券等等。 本教學課程中建置[通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)主題上，並示範如何傳送推入通知加入內容 （例如圖像）。


本教學課程適用於 iOS 7 和 8。

  ![][IOS1]

在高的層級︰

1. 應用程式後端︰
    - 儲存豐富的內容 （在此情況下，圖像） 後端資料庫/本機存放區中
    - 傳送至裝置的這個豐富的通知
2. 在裝置上的應用程式︰
    - 連絡人後端要求收到的識別碼豐富的內容
    - 傳送給使用者通知裝置上擷取資料已完成，而且使用者點選若要深入瞭解時立即顯示內容


## <a name="webapi-project"></a>WebAPI 專案

1. 在 Visual Studio 中，開啟**AppBackend**專案的[通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教學課程中所建立。
2. 取得您想要通知使用者，並將其放在您的專案目錄中的 [**影像**] 資料夾中的圖像。
3. 按一下 [**顯示所有檔案**在方案總管中，以滑鼠右鍵按一下要**包含在 Project 中**的資料夾。
4. 使用選取圖像，變更其內容] 視窗中的 [建立] 動作**內嵌**的資源。

    ![][IOS2]

5. 在**Notifications.cs**，新增下列使用陳述式︰

        using System.Reflection;

6. 更新下列程式碼的整個**通知**類別。 請務必取代預留位置與您的通知中心認證圖像檔案名稱。

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  （選用）如需有關如何新增，並取得專案資源，請參閱[如何內嵌](http://support.microsoft.com/kb/319292)並使用 Visual C# 存取資源。

7. 在**NotificationsController.cs**，重新**NotificationsController**定義與下列程式碼片段。 這會傳送至裝置的初始無豐富通知識別碼，並允許用戶端擷取圖像︰

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. 現在我們重新部署此應用程式至 Azure 網站，讓它可以從所有裝置存取。 以滑鼠右鍵按一下**AppBackend**專案，然後選取 [**發佈]**。

9. 選取您發佈的目標 Azure 網站。 登入您的 Azure 帳戶，選取現有或新的網站，並記下的 [**目的地 URL** ] 屬性中 [**連線**] 索引標籤。 我們將此 URL 為*後端端點*您稍後在本教學課程。 按一下 [**發佈**]。

## <a name="modify-the-ios-project"></a>修改 iOS 專案

現在，您修改您的應用程式後端傳送只*識別碼*的通知，您將變更 iOS 應用程式來處理的識別碼，從您的後端擷取豐富的訊息。

1. 開啟您的 iOS 專案，並啟用遠端通知，移至您主要的應用程式中的目標**目標**節。

2. 按一下**功能**、 開啟**背景模式**中，然後核取 [**遠端通知**核取方塊。

    ![][IOS3]

3. 移至**Main.storyboard**，並確定您擁有從[通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教學課程的檢視控制器 （來參考至常用檢視控制器為在本教學課程）。

4. 將**導覽控制器**新增至您的腳本，並控制拖曳來常用檢視控制器，使其**根檢視**的導覽。 請確定**是初始檢視控制器**屬性檢查中已選取導覽控制器只。

5. 新增**檢視控制站**台建立的腳本並新增**[圖像檢視]**。 這是使用者會看到後，若要深入瞭解 notifiication 上的 [選擇的頁面。 您的腳本應如下所示︰

    ![][IOS4]

6. 按一下 [腳本中**常用檢視控制器**上，並確定和**自訂類別**及**腳本識別碼**有**homeViewController**身分識別檢查下。

7. 針對執行相同的圖像檢視控制器為**imageViewController**。

8. 然後，建立新的檢視控制器類別標題**imageViewController**處理您剛剛建立的使用者介面。

9. 在**imageViewController.h**，新增下列控制站的介面宣告。 請確定控制項拖曳從腳本圖像連結兩個這些屬性︰

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. 在**imageViewController.m**，新增下列**viewDidload**結尾︰

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. 在**AppDelegate.m**，匯入您建立的圖像控制站︰

        #import "imageViewController.h"

12. 新增包含下列宣告介面區段︰

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. 在 [ **AppDelegate**，請確定您的應用程式中登錄中的無訊息告知**應用程式︰ didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. 採取腳本在下列實作中的**應用程式︰ didRegisterForRemoteNotificationsWithDeviceToken** Subsitute UI 變更帳戶︰

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. 然後，若要從您的端點擷取圖像，並擷取完成後，傳送本機通知**AppDelegate.m**新增以下兩種方法。 請確定替代版面配置區`{backend endpoint}`與您的後端端點︰

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. 開啟 [設定圖像] 檢視中的控制站**AppDelegate.m**使用下列方法處理上方的本機通知︰

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>執行應用程式

1. 在 XCode，請實體 iOS 裝置 （推入通知無法在模擬器） 上執行應用程式。

2. 在 iOS 應用程式使用者介面，輸入使用者名稱和密碼的相同的值進行驗證，然後按一下 [**登入**。

3. 按一下 [**傳送推入**，您應該會看到的應用程式通知。 如果您按一下**更多**，您會連線至您選擇要包含在您的應用程式後端中的圖像。

4. 您也可以按一下**傳送推入**，立即按下您的裝置的 [首頁] 按鈕。 在幾分鐘的時間，您會收到推入通知。 如果您在其上點選或按一下 [更多，您會連線到您的應用程式和豐富的圖像內容。


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
