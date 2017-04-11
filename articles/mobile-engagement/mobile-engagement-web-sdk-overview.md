<properties
    pageTitle="Azure 行動互動 Web SDK 概觀 |Microsoft Azure"
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
    ms.date="10/18/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk"></a>Azure 行動互動 Web SDK

從這裡開始，如需如何將 web 應用程式中整合 Azure 行動互動的所有詳細資訊。 如果您想要嘗試一下之前開始使用您自己的 web 應用程式，請參閱我們[15 分鐘教學課程](mobile-engagement-web-app-get-started.md)。

## <a name="integration-procedures"></a>整合程序
1. 瞭解[如何將整合 web 應用程式中的行動互動](mobile-engagement-web-integrate-engagement.md)。

2. 標記計劃實作，瞭解[如何使用進階標記 API web 應用程式中的行動互動。](mobile-engagement-web-use-engagement-api.md)

## <a name="release-notes"></a>版本資訊

### <a name="202-10182016"></a>2.0.2 (2016/10/18)

-   當機固定私下瀏覽 (Safari)。
-   在瀏覽器中使用 cookie 停用的固定當機。

所有的版本，請參閱[完整版本資訊](mobile-engagement-web-release-notes.md)。

## <a name="upgrade-procedures"></a>升級程序

### <a name="upgrade-from-121-to-200"></a>從 1.2.1 升級至 2.0.0

下列各節說明如何從 Azure 行動互動應用程式提供 Capptain SA Capptain 服務，移轉行動互動 Web SDK 整合。 如果您從這裡移轉版本早於 1.2.1，請參閱將第一次，移轉至 1.2.1 Capptain 網站，然後套用 [下列程序。

這個版本的行動互動 Web SDK 不支援針對 Samsung 智慧電視、 Opera 電視、 webOS 或到達功能。

>[AZURE.IMPORTANT] Capptain 和 Azure 行動參與不相同的服務，而且下列程序醒目提示要移轉的用戶端應用程式的方式。 移轉行動互動 Web SDK 的應用程式中將不行動互動伺服器的從 Capptain server 移轉您的資料。

#### <a name="javascript-files"></a>JavaScript 檔案

檔案 capptain sdk.js 取代檔案 azure-engagement.js，然後再適當地更新您的指令碼匯入。

#### <a name="remove-capptain-reach"></a>移除 Capptain 範圍

這個版本的行動互動 Web SDK 不支援到達功能。 如果您有 Capptain 達到整合應用程式，必須先將其移除。

移除達到 CSS 匯入] 頁面，並刪除相關的.css 檔案 (capptain-reach.css，依預設)。

刪除下列達到資源︰ 關閉圖像 (capptain-close.png，依預設) 和品牌圖示 （capptain-通知-圖示上，依預設）。

移除達到 UI 的應用程式通知]。 預設的版面配置看起來像這樣︰

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

移除文字和網頁的相關公告和輪詢的回覆在達到 ui。 預設的版面配置看起來像這樣︰

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

移除`reach`物件從您的設定，若有的話。 它看起來像這樣︰

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

移除任何其他範圍的自訂，例如類別。

#### <a name="remove-deprecated-apis"></a>移除已遭取代 Api

從 Capptain 一些 Api 是行動互動 Web SDK 中已遭取代。

移除任何的來電至] 下的 Api: `agent.connect`， `agent.disconnect`， `agent.pause`，及`agent.sendMessageToDevice`。

從您的 Capptain 設定移除任何下列回撥︰ `onConnected`， `onDisconnected`， `onDeviceMessageReceived`，及`onPushMessageReceived`。

#### <a name="configuration"></a>設定

行動互動使用連接字串，以設定 SDK 識別項，例如，應用程式識別碼。

取代您的連線字串中的應用程式識別碼。 請注意，從變更 SDK 設定全域物件`capptain`至`azureEngagement`。

前移轉︰

    window.capptain = {
      appId: ...,
      [...]
    };

移轉︰ 後

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

您的應用程式的連接字串會顯示在 Azure 入口網站。

#### <a name="javascript-apis"></a>JavaScript Api

全域 JavaScript 物件`window.capptain`已重新命名`window.azureEngagement`，但是您可以使用`window.engagement`API 通話的別名。 您無法使用此 alias （別名） 來定義 SDK 設定。

例如，`capptain.deviceId`變成`engagement.deviceId`，`capptain.agent.startActivity`變成`engagement.agent.startActivity`，依此類推。

如果您已經有舊版 Azure 行動互動 Web SDK 整合應用程式中，請閱讀有關[升級程序](mobile-engagement-web-upgrade-procedure.md)。
