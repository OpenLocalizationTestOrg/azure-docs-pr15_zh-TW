<properties
    pageTitle="Azure AD v2.0 iOS 應用程式 |Microsoft Azure"
    description="如何建立 iOS 應用程式的登入兩個個人的 Microsoft 帳戶的使用者，以及使用協力廠商文件庫的公司或學校帳戶。"
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>登入加入 iOS 應用程式使用 v2.0 端點的圖形 API 搭配使用協力廠商的文件庫

Microsoft 身分識別平台採用開啟的標準，例如 OAuth2 和 OpenID 連線。 開發人員可以使用他們想要與我們的服務整合任何文件的庫。 若要協助開發人員使用我們的平台與其他文件庫，我們已撰寫如下所示，示範如何以設定連線至 Microsoft 身分識別平台的協力廠商文件庫的幾個逐步解說。 實作[RFC6749 OAuth2 規格](https://tools.ietf.org/html/rfc6749)的大部分文件庫可以連線至 Microsoft 身分識別平台。

此逐步解說所建立的應用程式，使用者可以登入組織，然後搜尋其他人組織中使用圖表 API。

如果您是新 OAuth2 或 OpenID 連線，此範例設定的可能無法對您有意義。 我們建議您閱讀[v2.0 通訊協定-OAuth 2.0 授權的程式碼流程](active-directory-v2-protocols-oauth-code.md)背景。


> [AZURE.NOTE]
    我們的平台的運算式有 OAuth2 或 OpenID 連線標準，例如條件存取和 Intune 原則管理] 中的某些功能需要您使用我們開啟來源 Microsoft Azure 身分識別文件庫。

不支援 v2.0 端點，所有的 Azure Active Directory 案例和功能。

> [AZURE.NOTE]
    若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="download-code-from-github"></a>從 GitHub 下載的程式碼
本教學課程中的程式碼是來維護[GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)或複製基本架構︰

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

您也可以下載的範例，而且立刻開始︰

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>登錄應用程式
在[應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中，建立新的應用程式，或依照[如何註冊與 v2.0 端點應用程式](active-directory-v2-app-registration.md)的詳細的步驟。  請確定︰

- 複製**應用程式識別碼**，因為您將需要推出指派給您的應用程式。
- 新增您的應用程式的**行動**平台。
- 複製入口網站**重新導向 URI** 。 您必須使用的預設值`urn:ietf:wg:oauth:2.0:oob`。


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>下載協力廠商 NXOAuth2 文件庫，並建立工作區

在此逐步解說，您會使用 OAuth2Client 從 GitHub，也就是 Mac OS X 和 iOS （Cocoa 及 Cocoa 觸控） OAuth2 文件庫。 此文件庫根據 OAuth2 規格草稿 10。 它會實作原生應用程式的設定檔，而且支援使用者的授權端點。 這些是您需要整合與 Microsoft 身分識別平台的所有項目。

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>使用 CocoaPods 將文件庫新增至您的專案

CocoaPods 是 Xcode 專案的相依性管理員。 它會自動管理先前安裝的步驟。

```
$ vi Podfile
```
1. 新增此 podfile 下列動作︰

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. 使用 CocoaPods 載入 podfile。 這會建立新的 Xcode 工作區會載入。

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>探索專案的結構

下列結構設定中的基本架構我們專案︰

- 使用 UPN 搜尋 [母片] 檢視
- 所選使用者的相關資料的詳細資料檢視
- 登入檢視，使用者可以登入應用程式來查詢

若要新增驗證，我們會移到基本架構中各種不同的檔案。 其他部分的程式碼，例如視覺的程式碼，不屬於身分識別，但會提供。

## <a name="set-up-the-settingsplst-file-in-the-library"></a>設定文件庫中的 settings.plst 檔案

-   在 [快速入門] 專案中，開啟`settings.plist`檔案。 取代的值，以反映在 Azure 入口網站中所使用的值] 區段中的項目。 使用 Active Directory 驗證庫時，您的程式碼會參考這些值。
    -   `clientId`是您所複製的入口網站應用程式的用戶端識別碼。
    -   `redirectUri`是入口網站提供重新導向 URL。

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>設定您的 LoginViewController NXOAuth2Client 文件庫

NXOAuth2Client 文件庫需要一些設定的值。 完成工作之後，您可以使用取得的權杖撥打圖形 API。 因為`LoginView`會的隨時我們需要驗證，稱為這麼做，將該檔案中的設定值。

- 讓我們來新增一些值，以`LoginViewController.m`要設定的內容驗證與授權檔案。 值的詳細資料，請依照下列程式碼。

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

讓我們來看看程式碼的詳細資料。

第一個字串適用於`scopes`。  `User.Read`值可讓您瞭解基本的登入的使用者設定檔。

