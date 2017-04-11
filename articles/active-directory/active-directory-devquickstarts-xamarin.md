<properties
    pageTitle="快速入門 azure AD Xamarin |Microsoft Azure"
    description="如何建置 Xamarin 應用程式的整合 Azure AD 登入並通話 Azure AD 受到保護的 Api 使用 OAuth。"
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Azure AD 整合 Xamarin 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin 可讓您撰寫的 C# 程式在 iOS、 Android 和 Windows （行動裝置及電腦） 上執行的行動應用程式。 如果您正在建立的應用程式使用 Xamarin，Azure AD 可簡單且直接，以驗證您的使用者使用 Active Directory 帳戶。 同時也會啟用安全地使用任何受 Azure AD，例如 Office 365 Api 或 Azure API 的 API 的 web 應用程式。

需要存取受保護的資源的 Xamarin 應用程式，Azure AD 提供的 Active Directory 驗證庫或 ADAL。 ADAL 的生活中唯一的目的是讓您的應用程式，以取得存取權杖能輕鬆地。 若要示範如何只要輕鬆，以下我們會建立 「 目錄搜尋 」 應用程式的︰

-   IOS、 Android、 Windows 桌面、 Windows Phone 與 Windows 市集上執行。
- 若要驗證的使用者，並取得 Azure AD 圖形 API 的權杖使用單一可攜式類別文件庫 (PCL)
-   使用指定的 UPN 在目錄中搜尋。

若要建立完成的工作應用程式，您必須︰

2. 設定您 Xamarin 的開發環境
2. Azure AD 註冊您的應用程式。
3. 安裝並設定 ADAL。
5. 若要從 Azure AD 取得權杖使用 ADAL。

若要開始，[下載基本架構的專案](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)。 每一個是 Visual Studio 2013 解決方案。 您也必須 Azure AD 租用戶，您可以在其中建立使用者，並登錄應用程式。 如果您還沒有租用戶，[瞭解如何取得](active-directory-howto-tenant.md)。

## <a name="0-set-up-your-xamarin-development-environment"></a>*0。 設定 Xamarin 開發環境*
本教學課程中包括 iOS、 Android 和 Windows 版的專案，因此您必須 Visual Studio 與 Xamarin 放在一起。 若要建立所需的環境，依照完成上的指示[安裝和安裝的 Visual Studio 與 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) MSDN 上。 這些指示包含若要進一步瞭解 Xamarin 以完成安裝程式在您等待時，您可以檢閱的資料。 

一次您已完成所需的設定，方案 Visual Studio 中開啟，即可開始使用。 您會發現六個專案︰ 五個特定專案和一個可攜式類別的文件庫會在所有的平台之間共用`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1.註冊目錄搜尋應用程式*
若要啟用您的應用程式，以取得權杖，您必須以註冊 Azure AD 租用戶，並授與 Azure AD 圖形 API 的存取權限︰

-   登入[Azure 管理入口網站](https://manage.windowsazure.com)
-   在左側導覽中，按一下 [在**Active Directory**
-   選取要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   遵循提示，並建立新的**原生的用戶端應用程式**。
    -   應用程式的**名稱**會說明您的使用者的應用程式
    -   **重新導向 Uri**是配置和字串的組合，Azure AD 將用來傳回 token 回應。 輸入一個值，例如`http://DirectorySearcher`。
