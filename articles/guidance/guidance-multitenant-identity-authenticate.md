<properties
   pageTitle="驗證在 multitenant 應用程式 |Microsoft Azure"
   description="如何 multitenant 應用程式可以驗證使用者從 Azure AD"
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
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="authentication-in-multitenant-apps-using-azure-ad-and-openid-connect"></a>使用 Azure AD 的 multitenant 應用程式中的驗證及 OpenID 連線

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分](guidance-multitenant-identity.md)。 此外還有隨附這一系列完成[範例應用程式]。

本文將說明如何 multitenant 應用程式可以驗證使用者 Azure Active Directory (Azure AD)，用來驗證 OpenID 連線 (OIDC)。

## <a name="overview"></a>概觀

我們[參考實作](guidance-multitenant-identity-tailspin.md)是 ASP.NET 核心 1.0 應用程式。 應用程式會使用內建的 OpenID 連線介軟體，才能執行 OIDC 驗證流程。 下圖顯示當使用者登入，高層級的會發生什麼情況。

![驗證流程](media/guidance-multitenant-identity/auth-flow.png)

1.  使用者按一下 [登入] 按鈕的應用程式中。 這個動作會處理 MVC 控制器。
2.  MVC 控制器傳回**ChallengeResult**動作。
3.  介軟體攔截**ChallengeResult** ，並建立 302 回應，使用者重新導向至 Azure AD 登入頁面。
4.  Azure AD 驗證使用者。
5.  Azure AD 會傳送至應用程式識別碼權杖。
6.  介軟體驗證的識別碼 token。 此時，使用者現在驗證應用程式內。
7.  介軟體使用者重新導向至應用程式。

## <a name="register-the-app-with-azure-ad"></a>Azure AD 登錄應用程式

若要啟用 OpenID 連線，SaaS 提供者註冊內自己 Azure AD 租用戶的應用程式。

