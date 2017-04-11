<properties
   pageTitle="保護 multitenant 應用程式中的後端網路 API |Microsoft Azure"
   description="如何保護的後端網路 API"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="securing-a-backend-web-api-in-a-multitenant-application"></a>保護 multitenant 應用程式中的後端網路 API

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分]。 此外還有隨附這一系列完成[範例應用程式]。

[Tailspin 問卷]應用程式使用的後端 web API 管理上問卷的 CRUD 作業。 例如，使用者按一下 「 我的問卷調查 」，web 應用程式就會傳送 HTTP 要求網路 API:

```
GET /users/{userId}/surveys
```

網路 API 會傳回 JSON 物件︰

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

網路 API 不允許匿名要求，因此 web 應用程式必須使用 OAuth 2 承載者權杖自我驗證。

> [AZURE.NOTE] 這是伺服器對伺服器的案例。 應用程式不會無法撥打任何 AJAX API 從瀏覽器用戶端。

有兩個主要的方法，您可以採取︰

- 委派的使用者身分識別。 使用者的身分驗證的 web 應用程式。
- 應用程式的身分識別。 其用戶端識別碼，使用 OAuth2 用戶端認證流程驗證的 web 應用程式。

Tailspin 應用程式實作委派的使用者身分識別。 以下是主要差異︰

**委派的使用者身分識別**

- 傳送至 [網路 API 承載者權杖包含使用者身分識別。
- 網路 API 可讓您根據使用者身分識別的授權決策。
- Web 應用程式需要處理從網路 API，403 （禁止） 錯誤，如果使用者未獲授權執行的動作。
- 一般而言，web 應用程式仍會影響 UI，例如顯示或隱藏使用者介面元素部分授權決策）。
- Web API 可能可以使用不受信任的用戶端，例如 JavaScript 應用程式或原生的用戶端應用程式。

**應用程式識別碼**

- 網路 API 不會取得使用者的相關資訊。
- 網路 API 無法執行任何根據使用者身分識別的授權。 所有的授權決策所做的 web 應用程式。  
- Web API 無法使用不受信任的用戶端 （JavaScript 或原生的用戶端應用程式）。
- 這種方式可能較為簡單實作，因為 Web API 中沒有授權邏輯。

這兩種方法，web 應用程式必須取得存取權杖，也就是 [撥號網路 API 所需的認證。

- 委派的使用者身分識別的權杖有來自 IDP，其中可以發行的權杖代表使用者。

- 用戶端認證，可能會取得的權杖 IDP 從應用程式，或將其主控自己的權杖伺服器中。 (但沒有中從頭開始撰寫 token 伺服器; 使用像[IdentityServer3]完善測試的架構。)如果您驗證方法 Azure AD，強烈建議您從 Azure AD，即使使用用戶端認證流程取得存取權杖。

本文其餘假設與 Azure AD 驗證應用程式。

![快速存取權杖](media/guidance-multitenant-identity/access-token.png)

## <a name="register-the-web-api-in-azure-ad"></a>Azure AD 在註冊網路 API

為了讓 Azure AD 發出的網路 API 承載者權杖，您需要設定 Azure AD 的一些事項。

1. [註冊 API Azure AD 的網頁]。

2. 新增用戶端識別碼 web 應用程式的網路 API 應用程式資訊清單中，在`knownClientApplications`屬性。 請參閱[更新應用程式資訊清單]。

3. [給呼叫網路 API 的 web 應用程式權限]。

  在 Azure 管理入口網站，您可以設定兩種類型的權限︰ 應用程式識別碼 （用戶端認證流量），或 「 委派的權限 」 委派的使用者身分識別的 [應用程式權限]。

  ![委派的權限](media/guidance-multitenant-identity/delegated-permissions.png)

## <a name="getting-an-access-token"></a>快速存取權杖

呼叫之前網路 API，web 應用程式會存取權杖從 Azure AD。 在.NET 應用程式中，使用 [ [Azure AD 驗證文件庫 (ADAL).net][ADAL]。

在 OAuth 2 授權的程式碼流程，應用程式交換存取權杖授權程式碼。 下列程式碼會使用 ADAL 取得存取權杖。 將此程式碼稱為期間`AuthorizationCodeReceived`事件。

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

以下是所需的各種參數︰

- `authority`. 從登入的使用者的租用戶識別碼。 （不 SaaS 提供者的租用戶識別碼）  
- `authorizationCode`. 您回來從 IDP 驗證碼。
- `clientId`. Web 應用程式的用戶端識別碼
- `clientSecret`. Web 應用程式的用戶端的密碼。
- `redirectUri`. 重新導向 URI OpenID 為您設定連線。 這是與權杖回撥 IDP。
- `resourceID`. 應用程式識別碼的 URI 網路 API，您建立您註冊 Azure AD 中的網路 API 時
- `tokenCache`. 快取存取權杖物件。 請參閱[權杖快取]。

如果`AcquireTokenByAuthorizationCodeAsync`順利完成，ADAL 快取權杖。 之後，您也可以撥打 AcquireTokenSilentAsync，以取得權杖從快取︰

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

位置`userId`是使用者的物件識別碼中找不到`http://schemas.microsoft.com/identity/claims/objectidentifier`宣告。

## <a name="using-the-access-token-to-call-the-web-api"></a>使用呼叫網路 API 的存取權杖

一旦您有權杖，以傳送授權標頭的 HTTP 要求網路 API。

```
Authorization: Bearer xxxxxxxxxx
```

下列擴充方法的問卷調查應用程式設定授權標頭 HTTP 要求時，使用**HttpClient**類別。

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] 請參閱[HttpClientExtensions.cs]。

## <a name="authenticating-in-the-web-api"></a>驗證在網路 API

網路 API 有驗證承載者權杖。 在 ASP.NET 核心 1.0 中，您可以使用[Microsoft.AspNet.Authentication.JwtBearer] [JwtBearer]套件。 此套件提供可接收 OpenID 連線承載者權杖應用程式中介軟體。

在您的網路 API 註冊介軟體`Startup`類別。

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] 請參閱[Startup.cs]。

- **對象**。 將此設為網路 API，應用程式識別碼 URL 您註冊 Azure AD 網路 API 時所建立的。
- **授權**。 Multitenant 應用程式，將它設定為`https://login.microsoftonline.com/common/`。
- **TokenValidationParameters**。 Multitenant 應用程式，將**ValidateIssuer**為 false。 這表示應用程式會驗證發行者。
- **事件**是衍生自**JwtBearerEvents**的類別。

### <a name="issuer-validation"></a>簽發者驗證

驗證的權杖發行者**JwtBearerEvents.ValidatedToken**事件中。 以 「 iss 」 宣告傳送發行者。

問卷應用程式中網路 API 無法處理[註冊的租用戶]。 因此，它只會檢查簽發者是否已在應用程式資料庫中。 如果沒有，就會擲回例外狀況，這會導致驗證失敗。

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] 請參閱[SurveysJwtBearerEvents.cs]。

