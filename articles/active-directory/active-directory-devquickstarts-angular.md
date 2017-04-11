<properties
    pageTitle="快速入門 azure AD AngularJS |Microsoft Azure"
    description="如何建立的角度 JS 單一頁面應用程式整合 Azure AD 登入並 Azure AD 的通話受到保護的 Api 使用 OAuth。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>保護 AngularJS 單一頁面應用程式搭配使用 Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 很簡單且直接新增登入]，[登出]，及安全 OAuth API 的來電至您的單一頁面應用程式。  讓您的應用程式，來驗證其 Active Directory 帳戶的使用者，以及使用任何網頁 API 受 Azure AD，例如 Office 365 Api 或 Azure API。

在瀏覽器中執行 javascript 應用程式，Azure AD 提供的 Active Directory 驗證庫或 adal.js。  Adal.js 的生活中唯一的目的是讓您的應用程式，以取得存取權杖能輕鬆地。  若要示範如何只要輕鬆，以下我們會建立一個 AngularJS 待辦事項清單] 應用程式的︰

- 簽署使用者將使用 Azure AD 為身分識別提供者的應用程式。
- 顯示使用者的部分資訊。
- 安全地通話的應用程式來執行清單 API 使用從 AAD 承載者權杖。
- 簽署登出應用程式的使用者。

若要建立完成的工作應用程式，您必須︰

2. Azure AD 註冊您的應用程式。
3. 安裝的 ADAL 及設定選項。
5. 您可以使用 ADAL 來保護] 選項中的頁面。

若要開始，[下載應用程式的基本架構](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip)或[下載已完成的範例](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)。  您也必須 Azure AD 租用戶，您可以在其中建立使用者，並登錄應用程式。  如果您還沒有租用戶，[瞭解如何取得](active-directory-howto-tenant.md)。

## <a name="1-register-the-directorysearcher-application"></a>*1.註冊 DirectorySearcher 應用程式*
若要啟用您的應用程式驗證的使用者，並取得權杖，您必須註冊 Azure AD 租用戶︰

-   登入[Azure 管理入口網站](https://manage.windowsazure.com)
-   在左側導覽中，按一下 [在**Active Directory**
-   選取要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   依照提示操作，並建立新**Web 應用程式及/或 WebAPI**。
    -   應用程式的**名稱**會說明您的應用程式的使用者。
    -   **重新導向 Uri**是要 AAD 會傳回權杖的位置。  此範例中的預設位置`https://localhost:44326/`
-   當您完成註冊時，AAD 將會是唯一的**用戶端識別碼**指派您的應用程式。  此值必須在下一個區段中，因此複製的 [**設定**] 索引標籤。
- Adal.js 使用 OAuth 隱含流程 Azure AD 與通訊。  您必須啟用隱含流程的應用程式︰
    - 按一下 [**管理資訊清單**下載應用程式顯示。
    - 開啟資訊清單，然後找出`oauth2AllowImplicitFlow`屬性。 若要設定其值`true`。
    - 儲存並將應用程式顯示上傳一次，再按一下 [**管理資訊清單**。

## <a name="2-install-adal--configure-the-spa"></a>*2.安裝的 ADAL 及設定選項*
有 Azure AD 中的應用程式之後，您可以安裝 adal.js 和撰寫您的身分識別相關的程式碼。

-   開始使用封裝管理員主控台 TodoSPA 專案新增 adal.js:
  - 下載[adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) ，並將它`App/Scripts/`專案目錄。
  - 下載[adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) ，並將它`App/Scripts/`專案目錄。
  - 載入結束之前的每個指令碼`</body>`中`index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   後端] 選項的後端來執行清單 API 接受從瀏覽器的權杖，需要應用程式註冊的設定資訊。 在 TodoSPA 專案中，開啟`web.config`。  取代值] 中的項目`<appSettings>`一節，以反映您輸入到 [Azure 入口網站中的值。  您的程式碼會參考這些值時，它會使用 ADAL。
    -   `ida:Tenant`是 Azure AD 租用戶，例如 contoso.onmicrosoft.com 網域
    -   `ida:Audience`必須是您所複製的入口網站應用程式的**用戶端識別碼**。

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3.ADAL 使用中] 選項頁面的安全性*
Adal.js 已建置整合與 AngularJS 傳送及 http 提供者，可讓您以保護您的選項中的個別檢視。

- 在 [ `App/Scripts/app.js`，使 adal.js 模組中︰

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- 您現在可以初始化`adalProvider`與您的應用程式註冊的設定值也會在`App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- 現在，保護`TodoList`一行程式碼，則需要-僅在應用程式，檢視`requireADLogin`。

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

現在您已經有使用者登入，並以其後端 API 發出承載者權杖受保護的要求的安全的單一頁面應用的程式。  當使用者按一下`TodoList`連結，adal.js 會自動重新導向至登入 Azure AD 必要。  此外，adal.js 會自動傳送給應用程式的後端的任何 ajax 要求附加 access_token。  上述是建立與 adal.js-] 選項不包裝的最小必要，但有幾個其他 SPAs 位於實用的功能︰

- 明確問題登入和登出要求您可以定義叫用 adal.js 您控制站的函數。  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- 您也可以在應用程式使用者介面中顯示的使用者資訊。  Adal 服務已新增至`userDataCtrl`控制站，您就可以存取`userInfo`在相關聯的檢視中，物件`App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- 此外，還有許多情況下，您會想知道使用者或不登入。  您也可以使用`userInfo`物件收集這項資訊。  例如，在`index.html`您可以將顯示 「 登入 」 或 「 登出 」] 按鈕，以驗證狀態︰

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

恭喜您 ！ Azure AD 整合單一頁面應用程式現已完成。  它可以驗證使用者、 安全地呼叫使用 OAuth 2.0 其後端並取得基本的使用者資訊。  如果您未，現在是填入您的租用戶，且有些使用者的時間。  執行您要執行清單] 選項，然後使用其中一個這些使用者登入。  將任務新增至方式] 清單中，登出，然後再次登入的使用者。

Adal.js 可讓您更輕鬆地將所有這些常見的身分識別功能加入您的應用程式。  它為您的快取管理、 OAuth 通訊協定支援，透過登入 UI，重新整理過期的權杖，以及其他簡報使用者處理的 dirty 的所有工作。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)。  您可以立即移至其他的案例。  若要嘗試︰

[從選項呼叫 CORS 網路 API >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