若要註冊應用程式，請遵循[與 Azure Active Directory 的整合應用程式](../active-directory/active-directory-integrating-applications.md)，在 [[新增應用程式](../active-directory/active-directory-integrating-applications.md#adding-an-application)] 區段中的步驟進行。

在 [**設定**] 頁面︰

-   請注意，用戶端識別碼
-   在 [**應用程式是多租用戶**下, 選取 [**是**]。
-   設定**回覆 URL** url Azure AD 將傳送給驗證回應。 您可以使用您的應用程式的基本的 URL。
  - 附註︰ URL 路徑可以是任何項目，只要主機名稱符合您部署的應用程式。
  - 您可以將多個回覆 Url。 在開發期間，您可以使用`localhost`地址，並在本機上執行應用程式。
-   產生一個用戶端的秘訣︰**機碼**底下，按一下下拉式清單的 [顯示**選取持續時間**，然後挑選 [1 或 2 年。 當您按一下 [**儲存**時，會顯示索引鍵。 請務必要複製的值，因為它沒有顯示一次當您重新載入 [設定] 頁面。

## <a name="configure-the-auth-middleware"></a>設定驗證介軟體

本節說明如何設定在 ASP.NET 核心 1.0 驗證介軟體 multitenant 驗證 OpenID 連線。

在您啟動的類別，新增 OpenID 連線介軟體︰

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] 請參閱[Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs)。

如需啟動類別的詳細資訊，請參閱 ASP.NET 核心 1.0 文件中的[應用程式啟動](https://docs.asp.net/en/latest/fundamentals/startup.html)。

設定下列介軟體選項︰

- **ClientId**。 應用程式的用戶端識別碼，您適合您註冊 Azure AD 中的應用程式時。
- **授權**。 Multitenant 應用程式，將它設定為`https://login.microsoftonline.com/common/`。 這是 Azure AD 常見端點，可讓使用者的登入任何 Azure AD 租用戶的 URL。 如需常見的結束點的詳細資訊，請參閱[此部落格文章](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/)。
- 在**TokenValidationParameters**，設定**ValidateIssuer**為 false。 這表示應用程式會負責驗證識別碼權杖中的發行者] 值。 （介軟體仍驗證的權杖本身）。如需有關驗證發行者的詳細資訊，請參閱[發行者驗證](guidance-multitenant-identity-claims.md#issuer-validation)。
- **CallbackPath**。 將其設定為路徑的回覆 URL 中，您註冊 Azure AD 中。 例如，如果回覆 URL 是`http://contoso.com/aadsignin`，應該**CallbackPath** `aadsignin`。 如果您未設定此選項，預設值是`signin-oidc`。
- **PostLogoutRedirectUri**。 指定使用者登出之後重新導向的 URL。 這個應該可讓匿名要求的頁面&mdash;通常是 [首頁] 頁面。
- **SignInScheme**。 將此值設`CookieAuthenticationDefaults.AuthenticationScheme`。 這項設定表示驗證使用者之後，使用者宣告會儲存在本機 cookie 中。 此 cookie 是如何使用者皆會保持登入瀏覽工作階段。
- **事件。** 事件回撥;請參閱[驗證事件](#authentication-events)。

新增 Cookie 驗證介軟體以管道的郵件。 此介軟體負責撰寫使用者要宣告 cookie，並在後續的頁面載入時，然後讀取 cookie。

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## <a name="initiate-the-authentication-flow"></a>啟動驗證流程

若要在 ASP.NET MVC 開始驗證流程，請從控制器傳回**ChallengeResult** :

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

會傳回 302 （找） 回應重新導向至驗證端點介軟體。

## <a name="user-login-sessions"></a>使用者登入工作階段

如上所述，當使用者第一次登入，Cookie 驗證介軟體寫入到 cookie 使用者宣告。 之後，請閱讀 cookie 驗證 HTTP 要求。

根據預設，cookie 介軟體將[工作階段 cookie][session-cookie]，哪些取得一次刪除使用者關閉瀏覽器。 下一次使用者下一步造訪網站，則必須再次登入。 不過，如果您為 true **ChallengeResult**中設定**IsPersistent** ，介軟體將常設 cookie，讓使用者皆會保持登入之後關閉瀏覽器。 您可以設定 cookie 到期日。請參閱[控制 cookie 選項][cookie-options]。 常設 cookie 更方便的使用者，但可能不適用於某些應用程式 （例如，銀行應用程式） 要讓使用者在每次登入。

## <a name="about-the-openid-connect-middleware"></a>關於 OpenID 連線介軟體

Asp.net OpenID 連線介軟體隱藏大部分的通訊協定詳細資料。 本節包含有關實作，可能會讓您瞭解通訊協定流程很有用的一些事項。

首先，讓我們逐行查看驗證流程而言 ASP.NET （略過 OIDC 通訊協定流程應用程式和 Azure AD 之間的詳細資料）。 下圖顯示程序。

![登入流程](media/guidance-multitenant-identity/sign-in-flow.png)

在此圖表中，有兩個 MVC 控制器。 帳戶控制器處理登入要求並常用控制器提供 [首頁] 頁面。

以下是驗證程序︰

1. 使用者按一下 [登入] 按鈕，然後在瀏覽器傳送 GET 要求。 例如︰ `GET /Account/SignIn/`。
2. 帳戶控制器傳回`ChallengeResult`。
3. OIDC 介軟體會傳回 HTTP 302 回應時，重新導向到 Azure AD。
4. 在瀏覽器會驗證要求傳送給 Azure AD
5. 使用者登入 Azure AD，並將 Azure AD 給驗證回應。
6. OIDC 介軟體建立宣告本金，並將其傳遞給 Cookie 驗證介軟體。
7. Cookie 介軟體序列化宣告本金，並設定 cookie。
8. OIDC 介軟體重新導向至應用程式的回撥 URL。
10. 在瀏覽器遵循重新導向]，在邀請中傳送 cookie。
11. 還原序列化本金宣告 cookie cookie 介軟體，並設定`HttpContext.User`等於宣告本金。 要求傳送到 MVC 控制站。

### <a name="authentication-ticket"></a>驗證票證

如果驗證成功，OIDC 介軟體會建立驗證票證，其中包含宣告主體保留使用者的宣告。 您可以存取內**AuthenticationValidated**或**TicketReceived**事件票證。

> [AZURE.NOTE] 完成整個驗證流程時，直到`HttpContext.User`仍保留匿名主體_無法_驗證的使用者。 匿名本金具有空白宣告集合。 完成驗證，並還原序列化應用程式重新導向 cookie 介軟體之後的驗證 cookie 及集`HttpContext.User`宣告主體代表已驗證的使用者。

### <a name="authentication-events"></a>驗證事件

驗證程序期間 OpenID 連線介軟體會引發一系列的事件︰

- **RedirectToAuthenticationEndpoint**。 向右呼叫之前介軟體會重新導向至驗證結束點。 您可以使用此事件來修改重新導向 URL;例如，若要新增要求參數。 如需範例，請參閱[新增管理員同意提示](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt)。

- **AuthorizationResponseReceived**。 稱為介軟體身分識別提供者 (IDP)，從接收驗證回應後，但介軟體驗證回應。  

- **AuthorizationCodeReceived**。 名為 [與授權程式碼。

- **TokenResponseReceived**。 後介軟體存取權杖從取得 IDP 呼叫。 僅適用於授權的程式碼流程。

- **AuthenticationValidated**。 後介軟體驗證的識別碼 token 呼叫。 此時，應用程式有一組有關使用者驗證宣告。 若要執行其他驗證宣告，或轉換宣告，您可以使用此事件。 請參閱[使用宣告](guidance-multitenant-identity-claims.md)。

- **UserInformationReceived**。 如果介軟體取得使用者設定檔，從使用者資訊端點，呼叫。 僅適用於授權的程式碼流程和只有`GetClaimsFromUserInfoEndpoint = true`選項介軟體中。

- **TicketReceived**。 完成驗證一部分。 這是最後一個事件，假設變異數的驗證成功。 此事件處理完畢之後，使用者登入這個應用程式。

- **AuthenticationFailed**。 如果驗證失敗，呼叫。 使用此事件處理驗證失敗&mdash;，來重新導向到錯誤頁面。

若要提供這些事件回撥，設定**事件**選項介軟體。 有兩種不同方式宣告事件處理常式︰ 內嵌 lambda，或從**OpenIdConnectEvents**衍生的類別中。

內嵌於 lambda:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

從**OpenIdConnectEvents**衍生︰

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

如果您的事件回撥有大量的任何邏輯，它們不干擾您啟動的類別，建議第二種方法。 我們參考實作會使用這種方法。請參閱[SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs)。

### <a name="openid-connect-endpoints"></a>OpenID 連接端點

Azure AD 支援[OpenID 連線探索](https://openid.net/specs/openid-connect-discovery-1_0.html)該處身分識別提供者 (IDP) 會傳回 JSON 中繼資料文件從[已知的端點](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)。 中繼資料文件包含的資訊，例如︰

-   授權端點的 URL。 這是應用程式重新導向至使用者進行驗證的位置。
-   [結束工作階段 」 端點，應用程式移至登出使用者的位置的 URL。
-   若要取得簽章的金鑰，用戶端使用來驗證其會從 IDP OIDC 權杖 URL。

根據預設，OIDC 介軟體知道如何擷取這個中繼資料。 設定 [**授權**] 選項介軟體，在和介軟體建構中繼資料的 URL。 （您可以設定覆寫的中繼資料 URL **MetadataAddress**選項。）

### <a name="openid-connect-flows"></a>OpenID 連線流

根據預設，OIDC 介軟體，請使用表單文章的回應模式混合式流程。

-   _混合式流程_表示用戶端可以取得識別碼權杖和授權程式碼中相同的往返授權伺服器。
-   _表單張貼回應模式_表示授權伺服器使用 HTTP 張貼的邀請傳送至應用程式的識別碼權杖和授權程式碼。 值的表單 urlencoded (內容類型 ="應用程式/x-www-表單-urlencoded 」)。

當 OIDC 介軟體重新導向至授權端點時，重新導向 URL 會包含所有 OIDC 所需的查詢字串參數。 混合式流程︰

-   client_id。 此值設**ClientId**選項
-   範圍 = 」 openid 設定檔 」，也就是 OIDC 邀請，我們希望使用者的設定檔。
-   response_type = 「 程式碼 id_token 」。 這會指定混合式流程。
-   response_mode = 「 form_post 」。 指定表單文章的回應。

若要指定不同的流程，請將**ResponseType**屬性的選項。 例如︰

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

## <a name="next-steps"></a>後續步驟

- 閱讀本系列下一個文章︰[使用宣告式 multitenant 應用程式中的身分識別][claims]


[claims]: guidance-multitenant-identity-claims.md
[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
