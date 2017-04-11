<properties
    pageTitle="找不到我的 MVC 專案 （Visual Studio Azure Active Directory 連線服務） |Microsoft Azure "
    description="說明當您可以使用 [連線的 Visual Studio 服務連線至 Azure AD 變化 MVC 專案"
    services="active-directory"
    documentationCenter="na"
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

# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>找不到我的 MVC 專案 （Visual Studio Azure Active Directory 連線服務）？

> [AZURE.SELECTOR]
> - [快速入門](vs-active-directory-dotnet-getting-started.md)
> - [發生什麼事](vs-active-directory-dotnet-what-happened.md)



## <a name="references-have-been-added"></a>已新增參照

### <a name="nuget-package-references"></a>NuGet 套件參照

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel.Tokens.Jwt**

### <a name="net-references"></a>.NET 參照

- **Microsoft.IdentityModel.Protocol.Extensions**
- **Microsoft.Owin**
- **Microsoft.Owin.Host.SystemWeb**
- **Microsoft.Owin.Security**
- **Microsoft.Owin.Security.Cookies**
- **Microsoft.Owin.Security.OpenIdConnect**
- **Owin**
- **System.IdentityModel**
- **System.IdentityModel.Tokens.Jwt**
- **System.Runtime.Serialization**

## <a name="code-has-been-added"></a>已新增的程式碼

### <a name="code-files-were-added-to-your-project"></a>檔案已新增至您的專案

驗證啟動類別**App_Start/Startup.Auth.cs**已新增至包含啟動邏輯 Azure AD 驗證您的專案。 此外，已加入控制器類別，Controllers/AccountController.cs，其中包含**SignIn()**和**SignOut()**方法。 最後的部分檢視**Views/Shared/_LoginPartial.cshtml**已新增巨集指令連結之登入/登出。

### <a name="startup-code-was-added-to-your-project"></a>啟動程式碼新增至您的專案

如果您已在專案中啟動類別，**設定**方法已更新包含**ConfigureAuth(app)**在通話中。 否則，啟動類別已新增至您的專案。

### <a name="your-appconfig-or-webconfig-has-new-configuration-values"></a>您的 app.config 或 web.config 有新的設定值

已新增下列設定項目。


    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="The selected Azure AD Domain" />
        <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
        <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
    </appSettings>

### <a name="an-azure-active-directory-ad-app-was-created"></a>建立 Azure Active Directory (AD) 應用程式
Azure AD 應用程式建立的目錄，您在精靈中選取。

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>如果我核取 [*停用個別的使用者帳戶驗證*，哪些其他變更到專案？
已移除 NuGet 套件參照，請與檔案中移除，而且備份。 根據您的專案狀態，您可能必須以手動方式移除其他參考資料或檔案，或修改適當的程式碼。

### <a name="nuget-package-references-removed-for-those-present"></a>移除 （適用於這些簡報） NuGet 套件參照

- **Microsoft.AspNet.Identity.Core**
- **Microsoft.AspNet.Identity.EntityFramework**
- **Microsoft.AspNet.Identity.Owin**

### <a name="code-files-backed-up-and-removed-for-those-present"></a>備份及移除 （適用於這些簡報） 檔案的程式碼

每個下列檔案已備份，並且從專案移除。 備份檔案位於專案的目錄根目錄 '備份] 資料夾中。

- **App_Start\IdentityConfig.cs**
- **Controllers\ManageController.cs**
- **Models\IdentityModels.cs**
- **Models\ManageViewModels.cs**

### <a name="code-files-backed-up-for-those-present"></a>程式碼備份檔案 （適用於這些簡報）

每個下列檔案已備份之前被取代。 備份檔案位於專案的目錄根目錄 '備份] 資料夾中。

- **Startup.cs**
- **App_Start\Startup.Auth.cs**
- **Controllers\AccountController.cs**
- **Views\Shared\_LoginPartial.cshtml**

## <a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>如果我選取*讀取目錄資料*時，哪些其他變更到專案？

已新增額外的參照。

###<a name="additional-nuget-package-references"></a>其他 NuGet 套件參照

- **EntityFramework**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **System.Spatial**

###<a name="additional-net-references"></a>其他.NET 參照

- **EntityFramework**
- **EntityFramework.SqlServer**
- **Microsoft.Azure.ActiveDirectory.GraphClient**
- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.IdentityModel.Clients.ActiveDirectory**
- **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms**
- **System.Spatial**

###<a name="additional-code-files-were-added-to-your-project"></a>其他檔案已新增至您的專案

兩個檔案已新增至支援權杖快取︰ **Models\ADALTokenCache.cs**和**Models\ApplicationDbContext.cs**。  其他控制器和檢視已加入說明使用 Azure 圖形 Api 的存取使用者設定檔資訊。  這些檔案是**Controllers\UserProfileController.cs**和**Views\UserProfile\Index.cshtml**。

###<a name="additional-startup-code-was-added-to-your-project"></a>額外的啟動程式碼新增至您的專案

在**startup.auth.cs**檔案，**通知**的成員屬於**OpenIdConnectAuthenticationOptions**加入新**OpenIdConnectAuthenticationNotifications**物件。  這是啟用接收 OAuth 程式碼，並用於存取權杖 exchange 它。

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>您的 app.config 或 web.config 進行其他變更

已新增下列其他設定項目。

    <appSettings>
        <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
    </appSettings>

已新增下列設定區段和連線字串。

    <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>
    <connectionStrings>
        <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    <entityFramework>
        <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
          <parameters>
            <parameter value="mssqllocaldb" />
          </parameters>
        </defaultConnectionFactory>
        <providers>
          <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
        </providers>
    </entityFramework>


###<a name="your-azure-active-directory-app-was-updated"></a>Azure Active Directory 應用程式已更新
Azure Active Directory 應用程式的更新包含*讀取目錄資料*權限，這作為*ida: ClientSecret* **web.config**檔案中建立其他的金鑰。

[深入瞭解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
