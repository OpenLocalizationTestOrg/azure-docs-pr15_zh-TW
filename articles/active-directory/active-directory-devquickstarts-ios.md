<properties
    pageTitle="Azure AD iOS 快速入門 |Microsoft Azure"
    description="如何建立 iOS 應用程式的整合 Azure AD 登入並通話 Azure AD 受到保護的 Api 使用 OAuth。"
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Azure AD 整合 iOS 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure AD 提供的 Active Directory 驗證庫或 ADAL，需要存取受保護的資源的 iOS 用戶端。  ADAL 的生活中唯一的目的是讓您的應用程式，以取得存取權杖能輕鬆地。  若要示範如何只要輕鬆，在這裡，我們將建立的目標 C 待辦事項清單應用程式的︰

-   取得存取權杖撥 Azure AD 圖形 API 使用[OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
-   使用指定的別名在目錄中搜尋。

若要建立完成的工作應用程式，您必須︰

2. Azure AD 註冊您的應用程式。
3. 安裝並設定 ADAL。
5. 若要從 Azure AD 取得權杖使用 ADAL。

若要開始，[下載應用程式的基本架構](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。  您也必須 Azure AD 租用戶，您可以在其中建立使用者，並登錄應用程式。  如果您還沒有租用戶，[瞭解如何取得](active-directory-howto-tenant.md)。

> [AZURE.TIP] 請試試我們將協助您快速上手與 Azure Active Directory 中只需幾分鐘的新[開發人員入口網站](https://identity.microsoft.com/Docs/iOS)的預覽 ！  開發人員入口網站會引導您執行登錄應用程式，並將 Azure AD 整合至您的程式碼的程序。  當您完成後時，您會有一個簡單的應用程式，可以驗證您的租用戶和後端，可接受權杖，並執行驗證的使用者。 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1.決定您重新導向 URI 會為 iOS 版*

若要安全地啟動您的應用程式，我們需要您以特定格式建立**重新導向 URI**特定 SSO 案例中。 重新導向 URI 用來確保權杖返回正確要求他們的應用程式。

重新導向 uri iOS 格式為︰

```
<app-scheme>://<bundle-id>
```

-   **aap 配置**-這註冊 XCode 專案中。 這是其他應用程式可以打電話給您。 您可以找到這下 Info.plist]-> [URL 類型]-> [URL 識別碼。 您應該先建立一個，如果您還沒有一或多設定。
-   這是 「 身分識別] 下找到套件識別碼**套件 id** -取消您的專案中的設定 XCode。

此快速入門程式碼範例︰ ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2.註冊 DirectorySearcher 應用程式*
若要啟用您的應用程式，以取得權杖，您將必須註冊 Azure AD 租用戶，並授與 Azure AD 圖形 API 的存取權限︰

-   登入 Azure 管理入口網站
-   在左側導覽中，按一下 [在**Active Directory**
-   選取要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   遵循提示，並建立新的**原生的用戶端應用程式**。
    -   應用程式的**名稱**會說明您的使用者的應用程式
    -   **重新導向 Uri**是配置和字串的組合，Azure AD 將用來傳回 token 回應。  根據上述資訊應用程式中輸入特定的值。
-   當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  此值必須在下一個區段中，因此複製的 [**設定**] 索引標籤。
- 也在 [**設定**] 索引標籤，找到 」 權限以其他應用程式 」 區段。  「 Azure Active Directory 」 應用程式中，新增 [**委派的權限****存取您組織的目錄**權限。  如此一來查詢圖形 API 使用者的應用程式。

## <a name="3-install--configure-adal"></a>*3.安裝與設定 ADAL*
有 Azure AD 中的應用程式之後，您可以安裝的 ADAL 並撰寫您的身分識別相關的程式碼。  為了讓 ADAL 能夠與 Azure AD 通訊，您需要提供的一些相關應用程式註冊資訊。
-   開始使用 Cocapods DirectorySearcher 專案新增 ADAL。

```
$ vi Podfile
```
新增此 podfile 下列動作︰

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

現在載入使用 cocoapods podfile。 這會建立新的 XCode 工作區，您將會載入。

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   在 [快速入門] 專案中，開啟 [plist 檔案`settings.plist`。  取代的值，以反映您輸入到 [Azure 入口網站中的值] 區段中的項目。  您的程式碼會參考這些值時，它會使用 ADAL。
    -   `tenant`是 Azure AD 租用戶，例如 contoso.onmicrosoft.com 網域
    -   `clientId`會將您複製的入口網站應用程式的 clientId。
    -   `redirectUri`會重新導向註冊，讓您在入口網站的 url。

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4.使用 ADAL 取得 AAD 權杖*
基本原則 ADAL 後方是，每當您的應用程式需要存取權杖，它只會呼叫 completionBlock `+(void) getToken : `，ADAL 執行其餘部分。  

-   在 [`QuickStart`開啟專案`GraphAPICaller.m`並找出`// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.`上方附近的註解。  這是您將傳遞 ADAL 透過 Azure AD 與通訊並告知該如何快取權杖 CompletionBlock 座標。

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- 現在，我們需要使用此 token 加搜尋圖形中的使用者。 尋找`// TODO: implement SearchUsersList`commentThis 方法可讓您取得要求 Azure AD 圖形 api 來查詢使用者的 UPN 開頭為指定的搜尋字詞。  您要包含在 access_token 才能查詢圖形 API，但`Authorization`標頭的邀請-這是 ADAL 提供。

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- 當您的應用程式要求權杖，則可電話`getToken(...)`，ADAL 會傳回一個權杖，不必要求使用者認證。  如果 ADAL 判斷使用者需要取得權杖登入，它會顯示登入] 對話方塊，收集使用者的認證，並傳回時成功驗證的權杖。  如果 ADAL 無法傳回權杖基於任何原因，則會擲回`AdalException`。
- 請注意，`AuthenticationResult`物件包含`tokenCacheStoreItem`可以用來收集的資訊可能需要您的應用程式的物件。  在 [快速入門，`tokenCacheStoreItem`用來決定 authenitcation 是否已經發生。


## <a name="step-5-build-and-run-the-application"></a>步驟 5︰ 建立及執行應用程式



恭喜您 ！ 您現在有工作 iOS 應用程式已驗證的使用者、 安全地呼叫 [Web Api 使用 OAuth 2.0 的功能，並取得基本的使用者資訊。  如果您尚未選取，如此，填入您的租用戶，且有些使用者的時間。  執行您快速入門應用程式，並使用其中一個這些使用者登入。  根據其 UPN 其他使用者的搜尋。  關閉應用程式，然後重新執行。  請注意如何在使用者工作階段保持不變。

ADAL 可讓您更輕鬆地將所有這些常見的身分識別功能加入您的應用程式。  它會為您的快取管理、 OAuth 通訊協定支援，透過登入 UI，重新整理過期的權杖，以及其他簡報使用者處理 dirty 的所有工作。  您只需瞭解是單一 API 通話， `getToken`。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。  

## <a name="additional-scenarios"></a>其他的分析藍本
您可以立即移至其他的案例。  若要嘗試︰

- [安全 Node.JS 網路與 Azure AD 的 API](active-directory-devquickstarts-webapi-nodejs.md)
- 瞭解[如何啟用跨應用程式 SSO iOS 使用 ADAL 上](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