您也可以使用**ValidatedToken**事件執行[宣告轉換]。 請記住，宣告來自直接 Azure AD，因此如果 web 應用程式沒有任何宣告轉換，這些不會反映在承載者權杖網路 API 接收。

## <a name="authorization"></a>授權

一般討論區的授權，請參閱[角色和資源為基礎的授權][Authorization]。 

JwtBearer 介軟體處理授權回應。 例如，若要限制控制站的動作已驗證的使用者，使用**[授權]** atrribute 並指定**JwtBearerDefaults.AuthenticationScheme**為驗證配置︰

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```

如果使用者未通過驗證，這樣就會傳回 401 狀態碼。

若要限制控制器動作 authorizaton 原則，請在**[授權]**屬性中指定原則名稱︰

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

如果使用者已驗證，但未獲授權，傳回 401 狀態碼如果使用者未通過驗證及 403。 註冊啟動的原則︰

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```

## <a name="next-steps"></a>後續步驟

- 閱讀本系列下一個文章︰[在 multitenant 應用程式中的快取存取權杖][token cache]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[一系列的一部分]: guidance-multitenant-identity.md
[Tailspin 問卷]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Azure AD 在註冊網路 API]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[更新應用程式資訊清單]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[給呼叫網路 API 的 web 應用程式權限]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[權杖快取]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[註冊的租用戶]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[宣告轉換]: guidance-multitenant-identity-claims.md#claims-transformations
[Authorization]: guidance-multitenant-identity-authorize.md
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[token cache]: guidance-multitenant-identity-token-cache.md
