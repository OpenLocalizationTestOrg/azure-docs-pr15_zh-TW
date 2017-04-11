
<properties
    pageTitle="新增 HTTP + Swagger 邏輯應用程式中的動作 |Microsoft Azure"
    description="概觀 HTTP + Swagger 巨集指令和作業"
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

# <a name="get-started-with-the-http--swagger-action"></a>開始使用 HTTP + Swagger 巨集指令

使用 HTTP + Swagger 動作]，您可以建立第一類的連接器，以透過[Swagger 文件](https://swagger.io)的任何其他結束點。 您也可以延伸撥打與最高級邏輯應用程式的設計工具體驗任何其餘端點的邏輯應用程式。

若要開始使用 HTTP + Swagger 邏輯應用程式中的動作，請參閱[建立新的邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>使用 HTTP + Swagger 觸發程序或動作

HTTP + Swagger 觸發程序和動作函數[HTTP] 動作](connectors-native-http.md)相同，但在設計工具中顯示的 API 及輸出的圖案，從[Swagger 中繼資料](https://swagger.io)提供更好的設計體驗。 此外，您可以使用 HTTP + Swagger 的觸發程序。 如果您想要實作投票觸發程序，它應該遵循投票模式中[建立自訂的 API 使用邏輯應用程式](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers)所述。

[進一步瞭解邏輯應用程式引動程序和動作。](connectors-overview.md)

以下是使用 HTTP + 為邏輯應用程式中的工作流程動作 Swagger 作業的範例。

1. 選取**新的步驟**] 按鈕。
2. 選取 [**新增動作**。
3. 在 [動作] 搜尋方塊中，輸入**swagger**清單 HTTP + Swagger 動作。

    ![選取 [HTTP + Swagger 巨集指令](./media/connectors-native-http-swagger/using-action-1.png)

4. 輸入 Swagger 文件的 URL:
    - 若要從邏輯應用程式設計工具，請 URL 必須 HTTPS 端點，有 CORS 啟用。
    - 如果 Swagger 文件不符合這項需求，您可以使用[與 CORS 啟用 Azure 儲存體](#hosting-swagger-from-storage)來儲存文件。
5. 按一下 [**下一步**讀取及呈現 Swagger 文件。
6. 新增所需的 HTTP 通話任何參數。

    ![完成 HTTP 巨集指令](./media/connectors-native-http-swagger/using-action-2.png)

1. 按一下 [工具列和您邏輯應用程式將兩個儲存的左上角的 [**儲存**並發佈] （啟動）。

### <a name="host-swagger-from-azure-storage"></a>Host （主機) Swagger 從 Azure 儲存體

您可能會想要參照的不裝載，或不符合的安全性和設計工具的十字形，需求 Swagger 文件。 若要解決此問題，您可以將 Swagger 文件儲存在 Azure 儲存體，並啟用 CORS 參照文件。  

以下是建立、 設定和 Swagger 文件儲存在 Azure 儲存體中的步驟︰

1. [建立與 Azure Blob 儲存體 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。 （若要執行此動作，為**公用存取**的設定權限。）
2. 啟用 blob CORS。 您可以設定該設定會自動使用[這個 PowerShell 指令碼](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)。
3. Swagger 檔案上傳到 blob。 從[Azure 入口網站](https://portal.azure.com)，或從如[Azure 儲存檔案總管](http://storageexplorer.com/)工具，您可以執行此動作。
1. 參照 HTTPS 連結至 Azure Blob 儲存體中的文件。 (連結遵循格式`https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`。)



## <a name="technical-details"></a>技術詳細資料

以下是引動程序與動作的詳細資料，此 HTTP + Swagger 連接器支援。

## <a name="http--swagger-triggers"></a>HTTP + Swagger 引動程序

觸發程序是可以用來開始工作流程中的邏輯應用程式所定義的事件。 [進一步瞭解引動程序。](connectors-overview.md) HTTP + Swagger 連接器有一個觸發程序。

|觸發程序|描述|
|---|---|
|HTTP + Swagger|撥打 HTTP 通話，並傳回回應內容|

## <a name="http--swagger-actions"></a>HTTP + Swagger 動作

動作是由邏輯應用程式中定義的工作流程執行的作業。 [進一步瞭解動作。](connectors-overview.md) HTTP + Swagger 連接器具有一個可能的動作。

|巨集指令|描述|
|---|---|
|HTTP + Swagger|撥打 HTTP 通話，並傳回回應內容|

### <a name="action-details"></a>動作的詳細資料

HTTP + Swagger 連接器隨附一個可能的動作。 下列是每個動作、 其必要與選用輸入的欄位和對應及其使用方式與相關聯的輸出詳細資料的相關資訊。

#### <a name="http--swagger"></a>HTTP + Swagger

進行 HTTP 輸出要求 Swagger 中繼資料的協助。
星號 （*） 代表所需的欄位。

|顯示名稱|屬性名稱|描述|
|---|---|---|
|方法 *|方法|若要使用的 HTTP 動作。|
|URI *|uri|空的 URI。|
|頁首|頁首|將 HTTP 標頭 JSON 物件。|
|本文|本文|HTTP 邀請內文中。|
|驗證|驗證|用於要求驗證。 [如需詳細資訊，請參閱 HTTP](./connectors-native-http.md#authentication)。|

**輸出詳細資料**

HTTP 回應

|屬性名稱|資料類型|描述|
|---|---|---|
|頁首|物件|回應標頭|
|本文|物件|回應物件|
|狀態碼|int|HTTP 狀態碼|

### <a name="http-responses"></a>HTTP 回應

通話的各種動作時，您可能會收到特定的回應。 下列是概述相對應的回覆和描述的資料表。

|名稱|描述|
|---|---|
|200|[確定]|
|202|接受|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 未知錯誤。|

---

## <a name="next-steps"></a>後續步驟

現在 [試試看的平台和[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。 您可以查看我們[的 Api 的清單](apis-list.md)，以瀏覽邏輯應用程式中其他可用的連接器。
