<properties
    pageTitle="快速入門 azure AD Cordova |Microsoft Azure"
    description="如何建置 Cordova 應用程式的整合 Azure AD 登入並通話 Azure AD 受到保護的 Api 使用 OAuth。"
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Azure AD 整合 Apache Cordova 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova 可讓您開發 HTML5/JavaScript 應用程式，可以為完備的原生應用程式的行動裝置上執行。
使用 Azure AD，您可以新增成績驗證的企業功能 Cordova 應用程式。 獎狀文繞圖 iOS Android、 Windows 市集和 Windows Phone 上的 [Azure AD 原生 Sdk Cordova 外掛程式，您可以增強您的應用程式支援簽署您的使用者 AD 帳戶存取 Office 365 和 Azure API，甚至保護您自己的自訂網頁 API 來電。

在本教學課程中，我們將使用 Apache Cordova 外掛程式的 Active Directory 驗證文件庫 (ADAL) 來改善簡單的應用程式，使用下列功能︰

-   幾行程式碼，AD 使用者進行驗證並取得撥 Azure AD 圖形 API 的權杖。
-   使用該權杖叫用圖形 API 來查詢的目錄，並將結果顯示  
-   運用 ADAL 的權杖快取的最小化 [使用者驗證提示。

若要這麼做，您必須︰

2. 註冊 Azure AD 應用程式
2. 將程式碼新增至您的應用程式要求權杖
3. 新增查詢圖形 API 使用權杖的程式碼，並顯示結果。
4. 建立 Cordova 部署專案目標，您想要的平台與 Cordova ADAL 的外掛程式，並在模擬器中測試方案。

## <a name="0--prerequisites"></a>*0。 先決條件*

若要完成您需要在此教學課程︰

- Azure AD 租用戶，您有應用程式開發權限的帳戶
- 設定為使用 Apache Cordova 的開發環境  

如果您已經有同時設定，請直接跳到步驟 1。

如果您沒有安裝 Azure AD 租用戶，您可以找到[指示以取得一個以下](active-directory-howto-tenant.md)。

如果您沒有安裝在您的電腦上設定的 Apache Cordova，請安裝下列動作︰

- [給](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/)(透過 NPM 封裝管理員安裝︰ `npm install -g cordova`)

請注意，這些應該使用 PC 與 mac 上

每個目標平台有不同的先決條件。

- 若要建立並執行 Windows 平板電腦或手機應用程式版本
    - [更新 2 或更新版本的 Windows 版的 visual Studio 2013](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8)(Express 或另一個版本)。
- 若要建立並執行 iOS 版
    -   Xcode 5.x 或更大。 Http://developer.apple.com/downloads 或[Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)下載
    -   [ios sim](https://www.npmjs.org/package/ios-sim) – 可讓您將從命令列 iOS 模擬器啟動 iOS 應用程式 (透過終端機安裝︰ `npm install -g ios-sim`)

- 建立和執行 Android 適用的應用程式
    - 安裝[Java 開發套件 (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)或更新版本。 請確定`JAVA_HOME`（環境變數） 也正確地設定根據 JDK 安裝路徑 (例如 C:\Program Files\Java\jdk1.7.0_75)。
    - 安裝[Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools)並新增`<android-sdk-location>\tools`位置 (例如，C:\tools\Android\android-sdk\tools) 給您`PATH`環境變數。
    - 開啟 Android SDK 管理員 (例如，透過 terminal: `android`) 並安裝
    - *Android 5.0.1 (API 21)*平台 SDK
    - *Android SDK 建立工具*版本 19.1.0 或更新版本
    - *Android 支援存放庫*(Extras])

  Android sdk 不提供任何預設的模擬器執行個體。 建立新的執行`android avd`從 terminal，然後選取 [*建立...*如果您想要在模擬器執行 Android 應用程式。 建議的*Api 層級*是 19 或更新版本，請參閱 [AVD Manager] (http://developer.android.com/tools/help/avd-manager.html) 的 Android 模擬器] 和 [建立選項的相關資訊。


## <a name="1--register-an-application-with-azure-ad"></a>*1.註冊 Azure AD 應用程式*

注意︰ 此__為選用步驟__。 教學課程提供預先能夠會允許您看到巨集指令的範例，而不執行任何佈建您自己的租用戶中的值。 不過建議您不要執行此步驟，並熟悉程序，為它必須當您將會建立自己的應用程式。

Azure AD 只會發出權杖已知的應用程式。 您可以從您的應用程式中使用 Azure AD 之前，必須先在您的租用戶中為其建立項目。  若要在您的租用戶註冊新的應用程式

-   登入[Azure 管理入口網站](https://manage.windowsazure.com)
-   在左側導覽中，按一下 [在**Active Directory**
-   選取您想要註冊的應用程式的租用戶。
-   按一下 [**應用程式**] 索引標籤，然後按一下 [底部抽屜中的 [**新增**。
-   請依照提示進行，並建立新的**原生的用戶端應用程式**(儘管 Cordova 應用程式會以 HTML，我們建立原生的用戶端應用程式，因此`Native Client Application`必須選取選項; 否則應用程式無法使用)。
    -   應用程式的**名稱**會說明您的使用者的應用程式
    -   **重新導向 URI**是用來返回您的應用程式的權杖 URI。 輸入`http://MyDirectorySearcherApp`。

當您完成註冊時，AAD 將會在唯一的用戶端識別碼指派您的應用程式。  您必須在下一節中的此值︰ 您可以在新建立的應用程式中的 [**設定**] 索引標籤。

若要執行`DirSearchClient Sample`，授與查詢_Azure AD 圖形 API_的新建立的應用程式權限︰
-   在 [**設定**] 索引標籤，找到 」 權限以其他應用程式 」 區段。  「 Azure Active Directory 」 應用程式中，新增 [**委派的權限****存取的目錄以登入的使用者**權限。  如此一來查詢圖形 API 使用者的應用程式。

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2.複製所需的教學課程範例應用程式存放庫*

從您命令介面或命令列中，輸入以下命令︰

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3.建立 Cordova 應用程式*

有多種方式可以建立 Cordova 應用程式。 在本教學課程中，我們將使用 Cordova 命令列介面 (CLI)。
從您命令介面或命令列中，輸入以下命令︰


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

將會建立資料夾結構和臨時平台 Cordova 專案中，複製 www 子資料夾中的 [起始專案的內容。
移至新的 DirSearchClient 資料夾。

    cd .\DirSearchClient

新增 whitelist 的所需的叫用圖形 API 外掛程式。

     cordova plugin add cordova-plugin-whitelist

接下來，新增所有您想要支援平台。 才有使用範例，您必須執行下列命令中至少一個。 您無法模擬 iOS Windows 或 mac 上的 Windows/Windows Phone 上的筆記

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

最後，您可以新增 Cordova 外掛程式的 ADAL 至您的專案。

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3.加入程式碼驗證的使用者，並從 AAD 取得權杖*

您在本教學課程所開發的應用程式將會提供答對骨目錄搜尋功能，使用者可以位置目錄中輸入的任何使用者別名，並以視覺化方式呈現一些基本的屬性。  起始專案包含 （中 www/index.html) 的應用程式的基本的使用者介面的定義並能夠線路設定基本的應用程式的事件循環使用者介面繫結結果顯示邏輯 （在 www/js/index.js)。 左移您是將邏輯實作身分識別的工作。

首先您需要執行是介紹程式碼中的識別您的應用程式，以及您目標的資源來 AAD 所使用的通訊協定值。 這些值將用於稍後建構權杖要求。 插入 index.js 檔案的最下方的程式碼片段。

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri`和`clientId`值應該符合描述您的應用程式中 AAD 的值。 您可以從 [設定] 索引標籤中找到 Azure 入口網站中，如之前在本教學課程中所述步驟 1。
注意︰ 如果您選擇不註冊您的租用戶中的 [新的應用程式，您可以只貼上預先設定的值上方會-的會允許您看到範例執行，但您應該是針對生產您應用程式建立您自己的項目。

接下來，我們需要加入實際的權杖要求的程式碼。 插入下列程式碼片段之間`search `和`renderdata `定義。

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
現在就讓我們細分其兩個主要部分中，查看該函數。
此範例被為了使用任何的租用戶，而非繫結至特定的。 使用 「 / 常見 」 端點，可讓使用者輸入的任何帳戶驗證時，並指示要求其所屬的租用戶。
第一部分的方法會檢查已儲存的權杖-如果有，就會使用它來重新初始化 ADAL 來自租用戶的 ADAL 快取。 這是為了避免額外的提示，使用的 「 / 常見 」 一律會要求使用者輸入新的帳戶。
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
第二部分的方法執行適當 tokewn 要求。
`acquireTokenSilentAsync`方法要求 ADAL 傳回指定資源的權杖，但不顯示任何經驗 如果快取已儲存，適合存取權杖，可能會發生，或者可以用來取得新的存取權杖 shwoing 沒有任何提示重新整理權杖。
如果的嘗試失敗，我們改用`acquireTokenAsync`-的視覺化方式會提示使用者進行驗證。
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
現在，我們已權杖，我們可以最後叫用圖形 API，並執行搜尋查詢，我們希望。 插入下列程式碼片段的正下方`authenticate`定義。

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
開始點檔案提供 barebone UX 文字方塊中輸入使用者的別名。 這個方法使用此值，來建構查詢合併，以存取權杖，將其傳送給該圖表中，與分析結果。 若要以視覺化方式呈現結果負責 renderData 方法已經存在起始點檔案中。

## <a name="4-run"></a>*4.執行*
您的應用程式是最後準備好執行 ！ 作業系統，是相當簡單︰ 後應用程式啟動了，請在文字方塊中輸入您想要查詢-，然後按一下 [] 按鈕的使用者的別名。 系統會提示您進行驗證。 驗證成功及成功搜尋，而將會顯示搜尋使用者的屬性。 後續執行會執行搜尋，但不顯示任何提示時，獎狀先前取得的 token 的快取中的目前狀態。
執行的應用程式的具體步驟因平台。

####<a name="windows-10"></a>Windows 10:

   平板電腦/電腦︰`cordova run windows --archs=x64 -- --appx=uap`

   行動電話 （需要 Windows10 行動裝置連線到電腦）︰`cordova run windows --archs=arm -- --appx=uap --phone`

   __注意__︰ 您可能會要求登入開發人員授權的第一次執行期間。 如需詳細資訊，請參閱[開發人員的授權](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)。

####<a name="windows-81-tabletpc"></a>Windows 8.1 平板電腦/電腦上︰

   `cordova run windows`

   __注意__︰ 您可能會要求登入開發人員授權的第一次執行期間。 如需詳細資訊，請參閱[開發人員的授權](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)。

####<a name="windows-phone-81"></a>在 Windows Phone 8.1:

   若要連線的裝置上執行︰`cordova run windows --device -- --phone`

   若要執行預設模擬器上︰`cordova emulate windows -- --phone`

   使用`cordova run windows --list -- --phone`若要查看所有可用的目標和`cordova run windows --target=<target_name> -- --phone`特定裝置或模擬器上執行應用程式 (例如， `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`)。

####<a name="android"></a>Android:

   若要連線的裝置上執行︰`cordova run android --device`

   若要執行預設模擬器上︰`cordova emulate android`

   __附註__︰ 請確定您已經建立為*先決條件*一節說明使用*AVD 管理員*模擬器執行個體。

   使用`cordova run android --list`若要查看所有可用的目標和`cordova run android --target=<target_name>`特定裝置或模擬器上執行應用程式 (例如， `cordova run android --target="Nexus4_emulator"`)。

####<a name="ios"></a>iOS:

   若要連線的裝置上執行︰`cordova run ios --device`

   若要執行預設模擬器上︰`cordova emulate ios`

   __附註__︰ 請確定您有`ios-sim`模擬器上執行安裝套件。 如需詳細資訊，請參閱*先決條件*一節。

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

使用`cordova run --help`以查看其他建立並執行選項。

已完成的範例 （不含您設定的值） 所提供的參照，[在這裡](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient)。  您可以立即移至更進階的 （[確定]，與變得更有趣） 案例。  若要嘗試︰

[安全 Node.js Web 與 Azure AD API >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
