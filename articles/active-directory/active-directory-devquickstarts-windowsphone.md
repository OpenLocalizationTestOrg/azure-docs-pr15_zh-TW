<properties
    pageTitle="Azure AD Windows Phone 快速入門 |Microsoft Azure"
    description="如何建立的 Windows Phone 應用程式整合 Azure AD 登入並 Azure AD 的通話受到保護的 Api 使用 OAuth。"
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>



# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Azure AD 整合 Windows Phone 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

如果您開發的 Windows Phone 8.1 版應用程式，Azure AD 很簡單明瞭，以驗證您的使用者使用 Active Directory 帳戶。  同時也會啟用安全地使用任何受 Azure AD，例如 Office 365 Api 或 Azure API 的 API 的 web 應用程式。

> [AZURE.NOTE] 此程式碼範例使用 ADAL v2.0。  最新的技術，您可能會想要改為嘗試 [ [Windows 通用教學課程中使用 ADAL v3.0](active-directory-devquickstarts-windowsstore.md)我們。  如果您的確 Windows Phone 8.1 版的建置應用程式，這是正確的地方。  ADAL v2.0 仍完全受支援，且並開發應用程式 agianst Windows Phone 8.1 版的建議的方式使用 Azure AD。

Azure AD.NET 原生的用戶端需要存取受保護的資源，提供的 Active Directory 驗證庫或 ADAL。  ADAL 的生活中唯一的目的是讓您的應用程式，以取得存取權杖能輕鬆地。  若要示範如何只要輕鬆，以下我們會建立 「 目錄搜尋 「 Windows Phone 8.1 版應用程式的︰

-   取得存取權杖撥 Azure AD 圖形 API 使用[OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
-   使用指定的 UPN 在目錄中搜尋。
-   取出符號使用者。

若要建立完成的工作應用程式，您必須︰

2. Azure AD 註冊您的應用程式。
3. 安裝並設定 ADAL。
5. 若要從 Azure AD 取得權杖使用 ADAL。

若要開始，[下載基本架構的專案](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip)。  每一個是 Visual Studio 2013 解決方案。  您也必須 Azure AD 租用戶，您可以在其中建立使用者，並登錄應用程式。  如果您還沒有租用戶，[瞭解如何取得](active-directory-howto-tenant.md)。

## <a name="1-register-the-directory-searcher-application"></a>*1.註冊目錄搜尋應用程式*
若要啟用您的應用程式，以取得權杖，您必須以註冊 Azure AD 租用戶，並授與 Azure AD 圖形 API 的存取權限︰

-   登入[Azure 管理入口網站](https://manage.windowsazure.com)
-   在左側導覽中，按一下 [在**Active Directory**
-   選取要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   遵循提示，並建立新的**原生的用戶端應用程式**。
    -   應用程式的**名稱**會說明您的使用者的應用程式
    -   **重新導向 Uri**是配置和字串的組合，Azure AD 將用來傳回 token 回應。  現在，輸入的版面配置區的值，例如`http://DirectorySearcher`。  我們會稍後將此值。
-   當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  此值必須在下一個區段中，因此複製的 [**設定**] 索引標籤。
- 也在 [**設定**] 索引標籤，找到 」 權限以其他應用程式 」 區段。  「 Azure Active Directory 」 應用程式中，新增 [**委派的權限****存取您組織的目錄**權限。  如此一來查詢圖形 API 使用者的應用程式。

## <a name="2-install--configure-adal"></a>*2.安裝與設定 ADAL*
有 Azure AD 中的應用程式之後，您可以安裝的 ADAL 並撰寫您的身分識別相關的程式碼。  為了讓 ADAL 能夠與 Azure AD 通訊，您需要提供的一些相關應用程式註冊資訊。
-   開始使用封裝管理員主控台 DirectorySearcher 專案新增 ADAL。

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   在 DirectorySearcher 專案中，開啟`MainPage.xaml.cs`。  取代值`Config Values`區域，以反映您輸入到 [Azure 入口網站中的值。  您的程式碼會參考這些值時，它會使用 ADAL。
    -   `tenant`是 Azure AD 租用戶，例如 contoso.onmicrosoft.com 網域
    -   `clientId`會將您複製的入口網站應用程式的 clientId。
-   您現在要找出您的 Windows Phone 應用程式的回撥 uri。  在此一行設定中斷點`MainPage`方法︰

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- 執行應用程式，並保留複製的值`redirectUri`叫用中斷點。  看起來應該像

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- 回到 Azure 管理入口網站中的應用程式的 [**設定**] 索引標籤上用這個值來取代**RedirectUri**的值。  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3.使用 ADAL 取得 AAD 權杖*
前 ADAL 的基本原則是，每當您的應用程式需要存取權杖，它只會呼叫`authContext.AcquireToken(…)`，ADAL 執行其餘部分。  

-   第一個步驟是初始化您的應用程式`AuthenticationContext`-ADAL 的主要類別。  這是您將傳遞 ADAL 需要與 Azure AD 通訊，即該如何快取權杖座標。

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- 現在找出`Search(...)`方法，當使用者 cliks 「 搜尋 」] 按鈕的應用程式使用者介面中將叫用。  這個方法進行 Azure AD 圖形 API 來查詢 GET 要求使用者的 UPN 開頭為指定的搜尋字詞。  您要包含在 access_token 才能查詢圖形 API，但`Authorization`標頭的邀請-這是 ADAL 提供。

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- 如果互動式驗證有必要，ADAL 會使用 Windows Phone Web 驗證代理人 (WAB)，然後以顯示 Azure AD[接續模型](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/)登入頁面。  當使用者登入時，您的應用程式需要傳遞 ADAL WAB 互動的結果。  這是非常簡單，實作`ContinueWebAuthentication`介面︰

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- 現在是時候使用`AuthenticationResult`的 ADAL 的形式傳回至您的應用程式。  在 [`QueryGraph(...)`回撥，附加您 access_token 授權標頭 GET 要求︰

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- 您也可以使用`AuthenticationResult`物件，以顯示應用程式中的使用者的相關資訊。 在 [`QueryGraph(...)`方法，使用結果頁面上顯示的使用者識別碼︰

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- 最後，您可以使用 ADAL 登出的應用程式的使用者。  使用者按一下 [登出] 按鈕時，我們要確保的下一個呼叫`AcquireTokenSilentAsync(...)`會失敗。  使用 ADAL，這是只要簡單以清除權杖快取項目︰

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

恭喜您 ！ 您現在已有可用的 Windows Phone 應用程式已驗證的使用者、 安全地呼叫 [Web Api 使用 OAuth 2.0 的功能，並取得基本的使用者資訊。  如果您尚未選取，如此，填入您的租用戶，且有些使用者的時間。  執行 DirectorySearcher 應用程式，並使用其中一個這些使用者登入。  根據其 UPN 其他使用者的搜尋。  關閉應用程式，然後重新執行。  請注意如何在使用者工作階段保持不變。  登出，並再次登入以另一位使用者。

ADAL 可讓您更輕鬆地將所有這些常見的身分識別功能合併到您的應用程式。  它會為您的快取管理、 OAuth 通訊協定支援，透過登入 UI，重新整理過期的權杖，以及其他簡報使用者處理 dirty 的所有工作。  您只需瞭解是單一 API 通話， `authContext.AcquireToken*(…)`。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip)。  您可以立即移至其他的身分識別案例。  若要嘗試︰

[安全的.NET Web 與 Azure AD API >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]