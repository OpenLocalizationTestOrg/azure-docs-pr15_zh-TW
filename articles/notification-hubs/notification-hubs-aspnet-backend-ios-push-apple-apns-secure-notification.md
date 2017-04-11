<properties
    pageTitle="推播通知 azure 集線器安全"
    description="瞭解如何從 Azure 傳送至 iOS 應用程式的安全的推播通知。 撰寫目標 C 及 C# 程式碼範例。"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>推播通知 azure 集線器安全

> [AZURE.SELECTOR]
- [Windows 標準](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>概觀

Microsoft Azure 中的推入通知支援可讓您存取大幅簡化的行動裝置平台的同時消費者版和企業版應用程式的推播通知方便使用多平台、 縮放出推入基礎結構。

由於法規或安全性的限制式，有時候應用程式可能會想要併入項目無法傳送透過標準推入通知基礎結構的通知。 本教學課程說明如何藉由傳送透過安全、 已驗證的用戶端裝置與應用程式後端之間連線的機密資訊達成相同的體驗。

在高的層級，流程如下︰

1. 應用程式後端︰
    - 儲存安全後端資料庫中的內容。
    - （不安全的資訊會傳送） 的裝置傳送此通知的識別碼。
2. 在裝置上時收到通知, 應用程式︰
    - 裝置連絡人後端要求安全的內容。
    - 應用程式通知，以在裝置上的方式顯示的內容。

請務必請注意，上述 flow 中 （和在本教學課程），我們假設的裝置會儲存驗證權杖在本機的儲存空間，在使用者登之後。 這可以確保完全順暢的使用體驗，如裝置可擷取的通知安全的內容，使用此 token 加。 如果您的應用程式不會儲存在裝置上，驗證的權杖，或如果可以是這些權杖，裝置應用程式時，在收到通知，應該顯示一般通知提示使用者以啟動應用程式。 然後，應用程式授權的使用者，並顯示通知內容。

本安全推入教學課程中會顯示如何安全地傳送推入通知。 教學課程是根據[通知使用者](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教學課程中，因此您應該先完成步驟在教學課程中的第一次。

> [AZURE.NOTE] 本教學課程假設您建立，以及[快速入門通知集線器 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md)所述設定您的通知中心。

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>修改 iOS 專案

現在，您修改您應用程式後端傳送只*識別碼*的通知，您必須變更控點的通知及撥] 以回以擷取的安全的訊息，以顯示您後端 iOS 應用程式。

若要達到這個目的，我們必須撰寫若要保護的內容擷取的應用程式後端邏輯。

1. 在**AppDelegate.m**，請確定無訊息的通知，讓它處理通知識別碼的應用程式登錄傳送後端。 新增 didFinishLaunchingWithOptions **UIRemoteNotificationTypeNewsstandContentAvailability**選項︰

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. 在您**AppDelegate.m**加入實作區段包含下列宣告頂端︰

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. 然後在實作] 區段中新增下列程式碼，取代版面配置區`{back-end endpoint}`與先前取得您後端的結束點︰

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. 現在我們來處理的內送的通知，並使用上述方法來擷取顯示的內容。 首先，我們都必須啟用接收推入通知時，在背景中執行 iOS 應用程式。 在**XCode**，選取您的應用程式專案，在 [左] 面板，然後按一下您主要的應用程式中的目標從中央窗格的 [**目標**] 區段。

5. 然後按一下頂端的中央窗格中，您**功能**] 索引標籤，然後核取 [**遠端通知**核取方塊。

    ![][IOS1]


6. 在**AppDelegate.m**中加入下列方法來處理推入通知︰

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    請注意，最好的後端處理遺失驗證標題屬性或拒絕的情況。 這種情況下的特定處理大多取決於您目標的使用者體驗。 其中一個選項是顯示通知和一般提示要驗證的使用者来擷取的實際的通知。

## <a name="run-the-application"></a>執行應用程式

若要執行應用程式，執行下列動作︰

1. 在 XCode，請實體 iOS 裝置 （推入通知無法在模擬器） 上執行應用程式。

2. 在 iOS 應用程式使用者介面，輸入使用者名稱和密碼。 這些可以是任何字串，但必須相同的值。

3. 在 iOS 應用程式使用者介面，按一下 [**登入**]。 然後按一下 [**傳送推入**]。 您應該會看到安全顯示在您的通知中心的通知。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
