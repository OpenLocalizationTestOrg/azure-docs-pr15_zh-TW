<properties
    pageTitle="Azure Active Directory B2C |Microsoft Azure"
    description="如何建置具有註冊、 登入 web 應用程式，以及使用 Azure Active Directory B2C 重設密碼。"
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C︰ 註冊與登入 ASP.NET Web 應用程式中

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

藉由使用 Azure Active Directory (Azure AD) B2C，您可以新增至您的 web 應用程式，簡短的幾個步驟的強大的自助身分識別管理功能。 本文將討論如何建立 ASP.NET web 應用程式，其中包含註冊、 登入的使用者，重設密碼。 使用使用者名稱或電子郵件，並使用社交帳戶，例如 Facebook 和 Google 應用程式中會包含支援註冊和登入。

本教學課程不同於 [[我們其他.NET 網頁教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)，它提供使用者註冊與登入，兩個而不使用單一按鈕，使用[註冊或登入原則](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy)(另一個用於註冊，一個用於登入)。  在 nutshell，註冊或登入原則可讓使用者使用登入現有的帳戶有一個，或建立新的範本，如果這是使用應用程式的第一次。

## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。 目錄是針對您所有的使用者、 應用程式、 群組及更多的容器。  如果您沒有，[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續執行本指南中。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄中的應用程式。 這會提供 Azure AD 資訊，以便與您的應用程式安全地溝通。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。  請務必︰

- 包含**web 應用程式/網路 API**應用程式中。
- 輸入`https://localhost:44316/`為**重新導向 URI**。 則此程式碼範例的預設 URL。
- 複製下**應用程式識別碼**指派給您的應用程式。  您會用得到。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 此程式碼範例包含兩個身分識別體驗︰**註冊與登入**，和**重設密碼**。  您需要建立的每個類型，一個原則[原則參考本文](active-directory-b2c-reference-policies.md)所述。 當您建立的兩個原則時，請務必︰

- 身分識別提供者刀中，選擇 [**註冊的使用者識別碼**或**註冊電子郵件**]。
- 在您註冊與登入的原則中選擇**顯示名稱**] 和 [註冊的其他屬性。
- 選擇 [**顯示名稱**宣告為應用程式，每個原則宣告。 您可以選擇以及其他宣告。
- 建立後，請複製每個原則**名稱**。 您需要這些原則名稱後面。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的兩個原則之後，您可以準備開始建立您的應用程式。

## <a name="download-the-code-and-configure-authentication"></a>下載的程式碼，並設定驗證

此範例[來維護 GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI)程式碼。 若要建置範例，您即可，您可以[下載 zip 基本架構的專案](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip)。 您也可以複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

已完成的範例還有[另存為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip)或在`complete`分支的相同存放庫。

下載的範例程式碼之後，開啟 Visual Studio.sln 檔案，即可開始使用。

藉由傳送 HTTP 驗證要求，指定要執行要求的一部分的原則，與 Azure AD B2C 通訊應用程式。 .NET web 應用程式，您可以使用 Microsoft 的 OWIN 文件庫傳送 OpenID 連線驗證要求、 執行原則，管理使用者的工作階段，及其他內容。

若要開始，新增至專案使用 Visual Studio 封裝管理員主控台 OWIN 介軟體 NuGet 套件。

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

接下來，開啟`web.config`專案的根目錄中的檔案，然後輸入您的應用程式中的設定值`<appSettings>`] 區段中，以下的值取代為 [您自己。  您可以保留`ida:RedirectUri`和`ida:AadInstance`值，不會變更。

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下來，稱為專案中新增 OWIN 啟動類別`Startup.cs`。 以滑鼠右鍵按一下專案、 選取 [**新增**] 及 [**新項目**，然後搜尋 「 OWIN 」。 變更類別宣告，以`public partial class Startup`。 我們在另一個檔案，為您實作此課程的一部分。 將叫用 OWIN 介軟體`Configuration(...)`應用程式啟動時的方法。 在這種方法，請在通話`ConfigureAuth(...)`、 應用程式設定驗證的位置。

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

開啟檔案`App_Start\Startup.Auth.cs`及實作`ConfigureAuth(...)`方法。  在中所提供的參數`OpenIdConnectAuthenticationOptions`做為您的應用程式進行通訊的 Azure AD 座標。 您也需要設定 cookie 驗證。 OpenID 連線介軟體會使用 cookie 維護使用者的工作階段，除此之外。

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                SecurityTokenValidated = OnSecurityTokenValidated,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
...
```

## <a name="send-authentication-requests-to-azure-ad"></a>Azure ad 傳送驗證要求
若要使用的 OpenID 連線驗證通訊協定 Azure AD B2C 與通訊現在正確地設定您的應用程式。  OWIN 已付的所有製作驗證訊息、 驗證權杖從 Azure AD，以及維護使用者工作階段的詳細資料。  剩下的就是進行每個使用者的流程。

當使用者選取**登入**或**忘記您的密碼嗎？** web 應用程式中的相關聯的動作用叫用`Controllers\AccountController.cs`。 每一種情況，您可以使用內建 OWIN 方法來觸發右原則︰

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

在執行註冊或登入原則，使用者會有機會在按一下**忘記您的密碼嗎？**連結。  在此情況下，Azure AD B2C 將傳送給您的應用程式特定錯誤訊息，指出執行密碼重設原則。  您可以擷取的這個錯誤`Startup.Auth.cs`使用`AuthenticationFailed`通知︰

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


除了明確叫用原則，您可以使用`[Authorize]`會執行原則，如果使用者未登入您控制站的標籤。 開啟`Controllers\HomeController.cs`並新增`[Authorize]`宣告控制站的標籤。  OWIN 將選取的最後一個原則設定何時`[Authorize]`標籤叫用。

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

您也可以使用 OWIN 若要登出應用程式的使用者。 In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void Logout()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>顯示 [使用者資訊
當您使用 OpenID 連線驗證的使用者時，Azure AD 會傳回識別碼權杖包含**宣告**的應用程式。 以下是有關使用者判斷提示。 您可以使用宣告來個人化您的應用程式。  

開啟`Controllers\HomeController.cs`檔案。 您可以存取您的控制站透過使用者宣告`ClaimsPrincipal.Current`安全性主體物件。

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

您可以存取您的應用程式會以相同的方式收到任何宣告。  [**宣告**] 頁面上使用您的應用程式收到的所有宣告清單。

## <a name="run-the-sample-app"></a>執行範例應用程式

最後，您可以建立並執行您的應用程式。 登入應用程式的使用電子郵件地址或使用者名稱。 登出後再重新登入以相同的使用者。 編輯該使用者的設定檔。 登出，不同的使用者身分登入。 請注意，顯示在 [**宣告**] 索引標籤上的資訊就會對應到您在您的原則設定的資訊。

## <a name="add-social-idps"></a>新增社交 IDPs

目前的應用程式支援只有在註冊和登入的使用者才使用**本機帳戶**。 這些是儲存在 B2C 目錄使用的使用者名稱和密碼的帳戶。 藉由使用 Azure AD B2C，您可以新增其他**身分識別提供者**(IDPs) 的支援，而不變更任何程式碼。

若要新增您的應用程式社交 IDPs，開始追蹤這些文件中的詳細的指示。 每個您想要支援的 IDP，您需要的系統中登錄應用程式，並取得用戶端識別碼

- [設為 IDP Facebook](active-directory-b2c-setup-fb-app.md)
- [Google 設為 IDP](active-directory-b2c-setup-goog-app.md)
- [Amazon 設為 IDP](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn 設為 IDP](active-directory-b2c-setup-li-app.md)

您將身分識別提供者新增至您的 B2C 目錄之後，必須先編輯每個您要包含新 IDPs 的三個原則，[原則參考本文](active-directory-b2c-reference-policies.md)所述。 儲存您的原則後，請再次執行應用程式。  您應該會看到新的 IDPs 新增為 [登入，並且在每一個您的身分識別的註冊選項。

您可以嘗試使用您的原則，並可以一同觀看範例應用程式的效果。 新增或移除 IDPs、 管理應用程式宣告或變更註冊的屬性。 嘗試鍵，直到您可以看到原則、 驗證需求和 OWIN 的結合。

參照的已完成的範例 （不含您設定的值） [，提供為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip)。 您也可以複製從 GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
