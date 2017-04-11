<properties
    pageTitle="Azure AD B2C |Microsoft Azure"
    description="如何建立.NET Web API 使用 Azure Active Directory B2C，可以使用 OAuth 2.0 存取權杖進行驗證。"
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C︰ 建立.NET 網路 API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

與 Azure Active Directory (Azure AD) B2C，您可以使用 OAuth 2.0 存取權杖保護網路 API。 這些權杖可讓您使用 Azure AD B2C 驗證對 API 的用戶端應用程式。 本文將示範如何建立可讓使用者 CRUD 工作.NET 模型檢視控制器 (MVC) 「 待辦事項清單 」 API。 網路 API 使用 Azure AD B2C 受到保護，只允許已驗證的使用者管理其待辦事項清單。

## <a name="create-an-azure-ad-b2c-directory"></a>建立 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。 目錄是針對您所有的使用者、 應用程式、 群組及更多的容器。 如果您沒有，[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續執行本指南中。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄中的應用程式。 這會提供 Azure AD 資訊，以便與您的應用程式安全地溝通。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。 請務必︰

- 應用程式中包含的**web 應用程式**或**web API** 。
- 使用**重新導向統一資源識別元** `https://localhost:44316/` web 應用程式。 這是預設位置的 web 應用程式用戶端此程式碼範例。
- 複製**應用程式識別碼**指派給您的應用程式。 您必須將其更新版本。

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 在這個範例中的程式碼的用戶端包含三個身分識別體驗︰ 註冊、 登入，及 [編輯設定檔。 您必須針對每個類型，建立原則[原則參考本文](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。 當您建立您的三個原則時，請務必︰

- 身分識別提供者刀中，選擇 [**註冊的使用者識別碼**] 或 [**註冊電子郵件**。
- 選擇 [註冊原則中的 [**顯示名稱**和其他註冊屬性]。
- 您可以選擇 [**顯示名稱**] 和 [**物件識別碼**宣告為每個原則的應用程式宣告。 您可以選擇以及其他宣告。
- 建立後，請複製每個原則**名稱**。 您需要下列原則名稱後面。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

您已成功建立的三個原則之後，您可以準備開始建立您的應用程式。

## <a name="download-the-code"></a>下載的程式碼

此教學課程[GitHub 上維護](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet)的程式碼。 若要建置範例，您即可，您可以[下載基本架構專案另存為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip)。 您也可以複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

已完成的應用程式也是[使用 zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip)上或`complete`分支的相同存放庫。

下載的範例程式碼之後，開啟 Visual Studio.sln 檔案，即可開始使用。 解決方案檔案包含兩個專案︰`TaskWebApp`和`TaskService`。 `TaskWebApp`為使用者進行互動 MVC web 應用程式。 `TaskService`是的應用程式後端網路 API 儲存每個使用者的待辦事項清單。

## <a name="configure-the-task-web-app"></a>設定工作的 web 應用程式

當使用者進行互動`TaskWebApp`，用戶端 Azure ad 傳送邀請，並取得回可以用來呼叫的權杖`TaskService`網頁 API。 若要登入的使用者，並取得權杖，您需要提供`TaskWebApp`一些相關應用程式的資訊。 在 [`TaskWebApp`開啟專案`web.config`檔案中的專案根目錄及取代的值`<appSettings>`一節。  您可以將`AadInstance`， `RedirectUri`，及`TaskServiceUrl`為值-是。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

本文並未涵蓋建置`TaskWebApp`用戶端。  若要瞭解如何建立使用 Azure AD B2C 的 web 應用程式，請參閱[我們.NET web 應用程式教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="secure-the-api"></a>安全 API

如果您有代表使用者的通話 API 的用戶端，您可以保護`TaskService`使用 OAuth 2.0 承載者權杖。 您的 API，可以接受並驗證權杖 for.NET (OWIN) 文件庫中使用 Microsoft 的開啟 Web 介面。

### <a name="install-owin"></a>安裝 OWIN
開始安裝 OWIN OAuth 驗證管道的郵件︰

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>輸入您 B2C 詳細資料
開啟`web.config`檔案中的根目錄`TaskService`project 及取代的值`<appSettings>`一節。 這些值將用於整個 API 及 OWIN 文件庫。  您可以將`AadInstance`保持不變的值。

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>新增 OWIN 啟動類別
新增至 OWIN 啟動類別`TaskService`專案稱為`Startup.cs`。  以滑鼠右鍵按一下專案，選取 [**新增**] 及 [**新項目**，然後搜尋 OWIN。


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>設定 OAuth 2.0 驗證
開啟檔案`App_Start\Startup.Auth.cs`，及實作`ConfigureAuth(...)`方法︰

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>安全的任務控制站
應用程式設定成使用 OAuth 2.0 驗證後，您可以藉由新增來保護您的網路 API`[Authorize]`工作控制站的標籤。 這是的控制器位置所有待辦事項清單操作生效，因此您應該保護整個控制器類別層級。 您也可以新增`[Authorize]`標記，以控制多微調個別的動作。

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>從權杖取得使用者資訊
`TasksController`在資料庫中每個任務具有 「 擁有 」 任務相關聯的使用者的位置儲存工作。 使用者的**物件識別碼**識別擁有者。 (這是為什麼您需要新增的物件識別碼為應用程式中所有的原則宣告。)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>執行範例應用程式

最後，建立並執行兩者`TaskWebApp`和`TaskService`。 使用電子郵件地址或使用者名稱來登入應用程式。 建立使用者的待辦事項清單上的部分工作，並注意如何在保存在 API 即使您停止並重新啟動用戶端。

## <a name="edit-your-policies"></a>編輯您的原則

您有使用 Azure AD B2C 保護 API 之後，您可以嘗試使用您的應用程式原則和檢視效果上 （或缺少產品） API。 您可以管理應用程式中的宣告原則，並變更網路 API 中所提供的使用者資訊。 您新增任何宣告將可供您.NET MVC 網路 API 中`ClaimsPrincipal`物件，如本文稍早所述。

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
