<properties
   pageTitle="Multitenant 應用程式中的授權 |Microsoft Azure"
   description="如何在 multitenant 應用程式中執行的授權"
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

# <a name="role-based-and-resource-based-authorization-in-multitenant-applications"></a>Multitenant 應用程式中的角色和資源為基礎的授權

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分]。 此外還有隨附這一系列完成[範例應用程式]。

我們[參考實作]是 ASP.NET 核心 1.0 應用程式。 本文中我們會查看授權，兩個常見的方式使用授權 Api ASP.NET 核心 1.0 中提供。

-   **角色為基礎的授權**。 授權指派給使用者的角色所根據的動作。 例如，某些動作需要系統管理員角色。
-   **資源為基礎的授權**。 授權特定資源為基礎的動作。 例如，每個資源具有擁有者。 擁有者可以刪除資源。其他使用者無法使用。

一般的應用程式會採用混合兩者。 例如，若要刪除資源，使用者必須是資源擁有人_或_管理員]。


## <a name="role-based-authorization"></a>角色為基礎的授權

[Tailspin 問卷][Tailspin]應用程式定義的下列角色︰

- 系統管理員。 可以執行任何問卷屬於該租用戶上所有的 CRUD 作業。
- 建立者。 可以建立新的問卷
- 閱讀程式。 可以讀取該租用戶屬於任何問卷

角色套用到的應用程式的_使用者_。 問卷應用程式中，使用者會是系統管理員，建立者或閱讀程式。

如何定義和管理角色的討論，請參閱[應用程式角色]。

無論您如何管理角色，看起來類似授權程式碼。 ASP.NET 核心 1.0 引進了稱為[授權原則]抽象[policies]。 使用此功能，您可以程式碼中，定義授權原則，然後將這些原則套用至控制器的動作。 從控制器被分離原則。

### <a name="create-policies"></a>建立原則

若要定義的原則，先建立一個類別，實作`IAuthorizationRequirement`。 是衍生自最簡單的`AuthorizationHandler`。 在 [`Handle`方法，請檢查相關 claim(s)。

以下是從 Tailspin 問卷應用程式的範例︰

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] 請參閱[SurveyCreatorRequirement.cs]

這個類別定義使用者以建立新的問卷的需求。 使用者必須在 SurveyAdmin 或 SurveyCreator 角色。

在您啟動的類別，定義具名的原則，其中包含一或多個需求。 如果有多個的需求，使用者必須符合授權的_每一_項需求。 下列程式碼定義兩個原則︰

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] 請參閱[Startup.cs]

將此程式碼也會驗證配置，它會告訴的 ASP.NET 授權失敗時，應該都可以執行哪些驗證介軟體。 在此案例中，我們指定 cookie 驗證介軟體，因為 cookie 驗證介軟體可以將使用者導向至 「 禁止 」 頁面。 在 [cookie 介軟體; AccessDeniedPath 選項中設定禁止頁面的位置請參閱[設定驗證介軟體]。

### <a name="authorize-controller-actions"></a>授權控制器的動作

最後，若要授權 MVC 控制器動作，設定原則中`Authorize`屬性︰

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

在舊版的 ASP.NET 中，您應該屬性設定 [**角色**] 屬性︰

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

這仍支援 ASP.NET 核心 1.0，，但有一些缺點與授權原則︰

-   假設特定宣告類型。 原則可以檢查任何宣告類型。 角色是只宣告的類型。
-   角色名稱是硬屬性。 使用原則，授權邏輯是全部置於一處，讓它更容易更新或甚至載入從設定的設定。
-   原則啟用更複雜的授權決策 (例如年齡 > = 21) 的不表示依據簡單的角色的成員資格。

## <a name="resource-based-authorization"></a>依據資源授權

每當授權取決於特定資源的會受到作業時，就會發生_資源為主的授權_。 Tailspin 問卷應用程式中，每個問卷會具備擁有者及零對多參與者。

-   擁有者可以讀取、 更新、 刪除、 發佈及解除發佈問卷。
-   擁有者可以指派給問卷的參與者。
-   參與者可以讀取及更新問卷。

請注意，「 擁有者 」 和 「 參與者 」 不應用程式的角色。他們會儲存每個問卷，應用程式資料庫。 若要查看使用者是否可以刪除問卷，例如，應用程式會檢查是否使用者該問卷的擁有者。

在 ASP.NET 核心 1.0，來實作資源為基礎的授權衍生自**AuthorizationHandler**並覆寫的**處理**方法。

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

請注意此課程強型別問卷物件。  在啟動時註冊 DI 類別︰

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

若要執行授權檢查，請使用**IAuthorizationService**介面，您可以將您的控制站插入。 下列程式碼會檢查是否使用者可以讀取問卷︰

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

因為我們將在`Survey`物件，將叫用這個呼叫`SurveyAuthorizationHandler`。

在 [授權程式碼，好的方法是彙總的所有使用者的角色和資源權限]，然後核取彙總設定針對所需的作業。
以下是從問卷應用程式中的範例。 應用程式定義數種權限類型︰

- 管理員
- 參與者
- 建立者
- 擁有者
- 閱讀程式

應用程式也會定義一組在問卷調查可能的作業︰

- 建立
- 已讀取
- 更新
- 刪除
- 發佈
- Unpublsh

下列程式碼會建立特定的使用者] 和 [問卷的權限的清單。 請注意，將此程式碼的外觀在使用者的應用程式角色和調查的擁有者/參與者欄位。

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] 請參閱[SurveyAuthorizationHandler.cs]。

在多租用戶的應用程式，您必須確定的權限不 」 遺漏 「 另一個租用戶的資料。 在 [問卷應用程式中，[參與者] 權限允許跨租用戶&mdash;您可以從另一個租用戶 contriubutor 分派其他人。 其他的權限類型限於屬於該使用者的租用戶的資源。 強制執行這項需求，程式碼會檢查的租用戶識別碼之前授與權限。 (`TenantId`欄位建立問卷時所指派。)

下一步是檢查權限作業 （讀取、 更新、 刪除等）。 問卷應用程式實作此步驟的方式，使用函數的查閱表格︰

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```


## <a name="next-steps"></a>後續步驟

- 閱讀本系列下一個文章︰[保護後端網頁 multitenant 應用程式中的 API][web-api]
- 若要進一步瞭解 ASP.NET 1.0 核心中的資源式授權，請參閱[資源授權][rbac]。

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[一系列的一部分]: guidance-multitenant-identity.md
[應用程式角色]: guidance-multitenant-identity-app-roles.md
[policies]: https://docs.asp.net/en/latest/security/authorization/policies.html
[rbac]: https://docs.asp.net/en/latest/security/authorization/resourcebased.html
[參照實作]: guidance-multitenant-identity-tailspin.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[設定驗證介軟體]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[web-api]: guidance-multitenant-identity-web-api.md
