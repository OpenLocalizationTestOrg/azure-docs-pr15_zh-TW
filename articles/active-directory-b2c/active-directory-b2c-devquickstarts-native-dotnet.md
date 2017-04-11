<properties
    pageTitle="Azure Active Directory B2C |Microsoft Azure"
    description="如何建立 Windows 桌面應用程式，其中包含登入，註冊，以及使用 Azure Active Directory B2C 管理設定檔。"
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

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C︰ 建立 Windows 桌面應用程式

藉由使用 Azure Active Directory (Azure AD) B2C，您可以新增您的桌面應用程式的幾個步驟的強大自助身分識別管理功能。 本文將說明如何建立.NET Windows Presentation Foundation (WPF) 「 待辦事項清單 」 應用程式，包括使用者註冊、 登入，以及管理設定檔。 應用程式會使用使用者名稱或電子郵件中包含支援註冊和登入。 同時使用社交帳戶，例如 Facebook 和 Google 還包括支援註冊和登入。

## <a name="get-an-azure-ad-b2c-directory"></a>取得 Azure AD B2C 目錄

您可以使用 Azure AD B2C 之前，您必須建立目錄，或租用戶。  目錄是針對您所有的使用者、 應用程式、 群組及更多的容器。 如果您沒有，[建立 B2C 目錄](active-directory-b2c-get-started.md)之前繼續執行本指南中。

## <a name="create-an-application"></a>建立應用程式

接下來，您需要建立 B2C 目錄中的應用程式。 這會提供 Azure AD 資訊，以便與您的應用程式安全地溝通。 若要建立應用程式，請遵循[下列指示進行](active-directory-b2c-app-registration.md)。  請務必︰

- 包含**原生的用戶端**應用程式中。
- 複製**重新導向 URI** `urn:ietf:wg:oauth:2.0:oob`。 則此程式碼範例的預設 URL。
- 複製**應用程式識別碼**指派給您的應用程式。 您會用得到。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>建立您的原則

