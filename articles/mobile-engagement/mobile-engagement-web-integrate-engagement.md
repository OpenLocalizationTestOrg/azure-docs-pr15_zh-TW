<properties
    pageTitle="Azure 行動互動 Web SDK 整合 |Microsoft Azure"
    description="最新的更新和 Azure 行動互動 Web SDK 的程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>在 web 應用程式中整合 Azure 行動互動

> [AZURE.SELECTOR]
- [Windows 標準](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

本文中的程序會說明最簡單的方式啟動 [分析] 及 [web 應用程式中 Azure 行動互動的監控功能。

請依照啟動計算有關使用者、 工作階段、 活動、 當機和 technicals 所有統計資料所需的記錄報告。 應用程式相關統計資料，例如事件、 錯誤，以及工作，您必須啟動記錄報告手動使用 Azure 行動互動 API。 如需詳細資訊，瞭解[如何使用標記 API web 應用程式的進階的行動互動](mobile-engagement-web-use-engagement-api.md)。

## <a name="introduction"></a>簡介

[下載 Azure 行動互動 Web SDK](http://aka.ms/P7b453)。
行動互動 Web SDK 隨附為單一 JavaScript 檔案，azure-engagement.js，您有要包含在網站或 web 應用程式的每個頁面中。

> [AZURE.IMPORTANT] 執行這個指令碼之前，您必須執行您撰寫應用程式的設定行動互動的指令碼或程式碼片段。

## <a name="browser-compatibility"></a>在瀏覽器相容性

行動互動 Web SDK 使用原生 JSON 編碼和解碼，除了跨網域 AJAX 要求 （依賴 W3C CORS 規格）。 就會與下列瀏覽器相容的︰

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>設定行動裝置的互動

撰寫建立全域管理員的指令碼`azureEngagement`JavaScript 物件，如下列範例所示。 因為您的網站可能有多個頁面，假設，這個指令碼會包含在每一頁。 在此範例中，名為 JavaScript 物件`azure-engagement-conf.js`。

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

`connectionString`應用程式會顯示在 Azure 入口網站中的值。

> [AZURE.NOTE] `appVersionName`與`appVersionCode`是選擇性的。 不過，我們建議您設定他們，使分析可以處理版本資訊。

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>在您的頁面上包含行動互動指令碼
新增行動互動指令碼頁面下列方法之一︰

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

或這樣︰

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias （別名)

行動互動 Web SDK 指令碼載入之後，它會建立存取 SDK Api 的**互動**別名。 您無法使用此 alias （別名） 來定義 SDK 設定。 使用此別名此文件中的參考。

請注意，是否預設別名衝突] 頁面的另一個全域變數，您可以重新定義它的設定，如下所示載入行動互動 Web SDK 之前︰

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>基本報表

在行動互動的基本報表說明工作階段層級的統計資料，例如統計使用者、 工作階段、 活動及當機。

### <a name="session-tracking"></a>追蹤工作階段

行動互動工作階段分成一系列的活動，每個由名稱。

傳統的網站，我們建議您網站的每一頁上宣告不同的活動。 網站或 web 應用程式中的目前的頁面永遠不會變更，您可能會想要在頁面中例如追蹤活動在較小的縮放比例]。

兩種方式，若要開始，或變更目前的使用者活動，來電`engagement.agent.startActivity`函數。 例如︰

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

行動互動伺服器自動結束開啟的工作階段關閉應用程式頁面之後，三個幾分鐘內。

或者，您可以結束工作階段以手動方式，則可電話`engagement.agent.endActivity`。 這會將目前的使用者活動 」 閒置。 」  工作階段會結束稍後 10 秒內，除非新電話號碼至`engagement.agent.startActivity`繼續工作階段。

您可以在 [全域互動物件設定 10 秒延遲，如下所示︰

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] 您無法使用`engagement.agent.endActivity`中`onunload`回撥因為您無法在此階段進行 AJAX 來電。

## <a name="advanced-reporting"></a>進階報告

（選擇性） 如果您要回報特定應用程式的事件、 錯誤，以及工作時，您需要使用行動互動 API。 存取透過行動互動 API`engagement.agent`物件。

您可以存取所有的行動互動行動互動 API 的進階功能。 API 有詳細說明，請參閱[如何使用標記 API web 應用程式的進階的行動互動](mobile-engagement-web-use-engagement-api.md)。

## <a name="customize-the-urls-used-for-ajax-calls"></a>自訂用 AJAX 通話的 Url

您可以自訂行動互動 Web SDK 使用的 Url。 例如，重新定義記錄 URL （SDK 端點的記錄），您可以覆寫如下設定︰

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

如果您的 URL 函數會傳回字串開頭的`/`， `//`， `http://`，或`https://`，不會使用預設的配置。 根據預設，`https://`配置用於這些 Url。 如果您想要自訂預設的配置，覆寫的設定，像這樣︰

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
