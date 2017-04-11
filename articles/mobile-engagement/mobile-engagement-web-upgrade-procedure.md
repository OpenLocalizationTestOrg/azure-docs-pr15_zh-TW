<properties
    pageTitle="Azure 行動互動 Web SDK 升級程序 |Microsoft Azure"
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
    ms.date="06/07/2016"
    ms.author="piyushjo" />


# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure 行動互動 Web SDK 升級程序

如果您已經有舊版 Azure 行動互動 Web SDK 整合 web 應用程式中，您需要升級 SDK 時，請考慮下列幾點。

如果您略過多個版本的行動互動 Web SDK，您可能需要在升級程序完成數個程序。 比方說，如果從 1.4.0 移轉到 1.6.0 時，第一次遵循 1.5.0 從 1.4.0 升級程序。 然後，依照 1.6.0 從 1.5.0 升級程序。

您升級，不論版本會取代最新版的檔案中的任何舊版檔案 azure engagement.js。

## <a name="upgrade-from-121-to-200"></a>從 1.2.1 升級至 2.0.0

本節說明如何從 Azure 行動互動應用程式提供 Capptain SA Capptain 服務，移轉行動互動 Web SDK 整合。 如果您從舊版升級，請參閱將第一次移轉至 1.2.1，Capptain 網站，然後套用 [下列程序。

這個版本的行動互動 Web SDK 不支援針對 Samsung 智慧電視、 Opera 電視、 webOS 或到達功能。

>[AZURE.IMPORTANT] Capptain 和 Azure 行動互動不相同的服務。 下列程序會醒目提示要移轉的用戶端應用程式的方式。 移轉行動互動 Web SDK 的應用程式中將不行動互動伺服器的從 Capptain server 移轉您的資料。

### <a name="javascript-files"></a>JavaScript 檔案

檔案 capptain sdk.js 取代檔案 azure-engagement.js，然後再適當地更新您的指令碼匯入。

### <a name="remove-capptain-reach"></a>移除 Capptain 範圍

這個版本的行動互動 Web SDK 不支援到達功能。 如果您將 Capptain 達到整合應用程式時，必須先將其移除。

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

### <a name="remove-deprecated-apis"></a>移除已遭取代 Api

從 Capptain 一些 Api 是行動互動 Web SDK 中已遭取代。

移除任何的來電至] 下的 Api: `agent.connect`， `agent.disconnect`， `agent.pause`，及`agent.sendMessageToDevice`。

從您的 Capptain 設定中移除下列回撥的任何執行個體︰ `onConnected`， `onDisconnected`， `onDeviceMessageReceived`，及`onPushMessageReceived`。

### <a name="configuration"></a>設定

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

### <a name="javascript-apis"></a>JavaScript Api

全域 JavaScript 物件`window.capptain`已重新命名`window.azureEngagement`，但您可以使用`window.engagement`API 通話的別名。 您無法使用 alias （別名） 來定義 SDK 設定。

例如，`capptain.deviceId`變成`engagement.deviceId`，`capptain.agent.startActivity`變成`engagement.agent.startActivity`，依此類推。
