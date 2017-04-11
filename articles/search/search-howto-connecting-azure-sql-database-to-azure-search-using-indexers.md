<properties 
    pageTitle="Azure SQL 資料庫連線到 Azure 搜尋使用索引子 |Microsoft Azure |索引子" 
    description="瞭解如何從 Azure SQL 資料庫，拉取資料至使用索引子 Azure 搜尋索引。" 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/27/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Azure SQL 資料庫連線到使用索引子 Azure 搜尋

Azure 搜尋服務是裝載的雲端搜尋服務，方便您將提供好的搜尋體驗。 您可以搜尋之前，您需要填入資料 Azure 搜尋索引。 如果資料放在 Azure SQL 資料庫，新**Azure SQL 資料庫 Azure 搜尋索引**（或簡稱為**Azure SQL 索引**） 可以自動化編製索引的程序。 這表示您有撰寫程式碼較少和較少的基礎結構，以關注的資訊。

本文涵蓋的使用索引子，但其也說明的功能，僅適用於 Azure SQL 資料庫 （例如，整合式追蹤修訂）。 Azure 搜尋也支援其他資料來源，例如 Azure DocumentDB blob 儲存體，與資料表儲存體。 如果您想要查看支援的其他資料來源，提供您的意見反應[Azure 搜尋意見反應](https://feedback.azure.com/forums/263029-azure-search/)。

## <a name="indexers-and-data-sources"></a>索引子與資料來源

您可以設定，並設定 Azure SQL 索引子使用︰ 

- 匯入[Azure 入口網站](https://portal.azure.com)中的 [資料] 精靈
- Azure 搜尋[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)
- Azure 搜尋[REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173)

本文中，我們將使用 REST API，顯示您如何建立及管理**索引子**與**資料來源**。 

**資料來源**指定要編製索引，認證存取資料及原則可識別有效率地變更資料 （新增、 修改或刪除資料列） 中的所需的資料。 這樣就可以使用依多個索引子被定義為獨立的資源。

**重新**為資源連接資料來源與目標搜尋索引。 使用索引以下列方式︰
 
- 執行一次複製要填入索引的資料。
- 更新索引的排程的資料來源中的變更。
- 執行視需要更新索引。 

## <a name="when-to-use-azure-sql-indexer"></a>何時使用 Azure SQL 索引

根據下列因素與您的資料 Azure SQL 索引子使用可能，或可能不適用。 如果您的資料符合下列需求，您可以使用 Azure SQL 索引︰ 

- 單一資料表或檢視的所有資料都來源
    - 如果資料散佈在多個表格，您可以建立檢視，並使用索引器中檢視。 不過，如果您使用的檢視，您無法使用 SQL Server 整合變更偵測。 如需詳細資訊，請參閱[本節](#CaptureChangedRows)。 
- 支援使用資料來源中的資料類型的索引。 支援大部分但非全部 SQL 類型。 如需詳細資訊，請參閱[對應 Azure 搜尋中的資料類型](http://go.microsoft.com/fwlink/p/?LinkID=528105)。 
- 您不需要靠近即時更新索引時的資料列的變更。 
    - 索引器可以重新編製索引表格最多每 5 分鐘。 如果資料經常變更且變更需要秒或單一分鐘內，在索引中反映，建議您使用[Azure 搜尋索引 API](https://msdn.microsoft.com/library/azure/dn798930.aspx)直接。 
- 如果您有大型資料集，而且想要執行索引器處理的排程，您的結構描述可讓我們有效率地識別變更 （與刪除]，如果有的話） 列。 如需詳細資訊，請參閱 「 擷取變更並刪除列] 下方。 
- 資料列中的索引欄位的大小未超過大小上限為 16 MB 的要求編製索引作業 Azure 搜尋。 

## <a name="create-and-use-an-azure-sql-indexer"></a>建立及使用 Azure SQL 索引

首先，建立資料來源︰ 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


您可以從[Azure 傳統入口網站](https://portal.azure.com)，取得連線字串使用`ADO.NET connection string`選項。

如果您沒有，然後建立目標 Azure 搜尋索引。 您可以建立使用[入口網站使用者介面](https://portal.azure.com)或[建立索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx)索引。 請確定您的目標索引的結構描述相容的來源資料表的結構描述，請參閱[SQL 和 Azure 搜尋資料類型之間的對應](#TypeMapping)。

最後，建立索引以名稱和參考資料來源及目標索引︰

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

在這種方式中建立索引子沒有排程。 它會自動執行後建立的時機。 您可以隨時使用**執行重新**要求執行︰

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

您可以自訂的索引的行為，例如批次大小和重新執行失敗之前，可以略過多少文件。 如需詳細資訊，請參閱[建立索引子 API](https://msdn.microsoft.com/library/azure/dn946899.aspx)。
 
若要允許 Azure 服務連線到您的資料庫。 如需如何進行的指示，請參閱[從 Azure 連線](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。

若要監視索引狀態並執行歷程記錄 （索引項目數目、 失敗等），會使用**索引狀態**要求︰ 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

回應看起來如下︰ 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

執行歷程記錄包含最 50 的最近已完成的執行，會依時間先後倒著 （以便在回應中的最新的執行先後）。 回應的相關資訊，可以找到中[取得索引狀態](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>排程執行索引子

您也可以排列索引器處理的排程定期執行。 若要這麼做，請新增在建立或更新索引器中的 [**排程**] 屬性。 下列範例顯示的放入要求更新索引︰

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

需要**間隔**參數。 參照之間的兩個連續重新執行開始的時間間隔。 最小的可容許的間隔為 5 分鐘;最長為一天。 您必須格式化為 XSD 「 dayTimeDuration 」 的值 （限制[ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的子集）。 這個模式︰ `P(nD)(T(nH)(nM))`。 範例︰`PT15M`每 15 分鐘，`PT2H`每 2 小時。

選用的**開始時間**會指出開始排程的執行應該時。 如果省略，則會使用目前 UTC 時間。 這次可以是在過去 – 如果為重新執行持續自開始時間，排程大小寫第一次執行。  

執行一次只能執行一個索引子。 如果重新執行的排定執行時，執行延後直到下一步排定的時間。

我們來看，將此更具體的範例。 假設我們設定下列每小時排程︰ 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

以下是會發生什麼情況︰ 

1. 第一次重新執行啟動或 2015 年 3 月 1 日 12:00 a.m.周圍 UTC。
1. 假設此執行是 20 分鐘 （或任何時間小於 1 小時）。
1. 第二次執行啟動或 2015 年 3 月 1 日 1:00 a.m.周圍 
1. 現在，假設此執行發生的多個小時 – 例如 70 分鐘 –，讓它完成大約 2︰ 上午 10
1. 現在若要開始的第三個執行時間 2:00 a.m.，則。 不過，因為從 1 上午第二次執行 仍在執行中，第三次執行會略過。 第三次執行啟動上午 3

您可以新增、 變更或刪除現有的索引器排程使用**放置索引**的要求。 

<a name="CaptureChangedRows">/a >
## <a name="capturing-new-changed-and-deleted-rows"></a>擷取新的變更，並刪除列

如果您的表格有許多資料列，您應該使用資料變更偵測原則。 變更偵測啟用有效擷取只新增或變更資料列，而不必重新編製索引整個表格。

### <a name="sql-integrated-change-tracking-policy"></a>SQL 整合原則的追蹤修訂

如果您的 SQL 資料庫支援[追蹤修訂](https://msdn.microsoft.com/library/bb933875.aspx)，我們建議使用**SQL 整合式變更追蹤原則**。 這是最有效的原則。 此外，其允許 Azure 搜尋來識別您不必明確的 「 柔刪除 「 欄新增至表格的已刪除的資料列。

整合式追蹤的修訂支援下列 SQL Server 資料庫版本開始著手︰
 
- SQL Server 2008 R2 及更新版本，如果您使用的 SQL Server Azure Vm 上。 
- Azure SQL 資料庫 V12，如果您使用的 Azure SQL 資料庫。

何時使用整合式的 SQL 變更追蹤原則，不指定不同的資料刪除偵測原則-此原則已識別的內建支援刪除列。

此原則可以只能搭配資料表。它不能搭配檢視。 您需要讓您使用的您才能使用此原則表格的追蹤修訂。 如需相關指示，請參閱[啟用和停用 [追蹤修訂](https://msdn.microsoft.com/library/bb964713.aspx)。 

若要使用此原則，建立或更新您的資料來源，像這樣︰
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

<a name="HighWaterMarkPolicy"></a>
### <a name="high-water-mark-change-detection-policy"></a>高臨界標示變更偵測原則

建議的 SQL 整合式追蹤修訂的原則，而只使用表格，無法檢視用它。 如果您使用檢視，請考慮使用高臨界標示原則。 如果您的資料表或檢視包含資料行符合下列條件，則可以使用此原則︰

- 所有插入都指定資料行的值。 
- 所有的更新項目也會變更資料行的值。 
- 此欄的值會增加每項變更。
- 使用下列查詢，和 ORDER BY 子句可以有效率地執行︰ `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`。

例如，索引的**rowversion**欄是適合高臨界標示資料行。 若要使用此原則，建立或更新您的資料來源，像這樣︰ 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

> [AZURE.WARNING] 如果來源資料表沒有高臨界標示欄的索引，使用 SQL 索引子查詢可能逾時。 特別`ORDER BY [High Water Mark Column]`子句需要有效率地執行當資料表包含多個資料列的索引。 

如果發生逾時錯誤，您可以使用`queryTimeout`重新設定的設定值高於預設 5 分鐘逾時設定的查詢逾時。 例如，若要設定 10 分鐘的時間會出現逾時，建立或更新索引器以下列設定︰ 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

您也可以停用`ORDER BY [High Water Mark Column]`子句。 不過，建議您不要因為如果重新執行中斷的錯誤，索引器已重新處理所有資料列在稍後-即使索引器已經中斷的時間處理幾乎所有資料列。 若要停用`ORDER BY`子句，使用`disableOrderByHighWaterMarkColumn`重新定義中設定︰  

    {
     ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>柔邊刪除欄刪除偵測原則

從來源資料表刪除的列，您可能要刪除的搜尋索引中的資料列。 如果您使用整合式的 SQL 變更追蹤原則，這是付的適合您。 不過，高臨界標示變更追蹤原則無法協助您已刪除的列。 如何？ 

如果資料列實際從表格中移除，Azure 搜尋者無法推斷不存在的記錄的目前狀態。  不過，您可以使用 「 柔刪除 「 技巧，以邏輯方式而不移除其從表格刪除列。 新增欄至表格] 或 [檢視和 [標示列上，為刪除使用該資料行。

使用時柔刪除技巧，您可以指定軟刪除原則，如下所示建立或更新的資料來源時︰ 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

**SoftDeleteMarkerValue**必須是字串 – 使用您的實際值的字串表示。 例如，如果您有整數資料行的值 1 標示為已刪除的列，使用`"1"`。 如果您有元資料行的布林值 true 值標示為已刪除的列，請使用`"True"`。 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 資料類型與 Azure 搜尋資料類型之間的對應

|SQL 資料類型 | 允許目標索引的欄位類型 |備忘稿 
|------|-----|----|
|位元|Edm.Boolean Edm.String| |
|int、 小 tinyint |Edm.Int32，Edm.Int64，Edm.String| |
| bigint | Edm.Int64 Edm.String | |
| 實數，浮動時間 |Edm.Double Edm.String | |
| smallmoney，金錢小數位數的數字 | Edm.String| Azure 搜尋不支援將十進位類型轉換成 Edm.Double，因為這會遺失精確度 |
| char、 nchar、 varchar、 nvarchar | Edm.String<br/>Collection(Edm.String)|SQL 字串可用來填入 Collection(Edm.String) 欄位，如果字串代表 JSON 陣列的字串︰`["red", "white", "blue"]` | 
|29.998、 datetime、 datetime2、 日期、 datetimeoffset |Edm.DateTimeOffset Edm.String| |
|uniqueidentifer | Edm.String | |
|地理位置 | Edm.GeographyPoint | 僅限類型 （這是預設值） srid 都會 4326 點的地理位置執行個體支援 | | 
|rowversion| N/A |資料列版本欄不能儲存在搜尋索引，但可以用於追蹤修訂 | |
| 時間、 時段、 二進位、 varbinary、 圖像、 xml、 幾何、 CLR 類型 | N/A |不支援 |

## <a name="configuration-settings"></a>設定的設定

SQL 索引公開了數個設定的設定︰ 

|設定 |資料類型 | 用途 | 預設值 |
|--------|----------|---------|---------------|
| queryTimeout | 字串 | 設定為 SQL 查詢執行逾時 | 5 分鐘 (「 00: 05:00 」) |
| disableOrderByHighWaterMarkColumn | bool | 使高臨界標示原則用於省略 ORDER BY 子句的 SQL 查詢。 請參閱[高臨界標示原則](#HighWaterMarkPolicy) | false | 

會使用這些設定中`parameters.configuration`重新定義中的物件。 例如，若要設定 10 分鐘的查詢逾時，建立或更新索引器以下列設定︰ 

    {
      ... other indexer definition properties
     "parameters" : { 
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>常見問題集

**Q:**可以使用 Azure SQL 索引 SQL Azure 中 IaaS Vm 上執行的資料庫時嗎？

A: [是]。 不過，您必須允許您的搜尋服務連線到您的資料庫。 如需詳細資訊，請參閱[設定與 SQL Server Azure VM 上的 Azure 搜尋索引子的連線](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。


**Q:**可以使用 Azure SQL 重新執行內部部署的 SQL 資料庫時嗎？ 

答︰ 我們不建議或支援，執行此動作會要求您開啟網際網路流量的資料庫。 

**Q:**可以使用重新 Azure SQL 資料庫時，SQL Server Azure 上執行中 IaaS 以外嗎？ 

答︰ 我們不支援此案例中，，因為我們尚未測試與 SQL Server 以外的任何資料庫索引。  

**Q:**可以建立多個索引子排程上執行嗎？ 

A: [是]。 不過，只有一個重新執行一個節點上一次。 如果您需要多個索引子同時執行時，請考慮設定您的搜尋服務到多個搜尋單位縮放比例。 

**Q:**執行索引子影響我的查詢工作負載？ 

A: [是]。 重新執行在其中一個您搜尋的服務中的節點並編製索引作業，並提供服務的查詢的流量與其他 API 邀請共用該節點的資源。 如果您執行需要大量編製索引及查詢的工作量，會遇到率 503 錯誤或增加回應時間，請考慮設定您的搜尋服務縮放比例。
