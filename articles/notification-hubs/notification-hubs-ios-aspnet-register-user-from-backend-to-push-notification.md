<properties
    pageTitle="使用 Web API 登錄推播通知目前使用者 |Microsoft Azure"
    description="瞭解如何 registeration ASP.NET Web api 執行時要求推入通知註冊 Azure 通知集線器 iOS 應用程式中。"
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
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>使用 ASP.NET 登錄目前使用者推播通知

> [AZURE.SELECTOR]
- [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>概觀

本主題說明如何註冊 ASP.NET Web api 執行時要求推入通知註冊 Azure 通知集線器使用。 本主題將延伸教學課程[通知集線器通知使用者]。 您必須已經完成所需的步驟來建立經過驗證的行動訊息服務的教學課程中。 通知使用者案例的詳細資訊，請參閱[使用通知集線器通知使用者]。

##<a name="update-your-app"></a>更新您的應用程式  

1. 在您的 MainStoryboard_iPhone.storyboard 新增物件程式庫下列元件︰

    + **標籤**: 「 推入通知集線器與使用者 「
    + **標籤**: 「 InstallationId 」
    + **標籤**: 「 使用者 」
    + **文字欄位**: 「 使用者 」
    + **標籤**: 「 密碼 」
    + **文字欄位**: 「 密碼 」
    + **] 按鈕**: [登入]

    此時，您的腳本類似下列所示︰

    ![][0]

2. 在小幫手] 編輯器中，建立所有的切換控制項插座及撥打電話給、 [文字] 欄位與連線檢視控制器 （代理人），並建立**登入**] 按鈕的**動作**。

    ![][1]

    BreakingNewsViewController.h 檔案現在應該會包含下列程式碼︰

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. 建立一個名為**DeviceInfo**，然後將下列程式碼複製到檔案 DeviceInfo.h 介面一部分︰

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. DeviceInfo.m 檔案實作一節中複製下列程式碼︰

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. 在 PushToUserAppDelegate.h，新增下列屬性單一︰

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. 在**didFinishLaunchingWithOptions**方法 PushToUserAppDelegate.m 中，新增下列程式碼︰

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    第一行初始化**DeviceInfo**單一物件。 第二行開始註冊，推入通知，已呈現是您已完成[快速入門通知集線器]教學課程。

9. 在 PushToUserAppDelegate.m，在您 AppDelegate 實作方法**didRegisterForRemoteNotificationsWithDeviceToken** ，新增下列程式碼︰

        self.deviceInfo.deviceToken = deviceToken;

    這會設定為要求的裝置 token。

    > [AZURE.NOTE] 此時，不應該有任何其他程式碼在這個方法。 如果您已經有**registerNativeWithDeviceToken**方法當您完成[快速入門通知集線器](/manage/services/notification-hubs/get-started-notification-hubs-ios/)教學課程所新增的通話時，您必須註解出或移除的通話。

10. 在 [PushToUserAppDelegate.m 檔案，新增下列處理常式方法︰

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     在執行中時，您的應用程式會收到通知時，此方法會顯示通知 ui 上。

9. 開啟 PushToUserViewController.m 檔案，並傳回鍵盤下列實作中︰

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. 在**viewDidLoad**方法 PushToUserViewController.m 檔案中，初始化 installationId 標籤如下所示︰

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. 在 PushToUserViewController.m 介面中新增下列屬性︰

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. 然後，新增下列實作︰

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. 下列程式碼，複製到 XCode 所建立的**登入**處理常式方法︰

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    這種方式取得同時安裝識別碼和頻道推入通知並傳送，裝置類型，以及經過驗證的 Web API 方法通知集線器中所建立，註冊。 此網站的 API 被定義在[通知集線器通知使用者]。

現在已更新的用戶端應用程式，返回[通知使用者，通知集線器，]並使用通知集線器傳送通知行動訊息服務的更新。

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[向使用者通知集線器通知]: /manage/services/notification-hubs/notify-users-aspnet

[通知集線器快速入門]: /manage/services/notification-hubs/get-started-notification-hubs-ios
