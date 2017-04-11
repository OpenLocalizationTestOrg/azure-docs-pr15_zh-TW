<properties 
pageTitle="索引作業 (Azure 搜尋服務 REST API: 2015年 02-28 預覽) |Azure 搜尋預覽 API" 
description="索引作業 (Azure 搜尋服務 REST API: 2015年 02-28 預覽)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>索引作業 (Azure 搜尋服務 REST API: 2015年 02-28 預覽)#

> [AZURE.NOTE] 本文將說明在[2015 02-28 預覽 REST API](search-api-2015-02-28-preview.md)索引子。 此 API 版本加入 preview 版本與文件擷取 Azure Blob 儲存體索引和 Azure 資料表儲存體索引，以及其他改良項目。 API 也支援推出 (GA) 索引子，包括索引子 Azure SQL 資料庫、 SQL Server Azure Vm 和 Azure DocumentDB 上。

## <a name="overview"></a>概觀 ##

Azure 搜尋可以整合直接與一些常見的資料來源，移除需要撰寫程式碼資料的索引。 若要設定此設定，您可以呼叫 Azure 搜尋 API 建立及管理**索引子**與**資料來源**。 

**重新**為資源連接資料來源與目標搜尋索引。 使用索引以下列方式︰ 

- 執行一次複製要填入索引的資料。
- 同步處理變更排程的資料來源中的索引。 排程是重新定義的一部分。
- 視需要更新索引，視需要來叫用。 

