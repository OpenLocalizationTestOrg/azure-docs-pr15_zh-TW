<properties
    pageTitle="快速入門 azure AD.NET |Microsoft Azure"
    description="如何建立與驗證與授權 Azure AD 整合.NET MVC Web API。"
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>保護使用從 Azure AD 承載者權杖 Web API

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

如果您建立的應用程式，提供受保護的資源，您必須知道如何防止未經授權存取這些資源的存取權。
Azure AD 很簡單且直接保護網路 API OAuth 承載者 2.0 存取權杖使用只需幾行程式碼。

Asp.NET web 應用程式，您可以完成使用 Microsoft 的實作包含在.NET Framework 4.5 社群導向 OWIN 介軟體。  以下我們將使用 OWIN 若要建立的 「 待辦事項清單 」 web API:
-   將指定的 API 受到保護。
-   驗證的 Web API 呼叫包含有效存取權杖。

若要這麼做，您必須︰

1. 註冊 Azure AD 應用程式
2. 將您的應用程式設定為使用 OWIN 驗證管道的郵件。
3. 設定用戶端應用程式撥打至執行清單 Web API

若要開始，[下載應用程式的基本架構](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)。  每一個是 Visual Studio 2013 解決方案。  您也必須要註冊您的應用程式 Azure AD 租用戶。  如果您沒有，[瞭解如何取得](active-directory-howto-tenant.md)。


## <a name="1--register-an-application-with-azure-ad"></a>*1.註冊 Azure AD 應用程式*
若要保護您的應用程式，您必須在您的租用戶中建立的應用程式，並提供幾項重要資訊 Azure AD。

-   登入[Azure 管理入口網站](https://manage.windowsazure.com)
-   在左側導覽中，按一下 [在**Active Directory**
-   選取要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   依照提示操作，並建立新**Web 應用程式及/或 WebAPI**。
    -   應用程式的**名稱**會說明您的應用程式的使用者。  輸入 「 待辦事項清單服務]。
    -   **重新導向 Uri**是配置和字串組合 Azure AD 會用來傳回任何權杖您要求的應用程式。 輸入`https://localhost:44321/`這個值。
-   當您完成註冊時，請瀏覽至 [**設定**] 索引標籤，然後找出 [**應用程式識別碼 URI** ] 欄位。  租用戶專屬識別碼輸入此值，例如︰`https://contoso.onmicrosoft.com/TodoListService`
- 儲存設定。  保持開啟入口網站，您將需要引進了註冊您的用戶端應用程式。

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2.設定您的應用程式使用 OWIN 驗證管線*

您已註冊應用程式與 Azure AD，必須先設定您的應用程式才能驗證傳入要求和權杖 Azure AD 通訊。

-   若要開始，開啟方案，並使用套件管理員主控台 TodoListService 專案中新增 OWIN 介軟體 NuGet 套件。

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   稱為 TodoListService 專案中新增 OWIN 啟動類別`Startup.cs`。  右邊，按一下 [專案 1--**新增** --> **新項目**1--搜尋 「 OWIN 」。  將叫用 OWIN 介軟體`Configuration(…)`應用程式啟動時的方法。
-   變更類別宣告，以`public partial class Startup`-我們已實作您此課程的一部分，另一個檔案。  在 [`Configuration(…)`方法，請設定 web 應用程式的驗證 ConfgureAuth(...) 通話。

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   開啟檔案`App_Start\Startup.Auth.cs`及實作`ConfigureAuth(…)`方法。  在中所提供的參數`WindowsAzureActiveDirectoryBearerAuthenticationOptions`做為您的應用程式進行通訊的 Azure AD 座標。

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   現在，您可以使用`[Authorize]`來保護您的控制站和動作 JWT 承載者驗證的屬性。  裝飾`Controllers\TodoListController.cs`授權標記的類別。  這會強制使用者登入，才能存取該頁面。

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- 當有授權的來電者成功叫用下列其中一項`TodoListController`Api，動作可能需要來電者之間的相關資訊的存取權。  OWIN 提供存取中透過承載者權杖宣告`ClaimsPrincpal`物件。  
- Web Api 的一般需求驗證 」 範圍 」 中的權杖-這可確保使用者具有同意存取 Todo 清單服務所需的權限︰

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   最後，開啟`web.config`TodoListService 專案的根目錄中的檔案，然後輸入您的設定值，以`<appSettings>`一節。
  - `ida:Tenant` Azure AD 租用戶，例如 「 contoso.onmicrosoft.com 」 的名稱。
  - 您`ida:Audience`應用程式識別碼 uri 您輸入 Azure 入口網站中的應用程式。

## <a name="3--configure-a-client-application--run-the-service"></a>*3.設定用戶端應用程式與執行服務*
您可以看到 Todo 清單服務的動作前，必須先設定 Todo 清單用戶端，讓它可以先從 AAD 取得權杖進行通話的服務。

- 瀏覽回[Azure 管理入口網站](https://manage.windowsazure.com)
- 建立新的應用程式 Azure AD 租用戶，並在提示字元中，選取**原始的用戶端應用程式**。
    -   應用程式的**名稱**會說明您的使用者的應用程式
    -   輸入`http://TodoListClient/`**重新導向 Uri**值。
- 當您完成註冊時，AAD 將會是唯一的**用戶端識別碼**指派您的應用程式。 此值必須在下一個步驟中，因此複製的 [設定] 索引標籤。
- 也在 [**設定**] 索引標籤，找到 」 權限以其他應用程式 」 區段。 按一下 [新增應用程式]。 在 [顯示] 下拉式清單中，選取 [所有應用程式]，然後按一下上方的核取記號。 找出及按一下您要執行清單中的服務，然後按一下底部的核取記號，若要新增應用程式。 選取 「 Access 來執行清單服務 」 從 [委派的權限] 下拉式清單中，並儲存設定。


- 在 Visual Studio 中，開啟`App.config`TodoListClient 中的專案，並輸入您的設定值，以`<appSettings>`一節。
  - `ida:Tenant` Azure AD 租用戶，例如 「 contoso.onmicrosoft.com 」 的名稱。
  - 您`ida:ClientId`您複製從 Azure 入口網站應用程式識別碼。
  - 您`todo:TodoListResourceId`應用程式識別碼 uri 在 Azure 入口網站中所輸入的執行清單服務應用程式。

最後，清理、 建立及執行每個專案 ！  如果您未，現在正是您使用的租用戶中建立新的使用者 *。 onmicrosoft.com 網域。  登入的待辦事項清單用戶端與該使用者，並將部分任務新增至使用者的待辦事項清單]。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)。  您可以立即移至多個額外的身分識別案例。

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
