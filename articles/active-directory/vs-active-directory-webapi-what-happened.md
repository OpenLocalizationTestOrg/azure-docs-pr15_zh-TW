<properties
    pageTitle="找不到我的 WebApi 專案 （Visual Studio Azure Active Directory 連線服務） |Microsoft Azure "
    description="說明您可以使用 Visual Studio 連線至 Azure AD WebApi MVC 專案會發生什麼情況"
  services="active-directory"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>找不到我的 WebApi 專案 （Visual Studio Azure Active Directory 連線服務）

> [AZURE.SELECTOR]
> - [快速入門](vs-active-directory-webapi-getting-started.md)
> - [發生什麼事](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>已新增參照

###<a name="nuget-package-references"></a>NuGet 套件參照

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>.NET 參照

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>程式碼變更

###<a name="code-files-were-added-to-your-project"></a>檔案已新增至您的專案

驗證啟動類別**App_Start/Startup.Auth.cs**已新增至包含啟動邏輯 Azure AD 驗證您的專案。

###<a name="startup-code-was-added-to-your-project"></a>啟動程式碼新增至您的專案

如果您已在專案中啟動類別，以包含在通話更新**設定**方法`ConfigureAuth(app)`。 否則，啟動類別已新增至您的專案。


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>App.config 或 web.config 檔案有新的設定值。

已新增下列設定項目。
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>建立 Azure AD 應用程式

Azure AD 應用程式建立的目錄，您在精靈中選取。

[深入瞭解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>如果我核取 [*停用個別的使用者帳戶驗證*，哪些其他變更到專案？
已移除 NuGet 套件參照，請與檔案中移除，而且備份。 根據您的專案狀態，您可能必須以手動方式移除其他參考資料或檔案，或修改適當的程式碼。

###<a name="nuget-package-references-removed-for-those-present"></a>移除 （適用於這些簡報） NuGet 套件參照

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>備份及移除 （適用於這些簡報） 檔案的程式碼

每個下列檔案已備份，並且從專案移除。 備份檔案位於專案的目錄根目錄 '備份] 資料夾中。

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>程式碼備份檔案 （適用於這些簡報）

每個下列檔案已備份之前被取代。 備份檔案位於專案的目錄根目錄 '備份] 資料夾中。

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>如果我選取*讀取目錄資料*時，哪些其他變更到專案？

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>您的 app.config 或 web.config 進行其他變更

已新增下列其他設定項目。

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory 應用程式已更新
Azure Active Directory 應用程式更新包含*讀取目錄資料*權限及其他登錄機碼所建立的作為*Ida︰ 密碼*`web.config`檔案。

[深入瞭解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
