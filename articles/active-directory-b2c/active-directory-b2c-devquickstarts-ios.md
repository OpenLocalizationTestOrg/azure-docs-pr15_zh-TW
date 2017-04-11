<properties
    pageTitle="Azure Active Directory B2C: IOS 應用程式使用協力廠商文件庫呼叫網路 API |Microsoft Azure"
    description="本文會告訴您如何建立使用 OAuth 2.0 承載者權杖使用協力廠商的文件庫中呼叫 Node.js 網路 API iOS 「 待辦事項清單 」 應用程式"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< 標籤 ms.service= 「 作用中的目錄-b2c 」 ms.workload="identity 「 ms.tgt_pltfrm="na 」 ms.devlang="objectivec 「 ms.topic= 」 示範用文件 」

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C: IOS 應用程式使用協力廠商的文件庫呼叫網路 API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsoft 身分識別平台採用開啟的標準，例如 OAuth2 和 OpenID 連線。 這個選項可讓開發人員運用他們想要與我們的服務整合任何文件庫。 若要協助開發人員使用我們的平台與其他文件庫中，我們已撰寫如下所示以 demonstate 的幾個逐步解說如何設定連線至 Microsoft 身分識別平台的協力廠商文件庫。 實作[RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749)的大部分文件庫無法連線至 Microsoft 身分識別的平台。


如果您是新 OAuth2 或 OpenID 連線的此範例設定可能太合理給您。 我們建議您查看簡短的[概觀，我們已在此說明的通訊協定](active-directory-b2c-reference-protocols.md)。

> [AZURE.NOTE]
    我們的平台的運算式中這些標準，例如條件存取和 Intune 原則管理] 功能會要求您使用我們開啟來源 Microsoft Azure 身分識別文件庫。 
   
B2C 平台支援並非所有的 Azure Active Directory 案例與功能。  若要判斷是否您應該使用 B2C 平台，請閱讀有關[B2C 限制](active-directory-b2c-limitations.md)。


## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。 目錄是針對您所有的使用者、 應用程式、 群組及更多的容器。 如果沒有帳戶，請[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄中的應用程式。 這會提供 Azure AD 資訊，以便與您的應用程式安全地溝通。 應用程式，然後網路 API 會以單一**應用程式識別碼**在此情況下，因為它們組成一個邏輯的應用程式。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。 請務必︰

