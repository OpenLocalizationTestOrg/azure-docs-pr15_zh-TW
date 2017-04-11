<properties
    pageTitle="快速入門 azure AD.NET |Microsoft Azure"
    description="如何建立與登入 Azure AD 整合.NET MVC Web 應用程式。"
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>ASP.NET Web 應用程式登入與登出與 Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 可讓簡單且外包 web 應用程式的身分識別管理] 中，直接提供單一登入教具借出只有幾行程式碼。  Asp.NET web 應用程式，您可以完成使用 Microsoft.NET Framework 4.5 中所包含的社群導向 OWIN 介軟體實作。  以下我們將使用 OWIN 至︰
-   登入身分識別提供者為使用 Azure AD 這個應用程式的使用者。
-   顯示使用者的部分資訊。
-   登入登出應用程式的使用者。

若要這麼做，您必須︰

1. 註冊 Azure AD 應用程式
2. 將您的應用程式設定為使用 OWIN 驗證管道的郵件。
3. 使用 OWIN Azure ad 發出登入並教具借出的要求。
4. 列印之使用者的相關資料。

若要開始，[下載應用程式的基本架構](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)。  您也必須要註冊您的應用程式 Azure AD 租用戶。  如果您沒有，[瞭解如何取得](active-directory-howto-tenant.md)。

## <a name="1--register-an-application-with-azure-ad"></a>*1.註冊 Azure AD 應用程式*
若要啟用您的應用程式驗證的使用者，您首先要註冊您的租用戶中新的應用程式。

- 登入 Azure 管理入口網站。
- 在左側導覽中，按一下 [ **Active Directory**。
- 選取您想要註冊的應用程式的租用戶。
- 按一下 [**應用程式**] 索引標籤，底部抽屜中按一下 [新增]。
- 依照提示操作，並建立新**Web 應用程式及/或 WebAPI**。
    - 應用程式的**名稱**會說明您的使用者的應用程式
    -   **登入 URL**是您的應用程式的基本的 URL。  基本架構的預設值是`https://localhost:44320/`。
    - **應用程式識別碼 URI**是您的應用程式的唯一識別碼。  慣例是使用`https://<tenant-domain>/<app-name>`，例如︰`https://contoso.onmicrosoft.com/my-first-aad-app`
- 當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  此值必須在下一個區段中，因此複製的 [設定] 索引標籤。

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2.設定您的應用程式使用 OWIN 驗證管線*
在這裡，我們會設定為使用 OpenID 連線驗證通訊協定 OWIN 介軟體。  將用於 OWIN 發出登入並教具借出要求與管理使用者的工作階段，取得使用者，內的其他項目之間的相關資訊。

-   若要開始，請使用套件管理員主控台專案中新增 OWIN 介軟體 NuGet 套件。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   名為專案中新增 OWIN 啟動類別`Startup.cs`右邊，按一下 [專案 1--**新增** --> **新項目**1--搜尋 「 OWIN 」。  將叫用 OWIN 介軟體`Configuration(...)`應用程式啟動時的方法。
-   變更類別宣告，以`public partial class Startup`-我們已實作您此課程的一部分，另一個檔案。  在 [`Configuration(...)`方法，請設定 web 應用程式的驗證 ConfgureAuth(...) 通話  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   開啟檔案`App_Start\Startup.Auth.cs`及實作`ConfigureAuth(...)`方法。  在中所提供的參數`OpenIDConnectAuthenticationOptions`做為您的應用程式進行通訊的 Azure AD 座標。  您也需要設定 Cookie 驗證-OpenID 連線介軟體會使用 cookie 涵蓋下方。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   最後，開啟`web.config`檔案根目錄中的專案，並輸入您的設定值，以`<appSettings>`一節。
    -   您的應用程式`ida:ClientId`是您從 Azure] 入口網站，在 [步驟 1 中複製 Guid。
    -   `ida:Tenant` Azure AD 租用戶，例如 「 contoso.onmicrosoft.com 」 的名稱。
    -   您`ida:PostLogoutRedirectUri`指出 Azure ad 教具借出要求順利完成後，重新使用者。

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3.使用 OWIN Azure ad 發出登入並教具借出的要求*
您的應用程式現在已正確設定與使用 OpenID 連線驗證通訊協定的 Azure AD 通訊。  OWIN 具有付的所有醜陋製作驗證訊息、 驗證權杖從 Azure AD，及維護使用者工作階段的詳細資料。  剩下的就是為您的使用者登入與登出的方式。

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   現在，開啟`Views\Shared\_LoginPartial.cshtml`。  這是您將會顯示使用者您的應用程式登入] 和 [教具借出連結，及列印檢視中的使用者名稱的位置。

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4.顯示使用者資訊*
驗證與 OpenID 連線的使用者，Azure AD 傳回 id_token 包含 「 宣告 」 或判斷提示使用者的相關應用程式。  您可以使用這些宣告來個人化您的應用程式︰

- 開啟`Controllers\HomeController.cs`檔案。  您可以存取您的控制站透過使用者的宣告`ClaimsPrincipal.Current`安全性主體物件。

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

最後，建立並執行您的應用程式 ！  如果您未，現在正是您使用的租用戶中建立新的使用者 *。 onmicrosoft.com 網域。  登入的使用者，並注意如何上方導覽列中反映使用者的身分識別。  登出，並再次登入以在您的租用戶中的另一個使用者。  如果覺得特別野心勃勃，註冊，並執行此應用程式 （含自己 clientId)，並監看式，請參閱單一-登入另一個執行個體的實際操作。

參照的[以下提供](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)的已完成的範例 （不含您設定的值）。  

您現在可以移動到更進階的主題。  若要嘗試︰

[安全的 Web 與 Azure AD API >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
