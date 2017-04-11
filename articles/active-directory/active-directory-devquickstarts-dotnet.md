<properties
    pageTitle="快速入門 azure AD.NET |Microsoft Azure"
    description="如何建立的.NET Windows 桌面應用程式整合 Azure AD 登入並 Azure AD 的通話受到保護的 Api 使用 OAuth。"
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


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Azure AD 整合 Windows 桌面 WPF 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

如果您開發桌面應用程式，Azure AD 可簡單且直接，以驗證您的使用者使用 Active Directory 帳戶。  同時也會啟用安全地使用任何受 Azure AD，例如 Office 365 Api 或 Azure API 的 API 的 web 應用程式。

Azure AD.NET 原生的用戶端需要存取受保護的資源，提供的 Active Directory 驗證庫或 ADAL。  ADAL 的生活中唯一的目的是讓您的應用程式，以取得存取權杖能輕鬆地。  若要示範如何只要輕鬆，在這裡，我們將建立的.NET WPF 待辦事項清單應用程式的︰

-   取得存取權杖撥 Azure AD 圖形 API 使用[OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
-   使用指定的別名在目錄中搜尋。
-   取出符號使用者。

若要建立完成的工作應用程式，您必須︰

2. Azure AD 註冊您的應用程式。
3. 安裝並設定 ADAL。
5. 若要從 Azure AD 取得權杖使用 ADAL。

若要開始，[下載應用程式的基本架構](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)。  您也必須 Azure AD 租用戶，您可以在其中建立使用者，並登錄應用程式。  如果您還沒有租用戶，[瞭解如何取得](active-directory-howto-tenant.md)。

## <a name="1-register-the-directorysearcher-application"></a>*1.註冊 DirectorySearcher 應用程式*
若要啟用您的應用程式，以取得權杖，您將必須註冊 Azure AD 租用戶，並授與 Azure AD 圖形 API 的存取權限︰

-   登入 Azure 管理入口網站
-   在左側導覽中，按一下 [在**Active Directory**
-   選取要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   遵循提示，並建立新的**原生的用戶端應用程式**。
    -   應用程式的**名稱**會說明您的使用者的應用程式
    -   **重新導向 Uri**是配置和字串的組合，Azure AD 將用來傳回 token 回應。  例如您的應用程式中，輸入特定值`http://DirectorySearcher`。
-   當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  此值必須在下一個區段中，因此複製的 [**設定**] 索引標籤。
- 也在 [**設定**] 索引標籤，找到 」 權限以其他應用程式 」 區段。  「 Azure Active Directory 」 應用程式中，新增 [**委派的權限****存取您組織的目錄**權限。  如此一來查詢圖形 API 使用者的應用程式。

## <a name="2-install--configure-adal"></a>*2.安裝與設定 ADAL*
有 Azure AD 中的應用程式之後，您可以安裝的 ADAL 並撰寫您的身分識別相關的程式碼。  為了讓 ADAL 能夠與 Azure AD 通訊，您需要提供的一些相關應用程式註冊資訊。
-   開始使用封裝管理員主控台 DirectorySearcher 專案新增 ADAL。

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   在 DirectorySearcher 專案中，開啟`app.config`。  取代值] 中的項目`<appSettings>`一節，以反映您輸入到 [Azure 入口網站中的值。  您的程式碼會參考這些值時，它會使用 ADAL。
    -   `ida:Tenant`是 Azure AD 租用戶，例如 contoso.onmicrosoft.com 網域
    -   `ida:ClientId`會將您複製的入口網站應用程式的 clientId。
    -   `ida:RedirectUri`會重新導向註冊，讓您在入口網站的 url。

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3.使用 ADAL 取得 AAD 權杖*
前 ADAL 的基本原則是，每當您的應用程式需要存取權杖，它只會呼叫`authContext.AcquireTokenAsync(...)`，ADAL 執行其餘部分。  

-   在 [`DirectorySearcher`開啟專案`MainWindow.xaml.cs`並找出`MainWindow()`方法。  第一個步驟是初始化您的應用程式`AuthenticationContext`-ADAL 的主要類別。  這是您將傳遞 ADAL 需要與 Azure AD 通訊，即該如何快取權杖座標。

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- 現在找出`Search(...)`方法，當使用者 cliks 「 搜尋 」] 按鈕的應用程式使用者介面中將叫用。  這個方法進行 Azure AD 圖形 API 來查詢 GET 要求使用者的 UPN 開頭為指定的搜尋字詞。  您要包含在 access_token 才能查詢圖形 API，但`Authorization`標頭的邀請-這是 ADAL 提供。

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- 當您的應用程式要求權杖，則可電話`AcquireTokenAsync(...)`，ADAL 會傳回一個權杖，不必要求使用者認證。  如果 ADAL 判斷使用者需要取得權杖登入，它會顯示登入] 對話方塊，收集使用者的認證，並傳回在成功驗證的權杖。  如果 ADAL 無法傳回權杖基於任何原因，則會擲回`AdalException`。
- 請注意，`AuthenticationResult`物件包含`UserInfo`可以用來收集的資訊可能需要您的應用程式的物件。  在 [DirectorySearcher，`UserInfo`會用於自訂使用者識別碼的應用程式使用者介面。

- 使用者按一下 [登出] 按鈕時，我們要確認下一個通話`AcquireTokenAsync(...)`會要求使用者登入。  使用 ADAL，這是只要簡單以清除權杖快取項目︰

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- 不過，如果使用者並按一下 [登出] 按鈕，您會想要維持在下一次執行 DirectorySearcher 的使用者的工作階段。  當應用程式啟動時，您可以核取 [現有的權杖 ADAL 的權杖快取，然後適當地更新使用者介面。  在`CheckForCachedToken()`方法，另一個呼叫`AcquireTokenAsync(...)`、 傳入這次`PromptBehavior.Never`參數。  `PromptBehavior.Never`不會提示使用者的登入，而且 ADAL 應該改用引發例外狀況，無法傳回權杖時，會告訴 ADAL。

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

恭喜您 ！ 您現在已使用.NET WPF 應用程式已驗證的使用者、 安全地呼叫 [Web Api 使用 OAuth 2.0 的功能，並取得基本的使用者資訊。  如果您未，現在是填入您的租用戶，且有些使用者的時間。  執行 DirectorySearcher 應用程式，並使用其中一個這些使用者登入。  根據其 UPN 其他使用者的搜尋。  關閉應用程式，然後重新執行。  請注意如何在使用者工作階段保持不變。  登出，並再次登入以另一位使用者。

ADAL 可讓您更輕鬆地將所有這些常見的身分識別功能合併到您的應用程式。  它會為您的快取管理、 OAuth 通訊協定支援，透過登入 UI，重新整理過期的權杖，以及其他簡報使用者處理 dirty 的所有工作。  您只需瞭解是單一 API 通話， `authContext.AcquireTokenAsync(...)`。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)。  您可以立即移至其他的案例。  若要嘗試︰

[安全的.NET Web 與 Azure AD API >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
