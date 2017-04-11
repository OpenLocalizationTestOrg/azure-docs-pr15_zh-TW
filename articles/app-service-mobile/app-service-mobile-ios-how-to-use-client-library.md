<properties
    pageTitle="如何使用 iOS SDK Azure Mobile 應用程式"
    description="如何使用 iOS SDK Azure Mobile 應用程式"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>如何使用 iOS Azure Mobile 應用程式的用戶端文件庫

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南會指導您執行常見的案例使用最新的[Azure Mobile 應用程式在 iOS SDK][1]。 如果您是 Azure Mobile 應用程式的新項目，第一個完整[Azure 行動應用程式快速入門]建立後端，建立表格，並下載預先建立的 iOS Xcode 專案。 本指南中，我們著重於用戶端 iOS SDK。 若要進一步瞭解的後端伺服器端 SDK，請參閱伺服器 SDK HOWTOs。

## <a name="reference-documentation"></a>參照文件

IOS 用戶端 SDK 的參考文件的位置︰ [Azure Mobile 應用程式在 iOS 用戶端參照][2]。

## <a name="supported-platforms"></a>支援的平台

IOS SDK 支援 iOS 8.0 或更新版本的版本目標 C 專案、 快速 2.2 專案，以及快速 2.3 專案。

「 伺服器流程 「 驗證用於呈現的使用者介面的網頁檢視。  如果裝置無法展示網頁檢視的 UI，然後驗證的另一個方法是必要的超出產品的範圍。  
此 SDK 並因此不適用於監看型態或同樣在受限制的裝置。

##<a name="Setup"></a>設定與先決條件

本指南假設您已建立後端的資料表。 本指南假設表格這些教學課程中具有相同的結構描述的資料表。 本指南也假設的程式碼，您參照`MicrosoftAzureMobile.framework`及匯入`MicrosoftAzureMobile/MicrosoftAzureMobile.h`。

##<a name="create-client"></a>如何︰ 建立用戶端

若要存取您專案中的 Azure Mobile 應用程式後端，建立`MSClient`。 取代`AppUrl`使用應用程式的 URL。 您可以保留`gatewayURLString`和`applicationKey`空白。 如果您設定驗證閘道器，填入`gatewayURLString`閘道器 url。

**目標 C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**快速**︰

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>如何︰ 建立表格參照

Access 或更新的資料，建立後端表格的參照。 取代`TodoItem`的表格名稱

**目標 C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**快速**︰

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>如何︰ 查詢資料

若要建立資料庫查詢，查詢`MSTable`物件。 下列查詢取得所有項目`TodoItem`和記錄每個項目的文字。

**目標 C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**快速**︰

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>如何︰ 篩選傳回的資料

若要篩選的結果，有許多可用的選項。

若要篩選使用述詞，請使用`NSPredicate`和`readWithPredicate`。 下列篩選器會傳回資料以尋找完整 Todo 的項目。

**目標 C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**快速**︰

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>如何︰ 使用 MSQuery

執行複雜的查詢 （包括排序和分頁），請建立`MSQuery`物件、 直接或使用述詞︰

