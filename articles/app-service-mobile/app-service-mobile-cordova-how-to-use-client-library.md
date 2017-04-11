<properties
    pageTitle="如何使用 Apache Cordova 外掛程式 Azure 行動應用程式"
    description="如何使用 Apache Cordova 外掛程式 Azure 行動應用程式"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>如何使用 Apache Cordova 用戶端文件庫 Azure 行動應用程式

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南會指導您執行常見的案例使用最新的[Apache Cordova 外掛程式 Azure Mobile 應用程式]。 如果您是 Azure Mobile 應用程式的新項目，第一個完整[Azure 行動應用程式快速入門]建立後端，建立表格，並下載預先建立的 Apache Cordova 專案。 本指南中，我們著重於用戶端 Apache Cordova 外掛程式。

## <a name="supported-platforms"></a>支援的平台

此 SDK 支援 Apache Cordova v6.0.0 與稍後 iOS、 Android 和 Windows 裝置。  支援的平台如下所示︰

* Android API 19 24 (透過 Nougat KitKat)
* iOS 8.0 及更新版本。
* 在 Windows Phone 8.0
* 在 Windows Phone 8.1
* 通用 Windows 平台

##<a name="Setup"></a>設定與先決條件

本指南假設您已建立後端的資料表。 本指南假設表格這些教學課程中具有相同的結構描述的資料表。 本指南也假設您已新增您的程式碼 Apache Cordova 外掛程式。  如果您不這麼做，您可能會新增至命令列上專案的 Apache Cordova 外掛程式︰

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

如需有關如何建立[您的第一個 Apache Cordova 應用程式]的詳細資訊，請參閱其文件。

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>如何︰ 驗證的使用者

Azure 應用程式服務支援使用各種不同的外部的身分識別提供者的應用程式使用者驗證與授權︰ Facebook、 Google、 Microsoft 帳戶和 Twitter。 您可以設定權限限制為特定作業已驗證的使用者存取的表格。 您也可以使用已驗證的使用者身分的識別實作伺服器指令碼中的授權規則。 如需詳細資訊，請參閱[開始使用驗證]教學課程。

使用驗證時 Apache Cordova 應用程式中，必須提供下列 Cordova 外掛程式︰

* [cordova 外掛程式裝置]
* [cordova-外掛程式-inappbrowser]

兩個驗證流程支援︰ 伺服器流程和用戶端流程。  伺服器流程提供最簡單的驗證體驗，必須提供者的 web 驗證介面。 用戶端流程例如允許更深入的整合與裝置的特定功能單一登入時必須提供者特定裝置的特定 Sdk。

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>如何︰ 設定您的行動應用程式服務外部重新導向 url。

許多種類型的 Apache Cordova 應用程式會使用迴路功能來處理 OAuth UI 流。  由於驗證服務只有知道如何使用您的服務，根據預設，OAuth UI 流在本機上的就會造成問題。  有問題的 OAuth UI 流範例包括︰

- 漣漪模擬器。
- 即時與 Ionic 重新載入。
- 在本機執行行動的後端
- 在不同的 Azure 應用程式服務超過一個提供驗證執行行動的後端。

請遵循這些指示將本機的設定新增到設定︰

1. 登入[Azure 入口網站]
2. 選取**所有資源**或**應用程式服務**，然後按一下您的行動應用程式的名稱。
3. 按一下 [**工具]**
4. OBSERVE 在功能表中，按一下 [**資源檔案總管]** ，然後按一下 [ **Go**]。  在新視窗或] 索引標籤隨即會開啟。
5. 展開 [ **config**] 左側導覽中您網站的**authsettings**節點。
6. 按一下 [**編輯**]
7. 尋找 「 allowedExternalRedirectUrls 」 項目。  它可能會設定為 null，或者值的陣列。  將值變更為下列值︰

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    取代您的服務的 Url 中的 Url。  範例包括 「 http://localhost:3000 」 （適用於 Node.js 範例服務） 或 「 http://localhost:4400 」 （適用於漣漪服務）。  不過，這些 Url 是範例為您的情況，包括範例中, 所提及的服務可能會不同。
8. 按一下畫面右上角的**讀/寫**] 按鈕。
9. 按一下綠色**放置**] 按鈕。

此時會儲存設定。  請勿關閉瀏覽器視窗，直到完成設定儲存。
也這些迴路將 Url 新增到您的應用程式服務的 CORS 設定︰

1. 登入[Azure 入口網站]
2. 選取**所有資源**或**應用程式服務**，然後按一下您的行動應用程式的名稱。
3. 設定刀會自動開啟。  如果沒有的話，請按一下 [**所有設定**]。
4. 按一下 [API] 功能表下的 [ **CORS** ]。
5. 輸入您想要新增的方塊中的 URL 提供，然後按 enter 鍵。
6. 如有需要請輸入其他的 Url。
7. 按一下 [**儲存**] 儲存設定。

需要大約 10 到 15 秒的新設定才會生效。

##<a name="register-for-push"></a>如何︰ 註冊推入通知

安裝[phonegap 外掛程式-推入]處理推入通知。  此外掛程式可以輕鬆地新增使用`cordova plugin add`command 命令列中，或透過 Visual Studio 中給外掛程式安裝程式。  下列程式碼 Apache Cordova 應用程式中的註冊您的裝置推入通知︰

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

使用通知集線器 SDK 從伺服器傳送推入通知。  永不傳送推入通知，直接從用戶端。 如此一來可能會用來觸發服務攻擊通知集線器或 PNS 拒絕。  PNS 可能禁止您當做此類攻擊的流量。

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com
[Azure 行動應用程式快速入門]: app-service-mobile-cordova-get-started.md
[快速入門驗證]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure 行動應用程式的 Apache Cordova 外掛程式]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[您的第一個 Apache Cordova 應用程式]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[推-入外掛程式 phonegap]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova 外掛程式裝置]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-外掛程式-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
