<properties
    pageTitle="Azure AD v2.0.NET Web 應用程式 |Microsoft Azure"
    description="如何建立使用兩個個人的 Microsoft 帳戶使用者與公司或學校帳戶登入.NET MVC Web 應用程式。"
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>登入加入.NET MVC web 應用程式

與 v2.0 端點，您可以快速新增至您的 web 應用程式的兩個個人的 Microsoft 帳戶支援的驗證及公司或學校帳戶。  ASP.NET web 應用程式，您可以完成使用 Microsoft.NET Framework 4.5 中所包含的 OWIN 介軟體。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 結束點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

 以下我們會建立 web 應用程式的登入的使用者顯示一些使用者，相關資訊，登入應用程式出使用者使用 OWIN。
 
 ## <a name="download"></a>下載
本教學課程中的程式碼是來維護[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)或複製基本架構︰

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

已完成的應用程式提供在本教學課程結尾處。

## <a name="register-an-app"></a>登錄應用程式
在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，建立新的應用程式，或依照這些[詳細的步驟](active-directory-v2-app-registration.md)。  請確定︰

- 複製下指派給您的應用程式的**應用程式識別碼**，您必須將其推出。
- 新增您的應用程式的**Web**平台。
- 輸入正確的**重新導向 URI**。 重新導向 uri 指示 Azure ad 位置驗證回應應該會導向-本教學課程中的預設值是`https://localhost:44326/`。

## <a name="install--configure-owin-authentication"></a>安裝及設定 OWIN 驗證
在這裡，我們會設定為使用 OpenID 連線驗證通訊協定 OWIN 介軟體。  將用於 OWIN 發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

-   若要開始，請開啟`web.config`檔案根目錄中的專案，並輸入您的應用程式中的設定值`<appSettings>`一節。
    -   `ida:ClientId`指派給您的應用程式，請在註冊入口網站的**應用程式識別碼**。
    -   `ida:RedirectUri` **重新導向 Uri**您輸入在入口網站。

-   接下來，請使用套件管理員主控台專案中新增 OWIN 介軟體 NuGet 套件。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   新增 「 OWIN 啟動 「 專案稱為類別`Startup.cs`右邊，按一下 [專案 1--**新增** --> **新項目**1--搜尋 「 OWIN 」。  將叫用 OWIN 介軟體`Configuration(...)`應用程式啟動時的方法。
-   變更類別宣告，以`public partial class Startup`-我們已實作您此課程的一部分，另一個檔案。  在 [`Configuration(...)`方法，請設定 web 應用程式的驗證 ConfigureAuth(...) 通話  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   開啟檔案`App_Start\Startup.Auth.cs`及實作`ConfigureAuth(...)`方法。  在中所提供的參數`OpenIdConnectAuthenticationOptions`做為您的應用程式進行通訊的 Azure AD 座標。  您也需要設定 Cookie 驗證-OpenID 連線介軟體會使用 cookie 涵蓋下方。

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
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>傳送驗證要求
您的應用程式現在已正確設定通訊與使用 OpenID 連線驗證通訊協定 v2.0 端點。  OWIN 已付的所有醜陋製作驗證訊息、 驗證權杖從 Azure AD，及維護使用者工作階段的詳細資料。  剩下的就是為您的使用者登入與登出的方式。

- 您可以使用授權中您控制站，要求使用者登入，才能存取某一頁的標籤。  開啟`Controllers\HomeController.cs`，並新增`[Authorize]`相關控制站的標籤。

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   您也可以使用 OWIN 直接發出程式碼中的 [驗證要求。  開啟`Controllers\AccountController.cs`。  在 [SignIn() 和 SignOut() 動作時發生問題 OpenID 連線的挑戰及教具借出要求，分別。

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   現在，開啟`Views\Shared\_LoginPartial.cshtml`。  這是您將會顯示使用者您的應用程式登入] 和 [教具借出連結，及列印檢視中的使用者名稱的位置。

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>顯示 [使用者資訊
驗證與 OpenID 連線的使用者，v2.0 端點會傳回 id_token 含有[宣告](active-directory-v2-tokens.md#id_tokens)或判斷提示使用者的相關應用程式。  您可以使用這些宣告來個人化您的應用程式︰

- 開啟`Controllers\HomeController.cs`檔案。  您可以存取您的控制站透過使用者的宣告`ClaimsPrincipal.Current`安全性主體物件。

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>執行

最後，建立並執行您的應用程式 ！   使用個人 Microsoft 帳戶或公司或學校帳戶登入，並注意如何反映上方導覽列中的使用者的身分識別。  現在，您可以使用其個人及工作/學校的帳戶的使用者驗證的業界標準通訊協定保護的 web 應用程式。

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>後續步驟

您現在可以移動到更進階的主題。  若要嘗試︰

[安全的 Web API v2.0 端點 >>](active-directory-devquickstarts-webapi-dotnet.md)

其他資源，請參閱︰
- [V2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow 「 azure-主動-目錄 」 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