-   當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。 此值必須在下一個區段中，因此複製的 [**設定**] 索引標籤。
- 也在 [**設定**] 索引標籤，找到 」 權限以其他應用程式 」 區段。 「 Azure Active Directory 」 應用程式中，新增 [**委派的權限****存取您組織的目錄**權限。 如此一來查詢圖形 API 使用者的應用程式。

## <a name="2-install--configure-adal"></a>*2.安裝與設定 ADAL*
有 Azure AD 中的應用程式之後，您可以安裝的 ADAL 並撰寫您的身分識別相關的程式碼。 為了讓 ADAL 能夠與 Azure AD 通訊，您需要提供的一些相關應用程式註冊資訊。
-   開始使用套件管理員主控台方案中專案的每個藉 ADAL。

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- 您應該注意到兩個文件庫參照會新增至每個專案的 ADAL PCL 部分與特定平台的一部分。

-   在 DirectorySearcherLib 專案中，開啟`DirectorySearcher.cs`。 變更類別成員值，以反映您輸入到 [Azure 入口網站中的值。 您的程式碼會參考這些值時，它會使用 ADAL。
    -   `tenant`是 Azure AD 租用戶，例如 contoso.onmicrosoft.com 網域
    -   `clientId`會將您複製的入口網站應用程式的 clientId。
    - `returnUri`是您輸入在入口網站，例如 redirectUri `http://DirectorySearcher`。

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3.使用 ADAL 取得 AAD 權杖*
所有的應用程式驗證邏輯在於*Almost* `DirectorySearcher.SearchByAlias(...)`。 所有必要的平台特定專案中是將內容的參數`DirectorySearcher`PCL。

- 首先，開啟`DirectorySearcher.cs`並新增至新的參數`SearchByAlias(...)`方法。 `IPlatformParameters`是封裝 ADAL 必須執行驗證的平台特定物件的關聯式參數。

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   接下來，初始化`AuthenticationContext`-ADAL 的主要類別。 這是您將傳遞 ADAL 需要進行通訊的 Azure AD 座標。 然後呼叫`AcquireTokenAsync(...)`，可接受`IPlatformParameters`物件，並將叫用來傳回應用程式的權杖驗證流程。

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`將第一次嘗試而不會提示使用者輸入其認證 （透過快取，或重新整理舊的權杖） 傳回的權杖要求的資源 (在本例中圖形 API)。 只有必要時，隨即會顯示使用者 Azure AD 登在頁面上之前取得的要求的 token。


- 然後，您可以將存取權杖附加至圖形 API 邀請授權標頭中︰

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

這是所有的`DirectorySearcher`PCL 和您的應用程式的身分識別相關的程式碼。  剩下的就撥打是`SearchByAlias(...)`方法在每個平台的檢視中，視需要新增的正確處理的使用者介面生命週期的程式碼的位置。

####<a name="android"></a>Android:
- 在 [ `MainActivity.cs`，加入通話`SearchByAlias(...)`按鈕中按鈕上，按一下 [處理常式︰

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- 您也需要覆寫`OnActivityResult`轉寄任何驗證的生命週期方法會重新導向至適當的方法。  ADAL 提供協助程式方法此 Android 中︰

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Windows 桌面︰
- 在 [ `MainWindow.xaml.cs`，只要撥打`SearchByAlias(...)`傳遞`WindowInteropHelper`桌面應用程式中`PlatformParameters`物件︰

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS:
- 在 [ `DirSearchClient_iOSViewController.cs`，iOS`PlatformParameters`物件只需檢視控制站的參照︰

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Windows 標準︰
- 在 Windows 通用開啟`MainPage.xaml.cs`及實作`Search`方法，使用共用專案中的協助程式方法更新所需的使用者介面。

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

恭喜您 ！ 現在，您可以使用 Xamarin 應用程式已驗證的使用者和安全地呼叫使用 OAuth 2.0 五個不同的平台上的網頁 Api 的能力。 如果您尚未選取，如此，填入您的租用戶，且有些使用者的時間。 執行 DirectorySearcher 應用程式，並使用其中一個這些使用者登入。 根據其 UPN 其他使用者的搜尋。

ADAL 可讓您更輕鬆地將一般的身分識別功能合併到您的應用程式。 它會為您的快取管理、 OAuth 通訊協定支援，透過登入 UI，重新整理過期的權杖，以及其他簡報使用者處理 dirty 的所有工作。 您只需瞭解是單一 API 通話， `authContext.AcquireToken*(…)`。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip)。 您可以立即移至其他的身分識別案例。 若要嘗試︰

[安全的.NET Web 與 Azure AD API >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
