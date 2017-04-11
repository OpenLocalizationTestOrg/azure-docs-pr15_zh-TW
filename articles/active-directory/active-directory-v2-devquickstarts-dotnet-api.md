<properties
    pageTitle="Azure AD v2.0.NET 網路 API |Microsoft Azure"
    description="如何建立接受來自兩個個人的 Microsoft 帳戶的權杖.NET MVC Web Api 與公司或學校帳戶。"
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>安全 MVC 網路 API

Azure Active Directory v2.0 端點，您可以保護網頁的 API 使用[OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow)存取權杖，讓使用者同時使用個人 Microsoft 帳戶與工作或學校帳戶安全地存取您網站的 API。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

ASP.NET web Api，您可以完成使用 Microsoft.NET Framework 4.5 中所包含的 OWIN 介軟體。  以下我們將使用 OWIN 建立 「 待辦事項清單 」 MVC Web API，可讓用戶端建立並讀取使用者的待辦事項清單中的工作。  網路 API 會驗證傳入的邀請包含有效的存取權杖，並拒絕任何受保護的路徑，未通過驗證的要求。  此範例使用 Visual Studio 2015 建置。

## <a name="download"></a>下載
本教學課程中的程式碼是來維護[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)或複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

基本架構的應用程式簡單的 API，包括所有的指令碼，但會遺失所有的身分識別相關的項目。 如果您不想要遵循，您改用可以複製或[下載已完成的範例](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip)。

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>登錄應用程式
在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，建立新的應用程式，或依照這些[詳細的步驟](active-directory-v2-app-registration.md)。  請確定︰

- 複製下指派給您的應用程式的**應用程式識別碼**，您必須將其推出。

此 visual studio 方案中也包含 「 TodoListClient 」，這是一個簡單的 WPF 應用程式。  TodoListClient 用來示範如何使用者符號增益集，以及如何用戶端可以發給要求您網站的 API。  在此情況下，同時 TodoListClient 和 TodoListService 會以相同的應用程式。  若要設定 TodoListClient，您也應該︰

- 新增您的應用程式的**行動**平台。


## <a name="install-owin"></a>安裝 OWIN

您已註冊應用程式，必須先設定您的應用程式，以便驗證傳入要求和權杖通訊與 v2.0 端點。

- 若要開始，開啟方案，並使用套件管理員主控台 TodoListService 專案中新增 OWIN 介軟體 NuGet 套件。

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>設定 OAuth 驗證

- 稱為 TodoListService 專案中新增 OWIN 啟動類別`Startup.cs`。  右邊，按一下 [專案 1--**新增** --> **新項目**1--搜尋 「 OWIN 」。  將叫用 OWIN 介軟體`Configuration(…)`應用程式啟動時的方法。
- 變更類別宣告，以`public partial class Startup`-我們已實作您此課程的一部分，另一個檔案。  在 [`Configuration(…)`方法，請設定 web 應用程式的驗證 ConfgureAuth(...) 通話。

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- 開啟檔案`App_Start\Startup.Auth.cs`及實作`ConfigureAuth(…)`會設定為接受來自 v2.0 端點的權杖的 Web API 的方法。

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- 現在，您可以使用`[Authorize]`來保護您的控制站和動作 OAuth 2.0 承載者驗證的屬性。  裝飾`Controllers\TodoListController.cs`授權標記的類別。  這會強制使用者登入，才能存取該頁面。

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 當有授權的來電者成功叫用下列其中一項`TodoListController`Api，動作可能需要來電者之間的相關資訊的存取權。  OWIN 提供存取中透過承載者權杖宣告`ClaimsPrincpal`物件。  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   最後，開啟`web.config`TodoListService 專案的根目錄中的檔案，然後輸入您的設定值，以`<appSettings>`一節。
  - 您`ida:Audience`您輸入在入口網站的應用程式的**應用程式識別碼**。

## <a name="configure-the-client-app"></a>設定用戶端應用程式
您可以看到 Todo 清單服務的動作前，必須先設定 Todo 清單用戶端，讓它可以先從 v2.0 端點取得權杖進行通話的服務。

- 在 TodoListClient 專案中，開啟`App.config`並輸入您的設定值，以`<appSettings>`一節。
  - 您`ida:ClientId`您複製的入口網站應用程式識別碼。

最後，清理、 建立及執行每個專案 ！  現在您已經有.NET MVC Web API 接受權杖從兩個個人的 Microsoft 帳戶與公司或學校帳戶。  登入 TodoListClient，並撥打您的網站將工作新增至使用者的待辦事項清單的 api。

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>後續步驟
您現在可以移動到其他主題。  若要嘗試︰

[從 Web App 撥號網路 API >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

其他資源，請參閱︰
- [V2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow 「 azure-作用中的目錄 」 標記 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
