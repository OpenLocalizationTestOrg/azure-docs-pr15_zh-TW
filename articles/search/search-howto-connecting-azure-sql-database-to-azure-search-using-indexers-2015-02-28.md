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
    ms.date="05/26/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Azure SQL 資料庫連線到使用索引子 Azure 搜尋

Azure 搜尋服務是裝載的雲端搜尋服務，方便您將提供好的搜尋體驗。 您可以搜尋之前，您需要填入資料 Azure 搜尋索引。 如果資料放在 Azure SQL 資料庫，Azure 搜尋中的新**Azure SQL 資料庫 Azure 搜尋索引**（或**Azure SQL 索引**的簡短） 可以自動化編製索引的程序。 這表示您有撰寫程式碼較少和較少的基礎結構，以關注的資訊。

目前，索引子僅適用於用 Azure SQL 資料庫]，[SQL Server Azure Vm 和[Azure DocumentDB](../documentdb/documentdb-search-indexer.md)上。 本文中，我們將焦點放在索引子搭配 Azure SQL 資料庫。 如果您想要查看支援的其他資料來源，請提供您的意見反應[Azure 搜尋意見反應](https://feedback.azure.com/forums/263029-azure-search/)。

本文涵蓋的使用索引子，但我們會也向下切入功能和，僅適用於 SQL 資料庫 （例如，整合式追蹤修訂） 的行為。

## <a name="indexers-and-data-sources"></a>索引子與資料來源

若要設定，並設定 Azure SQL 索引，您可以呼叫[Azure 搜尋 REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173)來建立和管理**索引子**及**資料來源**。 

您也可以使用[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)或匯入資料] 精靈中的[索引子類別](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)中[Azure 入口網站](https://portal.azure.com)來建立及排程索引子。

**資料來源**指定要編製索引，認證存取資料及原則，讓 Azure 搜尋有效率地識別資料 （新增、 修改或刪除的資料列） 中的變更所需的資料。 這樣就可以使用依多個索引子被定義為獨立的資源。

**重新**為資源連接資料來源與目標搜尋索引。 使用索引以下列方式︰
 
- 執行一次複製要填入索引的資料。
- 更新索引的排程的資料來源中的變更。
- 執行視需要更新索引。 

## <a name="when-to-use-azure-sql-indexer"></a>何時使用 Azure SQL 索引

根據下列因素與您的資料 Azure SQL 索引子使用可能，或可能不適用。 如果您的資料符合下列需求，您可以使用 Azure SQL 索引︰ 

- 單一資料表或檢視的所有資料都來源
    - 如果資料散佈在多個表格，您可以建立檢視，並使用索引器中檢視。 不過，請注意，如果您使用的檢視，您將無法使用 SQL Server 整合變更偵測。 請參閱本節中的更多詳細資料。 
- 支援使用資料來源中的資料類型的索引。 大部分的 SQL 支援類型。 如需詳細資訊，請參閱[對應 Azure 搜尋中的資料類型](http://go.microsoft.com/fwlink/p/?LinkID=528105)。 
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

如果您沒有，然後建立目標 Azure 搜尋索引。 從[入口網站使用者介面](https://portal.azure.com)，或使用[建立索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx)，您可以執行此動作。  請確定您的目標索引的結構描述相容的來源資料表的結構描述，請參閱[SQL 和 Azure 搜尋資料類型之間的對應](#TypeMapping)，如需詳細資訊。

最後，建立索引以名稱和參考資料來源及目標索引︰

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

在這種方式中建立索引子沒有排程。 它會自動執行一次為其建立。 您可以隨時使用**執行重新**要求執行︰

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

您可以自訂的索引的行為，例如批次大小和之前會無法重新執行，可以略過多少文件。 如需詳細資訊，請參閱[建立索引子 API](https://msdn.microsoft.com/library/azure/dn946899.aspx)。
 
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

您也可以排列索引器處理的排程定期執行。 若要執行此動作，只要新增在建立或更新索引器中的 [**排程**] 屬性。 下列範例顯示的放入要求更新索引︰

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

需要**間隔**參數。 參照之間的兩個連續重新執行開始的時間間隔。 最小的可容許的間隔為 5 分鐘;最長為一天。 您必須格式化為 XSD 「 dayTimeDuration 」 的值 （限制[ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的子集）。 這個模式︰ `P(nD)(T(nH)(nM))`。 範例︰`PT15M`每 15 分鐘，`PT2H`每 2 小時。

選用的**開始時間**表示時應該開始排程的執行。如果省略，則會使用目前 UTC 時間。 這次可以是在過去 – 的大小寫第一次執行排定如同重新執行持續自開始時間。  

只能執行一個指定索引子可以執行一次。 如果索引子已在執行下一個應該要開始時，會略過執行，然後執行下一個間隔，假設變異數沒有其他索引作業的履歷表。

我們來看，將此更具體的範例。 假設我們設定下列每小時排程︰ 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

以下是會發生什麼情況︰ 

1. 第一次重新執行啟動或 2015 年 3 月 1 日 12:00 a.m.周圍 UTC。
1. 假設此執行是 20 分鐘 （或任何時間小於 1 小時）。
1. 第二次執行啟動或 2015 年 3 月 1 日 1:00 a.m.周圍 
1. 現在，假設此執行需要多個小時 （這需要大量的文件的實際完成，但很實用的圖例 –），例如 70 分鐘 –，讓它完成大約 2︰ 上午 10
1. 現在若要開始的第三個執行時間 2:00 a.m.，則。 不過，因為從 1 上午第二次執行 仍在執行中，第三次執行會略過。 第三次執行啟動上午 3

您可以新增、 變更或刪除現有的索引器排程使用**放置索引**的要求。 

## <a name="capturing-new-changed-and-deleted-rows"></a>擷取新增、 變更及刪除列

如果您使用的排程，您的資料表包含非一般數字的資料列，您應該使用資料變更偵測原則，以便重新可以有效率地擷取只新增或變更資料列，而不必重新編製索引整個表格。

### <a name="sql-integrated-change-tracking-policy"></a>SQL 整合原則的追蹤修訂

如果您的 SQL 資料庫支援[追蹤修訂](https://msdn.microsoft.com/library/bb933875.aspx)，我們建議使用**SQL 整合式變更追蹤原則**。 此原則可以讓最有效追蹤修訂，然後，您也可以 Azure 搜尋來識別您不必明確的 「 柔刪除 「 欄新增至表格的已刪除的資料列。

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

### <a name="high-water-mark-change-detection-policy"></a>高臨界標示變更偵測原則

雖然 SQL 整合式追蹤修訂的原則，建議您將無法使用它，如果資料是在檢視中，或如果您使用的舊版 Azure SQL 資料庫。 在這種情況下，請考慮使用高臨界標示原則。 如果資料表包含符合下列條件的資料行，可以使用此原則︰

- 所有插入都指定資料行的值。 
- 所有的更新項目也會變更資料行的值。 
- 此欄的值會增加每項變更。
- 查詢使用的`WHERE`子句類似`WHERE [High Water Mark Column] > [Current High Water Mark Value]`可以有效率地執行。

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

### <a name="soft-delete-column-deletion-detection-policy"></a>柔邊刪除欄刪除偵測原則

從來源資料表刪除的列，您可能要刪除的搜尋索引中的資料列。 如果您使用整合式的 SQL 變更追蹤原則，這是付的適合您。 不過，高臨界標示變更追蹤原則無法協助您已刪除的列。 如何？ 

實際列移除資料表中，如果您已用盡您好運-沒有推斷不存在的記錄的目前狀態的方法。  不過，您可以使用 「 柔刪除 「 技巧標示為邏輯已刪除的列，而不移除其資料表中新增資料行，並標示為已刪除該資料行中使用的標記值的資料列。

使用時柔刪除技巧，您可以指定軟刪除原則，如下所示建立或更新的資料來源時︰ 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

請注意， **softDeleteMarkerValue**必須字串 – 使用您的實際值的字串表示。 例如，如果您有整數資料行的值 1 標示為已刪除的列，使用`"1"`;如果您有元資料行的布林值 true 值標示為已刪除的列，請使用`"True"`。 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 資料類型與 Azure 搜尋資料類型之間的對應

|SQL 資料類型 | 允許目標索引的欄位類型 |備忘稿 
|------|-----|----|
|位元|Edm.Boolean Edm.String| |
|int、 小 tinyint |Edm.Int32，Edm.Int64，Edm.String| |
| bigint | Edm.Int64 Edm.String | |
| 實數，浮動時間 |Edm.Double Edm.String | |
| smallmoney，金錢小數位數的數字 | Edm.String| Azure 搜尋不支援將十進位類型轉換成 Edm.Double，因為這會遺失精確度 |
| char、 nchar、 varchar、 nvarchar | Edm.String<br/>Collection(Edm.String)|字串資料行轉換 Collection(Edm.String) 需要使用預覽 API 版本 2015年 02-28-預覽。 請參閱[這篇文章](search-api-indexers-2015-02-28-preview.md#CreateIndexer)︰ 如需詳細資訊| 
|29.998、 datetime、 datetime2、 日期、 datetimeoffset |Edm.DateTimeOffset Edm.String| |
|uniqueidentifer | Edm.String | |
|地理位置 | Edm.GeographyPoint | 僅限類型 （這是預設值） srid 都會 4326 點的地理位置執行個體支援 | | 
|rowversion| N/A |資料列版本欄不能儲存在搜尋索引，但可以用於追蹤修訂 | |
| 時間、 時段、 二進位、 varbinary、 圖像、 xml、 幾何、 CLR 類型 | N/A |不支援 |


## <a name="frequently-asked-questions"></a>常見問題集

**Q:**可以使用 Azure SQL 索引 SQL Azure 中 IaaS Vm 上執行的資料庫時嗎？

A: [是]。 不過，您必須允許您的搜尋服務連線到您的資料庫，執行下列兩個項目。 請參閱如需詳細資訊的文件[設定與 SQL Server Azure VM 上的 Azure 搜尋索引子的連線](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。

1. 您可能需要使用信任的憑證設定您的資料庫，使搜尋服務可以開啟 SSL 連線至資料庫。
2. 設定 [允許存取您的搜尋服務的 IP 位址的防火牆。

**Q:**可以使用 Azure SQL 重新執行內部部署的 SQL 資料庫時嗎？ 

答︰ 我們不建議或支援，執行此動作會要求您開啟網際網路流量的資料庫。 

**Q:**可以使用重新 Azure SQL 資料庫時，SQL Server Azure 上執行中 IaaS 以外嗎？ 

答︰ 我們不支援此案例中，，因為我們尚未測試與 SQL Server 以外的任何資料庫索引。  

**Q:**可以建立多個索引子排程上執行嗎？ 

A: [是]。 不過，只有一個重新執行一個節點上一次。 如果您需要多個索引子同時執行時，請考慮設定您的搜尋服務到多個搜尋單位縮放比例。 

**Q:**執行索引子影響我的查詢工作負載？ 

A: [是]。 重新執行在其中一個您搜尋的服務中的節點並編製索引作業，並提供服務的查詢的流量與其他 API 邀請共用該節點的資源。 如果您執行需要大量編製索引及查詢的工作量，會遇到率 503 錯誤或增加回應時間，請考慮設定您的搜尋服務縮放比例。
