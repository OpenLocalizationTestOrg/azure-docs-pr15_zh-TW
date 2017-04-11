<properties 
    pageTitle="邏輯應用程式建立的 API" 
    description="建立自訂的 API 與邏輯應用程式搭配使用" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>建立自訂的 API 與邏輯應用程式搭配使用

如果您想要擴充邏輯應用程式平台，有許多種方式，您可以撥入 Api 或無法做為其中一個我們多的方塊出連接器的系統。  若要建立 API 應用程式這些方法的其中一您可以從呼叫中的邏輯應用程式工作流程。

## <a name="helpful-tools"></a>實用的工具

最適合使用邏輯應用程式的 api，建議您產生[swagger](http://swagger.io)文件為您的 API 詳述支援的作業和參數。  有許多文件庫 （例如[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle))，會自動產生您 swagger。  您也可以使用[TRex](https://github.com/nihaue/TRex)協助也使用邏輯應用程式 （顯示名稱、 屬性類型等等） swagger 加上註釋。  邏輯應用程式的內建的 API 應用程式的一些範例，請務必請查看我們[GitHub 存放庫](http://github.com/logicappsio)或[部落格中](http://aka.ms/logicappsblog)。

## <a name="actions"></a>動作

邏輯應用程式的基本動作是控制站來接受 HTTP 要求，並傳回回應 (通常為 200)。  但是有不同模式您可以依照延伸動作，根據您的需求。

根據預設，邏輯應用程式引擎 1 分鐘後要求將會出現逾時。  不過，您可以讓您執行的更久的時間執行的動作，並讓引擎按照下列詳細的非同步 」 或 「 webhook 圖樣，完成時，請等候的 API。

標準的動作，只要您透過 swagger 公開的 API 中撰寫 HTTP 要求方法。  您可以看到範例使用我們[GitHub 存放庫](https://github.com/logicappsio)中的邏輯應用程式的 API 應用程式。  以下是完成常見的模式以自訂連接器的方法。

### <a name="long-running-actions---async-pattern"></a>長時間執行的動作-非同步圖樣

執行的是長的步驟或任務，的首先您需要執行時，請確定引擎可讓您知道您還沒有逾時。 您也需要進行通訊引擎的方式，就會知道當您完成工作之後，請時，最後，您必須返回相關資料引擎，還是可以繼續使用工作流程。 您可以透過 API 完成的按照下列流程。 這些步驟是從點--檢視的自訂 API 的︰

1. 收到要求時，立即前，傳回回應 （作業）。 此回應會`202 ACCEPTED`回應，讓知道有資料，引擎接受內容，並立即處理。 202 回應應該包含下列標題︰ 
 * `location`頁首 （必填）︰ 這是絕對路徑 URL 邏輯應用程式可用來檢查作業的狀態。
 * `retry-after`(選擇性的預設值為 20 動作)。 這是引擎要等候輪詢位置的標頭 URL，以檢查狀態的秒數。

2. 當已選取工作狀態時，請執行下列檢查︰ 
 * 如果工作完成︰ 傳回`200 OK`回應，回應內容。
 * 如果工作仍在處理︰ 傳回另一個`202 ACCEPTED`回應，請使用相同的頁首為初始回應

此模式可讓您執行太長的您自訂的 API 往來書信中顯示的工作，但保留作用中連線至作用邏輯應用程式引擎，它不會出現逾時讓或繼續之前已完成。 新增時這將邏輯應用程式，請務必注意您不需要執行任何動作，在您定義中的邏輯應用程式，以繼續投票，並檢查狀態。 一旦引擎看到具有有效的位置標頭的 202 接受回應時，它會接受非同步圖樣，並繼續以獲得位置標頭，直到非 202 會傳回。

您可以看到此模式中 GitHub 範例[以下](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Webhook 動作

在您的工作流程，您可以讓邏輯應用程式暫停，等待的 「 回撥] 以繼續進行。  此回撥隨附在 HTTP 張貼的表單。  若要執行這個模式，您需要控制器上提供兩個端點︰ 訂閱，並取消訂閱。

在 [訂閱]，將會建立邏輯應用程式，並將其登錄準備為 HTTP 張貼您的 API 可儲存回撥 URL] 與 [回撥中。  任何內容/標題將會傳送至邏輯應用程式，並可用於工作流程的其餘部分。  邏輯應用程式引擎會執行呼叫訂閱點，只要叫的步驟。

如果執行已取消，邏輯應用程式引擎會撥打電話至 「 取消 」 的端點。  如有需要您的 API 然後可取消回撥 URL。

目前不支援邏輯應用程式設計工具，探索 swagger，透過 webhook 結束點，讓您必須使用此類型的巨集指令新增 「 Webhook 」 動作，並指定 URL、 標題，以及您要求的內容。  您可以使用`@listCallbackUrl()`中任何這些欄位，視需要將回撥 URL 中的工作流程函數。

您可以看到 webhook 模式中 GitHub 範例[以下](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>觸發程序

除了 [動作]，您可以讓您自訂的 API 法邏輯應用程式的觸發程序。  有兩種模式，您可以遵循下列觸發邏輯應用程式︰

### <a name="polling-triggers"></a>投票引動程序

投票引動程序是非常類似上述長執行非同步動作。  邏輯應用程式引擎會呼叫的觸發程序端點經過一段時間後 （從屬 SKU 15 秒的進階版，請 1 分鐘標準和免費的 1 小時）。

如果沒有資料，則可使用，觸發程序會傳回`202 ACCEPTED`回應，與`location`和`retry-after`頁首。  不過，為了引動程序建議`location`標頭包含查詢參數的`triggerState`。  這是您知道當最後一次邏輯應用程式引發的 API 的一些識別碼。  如果資料，則可使用，觸發程序會傳回`200 OK`回應內容的內容。  這會引發邏輯應用程式。

如果我已投票，以查看如果檔案是可用的範例，您無法建立投票觸發程序，可以執行下列動作︰

* 如果沒有 triggerState 收到要求將會傳回 API`202 ACCEPTED`與`location`具有目前時間的 triggerState 的頁首和`retry-after`為 15。
* 如果使用 triggerState 收到的要求︰
 * 請檢查 triggerState DateTime 後是否已新增的任何檔案。 
  * 如果有 1 個檔案，傳回`200 OK`回應內容的內容，遞增至我傳回，並設定的檔的 DateTime triggerState `retry-after` 15。
  * 如果有多個檔案，我可以使用一次傳回 1 `200 OK`，遞增中的我 triggerState`location`標題，以及設定`retry-after`0。  這可讓知道有更多資料，並將其會立即要求在引擎`location`指定的標題。
  * 如果沒有可用的檔案，傳回`202 ACCEPTED`回應，並保留`location`triggerState 相同。  設定`retry-after`15。

您可以看到範例投票觸發程序中 GitHub[以下](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Webhook 引動程序

Webhook 引動程序行為類似上述 Webhook 動作。  邏輯應用程式引擎會呼叫 '訂閱 「 結束點，只要 webhook 觸發程序會新增並儲存。  您的 API 可以註冊 webhook URL，並透過 HTTP 張貼每當資料可供使用。  的標題及內容的內容將會傳送至執行的邏輯應用程式。

如果 webhook 觸發程序也能刪除 (邏輯應用程式完全，或只是 webhook 觸發程序)，引擎會撥打電話至 「 取消' URL 可以在移除註冊您的 API 回撥 URL，並視需要停止任何處理程序。

目前不支援邏輯應用程式設計工具，探索 swagger，透過 webhook 觸發程序，讓您必須使用此類型的動作 」 Webhook 」 引動程序，並指定 URL、 標題，以及您要求的內容。  您可以使用`@listCallbackUrl()`中任何這些欄位，視需要將回撥 URL 中的工作流程函數。

您可以看到範例 webhook 觸發程序中 GitHub[以下](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)