<properties
    pageTitle="傳送推入通知，可在 iOS 上使用 Azure 通知集線器 |Microsoft Azure"
    description="在本教學課程中，您可以瞭解如何使用 Azure 通知集線器 iOS 應用程式傳送推入通知。"
    services="notification-hubs"
    documentationCenter="ios"
    keywords="推播通知，推入通知，ios 推播通知"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>可在 iOS 上使用 Azure 通知集線器傳送推入通知

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>概觀

> [AZURE.NOTE] 若要完成此教學課程中，您必須使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)。

本教學課程教您如何使用 Azure 通知集線器推入通知傳送至 iOS 應用程式。 您會建立空白的 iOS 應用程式會使用[Apple 推入通知服務 (Apn)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)接收推入通知。 

完成後，您可以使用您的通知中心廣播執行您的應用程式的所有裝置的推播通知。

## <a name="before-you-begin"></a>開始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

已完成的程式碼，在此教學課程位於[GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)。 

##<a name="prerequisites"></a>必要條件

本教學課程的需求如下︰

+ [行動電話服務 iOS SDK 版本 1.2.4]
+ 最新版本的[Xcode]
+ IOS 8 （或更新版本）-簡訊裝置
+ [Apple 開發人員程式](https://developer.apple.com/programs/)成員資格。

   > [AZURE.NOTE] 由於推入通知設定需求，您必須部署，並測試實體 iOS 裝置 （iPhone 或 iPad） 上的推入通知，而不是 iOS 模擬器。

完成此教學課程是所有其他通知集線器教學課程 iOS 應用程式的必要條件。

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>設定您的通知中心的 iOS 推播通知

本節會引導您建立新的通知中心，並以使用您所建立的**.p12**推播憑證的 APN 設定驗證。 如果您想要使用您已經建立通知中心，您可以跳至步驟 5。

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>按一下 [<b>設定</b>刀中，[<b>通知服務</b>] 按鈕，然後選取 [ <b>Apple (APNS)</b>。 按一下 [<b>上傳</b>的憑證，然後選取您之前匯出<b>.p12</b>檔案。 請確定您也可以指定正確的密碼。</p>
<p>請務必選取<b>沙箱</b>模式，因為這是開發。 如果您想要傳送推入通知，購買應用程式存放區中的使用者，只使用<b>生產</b>。</p>
</li>
</ol>
&emsp;&emsp;![設定 APN Azure 入口網站中](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Azure 入口網站中設定 APN 憑證](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



您的通知中心正在設定以使用 APNS，且您擁有註冊您的應用程式，並傳送推入通知的連線字串。

##<a name="connect-your-ios-app-to-notification-hubs"></a>IOS 應用程式連線到通知

1. 在 Xcode，建立新的 iOS 專案並選取 [**單一檢視應用程式**範本。

    ![Xcode-單一檢視應用程式][8]

2. 時設定新專案的選項，請務必使用相同的 [**產品名稱**] 和 [**組織識別碼**您先前設定的套件識別碼 Apple 開發人員入口網站上時所使用。

    ![Xcode-專案選項][11]

3. 在**目標**] 之下按一下專案名稱**建立設定**] 索引標籤和展開的**登入身分識別程式碼**，，然後按一下然後在 [**偵錯**，設定您的程式碼簽署身分識別。 切換**層級**從**基本****所有**，並設定**佈建的設定檔**至提供您先前建立的設定檔。

    如果您沒有看到您在 Xcode 中建立新佈建設定檔，請嘗試重新整理您簽署的身分識別的設定檔。 按一下功能表列上的 [ **Xcode** 、 按一下 [**喜好設定**，按一下 [**帳戶**] 索引標籤、 按一下 [**檢視詳細資料**] 按鈕、 按一下您簽章的身分識別，然後按一下右下角的 [重新整理] 按鈕。

    ![Xcode-佈建的設定檔][9]

4. 下載[行動服務 iOS SDK 版本 1.2.4] ，並將它解壓縮檔案。 在 Xcode，以滑鼠右鍵按一下您的專案，按一下 [**新增檔案**選項，來新增**WindowsAzureMessaging.framework**資料夾至 Xcode 專案。 選取 [**複製的項目，如有需要**然後再按一下 [**新增**]。

    >[AZURE.NOTE] 通知集線器 SDK 目前不支援 bitcode Xcode 7 上。  您必須設定為**建立選項**中的 [**無**的**啟用 Bitcode** ，為您的專案。

    ![解壓縮 Azure SDK][10]

5. 名為您專案中新增新的標頭檔`HubInfo.h`。 此檔案會保留通知中心的常數。  新增下列定義並字串文字版面配置區以取代您的*中心名稱*和先前所述*DefaultListenSharedAccessSignature* 。

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. 開啟您`AppDelegate.h`檔案新增下列匯入指示詞︰

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. 在您`AppDelegate.m file`，新增下列程式碼中的`didFinishLaunchingWithOptions`方法會根據您的 iOS 版本。 將此程式碼會使用 APNs 註冊您的裝置控點︰

    針對 iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    IOS 8 之前的版本︰

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. 在相同的檔案，新增下列方法。 將此程式碼會連線到 [通知] 中心上使用您在 HubInfo.h 所指定的連線資訊。 然後可讓裝置權杖通知中心，好讓 [通知] 中心內可以傳送通知︰

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. 在相同的檔案，新增下列方法以顯示**UIAlert** ，如果應用程式時，會收到通知︰


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. 建立並執行應用程式，在您的裝置，請確認有無失敗。

## <a name="send-test-push-notifications"></a>傳送測試推入通知


您可以測試您的應用程式中收到通知，藉由傳送推播通知在[Azure 入口網站]透過 [**疑難排解**] 區段中中心刀 （使用*測試傳送*選項）。

![Azure 入口網站-測試傳送][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>（選用）從應用程式傳送推入通知

>[AZURE.IMPORTANT] 從用戶端應用程式傳送通知此範例是針對學習僅供提供。 因為這會要求`DefaultFullSharedAccessSignature`要出現在用戶端應用程式，它公開您的使用者存取傳送未經授權的通知給您的用戶端風險的通知中心。

如果您想要傳送的應用程式中的推入通知，本節會提供如何使用其餘介面的範例。

1. 在 [Xcode，開啟 [ `Main.storyboard` ，並從 [允許使用者傳送推入通知的應用程式中的物件程式庫新增下列使用者介面元件︰

    - 有任何標籤文字的標籤。 將會用於中傳送通知的報表錯誤。 [**線條**] 屬性應該設定為**0** ，讓它會自動大小限制為右邊左右的邊界和檢視的頂端。
    - 文字**版面配置區**文字設定為的欄位**輸入通知訊息**。 限制 [標籤] 正下方] 欄位，如下所示。 設定檢視控制器插座代理人。
    - 標題為**傳送的通知**] 按鈕，限制正下方的文字欄位，然後在 [水平置中。

    檢視應如下所示︰

    ![Xcode 設計工具][32]


2. [新增插座](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)標籤和文字方塊欄位連接您的檢視及更新您`interface`定義，以支援`UITextFieldDelegate`和`NSXMLParserDelegate`。 新增三個屬性宣告，協助支援撥打 REST API 及剖析回應所示。

    ViewController.h 檔案應如下所示︰

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. 開啟`HubInfo.h`並新增下列常數，將會使用傳送至您的中心的通知。 取代您實際*DefaultFullSharedAccessSignature*連線字串中的版面配置區字串常值。

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. 新增下列`#import`陳述式以您`ViewController.h`檔案。

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. 在 [`ViewController.m`介面實作中加入下列程式碼。 將此程式碼會剖析*DefaultFullSharedAccessSignature*連接字串。 如[REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx)中所述，此剖析的資訊會用來產生的**授權**要求標頭的 Sa 權杖。

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. 在 [ `ViewController.m`，更新`viewDidLoad`方法檢視載入時剖析的連接字串。 也新增介面實作下方，顯示公用程式方法。  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. 在 [ `ViewController.m`，將下列程式碼新增至介面實作產生**授權**標頭中，將提供 Sa 授權權杖，如[REST API 參考](http://msdn.microsoft.com/library/azure/dn495627.aspx)中所述。

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. Ctrl + 拖曳從**傳送通知**] 按鈕以`ViewController.m`新增**向下觸控**事件命名**SendNotificationMessage**動作。 使用下列程式碼，傳送通知使用 REST API 來更新方法。

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. 在 [ `ViewController.m`，新增下列委派方法支援關閉鍵盤的 [文字] 欄位。 Ctrl + 拖曳從 [文字] 欄位來設定檢視控制器插座代理人身分介面設計工具中的 [檢視控制器] 圖示。

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. 在 [ `ViewController.m`，新增下列代理人方法支援使用剖析回應`NSXMLParser`。

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. 建立專案，並確認沒有錯誤。


> [AZURE.NOTE] 如果您遇到關於 bitcode 支援 Xcode7 中的建置錯誤，您應該變更**建置設定** > **啟用 Bitcode (ENABLE_BITCODE)**為 [**無**Xcode 中。 通知集線器 SDK 目前不支援 bitcode。 

您可以找到所有可能的通知裝載在 Apple[本機和推播通知程式設計指南]。


##<a name="checking-if-your-app-can-receive-push-notifications"></a>檢查您的應用程式是否可接收推入通知

若要測試 iOS 上的推入通知，您必須在實體 iOS 裝置中部署應用程式。 您無法使用 iOS 模擬器傳送 Apple 推播通知。

1. 執行應用程式，並確認註冊順利完成，然後按**[確定]**。

    ![iOS 應用程式推播通知註冊測試][33]

2. 如上所述，您可以從[Azure 入口網站]，傳送測試推入通知。 如果您新增的應用程式中傳送推入通知程式碼，輕觸內輸入通知訊息的 [文字] 欄位。 在鍵盤或在檢視傳送通知訊息中的**傳送通知**] 按鈕，然後按下 [**傳送**] 按鈕。

    ![iOS 應用程式推播通知傳送測試][34]

3. 推入通知會傳送至已註冊要收到通知，從特定的通知中心的所有裝置。

    ![iOS 應用程式推播通知接收測試][35]


##<a name="next-steps"></a>後續步驟

在此範例中，您推入通知傳送到該網域所有已註冊的 iOS 裝置。 我們建議您繼續[Azure 通知集線器通知使用者使用.NET 後端 iOS 版]教學課程，會逐步引導您完成建立要傳送使用標籤推入通知後端您學習中的下一個步驟。 

如果您想要區段您感興趣的群組的使用者，您可以此外移至[使用通知集線器傳送相關消息]教學課程。 

如需通知集線器的一般資訊，請參閱[通知集線器指引]。



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[行動電話服務 iOS SDK 版本 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[通知集線器指南]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure 通知集線器通知使用者使用.NET 後端 iOS 版]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[使用通知集線器傳送相關消息]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[本機和推播通知程式設計指南]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure 入口網站]: https://portal.azure.com