- 在應用程式，包括在**行動裝置**。
- 複製**應用程式識別碼**指派給您的應用程式。 您也必須此更新版本。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 此應用程式包含一個身分識別體驗︰ 合併的登入，註冊。 您需要建立的每個類型，此原則[原則參考本文](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。 當您建立的原則時，請務必︰

- 在您的原則，選擇 [**顯示名稱**] 和 [註冊的屬性。
- 您可以選擇 [**顯示名稱**] 和 [**物件識別碼**的應用程式宣告每個原則。 您可以選擇以及其他宣告。
- 建立後，請複製每個原則**名稱**。 應有前置詞`b2c_1_`。  您必須在原則名稱後面。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的原則之後，您可以準備開始建立您的應用程式。


## <a name="download-the-code"></a>下載的程式碼

本教學課程中的程式碼是來維護[GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)。  若要依照，您可以[下載應用程式為.zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip） 或複製︰

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

或只要下載已完成的程式碼，然後立刻開始︰ 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>下載協力廠商文件庫 nxoauth2 並啟動工作區

在此逐步解說，我們將使用從 GitHub，OAuth2 文件庫的 Mac OS X （Cocoa 與 Cocoa 觸控） 的 iOS OAuth2Client。 此文件庫根據 OAuth2 規格草稿 10。 它會實作原生應用程式的設定檔，而且支援的使用者授權端點。 以下是我們需要 integrat 與 Microsoft 身分識別的平台的順序的所有項目。

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>將文件庫新增至專案使用 CocoaPods

CocoaPods 是 Xcode 專案的相依性管理員。 它會自動管理上述安裝步驟。

```
$ vi Podfile
```
新增此 podfile 下列動作︰

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

現在載入使用 cocoapods podfile。 這會建立新的 XCode 工作區，您將會載入。

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>專案的結構

我們還有設定中的基本架構我們專案的結構如下︰

* **母片] 檢視**的工作窗格
* **新增任務檢視**所選工作相關的資料
* **登入檢視**可讓使用者登入應用程式。

我們會跳中要新增驗證的專案中的各種檔案。 例如視覺化程式碼的程式碼的其他部分密切關係身分識別並不會提供。

## <a name="create-the-settingsplist-file-for-your-application"></a>建立`settings.plist`應用程式的檔案

輕鬆設定應用程式，如有集中的位置，以將我們的設定值。 也可協助您瞭解每項設定會在您的應用程式。 我們會運用*屬性的清單*，以提供這些應用程式的值。

* 建立/開啟`settings.plist`檔案下`Supporting Files`應用程式工作區中

* 輸入下列的值 （我們會移到他們的詳細資料推出）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

我們在移至這些的詳細資料。


針對`authURL`， `loginURL`， `bhh`，`tokenURL`您會發現您需要填入您的租用戶的名稱。 這是您已指派給您的 B2C 租用戶的租用戶名稱。 例如， `kidventusb2c.onmicrosoft.com`。如果您是使用 Microsoft Azure 身分識別文件庫我們開啟來源我們想下拉式功能表此資料您可以使用我們的中繼資料端點。 我們已完成擷取這些值為您的工作。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

`keychain`該值 NXOAuth2Client 文件庫會用來建立以儲存您的權杖鑰匙圈容器。 如果您想要跨多個應用程式取得 SSO 您可以指定相同的鑰匙圈中每個應用程式，以及您 XCode entitements 中要求的鑰匙圈使用。 這被涵蓋的 Apple 文件。

`<policy name>`結尾的每個 URL 是您想要的位置的地方，將您先前所建立的原則。 應用程式會呼叫流程根據下列原則。

`taskAPI`是其餘端點稱與您 B2C 權杖，先將新增工作] 或 [查詢現有的任務。 此設定為此範例。 您不需要變更為範例，才能運作。

這些值的其餘部分所需使用文件庫，只要建立位置，讓您執行的內容的值。

現在有`settings.plist`所建立的檔案，我們需要讀取程式碼。

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>閱讀我們的設定，設定 AppData 課程

現在讓我們建立一個簡單的檔案，只要剖析我們`settngs.plist`我們先前所建立，並對任何類別進行這些設定 avaialble 在未來的檔案。 因為我們不想要建立新複本的資料，每次課程要求時，我們會使用單一模式，並只傳回要求，設定每次建立相同的執行個體

* 建立`AppData.h`檔案︰

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* 建立`AppData.m`檔案︰

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

現在我們可以輕鬆地在我們的資料，只要呼叫`  AppData *data = [AppData getInstance];`任何我們為您的類別中看到的下方。



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>設定您的 AppDelegate NXOAuth2Client 文件庫

NXOAuthClient 文件庫需要一些設定的值。 完成後，便可以使用是 aquired 撥打 REST API 的 token。 因為我們知道`AppDelegate`會被稱為任何我們載入應用程式的時間這麼做我們將在我們的設定值放在該檔案。
* 開啟`AppDelegate.m`檔案

* 我們會在稍後使用一些頁首檔案匯入。

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* 新增`setupOAuth2AccountStore`AppDelegate 中的方法

我們需要建立 AccountStore，然後摘要的資料，我們只從讀取的`settings.plist`檔案。

有您應該知道的相關的 B2C 服務此時會讓此程式碼更容易理解的一些事項︰


1. Azure AD B2C 使用*原則*所提供的查詢參數服務要求。 這個選項可讓 Azure Active Directory 作為獨立的服務，只針對您的應用程式。 若要提供這些額外的查詢的參數必須提供`kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:`我們自訂原則參數的方法。 

2. Azure AD B2C 使用範圍中許多相同的方式作為其他 OAuth2 伺服器。 不過由於 B2C 使用太多有關驗證的使用者存取資源某些範圍是絕對必要正確工作流程的順序。 這是`openid`範圍。 Sdk 自動提供我們 Microsoft 身分識別`openid`，如此您就不會看到的我們 SDK 設定中的範圍。 不過，因為我們使用協力廠商的文件庫，我們需要指定此範圍。

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
接下來，請確定您呼叫中下 AppDelegate`didFinishLaunchingWithOptions:`方法。 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>建立`LoginViewController`類別，我們將用來處理驗證要求

我們可以使用的帳戶登入網頁檢視。 這個選項可讓我們 （如果有設定） 會提示使用者輸入文字的簡訊的其他因素，或回提供給使用者的錯誤訊息。 以下我們將會設定網頁檢視]，然後稍後撰寫程式碼，處理 Microsoft 身分識別服務網頁檢視中時會發生的回撥。

* 建立`LoginViewController.h`類別

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

我們會建立下列這些方法。

> [AZURE.NOTE] 
    請確定您繫結`loginView`至位於 [腳本實際網頁檢視。 否則，您不需要驗證時可以顯示網頁檢視。

* 建立`LoginViewController.m`類別

* 新增一些變數執行狀態，因為我們驗證

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* 覆寫處理驗證的網頁檢視方法

必須告知網頁檢視我們希望當使用者登入如上文所需要的行為。 您只可以剪下並貼上下列的程式碼。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* 撰寫程式碼來處理 OAuth2 要求的結果

我們需要將處理來自網頁檢視 redirectURL 的程式碼。 如果不是成功，我們會再試一次。 同時文件庫會提供您可以看到主控台中檢視或處理 asyncronously 錯誤。 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* 設定通知工廠。

我們建立相同的方法，我們在`AppDelegate`上方，但這次，將部分`NSNotification`s 告訴我們什麼新鮮事在我們的服務。 我們設定會告訴我們有任何變更與權杖的當觀察者。 一旦得到權杖我們會傳回使用者回到`masterView`。



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* 新增處理使用者，每當要求啟動時登原生的程式碼

現在就讓我們建立會在每次的要求，驗證呼叫的方法。 這將成為實際建立網頁檢視的方法

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* 最後，讓我們來呼叫所有上方每次寫這些方法`LoginViewController`載入。 我們來新增這些方法來執行這項我們`viewDidLoad`方法 Apple 會提供我們

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

您現在已完成建立主登入應用程式會互動的方式。 我們已登入之後，我們需要使用我們收到我們權杖。 為該我們要建立將通話 REST Api，我們使用此文件庫的部分協助程式碼。


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>建立`GraphAPICaller`類別來處理 REST API 我們要求

我們已載入每次我們載入我們應用程式的設定。 現在，我們需要進行一旦權杖。 

* 建立`GraphAPICaller.h`檔案

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

您看到的內容將此程式碼，我們會建立兩種方法︰ 一是從 API，另一個新增工作至 API 取得工作。

現在，我們已設定好我們的介面，讓我們來新增實際實作︰

* 建立`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>執行範例應用程式

最後，建立並執行應用程式中 Xcode。 註冊或登入應用程式，並建立登入使用者的工作。 登出並重新登入以不同的使用者，然後針對該使用者建立工作。

請注意任務會儲存每位使用者在 API，因為 API 收到存取權杖算起的使用者身分識別。


## <a name="next-steps"></a>後續步驟

您現在可以移動至 [更多進階 B2C 主題。 您可以嘗試︰

[通話 Node.js 網路 API 從 Node.js web 應用程式]()

[自訂 UX B2C 應用程式]()
