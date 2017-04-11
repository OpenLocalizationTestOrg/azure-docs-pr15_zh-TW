<properties
pageTitle="Azure 搜尋與編製索引的 Azure 資料表儲存體"
description="瞭解如何建立索引中使用 Azure 搜尋 Azure 資料表儲存的資料"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>Azure 搜尋與編製索引的 Azure 資料表儲存體

本文說明如何使用 Azure 搜尋索引資料儲存於 Azure 資料表儲存體。 新 Azure 搜尋表格索引器可讓此程序，快速且順暢。 

> [AZURE.IMPORTANT] 目前這項功能在預覽中。 使用只在使用版本**2015年 02-28 預覽**REST API 和版本 2.0-.NET SDK 的預覽。 請記住，[預覽 Api 供測試與評估，和不應該用於生產環境中。

## <a name="setting-up-azure-table-indexing"></a>設定 Azure 資料表索引編製功能

若要設定，並設定 Azure 資料表索引，您可以使用 Azure 搜尋 REST API 來建立和管理**索引子**與**資料來源**[索引作業](https://msdn.microsoft.com/library/azure/dn946891.aspx)中所述。 您也可以使用.NET SDK 的[版本 2.0 預覽](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)。 在未來，支援的表格索引編製功能將會新增至 Azure 入口網站。

資料來源指定要編製索引，認證存取資料及原則，讓 Azure 搜尋有效率地識別資料 （新增、 修改或刪除的資料列） 中的變更所需的資料。

索引子讀取從資料來源的資料，並載入至的目標搜尋索引。

若要設定表格編製索引作業︰

1. 建立資料來源
    - 設定`type`參數`azuretable`
    - 在您儲存帳戶連線字串中傳遞`credentials.connectionString`參數
    - 指定資料表名稱使用`container.name`參數
    - 或者，您可以指定查詢，使用`container.query`參數。 可能的話，使用篩選 PartitionKey 的最佳效能。任何其他查詢會導致完整資料表掃描，這可能會導致大型表格的效能不佳。
2. 建立您想要編製索引的資料表中的資料行對應的結構描述的搜尋索引。 
3. 藉由搜尋索引中連接資料來源建立索引。

### <a name="create-data-source"></a>建立資料來源

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

如需建立資料來源 API 的詳細資訊，請參閱[建立資料來源](search-api-indexers-2015-02-28-preview.md#create-data-source)。

### <a name="create-index"></a>建立索引 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

如需建立索引 API 的詳細資訊，請參閱[建立索引](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>建立索引 

最後，建立參照的資料來源及目標索引索引。 例如︰

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

如需建立索引子 API 的詳細資訊，請參閱[建立索引](search-api-indexers-2015-02-28-preview.md#create-indexer)。

這就是很簡單-快樂索引編製功能 ！

## <a name="dealing-with-different-field-names"></a>處理不同的欄位名稱

通常，您現有的索引中的欄位名稱會在表格中的屬性名稱不同。 您可以使用**功能變數對應**對應至您的搜尋索引中的欄位名稱的 [資料表中的屬性名稱。 若要深入瞭解功能變數對應，請參閱[Azure 搜尋索引的欄位對應搭資料來源與搜尋索引之間的差異](search-indexer-field-mappings.md)。

## <a name="handling-document-keys"></a>處理文件索引鍵

Azure 搜尋中的文件索引鍵可唯一識別文件。 每個搜尋索引必須有一個索引鍵的欄位類型的`Edm.String`。 索引鍵欄位，才會新增至索引每份文件 （事實上，是唯一的必要的欄位）。

表格列有複合索引鍵，因為 Azure 搜尋會產生綜合欄位稱為`Key`的是分割索引鍵和列關鍵值的串連。 例如，如果資料列的 PartitionKey 是`PK1`RowKey 是`RK1`，然後`Key`欄位的值會`PK1RK1`。 

> [AZURE.NOTE] `Key`值可能會包含在文件按鍵，例如虛線無效的字元。 您可以使用處理無效的字元`base64Encode`[欄位對應函數](search-indexer-field-mappings.md#base64EncodeFunction)。 如果這麼做，請記得也使用 URL 安全 Base64 編碼方式，例如查閱文件索引鍵傳入 API 呼叫時。

## <a name="incremental-indexing-and-deletion-detection"></a>累加編製索引及刪除偵測
 
當您設定表格索引排程執行時，其 reindexes 只有新的或更新的列，是由資料列的`Timestamp`值。 您沒有指定變更偵測原則 – 累加編製索引作業已啟用您自動。 

若要指出，必須從索引中移除特定文件，您可以使用 [柔邊刪除策略 – 而刪除資料列，請新增指出它已刪除，並設定柔刪除偵測原則的資料來源上的屬性。 例如，以下所示的原則會考慮是否有屬性時，就會刪除列`IsDeleted`值`"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>協助我們更有效地進行 Azure 搜尋

如果您有功能要求或想法的改良功能，請連絡我們我們[UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)上。