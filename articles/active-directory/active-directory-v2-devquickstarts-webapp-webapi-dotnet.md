<properties
    pageTitle="Azure AD v2.0.NET Web 應用程式 |Microsoft Azure"
    description="如何建立.NET MVC Web 應用程式的通話 web 服務使用個人 Microsoft 帳戶與公司或學校帳戶登入。"
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>撥號網路 API 從.NET web 應用程式

與 v2.0 端點，您可以快速將驗證新增至您的 web 應用程式，以及網頁 Api 支援兩個人的 Microsoft 帳戶與公司或學校帳戶。  在這裡，我們會建立 MVC web 應用程式的登入中使用 OpenID 連線，請從 Microsoft OWIN 介軟體協助使用者。  Web 應用程式將會取得 OAuth 2.0，可讓受保護的 api 建立的 web OAuth 2.0 存取權杖、 讀取及刪除特定的使用者的 「 待辦事項清單]。

本教學課程將著重於使用 MSAL 擷取及所述完整[以下](active-directory-v2-flows.md#web-apps)在 web 應用程式中使用的存取權杖。  為必要條件，您可能會想要先瞭解如何[新增基本登入 web app](active-directory-v2-devquickstarts-dotnet-web.md)或如何[保護網路 API](active-directory-v2-devquickstarts-dotnet-api.md)。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 結束點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="download-sample-code"></a>下載範例程式碼

本教學課程中的程式碼是來維護[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip)或複製基本架構︰

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

或者，您可以[下載.zip 為已完成的應用程式](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip)或複製已完成的應用程式︰

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>登錄應用程式
在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，建立新的應用程式，或依照這些[詳細的步驟](active-directory-v2-app-registration.md)。  請確定︰

- 複製下指派給您的應用程式的**應用程式識別碼**，您必須將其推出。
- 建立的**密碼**類型，[**應用程式密碼**並複製下其值，以供下次使用
- 新增您的應用程式的**Web**平台。
- 輸入正確的**重新導向 URI**。 重新導向 uri 指示 Azure ad 位置驗證回應應該會導向-本教學課程中的預設值是`https://localhost:44326/`。


## <a name="install-owin"></a>安裝 OWIN
新增至 OWIN 介軟體 NuGet 套件`TodoList-WebApp`使用封裝管理員主控台的專案。  將用於 OWIN 介軟體發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>登入的使用者
現在設定使用[OpenID 連線驗證通訊協定](active-directory-v2-protocols.md#openid-connect-sign-in-flow)OWIN 介軟體。  

-   開啟`web.config`檔案中的根目錄`TodoList-WebApp`專案，然後輸入您的應用程式中的設定值`<appSettings>`一節。
    -   `ida:ClientId`已指派給您在註冊入口網站的應用程式的**應用程式識別碼**。
    - `ida:ClientSecret` ，其中 [註冊] 入口網站中所建立的**應用程式的密碼**。
    -   `ida:RedirectUri` **重新導向 Uri**您輸入在入口網站。
- 開啟`web.config`檔案中的根目錄`TodoList-Service`專案，並取代`ida:Audience`上方的同一個**應用程式識別碼**。


- 開啟檔案`App_Start\Startup.Auth.cs`並新增`using`從上方的文件庫的陳述式。
- 在相同的檔案，實作`ConfigureAuth(...)`方法。  在中所提供的參數`OpenIDConnectAuthenticationOptions`做為您的應用程式進行通訊的 Azure AD 座標。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>使用 MSAL 取得存取權杖
在 [`AuthorizationCodeReceived`通知，我們想要兌換的待辦事項清單服務存取權杖 authorization_code 使用[OAuth 2.0 搭配 OpenID 連線](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow)。  MSAL 可以讓此程序簡單︰

- 首先，請安裝 MSAL 的 preview 版本︰

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- 並新增另一個`using`陳述式`App_Start\Startup.Auth.cs`MSAL 檔案。
- 現在加入新的方法，`OnAuthorizationCodeReceived`事件處理常式。  此處理常式會使用 MSAL 取得待辦事項清單 api，存取權杖，並會將儲存在 MSAL 的權杖快取的權杖以供下次使用︰

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- 在 web 應用程式，MSAL 會有可以用來儲存權杖可延伸權杖快取。  此範例會實作`NaiveSessionCache`會使用快取權杖 http 工作階段儲存空間。

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>撥號網路 API
現在是實際使用您在步驟 3 中取得 access_token 的時間。  開啟 web 應用程式的`Controllers\TodoListController.cs`待辦事項清單 API 讓所有的 crud 以要求的檔案。

- 您可以使用 MSAL 再次以下來擷取 access_tokens 從 MSAL 快取。  首先，將新增`using`MSAL 陳述式這個檔案。

    `using Microsoft.Identity.Client;`

- 在 [`Index`巨集指令，使用 MSAL 的`AcquireTokenSilentAsync`取得 access_token 可以用來從 [待辦事項清單服務讀取資料的方法︰

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- 範例然後新增為 HTTP GET 要求產生的權杖`Authorization`標頭，待辦事項清單服務用來驗證要求。
- 如果 [待辦事項清單服務會傳回`401 Unauthorized`回應時，在 MSAL access_tokens 有會變成無效基於某種原因。  在此情況下，您應該放任何 access_tokens 從 MSAL 快取，顯示使用者可能需要登入，其會重新啟動 token 擷取流程的訊息。

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- 同樣地，如果 MSAL 無法傳回 access_token 基於任何原因，您應該指示使用者，以再次登入。  這是非常簡單，擷取任何`MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- 確切的相同`AcquireTokenSilentAsync`呼叫是在 implementd`Create`和`Delete`動作。  在 web 應用程式，您可以使用這個 MSAL 方法以取得 access_tokens，每當您需要在您的應用程式。  取得、 快取，以及重新整理您的權杖會負責 MSAL。

最後，建立並執行您的應用程式 ！  使用 Microsoft 帳戶或 Azure AD 帳戶，請登入，並注意如何上方導覽列中反映使用者的身分識別。  新增和刪除使用者的待辦事項清單，以查看 OAuth 2.0 受到保護的實際操作的 API 呼叫中的某些項目。  現在您有 web 應用程式與 web API，同時使用業界標準的通訊協定，可以驗證使用者自己個人及工作/學校的帳戶以保護。

參照的[以下提供](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip)的已完成的範例 （不含您設定的值）。  

## <a name="next-steps"></a>後續步驟

其他資源，請參閱︰
- [V2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow 「 azure-作用中的目錄 」 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