當您想要索引的一般更新時，**重新**相當實用。 您可以設定內嵌排程為重新定義，或使用[執行重新](#RunIndexer)執行。 

**資料來源**指定什麼資料必須能編製索引，認證以存取資料和原則，以啟用 Azure 搜尋有效率地識別資料 （例如修改或刪除的資料庫資料表中的資料列） 中的變更。 這樣就可以使用依多個索引子被定義為獨立的資源。

目前支援下列資料來源︰

- **Azure SQL 資料庫**和**SQL Server Azure Vm 上**。 目標的逐步解說，請參閱[本文](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)。 
- **Azure DocumentDB**。 目標的逐步解說，請參閱[本文](../documentdb/documentdb-search-indexer.md)。 
- **Azure Blob 儲存體**，包括下列文件格式︰ PDF、 Microsoft Office （DOCX/文件、 XSLX/XLS、 PPTX/PPT、 訊息）、 HTML、 XML、 郵遞區號和純文字檔案 （包括 JSON）。 目標的逐步解說，請參閱[本文](search-howto-indexing-azure-blob-storage.md)。
- **Azure 資料表儲存體**。 目標的逐步解說，請參閱[本文](search-howto-indexing-azure-tables.md)。
     
我們正在考慮在未來新增其他資料來源的支援。 若要協助我們這些決策排列其優先順序，請提供您的意見反應[Azure 搜尋意見反應](http://feedback.azure.com/forums/263029-azure-search)。

重新與資料來源資源相關的最大限制，請參閱[服務限制](search-limits-quotas-capacity.md)。

## <a name="typical-usage-flow"></a>一般的用法流程 ##

您可以建立及管理索引子與資料來源透過簡單 HTTP 要求 （文章，取得，將，刪除），針對給定`data source`或`indexer`資源。

設定自動編製索引作業，通常是四個步驟的程序︰

1. 找出含有必須能編製索引的資料的資料來源。 請記住 Azure 搜尋可能不支援所有的資料來源中的資料類型。 清單，請參閱[支援的資料類型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。

2. 建立 Azure 搜尋索引的結構描述是與您的資料來源。
  
3. 建立 Azure 搜尋資料來源[建立資料來源](#CreateDataSource)中所述。
  
4. 描述如何在[建立重新](#CreateIndexer)建立 Azure 搜尋索引。

您應該計劃建立一個索引的每個目標索引與資料來源的組合。 您有多個索引子寫入的相同的索引，您可以重複使用相同的資料來源的多個索引子。 不過，索引子可以只使用一次一個資料來源，而且只能寫入單一索引。 

建立索引之後，您可以擷取其使用[取得索引狀態](#GetIndexerStatus)作業的執行狀態。 您也可以執行索引子 （而不是或除了的排程定期執行） 隨時使用[執行索引](#RunIndexer)作業。

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>建立資料來源 ##

Azure 搜尋資料來源使用索引子，提供目標索引的臨機操作或排程資料重新整理的連線資訊。 您可以建立新的資料來源中使用 HTTP 文章要求 Azure 搜尋服務。
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

或者，您可以使用放入，並指定 URI 上的 [資料來源的名稱。 如果資料來源不存在，它將會建立。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**附註**︰ 會因價格層允許的最大的數字的資料來源。 免費的服務可讓進位到 3 的資料來源。 標準服務可讓 50 資料來源。 如需詳細資訊，請參閱[服務限制](search-limits-quotas-capacity.md)。

**要求**

HTTPS 時需要所有服務要求。 您可使用方法文章或放入建構**建立資料來源**要求。 使用時的文章，您必須提供以及資料來源定義邀請內文中的資料來源名稱。 使用放入，則名稱為 URL 的一部分。 如果資料來源不存在，則會建立。 如果已經存在，其會更新為新的定義。 

資料來源名稱必須小寫，開頭字母或數字、 有沒有斜線或點，而小於 128 個字元。 啟動之後的資料來源名稱以字母或數字，其餘的名稱可以包含任何字母、 數字和虛線，只要不連續連字號。 如需詳細資訊，請參閱[命名規則](https://msdn.microsoft.com/library/azure/dn857353.aspx)。

`api-version`資格。 目前使用的是`2015-02-28`。

**要求標頭**

下列清單說明必要與選用要求的標頭。 

- `Content-Type`︰ 必要。 將此值設`application/json`
- `api-key`︰ 必要。 `api-key`會用於驗證您的搜尋服務要求。 是字串的值，為您的服務的唯一。 **建立資料來源**要求必須包含`api-key`頁首設為您的管理員金鑰 （而不是查詢鍵）。 
 
您也必須服務名稱，以建構要求 URL。 您可以取得服務名稱和`api-key`從您的服務儀表板中[Azure 入口網站](https://portal.azure.com/)。 請參閱[建立搜尋服務入口網站中](search-create-service-portal.md)的 [頁面導覽協助。

<a name="CreateDataSourceRequestSyntax"></a>
**邀請本文語法**

邀請內文包含資料來源定義，包括類型的資料來源的認證以讀取資料，以及選擇性的資料變更偵測和資料以有效率地識別所使用的刪除偵測原則變更或刪除資料來源時使用定期排程的索引中的資料。 

組織結構的要求裝載的語法如下所示。 進一步本主題中所提供的範例要求。

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

要求包含下列屬性︰ 

- `name`︰ 必要。 資料來源的名稱。 資料來源名稱必須只包含大小寫字母、 數字或連字號、 無法開始或結束的虛線及超過 128 個字元。
- `description`︰ 選擇性的描述。 
- `type`︰ 必要。 必須是其中一個支援的資料來源類型︰
    - `azuresql`-Azure SQL 資料庫或 SQL Server Azure Vm 上
    - `documentdb`-Azure DocumentDB
    - `azureblob`-Azure Blob 儲存體
    - `azuretable`-Azure 資料表儲存體
- `credentials`:
    - 必要`connectionString`屬性會指定資料來源的連線字串。 資料來源類型而定的連接字串的格式︰ 
        - Azure SQL，這是主要的 SQL Server 連線字串。 如果您用來擷取連接字串 Azure 入口網站，使用`ADO.NET connection string`選項。
        - 為 DocumentDB，連接字串必須是以下列格式︰ `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`。 所有的值是必要。 您可以找到這些[Azure 入口網站](https://portal.azure.com/)中。  
        - 如需 Azure Blob 與資料表儲存體，這是儲存帳戶的連線字串。 格式描述[以下](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/)。 需要 HTTPS 端點通訊協定。  
- `container`必要︰ 指定要編製索引使用的資料`name`和`query`屬性︰ 
    - `name`需要︰
        - Azure SQL︰ 指定的資料表或檢視。 您可以使用結構描述完整的名稱，例如`[dbo].[mytable]`。
        - DocumentDB︰ 指定集合。 
        - Azure Blob 儲存體中︰ 指定存放容器。
        - Azure 資料表儲存體中︰ 指定資料表的名稱。 
    - `query`可省略︰
        - DocumentDB︰ 可讓您指定簡任意 JSON 文件版面配置維成一般的結構描述的 Azure 搜尋可以編製索引的查詢。  
        - Azure Blob 儲存體中︰ 可讓您指定的 blob 容器內的一個虛擬資料夾。 例如，對於 blob 路徑`mycontainer/documents/blob.pdf`，`documents`可做為虛擬資料夾。
        - Azure 資料表儲存體中︰ 可讓您指定要匯入的資料列設定篩選的查詢。
        - Azure SQL︰ 不支援查詢。 如果您需要這項功能，請投票支持[此建議](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- 選擇性`dataChangeDetectionPolicy`和`dataDeletionDetectionPolicy`如下所述的屬性。

<a name="DataChangeDetectionPolicies"></a>
**資料變更偵測原則**

資料變更偵測原則的目的，是以有效率地識別變更的資料的項目。 支援的原則會根據資料來源類型而定。 以下各節說明每個原則。 

***標準變更偵測原則*** 

當您的資料來源包含一欄或符合下列條件的屬性，請使用此原則︰
 
- 所有插入都指定資料行的值。 
- 所有的更新項目也會變更資料行的值。 
- 此欄的值會增加每項變更。
- 使用篩選子句類似以下的查詢`WHERE [High Water Mark Column] > [Current High Water Mark Value]`可以有效率地執行。

例如，使用 Azure SQL 資料來源索引時`rowversion`資料行是用於與高臨界標示原則適合。 

此原則可以指定，如下所示︰

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] 在使用 DocumentDB 資料來源時，您必須使用`_ts`DocumentDB 所提供的屬性。 

> [AZURE.NOTE] 自動使用 Azure 搜尋、 Azure Blob 資料來源時使用高浮水印變更偵測原則依據 blob 的上次修改時間戳記。您不需要自行指定此原則。   

***SQL 整合變更偵測原則***

如果您的 SQL 資料庫支援[追蹤修訂](https://msdn.microsoft.com/library/bb933875.aspx)，我們建議使用 SQL 整合式變更追蹤原則。 此原則可讓最有效追蹤修訂，並可讓您不必結構描述中有明確的 「 柔刪除 「 資料行識別刪除的資料列的 Azure 搜尋。

整合式追蹤的修訂支援下列 SQL Server 資料庫版本開始著手︰ 
- SQL Server 2008 R2，如果您使用的 SQL Server Azure Vm 上。
- Azure SQL 資料庫 V12，如果您使用的 Azure SQL 資料庫。  

何時使用 SQL 整合式追蹤修訂的原則，不指定不同的資料刪除偵測原則-此原則已識別的內建支援刪除列。 

此原則可以只能搭配資料表。它不能搭配檢視。 您需要讓您使用的您才能使用此原則表格的追蹤修訂。 如需相關指示，請參閱[啟用和停用 [追蹤修訂](https://msdn.microsoft.com/library/bb964713.aspx)。    
 
當組織**建立資料來源**要求、 SQL 整合變更追蹤原則可以指定，如下所示︰

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**資料刪除偵測原則**

資料刪除偵測原則的目的，是以有效率地識別資料已刪除的項目。 目前，只支援的原則的是`Soft Delete`原則，可讓您識別根據的值已刪除的項目`soft delete`資料行或資料來源中的屬性。 此原則可以指定，如下所示︰

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**附註︰**支援的字串、 整數或布林值的資料行。 用來做為的值`softDeleteMarkerValue`必須是字串，即使對應的欄位保留的整數或布林值。 例如，如果您的資料來源中顯示的值為 1，使用`"1"`為`softDeleteMarkerValue`。    

<a name="CreateDataSourceRequestExamples"></a>
**邀請本文範例**

如果您想要使用索引子所執行的排程中的資料來源，此範例會示範如何指定變更及刪除偵測原則︰ 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

如果您只想要使用的資料來源的資料的一次性複本，就可以省略原則︰

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**回應**

成功的要求︰ 201 已建立。 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>更新資料來源 ##

您可以更新現有的資料來源使用的 HTTP 保留要求。 您指定資料來源，以要求 URI 上更新的名稱︰

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version`資格。 目前使用的是`2015-02-28`。 [Azure 搜尋版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)有詳細資料] 與 [深入瞭解替代的版本。

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**要求**

邀請本文語法會先[建立資料來源要求](#CreateDataSourceRequestSyntax)相同。

> [AZURE.NOTE] 某些屬性無法更新現有的資料來源。 例如，您無法變更現有的資料來源類型。  

> [AZURE.NOTE] 如果您不想要變更現有的資料來源的連線字串，您可以指定常值`<unchanged>`連接字串。 這是有幫助的情況下您需要更新資料來源，但沒有方便存取 [連線字串，因為它是機密資料。

**回應**

成功的要求︰ 201 已建立新的資料來源屬性的狀況建立，而且 204 沒有內容如果更新現有的資料來源。

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>清單資料來源 ##

[**清單資料來源**] 作業會傳回資料來源的清單中您 Azure 搜尋服務。 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version`資格。 目前使用的是`2015-02-28`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

成功的要求︰ 200 [確定]。

以下是範例回應內容︰

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

請注意，您也可以篩選下您感興趣屬性的回應。 如果您想要的資料來源名稱清單，例如，使用 OData`$select`查詢選項︰

    GET /datasources?api-version=205-02-28&$select=name

在此情況下，從上述範例回應會出現，如下所示︰ 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

這是很有用的技巧，若要儲存的頻寬，如果您有大量的資料來源，在您的搜尋服務。

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>取得資料來源 ##

[**取得資料來源**] 作業會取得資料來源定義從 Azure 搜尋。

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version`資格。 目前使用的是`2015-02-28`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

回應非常類似中[建立資料來源範例要求](#CreateDataSourceRequestExamples)範例︰ 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] 沒有設定`Accept`要求標頭`application/json;odata.metadata=none`時呼叫這個 API 為如此一來就會導致`@odata.type`屬性省略的回應，且您無法區別資料變更與不同類型的資料刪除偵測原則。 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>刪除資料來源 ##

**刪除資料來源**作業會移除您 Azure 搜尋服務中的資料來源。

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] 如果任何索引子參考您要刪除的資料來源，仍會繼續執行刪除操作。 不過，這些索引子就會轉換成錯誤狀態時他們在下次執行。  

`api-version`資格。 目前使用的是`2015-02-28`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 204 沒有內容會傳回成功的回應。

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>建立索引 ##

您可以建立新的索引子內使用 HTTP 文章要求 Azure 搜尋服務。
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

或者，您可以使用放入，並指定 URI 上的 [資料來源的名稱。 如果資料來源不存在，它將會建立。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] 索引子允許的最大的數字會依價格層而有所不同。 免費的服務可讓您最多 3 索引子。 標準服務可讓 50 索引子。 如需詳細資訊，請參閱[服務限制](search-limits-quotas-capacity.md)。

`api-version`資格。 目前使用的是`2015-02-28`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。


<a name="CreateIndexerRequestSyntax"></a>
**邀請本文語法**

邀請內文包含重新定義，以指定的資料來源及目標索引編製索引，以及選擇性編製索引的排程和參數。 


組織結構的要求裝載的語法如下所示。 進一步本主題中所提供的範例要求。

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**重新排程**

索引子也可以指定的排程。 如果排程，索引器會依排程定期執行。 排程具有下列屬性︰

- `interval`︰ 必要。 執行指定間隔或索引器期間的持續時間值。 最小的可容許的間隔為 5 分鐘;最長為一天。 您必須格式化為 XSD 「 dayTimeDuration 」 的值 （限制[ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的子集）。 這個模式︰ `"P[nD][T[nH][nM]]"`。 範例︰`PT15M`每 15 分鐘，`PT2H`每 2 小時。 

- `startTime`︰ 必要。 UTC 日期時間索引子應該開始執行。 

**重新參數**

索引子可以選擇性地指定會影響其行為的數個參數。 所有的參數是選擇性的。  

- `maxFailedItems`︰ 無法重新執行視為失敗之前能編製索引的項目數字。 預設值為 0。 [取得索引狀態](#GetIndexerStatus)作業傳回失敗項目的相關資訊。 

- `maxFailedItemsPerBatch`︰ 無法能編製索引中每個批次重新執行視為失敗之前的項目數字。 預設值為 0。

- `base64EncodeKeys`︰ 指定為文件索引鍵會 base 64 編碼。 Azure 搜尋會可以出現在文件索引鍵的字元限制。 不過，您的來源資料中的值可能會包含無效的字元。 如有必要這類值做為文件索引鍵編製索引，可以設定這個旗標為 true。 預設值是`false`。

- `batchSize`︰ 指定資料來源的讀取和編製索引的項的目數單一批次以改善效能。 預設值的資料來源類型而定︰ 1000 Azure SQL 和 DocumentDB，而且 10 Azure Blob 儲存體。

**欄位對應**

您可以使用功能變數對應到資料來源中的欄位名稱，對應到目標索引中的不同的欄位名稱。 例如，請考慮欄位的來源資料表`_id`。 Azure 搜尋並不允許開始以底線，所以必須重新命名欄位的欄位名稱。 這可以使用`fieldMappings`，如下所示索引] 屬性︰ 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

您可以指定多個欄位對應︰ 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

來源和目標欄位名稱不區分大小寫。

<a name="FieldMappingFunctions"></a>
***欄位對應函數***

欄位對應可以用於轉換使用*對應函數*來源欄位的值。

只有一個這類函數目前支援︰ `jsonArrayToStringCollection`。 剖析包含 Collection(Edm.String) 欄位在目標索引中將格式設定為 JSON 陣列的字串的欄位。 它適用於 Azure SQL 索引搭配使用，因為 SQL 沒有原生集合資料類型。 它可以用於，如下所示︰ 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

比方說，如果來源] 欄位包含的字串`["red", "white", "blue"]`，然後輸入目標欄位`Collection(Edm.String)`填入三個值`"red"`，`"white"`和`"blue"`。

請注意，`targetFieldName`屬性是選擇性的。如果省略了，`sourceFieldName`值。 

<a name="CreateIndexerRequestExamples"></a>
**邀請本文範例**

下列範例會建立將資料從參考的資料表重新`ordersds`資料來源`orders`索引的排程，在 2015 年 1 月 1 日 UTC 開始，並執行每小時。 每個重新叫用為成功如果不超過 5 個項目無法在索引中每個批次，而不超過 10 個項目無法能編製索引的總計。 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**回應**

201 已成功邀請的建立。


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>更新索引 ##

您可以更新現有索引子使用 HTTP 保留要求。 您指定更新要求 URI 重新的名稱︰

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version`資格。 目前使用的是`2015-02-28`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**要求**

邀請本文語法是[建立重新邀請](#CreateIndexerRequestSyntax)一樣。

**回應**

成功的要求︰ 201 已建立新的索引屬性的狀況建立，而且 204 沒有內容如果更新現有的索引。


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>清單索引子 ##

**清單索引子**作業會傳回您 Azure 搜尋服務中的索引子的清單。 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version`資格。 預覽版本`2015-02-28-Preview`。 [Azure 搜尋版本設定](https://msdn.microsoft.com/library/azure/dn864560.aspx)有詳細資料] 與 [深入瞭解替代的版本。

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

成功的要求︰ 200 [確定]。

以下是範例回應內容︰

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

請注意，您也可以篩選下您感興趣屬性的回應。 例如，若要重新名稱的清單，請使用 OData`$select`查詢選項︰

    GET /indexers?api-version=2014-10-20-Preview&$select=name

在此情況下，從上述範例回應會出現，如下所示︰ 

    {
      "value" : [ { "name": "myindexer" } ]
    }

這是很有用的技巧，若要儲存的頻寬，如果您有很多索引子在您的搜尋服務。


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>取得索引 ##

**取得索引**作業取得重新定義從 Azure 搜尋。

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version`資格。 預覽版本`2015-02-28-Preview`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 200 確定會傳回成功的回應。

回應非常類似在[建立重新範例邀請](#CreateIndexerRequestExamples)中的範例︰ 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>刪除索引 ##

**刪除索引**作業移除索引子 Azure 搜尋服務。

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

刪除索引子時，進行重新執行，在該時間將完成，執行，但會排程沒有進一步執行。 嘗試使用不存在於重新會導致 HTTP 狀態碼 404 找不到。 
 
`api-version`資格。 預覽版本`2015-02-28-Preview`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 204 沒有內容會傳回成功的回應。

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>執行索引 ##

除了執行定期排程，索引子可以叫視需要透過**執行索引**作業︰ 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version`資格。 預覽版本`2015-02-28-Preview`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 202 接受會傳回成功的回應。

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>取得索引狀態 ##

**取得索引狀態**作業擷取索引子的目前狀態和執行歷程記錄︰ 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version`資格。 預覽版本`2015-02-28-Preview`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 為 200 確定成功的回應。

回應本文會包含整體重新健康狀態，最後一個重新叫用，以及最近重新叫用的歷程記錄的相關資訊 （如果有的話）。 

範例回應內容看起來像這樣︰ 

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

**索引狀態**

索引狀態可能是下列的值的其中一個︰

- `running`指出索引子運作正常。 請注意，重新執行的一些可能仍會失敗，因此建議您先檢查`lastResult`屬性。 

- `error`指出索引子時發生錯誤，無法修正沒有人互動的情況。 例如，資料來源的認證可能已過期，或資料來源或目標索引的結構描述已變更的相關方式。 

**重新執行結果**

重新執行結果包含單一重新執行的相關資訊。 最新的結果呈現為`lastResult`索引狀態的屬性。 其他最近，如果有的話，結果為`executionHistory`索引狀態的屬性。 

重新執行結果包含下列屬性︰ 

- `status`︰ 的執行狀態。 如需詳細資訊，請參閱下方[重新執行狀態](#IndexerExecutionStatus)。 

- `errorMessage`︰ 執行失敗的錯誤訊息。 

- `startTime`︰ 以 utc 表示此執行開始的時間。

- `endTime`︰ 以 utc 表示當此執行結束時間。 如果執行仍在進行中未設定此值。

- `errors`︰ 的項目層級的錯誤，如果有任何陣列。 每個項目包含一個文件索引鍵 (`key`屬性) 和錯誤訊息 (`errorMessage`屬性)。 

- `itemsProcessed`︰ 的資料來源重新嘗試此執行期間編製索引的項目 （例如資料表資料列）。 

- `itemsFailed`︰ 此執行期間失敗的項目數目。  
 
- `initialTrackingState`︰ 永遠`null`的第一次重新執行中，或如果的資料變更追蹤原則不會啟用上所使用的資料來源。 如果啟用此原則，則在後續的執行此值會指出追蹤處理此執行值的第一個 （最低） 變更。 

- `finalTrackingState`︰ 永遠`null`如果資料變更追蹤原則不會啟用上所使用的資料來源。 否則，表示最新的 （最高） 變更追蹤順利處理此執行的值。 

<a name="IndexerExecutionStatus"></a>
**重新執行狀態**

重新執行狀態擷取單一重新執行的狀態。 它可以是下列的值︰

- `success`指出已順利完成重新執行。

- `inProgress`重新執行正在進行中的指示。 

- `transientFailure`指出重新執行失敗。 請參閱`errorMessage`詳細資料] 屬性。 可能會失敗，或可能不需要有人可以操作來修正-，例如修正架構不相容的資料來源和目標索引之間需要使用者動作，而不會暫時資料來源停機時間。 如果有的話，將會每排程，繼續重新叫用。 

- `persistentFailure`指出索引失敗的需要手動操作方式。 排定的重新執行停駐點。 之後解決問題，使用重設索引子 API 重新排程的執行。 

- `reset`指出索引子已重設呼叫重設索引子 API （如下所示）。 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>重設索引 ##

**重設索引**作業，重設變更追蹤索引器相關聯的狀態。 這個選項可讓您觸發從從頭開始重新編製索引 （例如，如果已變更您的資料來源結構描述） 中，或變更索引器相關聯的資料來源的資料變更偵測原則。   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version`資格。 預覽版本`2015-02-28-Preview`。 

`api-key`必須是管理員按鍵 （而不是查詢鍵）。 請參閱[搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) ，若要進一步瞭解機碼中的 [驗證] 區段。 [建立搜尋服務入口網站中](search-create-service-portal.md)會說明如何取得服務 URL] 與 [在邀請中所用的主要內容。

**回應**

狀態碼︰ 204 沒有內容的成功的回應。

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 資料類型和 Azure 搜尋資料類型之間的對應 ##

<table style="font-size:12">
<tr>
<td>SQL 資料類型</td>  
<td>允許目標索引的欄位類型</td>
<td>備忘稿</td>
</tr>
<tr>
<td>位元</td>
<td>Edm.Boolean Edm.String</td>
<td></td>
</tr>
<tr>
<td>int、 小 tinyint</td>
<td>Edm.Int32，Edm.Int64，Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64 Edm.String</td>
<td></td>
</tr>
<tr>
<td>實數，浮動時間</td>
<td>Edm.Double Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney 金錢<br>小數位數<br>數字
</td>
<td>Edm.String</td>
<td>Azure 搜尋不支援將十進位類型轉換成 Edm.Double，因為這會遺失精確度
</td>
</tr>
<tr>
<td>char、 nchar、 varchar、 nvarchar</td>
<td>Edm.String<br/>Collection(Edm.String)</td>
<td>請參閱[欄位對應函數](#FieldMappingFunctions)的詳細資料，將字串資料行轉換成 Collection(Edm.String) 文件中</td>
</tr>
<tr>
<td>29.998、 datetime、 datetime2、 日期、 datetimeoffset</td>
<td>Edm.DateTimeOffset Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>地理位置</td>
<td>Edm.GeographyPoint</td>
<td>僅限類型 （這是預設值） srid 都會 4326 點的地理位置執行個體支援</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/A</td>
<td>資料列版本欄不能儲存在搜尋索引，但可以用於追蹤修訂</td>
</tr>
<tr>
<td>時段的時間<br>二進位 varbinary、 圖像、<br>xml，幾何，CLR 類型</td>
<td>N/A</td>
<td>不支援</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON 資料類型和 Azure 搜尋資料類型之間的對應 ##

<table style="font-size:12">
<tr>
<td>JSON 資料類型</td> 
<td>允許目標索引的欄位類型</td>
<td>備忘稿</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean Edm.String</td>
<td></td>
</tr>
<tr>
<td>整數的數字</td>
<td>Edm.Int32，Edm.Int64，Edm.String</td>
<td></td>
</tr>
<tr>
<td>浮點數字</td>
<td>Edm.Double Edm.String</td>
<td></td>
</tr>
<tr>
<td>字串</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>陣列的基本項目類型，例如 ["a"，"b"，"c"]</td>
<td>Collection(Edm.String)</td>
<td></td>
</tr>
<tr>
<td>看起來就像日期的字串</td>
<td>Edm.DateTimeOffset Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON 點的物件</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON 點是 JSON 物件以下列格式: {「 type 」: 「 點 「，」 座標 」: [長，lat]} </td>
</tr>
<tr>
<td>其他 JSON 物件</td>
<td>N/A</td>
<td>不受支援。Azure 搜尋目前支援僅基本類型及字串集合</td>
</tr>
</table>