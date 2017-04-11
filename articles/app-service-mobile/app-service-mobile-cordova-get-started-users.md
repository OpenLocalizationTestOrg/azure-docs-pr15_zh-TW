<properties
    pageTitle="新增行動應用程式的 Apache Cordova 驗證 |Azure 應用程式服務"
    description="瞭解如何使用 Azure 應用程式服務中的行動應用程式，來驗證透過各種不同的身分識別提供者，包括 Google、 Facebook、 Twitter 和 Microsoft Apache Cordova 應用程式的使用者。"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>新增驗證 Apache Cordova 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>摘要

在本教學課程中，您專案中新增驗證 todolist 快速入門 Apache Cordova 使用支援的身分識別提供者上。 本教學課程為基礎[開始 Mobile 應用程式使用]教學課程中，您必須先完成的。

##<a name="register"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[觀看影片，顯示類似的步驟](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

現在，您也可以驗證已停用匿名存取您的後端。 在 Visual Studio 中，開啟您完成教學課程[開始使用行動應用程式]時所建立的專案，然後**Google Android 模擬器**中執行應用程式並確認應用程式啟動後，會顯示未預期的連線失敗。

接下來，您將會更新驗證的使用者行動應用程式後端要求資源之前先將應用程式。

##<a name="add-authentication"></a>新增驗證應用程式

1. 在**Visual Studio**中，開啟您的專案，然後開啟`www/index.html`檔案以進行編輯。

2. 找出`Content-Security-Policy`中繼標籤的標頭區段。  您必須新增 OAuth 主應用程式的可容許的來源清單。

  	| 提供者               | SDK 提供者名稱 | OAuth host （主機)                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | aad               | https://login.windows.net   |
  	| Facebook               | facebook          | https://www.facebook.com    |
  	| Google                 | google            | https://accounts.google.com |
  	| Microsoft              | microsoftaccount  | https://login.live.com      |
  	| Twitter                | twitter           | https://api.twitter.com     |

    內容-安全性-原則 （適用於 Azure Active Directory 實作） 的範例如下所示︰

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    您應該取代`https://login.windows.net`與 OAuth 主機從上述資料表。  如需此中繼標籤相關資訊，請參考[內容安全性原則文件]。

    請注意的一些驗證提供者不需要的內容安全性原則會在適當的行動裝置上使用。  例如，沒有內容安全性原則所做的變更需要 Android 裝置上使用 Google 驗證時。

3. 開啟`www/js/index.js`檔案進行編輯，請找出`onDeviceReady()`方法，並在建立用戶端程式碼下新增︰

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    將此程式碼取代現有的程式碼，建立表格參照，並重新整理 UI 附註。

    Login() 方法啟動驗證提供者。 Login() 方法是傳回 JavaScript 品非同步函數。  初始設定的其餘部分置於品回應，使其不會執行，直到 login() 方法完成。

4. 在您剛才新增的程式碼，取代`SDK_Provider_Name`與您的登入提供者的名稱。 例如的 Azure Active Directory 使用`client.login('aad')`。

4. 執行您的專案。  當專案完成初始化時，您的應用程式就會顯示 OAuth 登入頁面所選的驗證提供者。

##<a name="next-steps"></a>後續步驟

* 瞭解更多[關於驗證]Azure 應用程式服務。
* 繼續教學課程以加 Apache Cordova 應用程式的[推播通知]] 。

瞭解如何使用 Sdk。

* [Apache Cordova SDK]
* [ASP.NET 伺服器 SDK]
* [Node.js 伺服器 SDK]

<!-- URLs. -->
[開始使用行動應用程式]: app-service-mobile-cordova-get-started.md
[內容安全性原則文件]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[推播通知]: app-service-mobile-cordova-get-started-push.md
[有關驗證]: app-service-mobile-auth.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md 
[ASP.NET 伺服器 SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js 伺服器 SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
