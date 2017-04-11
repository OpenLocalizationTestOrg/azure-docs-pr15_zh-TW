<properties
    pageTitle="快速入門 azure AD v2.0 AngularJS |Microsoft Azure"
    description="如何建立角度 JS 單一頁面應用程式的登入兩個個人的 Microsoft 帳戶的使用者和公司或學校帳戶。"
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>登入加入 AngularJS 單一頁面應用程式-NodeJS

本文中，我們會使用可的 Microsoft 帳戶登入加入使用 Azure Active Directory v2.0 端點 AngularJS 應用程式中。 v2.0 端點可讓您執行的單一整合應用程式中的使用者，驗證使用個人與工作/學校帳戶。

此範例是簡單的待辦事項清單單一頁面應用程式，將儲存在後端 REST API，以撰寫 NodeJS，並使用 [從 Azure AD OAuth 承載者權杖保護的工作。  AngularJS 應用程式會使用我們開啟來源 JavaScript 驗證文件庫[adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js)處理整個的登入程序，並取得撥 REST API 的權杖。  相同模式，可以套用至其他 REST api，例如[Microsoft Graph](https://graph.microsoft.com)或 Azure 資源管理員 Api 進行驗證。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="download"></a>下載

若要開始，您必須下載並安裝[node.js](https://nodejs.org)。  然後您可以複製或[下載](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip)的基本架構的應用程式︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

基本架構的應用程式包含您所有的指令碼簡單 AngularJS 應用程式，但遺失所有的身分識別相關的項目。  如果您不想要遵循，您改用可以複製或[下載](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip)已完成的範例。

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>登錄應用程式

首先，請在[應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中，建立應用程式，或請遵循下列[詳細的步驟](active-directory-v2-app-registration.md)。  請確定︰

- 新增您的應用程式的**Web**平台。
- 輸入正確的**重新導向 URI**。 此範例的預設值是`http://localhost:8080`。
- [離開啟用**允許隱含流程**的核取方塊。 

複製下**應用程式識別碼**指派給您的應用程式，您必須將其引進了。 

## <a name="install-adaljs"></a>安裝 adal.js
若要開始，請瀏覽至專案您下載並安裝 adal.js。  如果您有安裝[bower](http://bower.io/)時，您就可以執行此命令。  針對任何相依性版本不相符，只選擇較高的版本。
```
bower install adal-angular#experimental
```

或者，您可以手動下載[adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js)和[adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js)。  新增兩個檔案`app/lib/adal-angular-experimental/dist`目錄。

現在您最愛的文字編輯器中開啟專案，並載入頁面本文結尾的 adal.js:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>設定 REST API

我們正在設定項目，可讓開始後端 REST API 工作。  在命令提示字元中，請執行安裝所有必要的套件 （請確定您在專案的最上層的目錄）︰

```
npm install
```

現在，請開啟`config.js`及取代`audience`值︰

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
     
     ...
}
```

REST API 會使用這個值來驗證其會接收來自上 AJAX 要求的角度應用程式的權杖。  這個簡單的 REST API 儲存記憶體中資料集，因此每個附註若要停止伺服器時間，將會遺失所有先前建立的工作。

這是我們要花在討論 REST API 的運作方式的任何時間。  請隨意一下程式碼，但如果您想要深入瞭解保護 web Api 使用 Azure AD，請參閱[本文](active-directory-v2-devquickstarts-node-api.md)。 

## <a name="sign-users-in"></a>符號中的使用者
若要撰寫的部分身分識別程式碼的時間。  您可能已經注意到該 adal.js 包含精美效果角度路由機制 AngularJS 提供者。  請先將 adal 的模組新增至應用程式︰

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

您現在可以初始化`adalProvider`與您的應用程式識別碼︰

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

好，現在 adal.js 有需保護您的應用程式及登入使用者的所有資訊。  若要強制登入應用程式中的特定路由，只需要是一行程式碼︰

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

現在當使用者按一下`TodoList`連結，adal.js 會自動重新導向至 Azure AD 登入的必要。  明確也可以叫用在您的控制站 adal.js，傳送登入和教具借出的要求︰

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>顯示 [使用者資訊
現在，當使用者登入，您可能需要存取應用程式的登入使用者驗證資料。  Adal.js 公開您在這項資訊`userInfo`物件。  若要存取此檢視] 中的物件，請先新增至對應的控制站的根範圍的 adal.js:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

然後您可以直接地址`userInfo`檢視中的物件︰ 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

您也可以使用`userInfo`物件，以判斷是否，是否要使用者登入。

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>通話 REST API
最後，則若要取得一些權杖通話 REST API 來建立、 讀取、 更新和刪除工作的時間。  以及您知道？  您不需要做*的事*。  Adal.js 會自動負責取得、 快取，以及重新整理權杖。  它會也可將這些權杖附加至外寄 AJAX 要求您傳送至 REST API。  

完全如何運作？ 這是所有獎狀神奇[AngularJS 攔截](https://docs.angularjs.org/api/ng/service/$http)，可讓 adal.js 轉換傳出及傳入 http 郵件之處。  此外，adal.js 假設當視窗應該使用適用於相同的應用程式識別碼，作為 AngularJS 應用程式的權杖，任何要求傳送到相同的網域。  這是為什麼 NodeJS REST API 和兩個角度應用程式中，我們會使用相同的應用程式識別碼。  當然，您可以覆寫此行為，並告知 adal.js 權杖取得其他 REST Api，如有必要-但預設值會執行這個簡單案例的功能。

以下是如何輕鬆從 Azure AD 傳送邀請與承載者權杖會顯示的程式碼片段︰

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

恭喜您 ！  Azure AD 整合式的單一頁面應用程式現已完成。  請繼續進行，蝴蝶結。  它可以驗證使用者、 安全地呼叫其後端 REST API 使用 OpenID 連線，並取得基本的使用者資訊。  不在] 方塊中，則支援的任何使用者使用個人 Microsoft 帳戶或從 Azure AD 工作/學校帳戶。  嘗試應用程式執行︰

```
node server.js
```

在瀏覽器中瀏覽至`http://localhost:8080`。  使用個人 Microsoft 帳戶或工作/學校帳戶登入。  將任務新增至使用者的待辦事項清單，然後登出。  請嘗試使用其他類型的帳戶登入。 如果您需要建立工作/學校使用者 Azure AD 租用戶[了解如何取得以下](active-directory-howto-tenant.md)（它是免費的）。

若要繼續學習如何 v2.0 端點，回到我們[v2.0 開發人員指南](active-directory-appmodel-v2-overview.md)。  其他資源，請參閱︰

- [Azure-範例上 GitHub >>](https://github.com/Azure-Samples)
- [Azure AD 堆疊溢位在 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Azure AD 文件上的[Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
