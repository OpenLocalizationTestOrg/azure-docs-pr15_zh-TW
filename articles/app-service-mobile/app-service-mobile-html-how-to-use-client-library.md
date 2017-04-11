<properties
    pageTitle="如何使用 JavaScript SDK Azure 行動應用程式"
    description="如何使用 v Azure Mobile 應用程式"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>如何使用 JavaScript 用戶端文件庫 Azure 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南會指導您執行常見的案例使用最新的[JavaScript SDK Azure Mobile 應用程式]。 如果您是新 Azure Mobile 應用程式，先完成建立後端並建立表格的[Azure 行動應用程式快速入門]。 本指南中，我們焦點放在 HTML/JavaScript Web 應用程式中使用行動裝置的後端。

## <a name="supported-platforms"></a>支援的平台

我們會限制至目前和最後一個版本的主要的瀏覽器支援的瀏覽器︰ Google Chrome、 Microsoft Edge、 Microsoft Internet Explorer 及 Mozilla Firefox。  我們預期 SDK 任何較新的瀏覽器使用的函數。

通用 JavaScript 模組，請為分佈套件的支援全域，AMD，因此 CommonJS 格式。

##<a name="Setup"></a>設定與先決條件

本指南假設您已建立後端的資料表。 本指南假設表格這些教學課程中具有相同的結構描述的資料表。

安裝 Azure 行動應用程式 JavaScript SDK 可以完成透過`npm`命令︰

```
npm install azure-mobile-apps-client --save
```

安裝後，位於文件庫`node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`。  將此檔案複製至您的網頁區域。

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

文件庫也作為 ES2015 模組，例如 Browserify 和 Webpack 以及 AMD 文件庫的 CommonJS 環境中使用。  例如︰

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>如何︰ 驗證的使用者

Azure 應用程式服務支援使用各種不同的外部的身分識別提供者的應用程式使用者驗證與授權︰ Facebook、 Google、 Microsoft 帳戶和 Twitter。 您可以設定權限限制為特定作業已驗證的使用者存取的表格。 您也可以使用已驗證的使用者身分的識別實作伺服器指令碼中的授權規則。 如需詳細資訊，請參閱[開始使用驗證]教學課程。

兩個驗證流程支援︰ 伺服器流程和用戶端流程。  伺服器流程提供最簡單的驗證體驗，必須提供者的 web 驗證介面。 用戶端流程例如允許更深入的整合與裝置的特定功能單一登入時必須提供者特有 Sdk。

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>如何︰ 設定您的行動應用程式服務外部重新導向 url。

許多種類型的 JavaScript 應用程式會使用迴路功能來處理 OAuth UI 流。  這些功能包括︰

* 在本機執行您的服務
* 使用即時重新載入 Ionic 架構
* 重新導向至應用程式服務進行驗證。 

在本機執行會造成問題，因為根據預設，應用程式服務驗證只設定為允許存取您的行動應用程式後端。 若要變更應用程式服務設定]，在本機執行伺服器時，啟用驗證使用下列步驟︰

1. 登入[Azure 入口網站]
2. 瀏覽至您的行動應用程式後端。
3. 選取 [在**開發工具**] 功能表中的 [**資源總管**]。
4. 按一下 [**移**至新的索引標籤或視窗中開啟您的行動應用程式後端的資源檔案總管]。
5. 展開 [ **config** > **authsettings**節點應用程式。
6. 按一下 [啟用編輯資源的 [**編輯**] 按鈕。
7. 尋找**allowedExternalRedirectUrls**項目應為 null。 在陣列中新增您的 Url:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    在陣列中的 Url 取代您的服務，即在此範例中的 Url`http://localhost:3000`本機 Node.js 範例服務。 您也可以使用`http://localhost:4400`漣漪服務或一些其他 URL，視您的應用程式的設定方式而定。

8. 在頁面頂端，按一下**讀/寫**的然後按一下 [**保留**儲存您所做的更新。

您也需要將相同的迴路 Url 新增到 CORS whitelist 設定︰

1. 瀏覽回[Azure 入口網站]。
2. 瀏覽至您的行動應用程式後端。
3. **API**功能表中，按一下**CORS** 。
4. 在空白的 [**允許來源**] 文字方塊中輸入每一個的 URL。  建立新的文字方塊。
5. 按一下 [**儲存**]
    
後端更新之後，您可以在您的應用程式中使用新的迴路 Url。

<!-- URLs. -->
[Azure 行動應用程式快速入門]: app-service-mobile-cordova-get-started.md
[快速入門驗證]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure 入口網站]: https://portal.azure.com/
[JavaScript SDK Azure 行動應用程式]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

