<properties
    pageTitle="Azure 行動互動 Web SDK Api |Microsoft Azure"
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

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>在 web 應用程式中使用 Azure 行動互動 API

這份文件是會告訴您的文件的新增[整合在 web 應用程式中的行動互動](mobile-engagement-web-integrate-engagement.md)的方式。 會提供您採取進階的詳細資料，瞭解如何使用 Azure 行動互動 API 報告您的應用程式統計資料。

行動互動 API 提供的`engagement.agent`物件。 Azure 行動互動 Web SDK 別名是預設`engagement`。 您可以重新定義此從 SDK 設定別名。

## <a name="mobile-engagement-concepts"></a>行動互動的概念

下列組件來調整 web 平台版的常見[行動互動的概念](mobile-engagement-concepts.md)。

### <a name="session-and-activity"></a>`Session`與`Activity`

如果使用者皆會保持閒置的兩個活動之間的多個秒數，會將使用者的順序的活動分成兩個不同的工作階段。 下列幾秒鐘稱為工作階段逾時。

Web 應用程式無法單獨宣告使用者活動結尾 (呼叫`engagement.agent.endActivity`函數)，行動互動伺服器自動到期後會關閉 [應用程式] 頁面的三個分鐘內的使用者工作階段。 這稱為伺服器工作階段逾時。

### `Crash`

根據預設，不會建立無法攔截 JavaScript 例外狀況的自動化的報告。 不過，您可以在此報告當機使用手動`sendCrash`函數 （請參閱 [] 區段中，報告當機）。

## <a name="reporting-activities"></a>報表的活動

使用者活動報表包含當使用者開始新的活動，以及當使用者結束目前的活動。

### <a name="user-starts-a-new-activity"></a>使用者啟動新的活動

    engagement.agent.startActivity("MyUserActivity");

您需要呼叫`startActivity()`變更每個時間使用者活動。 此函數的第一個呼叫開始新的使用者工作階段。

### <a name="user-ends-the-current-activity"></a>使用者結束目前的活動

    engagement.agent.endActivity();

您需要呼叫`endActivity()`至少一次當使用者結束他們的最後一個活動。 這會通知使用者目前閒置，而且使用者工作階段，必須先關閉工作階段逾時之後行動互動 Web SDK。 如果您呼叫`startActivity()`工作階段逾時到期之前，只要繼續工作階段。

因為沒有可靠呼叫的 [導覽器] 視窗中關閉時，通常是難以或無法找出在 web 環境中的使用者活動的結尾。 這就是為什麼行動互動伺服器自動到期後會關閉 [應用程式] 頁面的三個分鐘內的使用者工作階段。

## <a name="reporting-events"></a>報告的事件

報告的事件涵蓋工作階段和獨立的事件。

### <a name="session-events"></a>工作階段事件

工作階段事件通常用於報告使用者的工作階段期間由使用者執行的動作。

**不含額外資料的範例︰**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**額外資料的範例︰**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>獨立的事件

與不同的工作階段事件獨立事件發生內容以外的工作階段。

針對，使用``engagement.agent.sendEvent``而不是``engagement.agent.sendSessionEvent``。

## <a name="reporting-errors"></a>報表錯誤

報告錯誤說明工作階段的錯誤和獨立錯誤。

### <a name="session-errors"></a>工作階段錯誤

工作階段通常會使用錯誤報告錯誤的使用者會影響使用者的工作階段期間的訊息。

**不含額外資料的範例︰**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**額外資料的範例︰**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>獨立的錯誤

工作階段與錯誤不同，獨立錯誤可能會發生內容以外的工作階段。

針對，使用`engagement.agent.sendError`而不是`engagement.agent.sendSessionError`。

## <a name="reporting-jobs"></a>報告工作

報告工作涵蓋報告的錯誤和工作，過程中發生的事件與報告當機。

**範例︰**

如果您想要監視 AJAX 要求，您會使用下列步驟︰

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>工作期間報告錯誤

可以相關錯誤，而不是到目前的使用者工作階段執行的工作。

**範例︰**

如果您想要報告錯誤，如果 AJAX 要求失敗︰

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>報告工作期間的事件

事件可以與相關而不是到目前的使用者工作階段，執行的工作感謝`engagement.agent.sendJobEvent`函數。

此函數的運作方式完全想`engagement.agent.sendJobError`。

### <a name="reporting-crashes"></a>報告當機

使用`sendCrash`手動函數，報告當機。

`crashid`引數是識別損毀的類型的字串。
`crash`引數，通常是為字串損毀的堆疊追蹤。

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>額外的參數

您可以附加任意資料事件、 錯誤、 活動或工作。

資料可以是任何 JSON 物件 （但不是陣列或基本類型）。

**範例︰**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>限制

套用額外的參數的限制會在區域中的規則運算式的索引鍵、 值類型和大小。

#### <a name="keys"></a>索引鍵

物件中的每個機碼，必須符合下列規則運算式︰

    ^[a-zA-Z][a-zA-Z_0-9]*

這表示鍵開頭必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="values"></a>值

值會限制為字串、 數字，與布林類型。

#### <a name="size"></a>大小

Extras 限於每個呼叫 1024 個字元 （之後行動互動 Web SDK 會將該編碼 JSON 中）。

## <a name="reporting-application-information"></a>報告應用程式的資訊

您可以手動在報告追蹤資訊 （或任何其他特定應用程式的資訊） 使用`sendAppInfo()`函數。

請注意，這項資訊傳送從屬參照。 特定裝置，將會保留最新值的特定索引鍵。

例如事件 extras]，您可以使用任何 JSON 物件擷取應用程式的資訊。 請注意陣列或子物件會視為一般的字串 （使用 JSON 序列化）。

**範例︰**

以下是傳送給使用者的性別和出生日期的程式碼範例︰

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>限制

套用到應用程式資訊的限制是在區域中的規則運算式的索引鍵和大小。

#### <a name="keys"></a>索引鍵

物件中的每個機碼，必須符合下列規則運算式︰

    ^[a-zA-Z][a-zA-Z_0-9]*

這表示鍵開頭必須至少有一個字母，後面接著字母、 數字或底線 (\_)。

#### <a name="size"></a>大小

應用程式的資訊後限制為每個呼叫 1024 個字元 （行動互動 Web SDK 將該編碼 JSON 中）。

在上述範例中，傳送至伺服器 JSON 是 44 字元︰

    {"birthdate":"1983-12-07","gender":"female"}
