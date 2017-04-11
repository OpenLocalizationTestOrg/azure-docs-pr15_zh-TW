<properties
pageTitle="Azure Active Directory v2.0.NET 原生應用程式 |Microsoft Azure"
description="如何建立使用兩個個人的 Microsoft 帳戶使用者與公司或學校帳戶登入的.NET 原生應用程式。"
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>新增登入 Windows 桌面應用程式

使用 v2.0 端點，您可以快速新增至桌面應用程式的兩個個人的 Microsoft 帳戶支援的驗證及公司或學校帳戶。  也可讓您的應用程式 api，以及[Microsoft Graph](https://graph.microsoft.io)安全地通訊後端網頁，多個[Office 365 整合 Api](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)。

> [AZURE.NOTE] 並非所有的 Azure Active Directory (AD) 案例與功能支援 v2.0 結束點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

Azure AD [.NET 原生應用程式的執行裝置](active-directory-v2-flows.md#mobile-and-native-apps)，提供 Microsoft 身分識別驗證文件庫或 MSAL。  MSAL 的生活中唯一的目的是輕鬆取得權杖呼叫 web 服務應用程式。  若要示範如何只要輕鬆，以下我們會建立.NET WPF 待辦事項清單應用程式的︰

- 登入的使用者，並取得存取權杖使用[OAuth 2.0 驗證通訊協定](active-directory-v2-protocols.md#oauth2-authorization-code-flow)]。
- 安全地通話後端待辦事項清單 web 服務，也會受到 OAuth 2.0。
- 登出使用者。

## <a name="download-sample-code"></a>下載範例程式碼

本教學課程中的程式碼是來維護[GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet)。  若要遵循，您可以[下載應用程式的基本架構為.zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip)或複製基本架構︰

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

已完成的應用程式提供在本教學課程結尾處。

## <a name="register-an-app"></a>登錄應用程式
在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，建立新的應用程式，或依照這些[詳細的步驟](active-directory-v2-app-registration.md)。  請確定︰

- 複製下指派給您的應用程式的**應用程式識別碼**，您必須將其推出。
- 新增您的應用程式的**行動**平台。

## <a name="install--configure-msal"></a>安裝及設定 MSAL
有登錄的 Microsoft 應用程式之後，您可以安裝 MSAL 和撰寫您的身分識別相關的程式碼。  為了讓 MSAL 能夠進行通訊 v2.0 端點，您需要提供的一些相關應用程式註冊資訊。

-   開始使用封裝管理員主控台 TodoListClient 專案新增 MSAL。

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   在 TodoListClient 專案中，開啟`app.config`。  取代值] 中的項目`<appSettings>`一節，以反映您輸入到應用程式註冊入口網站中的值。  您的程式碼會參考這些值時，它會使用 MSAL。
    -   `ida:ClientId`您複製的入口網站應用程式的**應用程式識別碼**。

- 在 TodoList 服務專案中，開啟`web.config`根目錄中的專案。  
    - 取代`ida:Audience`從入口網站的同一個**應用程式識別碼**的值。

## <a name="use-msal-to-get-tokens"></a>使用 MSAL 取得權杖
前 MSAL 的基本原則是您的應用程式需要存取權杖，每當您只要呼叫`app.AcquireToken(...)`，MSAL 執行其餘部分。  

-   在 [`TodoListClient`開啟專案`MainWindow.xaml.cs`並找出`OnInitialized(...)`方法。  第一個步驟是初始化您的應用程式`PublicClientApplication`-MSAL 的主要的類別，表示原生應用程式。  這是位置傳遞 MSAL 需要與 Azure AD 通訊，即該如何快取權杖座標。

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- 應用程式啟動時，我們要核取 [請參閱是否使用者已登入這個應用程式。  不過，我們不想還呼叫登入 UI-我們要使用者按一下 [登入 」 執行此作業。  也會在`OnInitialized(...)`方法︰

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- 如果使用者未登入，他們可以按一下 [登入 」] 按鈕，我們要叫用的登入使用者介面，讓使用者輸入其認證。  實作登入按鈕處理︰

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

- 若使用者成功符號增益集，MSAL 將接收和快取權杖，您可以繼續通話`GetTodoList()`放心的方法。  取得使用者的任務剩下的是實作`GetTodoList()`方法。

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>執行

恭喜您 ！ 現在，您可以使用.NET WPF 應用程式已驗證的使用者與安全地呼叫 [Web Api 使用 OAuth 2.0 的能力。  執行這兩個專案，並使用個人 Microsoft 帳戶或公司或學校帳戶登入。  將任務新增至該使用者的待辦事項清單。  登出，並再次登入以另一位使用者來檢視其待辦事項清單。  關閉應用程式，然後重新執行。  請注意如何保持不變，使用者的工作階段-這是因為應用程式快取中的本機檔案的權杖。

MSAL 可輕鬆將一般的身分識別功能合併到您的應用程式，使用個人和公司帳戶。  它會為您的快取管理、 OAuth 通訊協定支援，透過登入 UI，重新整理過期的權杖，以及其他簡報使用者處理 dirty 的所有工作。  您只需瞭解是單一 API 通話， `app.AcquireTokenAsync(...)`。

參照的已完成的範例 （不含您設定的值） [，提供為的.zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)，或者您可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>後續步驟

您現在可以移動到更進階的主題。  若要嘗試︰

- [保護 TodoListService Web API 與 v2.0 端點](active-directory-v2-devquickstarts-dotnet-api.md)

其他資源，請參閱︰  

- [V2.0 開發人員指南 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow 「 msal 」 標記 >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
