<properties
    pageTitle="使用要求與回應動作 |Microsoft Azure"
    description="邀請及回應的觸發程序和 Azure 邏輯應用程式中的動作概觀"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>快速入門的邀請及回應的元件

使用邏輯應用程式中的邀請及回應元件，您可以即時事件回應。

例如，您可以︰

- 從內部部署資料庫透過邏輯應用程式回應 HTTP 要求資料。
- 觸發程序的邏輯應用程式，從外部 webhook 事件。
- 來電要求與回應動作從另一個邏輯應用程式中的邏輯應用程式。

若要開始在邏輯應用程式中使用的邀請及回應的動作，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="use-the-http-request-trigger"></a>使用 HTTP 要求觸發程序

觸發程序是可以用來開始工作流程中的邏輯應用程式所定義的事件。 [深入瞭解引動程序](connectors-overview.md)。

以下是如何設定 HTTP 要求邏輯應用程式設計工具中的範例順序。

1. 將**收到要求-時 HTTP 要求**的觸發程序新增邏輯應用程式中。 您可以選擇性地提供 JSON 結構描述 （藉由使用的工具，例如[JSONSchema.net](http://jsonschema.net)） 邀請內文。 這個選項可讓產生的屬性的權杖 HTTP 邀請中的設計工具。
2. 新增另一個動作，讓您可以儲存邏輯應用程式。
3. 儲存之後邏輯應用程式，您可以從邀請卡取得 HTTP 要求 URL。
4. HTTP 張貼 （您可以使用的工具，例如[郵差](https://www.getpostman.com/)） 的 url 會觸發邏輯應用程式。

>[AZURE.NOTE] 如果您沒有定義回應動作，`202 ACCEPTED`回應立即傳回給呼叫者。 若要自訂回應，您可以使用 [回應] 動作。

![回應觸發程序](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>使用 [HTTP 回應指令

HTTP 回應動作只有在 HTTP 要求觸發工作流程中使用時才有效。 如果您沒有定義回應動作，`202 ACCEPTED`回應立即傳回給呼叫者。  在任何工作流程中的步驟，您可以新增回應動作。 邏輯應用程式只會保留要求開啟之一分鐘的回應。  如果沒有回應傳送工作流程 （及回應動作存在於定義） 的一分鐘後`504 GATEWAY TIMEOUT`會傳回給呼叫者。

以下說明如何新增 HTTP 回應動作︰

1. 選取**新的步驟**] 按鈕。
2. 選擇 [**新增動作**]。
3. 在 [巨集指令搜尋] 方塊中，輸入**回應**] 清單中的 [回應] 動作。

    ![選取 [回應] 動作](./media/connectors-native-reqres/using-action-1.png)

4. 新增所需的回應訊息任何參數。

    ![完成回應的動作](./media/connectors-native-reqres/using-action-2.png)

5. 按一下 [儲存] 工具列的左上角，邏輯應用程式將會同時儲存並發佈 （啟動）。

## <a name="request-trigger"></a>要求觸發程序

以下是支援此連接器的觸發程序的詳細資料。 有單一要求觸發程序。

|觸發程序|描述|
|---|---|
|要求|收到 HTTP 要求時，就會發生|

## <a name="response-action"></a>回應巨集指令

以下是支援此連接器的動作的詳細資料。 有單一回應動作，您可以只使用伴隨要求觸發程序。

|巨集指令|描述|
|---|---|
|回應|傳回相關聯的 HTTP 要求的回應|

### <a name="trigger-and-action-details"></a>觸發程序和動作的詳細資料

下表描述的觸發程序及動作]，輸入的欄位和對應的輸出詳細資料。

#### <a name="request-trigger"></a>要求觸發程序
以下是從傳入的 HTTP 要求觸發程序輸入的欄位。

|顯示名稱|屬性名稱|描述|
|---|---|---|
|JSON 結構描述|結構描述|HTTP 要求主體 JSON 結構描述|
<br>

**輸出詳細資料**

以下是要求的輸出詳細資料。

|屬性名稱|資料類型|描述|
|---|---|---|
|頁首|物件|要求標頭|
|本文|物件|要求物件|

#### <a name="response-action"></a>回應巨集指令

以下是輸入的欄位 HTTP 回應的動作。 A * 表示它是必要的欄位。

|顯示名稱|屬性名稱|描述|
|---|---|---|
|狀態碼 *|statusCode|HTTP 狀態碼|
|頁首|頁首|若要加入的任何回應標頭的 JSON 物件|
|本文|本文|回應內容|

## <a name="next-steps"></a>後續步驟

現在，請試試看的平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。 您可以查看我們的[Api 清單](apis-list.md)來探索邏輯應用程式中其他可用的連接器。
