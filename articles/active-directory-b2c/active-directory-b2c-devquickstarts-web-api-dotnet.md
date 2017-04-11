<properties
    pageTitle="Azure Active Directory B2C |Microsoft Azure"
    description="如何建立使用 Azure Active Directory B2C 呼叫網路 API 的 web 應用程式。"
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

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C︰ 撥網路 API.NET web 應用程式

藉由使用 Azure Active Directory (Azure AD) B2C，您可以新增功能強大的自助身分識別管理功能至 web 應用程式與 web Api 簡短的幾個步驟。 本文將討論如何建立使用承載者權杖呼叫網路 API 的.NET 模型檢視控制器 (MVC) 「 待辦事項清單 」 web 應用程式

本文並未涵蓋如何實作登入，註冊及管理設定檔與 Azure AD B2C。 它會解釋呼叫 web Api 之後已驗證使用者。 如果您尚未選取，您應該開頭的[.NET web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)，瞭解如何 Azure AD B2C 的基本概念。

## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。  目錄是為您的使用者、 應用程式、 群組及更多的容器。  如果您沒有，[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續執行本指南中。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄中的應用程式。 這會提供 Azure AD 資訊，以便與您的應用程式安全地溝通。 在此情況下，將 [web 應用程式] 與 [web API 代表單一**應用程式識別碼**，因為它們組成一個邏輯的應用程式。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。 請務必︰

- 包含**web 應用程式/網路 API**應用程式中。
- 輸入`https://localhost:44316/`為**回覆的 URL**。 則此程式碼範例的預設 URL。
- 複製**應用程式識別碼**指派給您的應用程式。 您也必須此更新版本。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 此 web 應用程式包含三個身分識別體驗︰ 註冊、 登入，及 [編輯設定檔。 您需要建立的每個類型，一個原則[原則參考本文](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。 當您建立的三個原則時，請務必︰

- 在您註冊的原則，選擇**顯示名稱**] 和 [註冊的其他屬性。
- 您可以選擇 [**顯示名稱**] 和 [**物件識別碼**的應用程式宣告每個原則。 您可以選擇以及其他宣告。
- 建立後，請複製每個原則**名稱**。 應有前置詞`b2c_1_`。 您需要這些原則名稱後面。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

建立您的三個原則之後，您可以準備開始建立您的應用程式。

請注意本文並未涵蓋如何使用您剛剛建立的原則。 若要深入瞭解原則 Azure AD B2C 中的運作方式，請從[.NET web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="download-the-code"></a>下載的程式碼

此教學課程[GitHub 上維護](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet)的程式碼。 若要建置範例，您即可，您可以[下載 zip 基本架構的專案](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip)。 您也可以複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

已完成的應用程式也是[使用 zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)上或`complete`分支的相同存放庫。

下載的範例程式碼之後，開啟 Visual Studio.sln 檔案，即可開始使用。

## <a name="configure-the-task-web-app"></a>設定工作的 web 應用程式

若要取得`TaskWebApp`與 Azure AD B2C 通訊，您需要提供一些常見的參數。 在 [`TaskWebApp`開啟專案`web.config`檔案中的專案根目錄及取代的值`<appSettings>`一節。 您可以將`AadInstance`， `RedirectUri`，及`TaskServiceUrl`值一樣。

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>取得存取權杖，然後呼叫工作 API

本節將討論如何才能存取網路與 Azure AD B2C 也受到保護的 API 使用期間使用 Azure AD B2C 登入接收到的 token。

本文並未涵蓋如何保護 API 的詳細資料。 若要瞭解如何網路 API 安全地驗證要求使用 Azure AD B2C，查看[網路 API 快速入門文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

### <a name="save-the-sign-in-token"></a>儲存權杖的正負號

首先，使用者進行驗證，（使用其中的原則），並從 Azure AD B2C 接收的登入權杖。  如果您不確定如何執行原則，請返回並嘗試[.NET web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)，瞭解如何 Azure AD B2C 的基本概念。

開啟檔案`App_Start\Startup.Auth.cs`。  有一個重要的變更，您必須對`OpenIdConnectAuthenticationOptions`-您必須設定`SaveSignInToken = true`。

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>取得控制站的權杖

`TasksController`負責通訊與 web API，傳送 HTTP 要求至 API 讀取、 建立及刪除工作。  Azure AD B2C 受保護的 API Becuase，您需要先擷取您在上述步驟中儲存的 token。

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

`BootstrapContext`包含登入您取得執行其中您 B2C 原則的 token。

### <a name="read-tasks-from-the-web-api"></a>從網路 API 讀取工作

如果您有一個權杖，您可以附加到 HTTP`GET`中要求`Authorization`安全地呼叫的標頭`TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>建立及刪除 API 在網頁上的工作

當您傳送時，請遵循相同的模式`POST`和`DELETE`網路 API，要求使用`BootstrapContext`擷取登入權杖。 我們實作您建立巨集指令。 您可以嘗試完成中的 [刪除] 動作`TasksController.cs`。

## <a name="run-the-sample-app"></a>執行範例應用程式

最後，建立並執行應用程式。 登入並登入，並建立登入使用者的工作。 登出，不同的使用者身分登入。 建立使用者的工作。 請注意工作儲存每位使用者在 API，因為 API 起收到的 token 使用者的身分識別。

參照的已完成的範例[，提供為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip)。 您也可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