Azure AD B2C，在每個使用者體驗定義[原則](active-directory-b2c-reference-policies.md)。 此程式碼範例包含三個身分識別體驗︰ 註冊、 登入，及 [編輯設定檔。 您需要針對每個類型，建立原則[原則參考本文](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)所述。 當您建立的三個原則時，請務必︰

- 身分識別提供者刀中，選擇 [**註冊的使用者識別碼**] 或 [**註冊電子郵件**。
- 選擇 [註冊原則中的 [**顯示名稱**和其他註冊屬性]。
- 您可以選擇 [**顯示名稱**] 和 [**物件識別碼**宣告為每個原則的應用程式宣告。 您可以選擇以及其他宣告。
- 建立後，請複製每個原則**名稱**。 應有首碼`b2c_1_`。  您需要下列原則名稱後面。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

您已成功建立的三個原則之後，您可以準備開始建立您的應用程式。

## <a name="download-the-code"></a>下載的程式碼

此教學課程[GitHub 上維護](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet)的程式碼。 若要建置範例，您即可，您可以[下載基本架構專案另存為.zip 檔案](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)。 您也可以複製基本架構︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

已完成的應用程式也是[使用 zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)上或`complete`分支的相同存放庫。

下載的範例程式碼之後，開啟 Visual Studio.sln 檔案，即可開始使用。 `TaskClient`的專案是 WPF 桌面應用程式的使用者進行互動。 進行本教學課程，其通話的後端工作 web API，裝載於 Azure 儲存每個使用者的待辦事項清單。  您不需要建立網路 API，我們已為您執行。

若要瞭解如何網路 API 安全地驗證要求使用 Azure AD B2C，查看[網路 API 快速入門文章](active-directory-b2c-devquickstarts-api-dotnet.md)。

## <a name="execute-policies"></a>執行原則
您的應用程式會與 Azure AD B2C 通訊傳送給指定要執行 HTTP 要求的一部分的原則的驗證訊息。 對於.NET 桌面應用程式，您可以傳送 OAuth 2.0 驗證訊息，請執行原則，使用預覽 Microsoft 驗證文件庫 (MSAL)，並取得呼叫網頁 Api 的權杖。

### <a name="install-msal"></a>安裝 MSAL
新增至 MSAL`TaskClient`使用 Visual Studio 封裝管理員主控台的專案。

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>輸入您 B2C 詳細資料
開啟檔案`Globals.cs`和取代每個屬性值。 這個類別用整個`TaskClient`常用的參考的值。

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>建立 PublicClientApplication
MSAL 的主要類別是`PublicClientApplication`。 這個類別 Azure AD B2C 系統中表示您的應用程式。 當應用程式 initalizes 中，建立的執行個體`PublicClientApplication`中`MainWindow.xaml.cs`。 這可用於整個視窗。

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>啟動註冊的流程
使用者選擇從符號設定，當您想要開始使用您所建立的註冊原則註冊流程。 藉由使用 MSAL，您只要呼叫`pca.AcquireTokenAsync(...)`。 參數傳遞給`AcquireTokenAsync(...)`決定哪些權杖您收到，用於驗證要求，以及更多的原則。

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>啟動登入的流程
您可以在您初始的註冊流程的相同方式啟動的登入流程。 當使用者登入時，請相同通話 MSAL，這次請使用您的登入原則︰

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>啟動 [編輯設定檔流程
同樣地，您可以執行的編輯設定檔原則相同的方式︰

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

在所有這種情況下，MSAL [傳回中的權杖`AuthenticationResult`或擲回例外狀況。 您可以使用您從 MSAL，取得權杖每次`AuthenticationResult.User`來更新使用者資料，請在應用程式，例如使用者介面中的物件。 ADAL 也會快取以用於其他部分應用程式的 token。


### <a name="check-for-tokens-on-app-start"></a>在 [應用程式開始檢查權杖
您也可以使用 MSAL 來追蹤使用者的登入狀態。  在此應用程式，我們希望使用者將其關閉應用程式，並重新開啟時，即使保持登入。  返回內`OnInitialized`覆寫，請使用 MSAL 的`AcquireTokenSilent`方法來檢查快取權杖︰

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>打電話給工作 API
您現在使用 MSAL 執行原則和取得權杖。  當您想要使用其中一個這些權杖撥打任務 API 時，您可以一次使用 MSAL 的`AcquireTokenSilent`方法來檢查快取權杖︰

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

當通話`AcquireTokenSilentAsync(...)`成功快取中找到的權杖，您可以新增至權杖`Authorization`HTTP 要求的標題。 任務網路 API 會使用此標題來驗證要求讀取使用者的待辦事項清單︰

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>登出使用者
最後，您可以使用 MSAL 使用者選取**登出**時結束與應用程式的使用者的工作階段。  使用時 MSAL，這是清除所有的權杖快取權杖︰

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>執行範例應用程式

最後，建立並執行範例。  登入應用程式的使用電子郵件地址或使用者名稱。 登出後再重新登入以相同的使用者。 編輯該使用者的設定檔。 登出，使用不同的使用者登入。

## <a name="add-social-idps"></a>新增社交 IDPs

目前應用程式支援只有註冊的使用者和登入的使用**本機帳戶**。 這些是儲存在 B2C 目錄使用的使用者名稱和密碼的帳戶。 藉由使用 Azure AD B2C，您可以新增支援其他身分識別提供者 (IDPs)，而不變更任何程式碼。

若要新增您的應用程式社交 IDPs，開始追蹤這些文件中的詳細的指示。 每個您想要支援的 IDP，您需要的系統中登錄應用程式，並取得用戶端識別碼

- [設為 IDP Facebook](active-directory-b2c-setup-fb-app.md)
- [Google 設為 IDP](active-directory-b2c-setup-goog-app.md)
- [Amazon 設為 IDP](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn 設為 IDP](active-directory-b2c-setup-li-app.md)

您將身分識別提供者新增至您的 B2C 目錄之後，必須先編輯每個您要包含新 IDPs 的三個原則，[原則參考本文](active-directory-b2c-reference-policies.md)所述。 儲存您的原則後，請再次執行應用程式。 您應該會看到新的 IDPs 新增為 [登入，並且在每一個您的身分識別的註冊選項。

您可以嘗試使用您的原則，並可以一同觀看範例應用程式中的效果。 新增或移除 IDPs、 管理應用程式宣告或變更註冊的屬性。 嘗試鍵，直到您可以看到原則、 驗證需求和 MSAL 的結合。

參照的[zip 提供](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)的已完成的範例。 您也可以複製從 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