您可以進一步瞭解所有可用的領域，在[Microsoft Graph 權限的範圍](https://graph.microsoft.io/docs/authorization/permission_scopes)。

針對`authURL`， `loginURL`， `bhh`，及`tokenURL`，您應該使用先前所提供的值。 如果您使用的開啟來源 Microsoft Azure 身分識別文件庫，我們下拉式功能表此資料為您使用我們的中繼資料端點。 我們已完成擷取這些值為您的工作。

`keychain`該值 NXOAuth2Client 文件庫會用來建立以儲存您的權杖鑰匙圈容器。 如果您想要跨多個應用程式取得單一登入 (SSO)，您可以指定相同的鑰匙圈中每個應用程式，並要求使用該鑰匙圈中您 Xcode 權利。 這是 Apple 文件中說明。

這些值的其餘部分所需使用文件庫，並建立您要執行的內容的值的位置。

### <a name="create-a-url-cache"></a>建立 URL 的快取

內`(void)viewDidLoad()`，其永遠稱為載入檢視後，下列程式碼做我們使用快取。

新增下列程式碼︰

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>建立用於登入網頁檢視

網頁檢視可以 （如果有設定） 會提示使用者輸入文字的簡訊的其他因素，或在傳回的錯誤訊息給使用者。 您可以在這裡設定設定網頁檢視]，然後稍後撰寫程式碼，處理回撥會發生這種情形的身分識別服務的網頁檢視。

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>覆寫處理驗證的網頁檢視方法

若要判斷網頁檢視當使用者需要登入，先前所述，會發生什麼情況，您可以貼上下列程式碼。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>撰寫程式碼來處理 OAuth2 要求的結果

下列程式碼會處理從網頁檢視傳回 redirectURL。 如果驗證不成功，程式碼會再試一次。 同時，文件庫會提供您可以看到主控台中檢視或非同步處理的錯誤。

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>設定 OAuth 內容 （稱為帳戶存放區）

您可以在這裡呼叫`-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]`共用的帳戶存放區，您要的應用程式可存取的每個服務上。 帳戶類型是字串，做為識別項用於特定服務。 因為您存取圖形 API，程式碼代表為`"myGraphService"`。 然後設定會告訴您任何項目會變更與權杖觀察者。 取得的權杖之後，您傳回使用者返回`masterView`。



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>設定搜尋及顯示圖表 API 的使用者的 [母片] 檢視

會傳回的資料顯示在格線的母片檢視控制器 (MVC) 應用程式限制，超出此逐步解說，範圍，而許多線上教學課程說明如何建立一個。 所有此程式碼是在基本架構的檔案。 不過，您需要處理此 MVC 應用程式中的一些事項︰

* 當使用者內容類型的搜尋欄位中的截距
* 回到 MasterView 提供資料的物件，讓它可以在格線中顯示結果

我們會執行這些下方。

### <a name="add-a-check-to-see-if-youre-logged-in"></a>新增檢查，請參閱是否您登入

應用程式沒有什麼如果使用者未登入，因此智慧檢查是否已權杖快取中。 如果沒有出現，您重新導向至 LoginView 使用者登入。 如果您重新叫用，檢視載入時採取動作的最佳方式是使用`viewDidLoad()`Apple 提供我們的方法。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>收到資料時，更新 [表格] 檢視

當圖形 API 傳回的資料時，您需要顯示的資料。 為求，以下是更新表格的所有程式碼。 您只可以在 MVC 重複使用程式碼貼正確的值。

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>提供當其他人的搜尋欄位中輸入時，呼叫圖形 API

當使用者在] 方塊中輸入搜尋時，必須先圖形 api 急需的。 `GraphAPICaller`類別，您會建立下列程式碼，從簡報分隔查閱功能。 現在，我們要撰寫摘要圖表 API 搜尋的任何字元的代碼。 我們來提供稱為方法執行這項`lookupInGraph`，其所需的時間，我們要搜尋的字串。

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>撰寫協助類別圖形 API 的存取

這是我們的應用程式的核心。 而其餘所插入程式碼，從 Apple 預設 MVC 模式中，在這裡您撰寫程式碼查詢為使用者類型，然後再將該資料。 以下程式碼，以及它後面的詳細的說明。

### <a name="create-a-new-objective-c-header-file"></a>建立新的目標 C 頁首檔案

為檔案命名`GraphAPICaller.h`，並新增下列程式碼。

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

以下您看到指定的方法是一個字串，並傳回 completionBlock。 此 completionBlock，當您可能會想，會更新表格提供物件即時填入資料，為使用者進行搜尋。


### <a name="create-a-new-objective-c-file"></a>建立新的目標 C 檔案

為檔案命名`GraphAPICaller.m`，並新增下列方法。

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

我們幫助這個方法的詳細資料。

將此程式碼的核心位於`NXOAuth2Request`，採用參數的方法您已經定義 settings.plist 檔案中。

第一步是建構右圖形 API 通話。 因為您呼叫`/users`，您指定的附加至圖形 API 資源以及版本。 這麼做，因為這些能改變隨著 API 發展放置這些外部設定檔案中。


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

接下來，您需要指定參數，您也可以提供圖表 API 通話。 則*非常重要*的您不要將參數中資源端點因為的在執行階段的非 URI 合格字元在刪除。 查詢的所有程式碼，必須在參數中提供。

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

您可能會注意到此通話`convertParamsToDictionary`尚未撰寫的方法。 讓我們來執行，因此現在結尾的檔案︰

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
接下來，我們要使用`NXOAuth2Request`返回資料從 API JSON 格式的方法。

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

最後，我們來看看如何回到資料 MasterViewController。 資料會傳回為序列化，而且必須序列化和載入 MainViewController 可使用的物件。 達到這個目的，具有基本架構`User.m/h`建立使用者物件的檔案。 您填入該使用者物件，該圖表中的資訊。

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>執行範例

如果您已使用基本架構，或追蹤以及您的應用程式現在應該執行的逐步解說。 啟動模擬，然後按一下 [使用應用程式的 [**登入**]。

## <a name="get-security-updates-for-our-product"></a>取得產品的安全性更新

我們建議您收到通知，造訪[安全性技術中心](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