**目標 C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**快速**︰

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`可讓您控制多個查詢的行為。

* 指定結果的順序
* 限制傳回的欄位
* 限制傳回的記錄數目
* 指定回應的總字數統計
* 在邀請中指定自訂查詢字串參數
* 套用其他函數

執行`MSQuery`查詢，則可電話`readWithCompletion`物件上。

## <a name="sorting"></a>如何︰ 排序資料與 MSQuery

若要排序的結果，我們來看看範例。 若要排序遞增欄位 [文字]，然後以遞減的 「 完成 」，叫用`MSQuery`就像這樣︰

**目標 C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**快速**︰

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>如何︰ 限制] 欄位，並展開 [查詢字串參數與 MSQuery

若要限制傳回查詢中的欄位，指定**selectFields** ] 屬性中的欄位的名稱。 此範例會傳回只能依照文字和已完成的欄位︰

**目標 C**:

```
query.selectFields = @[@"text", @"complete"];
```

**快速**︰

```
query.selectFields = ["text", "complete"]
```

其他查詢字串參數伺服器邀請中 （例如，因為自訂的伺服器端指令碼使用），請填入`query.parameters`就像這樣︰

**目標 C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**快速**︰

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>如何︰ 設定頁面大小

Azure 行動應用程式，使用頁面大小會控制會提取一次後端的資料表的記錄數目。 在通話`pull`資料想再批次根據此頁面大小，直到拉沒有其他記錄的資料。

若要設定使用**MSPullSettings** ，如下所示的頁面大小可能是。 預設頁面大小為 50，，下列範例會將它變更為 3。

您可以設定不同的頁面大小的效能。 如果您有大量的小型資料記錄中，高的頁面大小會減少伺服器來回。 

這項設定控制只在用戶端的頁面大小。 如果用戶端要求較大的頁面大小超過 Mobile 應用程式後端支援，頁面大小會處於後端設定為支援的最大值。 

此設定也是_數字_的資料記錄中，不_位元組大小_。

如果您增加用戶端頁面大小，[您也應該增加伺服器上的頁面大小](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size)。

**目標 C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**快速**︰

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>如何︰ 插入資料

若要插入新的表格列，請建立`NSDictionary`和叫用`table insert`。 Azure 應用程式服務行動後端如果啟用[動態結構描述]，則會自動產生新的資料行根據`NSDictionary`。

如果`id`是未提供後, 端會自動產生新的唯一識別碼。 提供您自己`id`若要使用電子郵件地址，使用者名稱，或您自己的自訂值為識別碼。 提供您自己的識別碼，可以簡化連接及商務資料庫邏輯。

`result`包含已插入的新項目。 根據您的伺服器邏輯，它可能需要額外或修改資料相較於什麼傳遞至伺服器。

**目標 C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**快速**︰

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>如何︰ 修改資料

若要更新現有的資料列，修改的項目和通話`update`:

**目標 C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**快速**︰

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

或者，提供資料列識別碼] 和 [更新功能變數︰

**目標 C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**快速**︰

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

在 [最小值，`id`屬性必須進行更新時，設定。

##<a name="deleting"></a>如何︰ 刪除資料

若要刪除的項目，叫用`delete`與項目︰

**目標 C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**快速**︰

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

或者，刪除提供的資料列識別碼︰

**目標 C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**快速**︰

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

在 [最小值，`id`進行刪除時，必須設定屬性。

##<a name="customapi"></a>如何︰ 呼叫自訂 API

使用自訂的 API，您可以公開任何後端的功能。 不知道要對應至表格作業。 不只取得更多的控制訊息，您可以偶數閱讀組標題和回應本文格式變更。 若要瞭解如何在後端上建立自訂的 API，閱讀[自訂 Api](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

若要撥打電話自訂的 API，請連絡`MSClient.invokeAPI`。 要求並回應內容會被視為 JSON。 若要使用其他類型的媒體，[使用其他超量的狀況的`invokeAPI`] [ 5]。  進行`GET`要求而不是`POST`要求，請設定參數`HTTPMethod`至`"GET"`參數及參數`body`至`nil`（因為 GET 要求沒有郵件本文。）如果您的自訂 API 支援其他 HTTP 動作，請變更`HTTPMethod`適當。

**目標 C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**快速**︰

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>如何︰ 傳送跨平台通知 Register 推入範本

若要註冊範本，傳遞範本與用戶端應用程式中您**client.push registerDeviceToken**的方法。

**目標 C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**快速**︰

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

範本的類型 NSDictionary 和最多可以包含多個範本以下列格式︰

**目標 C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**快速**︰

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

所有標籤都都會從安全性的要求。  若要新增標籤至安裝或安裝中的範本，請參閱[使用.NET 後端伺服器 Azure Mobile 應用程式的 SDK][4]。  若要傳送使用這些登錄的範本的通知，請使用 [[通知集線器 Api][3]。

##<a name="errors"></a>如何︰ 處理錯誤

當您呼叫 Azure 應用程式服務行動的後端時，完成區塊包含`NSError`參數。 發生錯誤時，此參數是非墩。 在程式碼，您應該核取此參數，並處理錯誤，視需要前面的程式碼片段所示。

檔案[`<WindowsAzureMobileServices/MSError.h>`][6]定義常數`MSErrorResponseKey`， `MSErrorRequestKey`，及`MSErrorServerItemKey`。 若要取得更多資料相關錯誤︰

**目標 C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**快速**︰

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

此外，檔案會定義常數的每個錯誤碼︰

**目標 C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**快速**︰

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>如何︰ 驗證與 Active Directory 驗證文件庫的使用者

您可以使用 Active Directory 驗證文件庫 (ADAL) 登入應用程式使用 Azure Active Directory 的使用者。 若要使用的用戶端流程驗證使用的身分識別提供者 SDK 最好`loginWithProvider:completion:`方法。  用戶端流程驗證提供更多原生的 UX 風格，並讓其他自訂作業。

1. 設定您的行動應用程式後端的 AAD 登入，依照[如何設定 Active Directory 登入應用程式服務][7]教學課程。 請確定完成註冊的原生的用戶端應用程式的選用步驟。 為 iOS，我們建議的 URI 是表單的重新導向`<app-scheme>://<bundle-id>`。 如需詳細資訊，請參閱[ADAL iOS 快速入門][8]。

2. 安裝的 ADAL 使用 Cocoapods。 編輯您 Podfile 包含下列的定義，以 Xcode 專案的名稱取代**您專案**︰

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   然後 Pod:

        pod 'ADALiOS'

