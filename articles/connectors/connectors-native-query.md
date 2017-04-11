<properties
    pageTitle="邏輯應用程式中新增查詢動作 |Microsoft Azure"
    description="執行動作，例如篩選陣列的查詢動作的概觀。"
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
   ms.date="07/20/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-query-action"></a>開始使用 [查詢] 動作

藉由使用 [查詢] 動作，您可以使用批次並完成的工作流程的陣列︰

- 從資料庫中建立任務的最高優先順序的所有記錄。
- 將所有 PDF 附件的電子郵件到 Azure blob 都儲存。

若要開始使用邏輯應用程式中的 [查詢] 動作，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="use-the-query-action"></a>使用 [查詢] 動作

動作是由邏輯應用程式中定義的工作流程執行的作業。 [深入瞭解動作](connectors-overview.md)。  

[查詢] 動作目前有一項作業，稱為篩選的陣列，公開設計工具中。 這個選項可讓您陣列的查詢，並傳回一組篩選的結果。

以下是如何您可以將其新增邏輯應用程式中︰

1. 選取**新的步驟**] 按鈕。
2. 選擇 [**新增動作**]。
3. 在 [動作] 搜尋方塊中，輸入**篩選**來**篩選陣列**中的動作] 清單中。

    ![選取 [查詢] 動作](./media/connectors-native-query/using-action-1.png)

4. 選取要篩選的陣列。 （以下的螢幕擷取畫面顯示 Twitter 搜尋結果的陣列）。
5. 建立條件評估在每個項目。 （以下的螢幕擷取畫面篩選傳出 tweets 從 100 個以上的追蹤者的使用者）。

    ![完成查詢的動作](./media/connectors-native-query/using-action-2.png)

    此動作會輸出新的陣列，包含符合篩選需求的結果。
6. 按一下 [儲存] 工具列的左上角，邏輯應用程式將會同時儲存並發佈 （啟動）。

## <a name="query-action"></a>查詢巨集指令

以下是支援此連接器的動作的詳細資料。 連接線會有一個可能的動作。

|巨集指令|描述|
|---|---|
|篩選陣列|評估陣列中的每個項目的條件，並傳回結果|

## <a name="action-details"></a>動作的詳細資料

[查詢] 動作隨附一個可能的動作。 下表描述的必要與選用輸入的欄位的動作，並使用動作相關聯的相對應輸出詳細資料。

### <a name="filter-array"></a>篩選陣列
以下是動作，讓 HTTP 輸出要求輸入的欄位。
A * 表示它是必要的欄位。

|顯示名稱|屬性名稱|描述|
|---|---|---|
|從 *|從|若要篩選陣列|
|條件 *|位置|要評估的每個項目的條件|
<br>

### <a name="output-details"></a>輸出詳細資料

以下是 HTTP 回應的輸出詳細資料。

|屬性名稱|資料類型|描述|
|---|---|---|
|篩選的陣列|陣列|陣列，其中包含每個篩選的結果的物件|

## <a name="next-steps"></a>後續步驟

現在，請試試看的平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。 您可以查看我們的[Api 清單](apis-list.md)來探索邏輯應用程式中其他可用的連接器。
