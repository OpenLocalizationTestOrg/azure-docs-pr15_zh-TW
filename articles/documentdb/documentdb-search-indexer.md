<properties
    pageTitle="使用索引子 Azure 搜尋與連線 DocumentDB |Microsoft Azure"
    description="本文將示範如何使用做為資料來源與 DocumentDB Azure 搜尋索引。"
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>使用索引子 Azure 搜尋與連線 DocumentDB

如果您正在尋找您的 DocumentDB 資料實作好的搜尋體驗，用於 DocumentDB Azure 搜尋索引子 ！ 本文中，我們會告訴您如何與 Azure 搜尋整合 Azure DocumentDB，而不需撰寫維護編製索引的基礎結構的任何程式碼 ！

此設定，您將必須[設定 Azure 搜尋帳戶](../search/search-create-service-portal.md)（您不需要升級至標準搜尋）]，然後呼叫 [ [Azure 搜尋 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)來建立 DocumentDB**資料來源**與資料來源的**索引**。

在進行互動 REST Api 順序傳送邀請，您可以使用[郵差](https://www.getpostman.com/)、 [Fiddler](http://www.telerik.com/fiddler)或您的喜好設定的任何工具。


##<a id="Concepts"></a>Azure 搜尋索引的概念

建立及管理資料來源 （包括 DocumentDB） azure 搜尋支援和索引子的操作這些資料來源。

**資料來源**指定什麼資料必須能編製索引，認證以存取資料和原則，以啟用 Azure 搜尋有效率地識別資料 （例如修改或刪除您集合內的文件） 中的變更。 這樣就可以使用依多個索引子獨立的資源的定義資料來源。

**重新**說明資料來源的資料流動到目標搜尋索引方式。 您應該計劃建立一個索引的每個目標索引與資料來源的組合。 雖然您可以有多個索引子撰寫相同的索引，重新只能寫入到單一索引。 索引子是用來︰

- 執行一次複製要填入索引的資料。
- 同步處理變更排程的資料來源中的索引。 排程是重新定義的一部分。
- 視需要更新索引視需要來叫用。

##<a id="CreateDataSource"></a>步驟 1︰ 建立資料來源

發出 HTTP 文章要求 Azure 搜尋服務，包括下列要求的標頭中建立新的資料來源。

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version`資格。 有效的值包括`2015-02-28`或更新版本。 若要查看所有支援的搜尋 API 版本[中 Azure 搜尋的 API 版本](../search/search-api-versions.md)，請造訪。

邀請內文包含資料來源定義，應該包含下列欄位︰

- **名稱**︰ 選擇任何代表 DocumentDB 資料庫的名稱。

- **類型**︰ 使用`documentdb`。

- **認證**︰

    - **連接字串**︰ 必要。 指定 Azure DocumentDB 資料庫的連線資訊以下列格式︰`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **容器**︰

    - **名稱**︰ 必要。 指定 DocumentDB 集合能編製索引的識別碼。

    - **查詢**︰ 選用。 您可以指定簡任意 JSON 文件維成一般的結構描述的 Azure 搜尋可以編製索引的查詢。

- **dataChangeDetectionPolicy**︰ 選用。 請參閱[資料變更偵測原則](#DataChangeDetectionPolicy)的下方。

- **dataDeletionDetectionPolicy**︰ 選用。 請參閱[資料刪除偵測原則](#DataDeletionDetectionPolicy)下方。

請參閱下方[範例邀請內文](#CreateDataSourceExample)。

###<a id="DataChangeDetectionPolicy"></a>擷取已變更的文件

資料變更偵測原則的目的，是以有效率地識別變更的資料的項目。 目前，只支援的原則的是`High Water Mark`原則使用`_ts`上次修改時間戳記屬性提供 DocumentDB-指定，如下所示︰

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

您也需要新增`_ts`投影中和`WHERE`子句查詢。 例如︰

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>擷取已刪除的文件

從來源資料表刪除的列，則那些列應該刪除的搜尋索引。 資料刪除偵測原則的目的，是以有效率地識別資料已刪除的項目。 目前，只支援的原則的是`Soft Delete`原則 （刪除標示為 [旗標為某些排序），指定，如下所示︰

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] 您必須在 SELECT 子句中包含 softDeleteColumnName 屬性，如果您使用的自訂預測。

###<a id="CreateDataSourceExample"></a>邀請本文範例

下列範例會建立資料來源以自訂的查詢和原則提示︰

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>回應

如果已成功建立資料來源，您會收到 HTTP 201 建立的回應。

##<a id="CreateIndex"></a>步驟 2︰ 建立索引

如果您沒有，建立目標 Azure 搜尋索引。 從[Azure 入口網站使用者介面](../search/search-create-index-portal.md)，或是使用[建立索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx)，您可以執行此動作。

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


確定您的目標索引的結構描述與來源 JSON 文件的結構描述或您的自訂查詢預測的輸出相容。

>[AZURE.NOTE] 分割的集合，預設文件索引鍵是 DocumentDB 的`_rid`屬性，取得重新命名為`rid`Azure 搜尋中。 此外，DocumentDB 的`_rid`值包含 Azure 搜尋索引鍵; 無效的字元因此，`_rid`值的 Base64 編碼。

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>答︰ 圖對應之間 JSON 資料類型和 Azure 搜尋資料類型

| JSON 資料類型|   相容的目標索引欄位類型|
|---|---|
|Bool|Edm.Boolean Edm.String|
|看起來像整數的數字|Edm.Int32，Edm.Int64，Edm.String|
|數字看起來像浮動點|Edm.Double Edm.String|
|字串|Edm.String|
|陣列的基本項目類型例如"a"，"b"，"c" |Collection(Edm.String)|
|看起來就像日期的字串| Edm.DateTimeOffset Edm.String|
|GeoJSON 物件例如 {「 type 」: 「 點 「，」 座標 」: [長，lat]} | Edm.GeographyPoint |
|其他 JSON 物件|N/A|

###<a id="CreateIndexExample"></a>邀請本文範例

下列範例會建立索引識別碼和描述的欄位︰

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>回應

如果已成功建立索引，您會收到 HTTP 201 建立的回應。

##<a id="CreateIndexer"></a>步驟 3︰ 建立索引

您可以建立 Azure 搜尋服務中的新索引 HTTP 文章要求使用下列的標題。

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

邀請內文包含重新定義中，應包含下列欄位︰

- **名稱**︰ 必要。 索引子的名稱。

- **dataSourceName**︰ 必要。 現有的資料來源的名稱。

- **targetIndexName**︰ 必要。 現有索引的名稱。

- **排程**︰ 選用。 請參閱[排程編製索引](#IndexingSchedule)的下方。

###<a id="IndexingSchedule"></a>執行索引子排程

索引子也可以指定的排程。 如果排程，索引器會依排程定期執行。 排程具有下列屬性︰

- **間隔**︰ 必要。 執行指定間隔或索引器期間的持續時間值。 最小的可容許的間隔為 5 分鐘;最長為一天。 您必須格式化為 XSD 「 dayTimeDuration 」 的值 （限制[ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的子集）。 這個模式︰ `P(nD)(T(nH)(nM))`。 範例︰`PT15M`每 15 分鐘，`PT2H`每 2 小時。

- **開始時間**︰ 必要。 指定索引子應該啟動時 UTC 日期時間執行。

###<a id="CreateIndexerExample"></a>邀請本文範例

下列範例會建立索引子將資料從所參考的集合複製`myDocDbDataSource`資料來源`mySearchIndex`索引的排程，在 2015 年 1 月 1 日 UTC 開始，並執行每小時。

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>回應

如果已成功建立索引，您會收到 HTTP 201 建立的回應。

##<a id="RunIndexer"></a>步驟 4︰ 執行索引

除了執行定期排程，索引子可以也叫用視發出下列 HTTP 文章要求︰

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>回應

如果重新叫成功，您會收到 HTTP 202 接受的回應。

##<a name="GetIndexerStatus"></a>步驟 5︰ 取得索引狀態

您可以發行的 HTTP GET 要求擷取索引子的目前狀態和執行歷程記錄︰

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>回應

您會看到 HTTP 200 OK 回應傳回以及回應主體包含整體重新健康狀態，最後一個重新叫用，以及最近重新叫用的歷程記錄的相關資訊 （如果有的話）。

回應看起來如下︰

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

執行歷程記錄包含最 50 最近完成執行，其會依時間先後倒著 （以便在回應中的最新的執行先後）。

##<a name="NextSteps"></a>後續步驟

恭喜您 ！ 您只已經學會如何使用 Azure 搜尋索引器用 DocumentDB 整合 Azure DocumentDB。

 - 若要瞭解如何更多關於 Azure DocumentDB，請參閱[DocumentDB 服務] 頁面](https://azure.microsoft.com/services/documentdb/)。

 - 若要瞭解如何關於 Azure 搜尋的詳細資訊，請參閱[搜尋服務] 頁面](https://azure.microsoft.com/services/search/)。