3. 使用 Terminal 中，執行`pod install`目錄中包含您的專案，然後開啟 [產生的 Xcode 工作區 （而非專案）。

4. 將下列程式碼新增至 [應用程式中，根據您所使用的語言。 在每個，讓這些取代項目︰

    * **插入授權這裡**取代為您佈建應用程式的租用戶的名稱。 格式應該是 https://login.windows.net/contoso.onmicrosoft.com。 此值可以複製您在 [Azure 傳統入口網站] 的 Azure Active Directory 中的 [網域] 索引標籤。
    * **插入資源識別碼-以下**換成您的行動應用程式後端的用戶端識別碼。 您可以從 [**進階]**索引標籤底下**Azure Active Directory 設定**入口網站中，以取得的用戶端識別碼。
    * **插入用戶端識別碼-以下**換成您複製的原生的用戶端應用程式的用戶端識別碼。
    * 取代您的網站_/.auth/login/done_端點，請使用 HTTPS 配置**插入重新導向-URI 這裡**。 此值應該類似_https://contoso.azurewebsites.net/.auth/login/done_。

**目標 C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**快速**︰

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>如何︰ 驗證與 Facebook SDK iOS 版的使用者

您可以使用 Facebook SDK iOS 版登入使用 Facebook 應用程式的使用者。  使用用戶端流程驗證最好使用`loginWithProvider:completion:`方法。  用戶端流程驗證提供更多原生的 UX 風格，並讓其他自訂作業。

1. 設定您的行動應用程式後端的 Facebook 登入，依照[如何設定 Facebook 登入應用程式服務][9]教學課程。

2. 安裝 iOS 版 Facebook SDK [IOS-快速入門的 Facebook SDK] [10]文件。 而非建立應用程式，您可以新增您現有的註冊 iOS 平台。 

3. Facebook 的文件包含在應用程式委派的目標 C 中的部分程式碼。 如果您使用的**快速**，您可以使用下列的翻譯的 AppDelegate.swift:
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. 除了新增`FBSDKCoreKit.framework`至專案，也新增參照`FBSDKLoginKit.framework`以相同的方式。 

4. 將下列程式碼新增至 [應用程式中，根據您所使用的語言。 

**目標 C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**快速**︰

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>如何︰ 驗證與 Twitter 布料的轉印圖樣 iOS 版的使用者

登入應用程式使用 Twitter 的使用者，您可以使用 iOS 版布料的轉印圖樣。 若要使用的用戶端流程驗證最好`loginWithProvider:completion:`方法，提供更多原生的 UX 風格，並讓其他自訂作業。

1. 依照[如何設定 Twitter 登入應用程式服務](app-service-mobile-how-to-configure-twitter-authentication.md)教學課程中設定您的行動應用程式後端的 Twitter 登入。

2. 追蹤[布料的轉印圖樣 iOS-快速入門的]文件，並設定 [TwitterKit 中新增至專案布料的轉印圖樣。

    > [AZURE.NOTE] 根據預設，布料的轉印圖樣，請為您建立的 Twitter 應用程式。 您可以避免建立註冊消費者索引鍵和消費者私人您先前使用下列的程式碼片段所建立的應用程式。  或者，您可以取代您看到[布料的轉印圖樣儀表板]中的值提供應用程式服務的消費者索引鍵和消費者私人值。 如果您選擇此選項，請務必將回撥 URL 的版面配置區值，例如`https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`。

    如果您選擇使用您先前建立的密碼，新增下列程式碼至您的應用程式代理人︰
    
    **目標 C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **快速**︰
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. 將下列程式碼新增至 [應用程式中，根據您所使用的語言。 

**目標 C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**快速**︰

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>如何︰ 驗證與 Google 登入 SDK iOS 版的使用者

您可以使用 iOS 版登入 SDK Google 登入應用程式使用的是 Google 帳戶的使用者。  Google 最近本項變更其 OAuth 安全性原則。  這些原則變更將會在未來需要使用 Google SDK。

1. 依照[如何設定 Google 登入應用程式服務](app-service-mobile-how-to-configure-google-authentication.md)教學課程中設定您的行動應用程式後端用於 Google 登入。

2. 安裝 iOS 版 Google SDK 的[Google 登入的 iOS-啟動整合](https://developers.google.com/identity/sign-in/ios/start-integrating)文件。 您可以略過的 「 驗證與後端伺服器 」 區段。

3. 將下列文字新增至您的代理人`signIn:didSignInForUser:withError:`方法，根據您所使用的語言。

**目標 C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**快速**︰

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. 請確定您也可以新增下列`application:didFinishLaunchingWithOptions:`在您的應用程式代理人，您用來在步驟 1 中設定應用程式服務的同一個識別碼取代 「 SERVER_CLIENT_ID 」。

**目標 C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **快速**︰
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. 將下列程式碼新增至您的應用程式中實作 UIViewController`GIDSignInUIDelegate`通訊協定，根據您所使用的語言。  再次登入之前已登出，雖然您不需要再次輸入您的認證，您會看到同意] 對話方塊。  工作階段權杖已經過期時，才呼叫這個方法。
 
 **目標 C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **快速**︰
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure 行動應用程式快速入門]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[動態結構描述]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[布料的轉印圖樣儀表板]: https://www.fabric.io/home
[IOS-快速入門版布料的轉印圖樣]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
