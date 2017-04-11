<properties
pageTitle="使用搜尋 Azure blob 索引編製索引 JSON 二進位大型物件"
description="使用搜尋 Azure blob 索引編製索引 JSON 二進位大型物件"
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
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>使用搜尋 Azure blob 索引編製索引 JSON 二進位大型物件 

本文說明如何設定 Azure 搜尋 blob 重新從包含 JSON blob 擷取結構化的內容。

## <a name="scenarios"></a>案例

根據預設，[搜尋 Azure blob 重新](search-howto-indexing-azure-blob-storage.md)會作為單一文字區塊的剖析 JSON 二進位大型物件。 通常，您想保留 JSON 文件的結構。 例如，假設 JSON 文件 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

您可能會想要剖析 「 文字 」、 「 datePublished，」 與 「 標籤 」 欄位 Azure 搜尋文件。

或者，當您 blob 包含**JSON 物件的陣列**，您可能會想要成為個別 Azure 搜尋文件的陣列的每個項目。 例如，假設與此 JSON blob:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

您可以填入 「 識別碼 」 和 「 文字 」 欄位的每個您 Azure 搜尋索引的 3 的個別文件。 

> [AZURE.IMPORTANT] 此功能目前預覽中。 僅在使用版本**2015年 02-28 預覽**REST API 中使用。 請記住，[預覽 Api 供測試與評估，和不應該用於生產環境中。 

## <a name="setting-up-json-indexing"></a>設定 JSON 編製索引

若要編製索引 JSON 二進位大型物件，請設定`parsingMode`設定參數`json`（若要為一份文件的每個 blob 編製索引） 或`jsonArray`（如果您的二進位大型物件包含 JSON 陣列）︰ 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

如有需要可用於**欄位對應**選擇用來填入您的目標搜尋索引的來源 JSON 文件的屬性。  請見下方說明這。 

> [AZURE.IMPORTANT] 當您使用`json`或`jsonArray`剖析模式，Azure 搜尋假設您的資料來源中的所有 blob 都會 JSON。 如果您需要支援 JSON 和非 JSON blob 的相同的資料來源，請讓我們知道[我們 UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上。

## <a name="using-field-mappings-to-build-search-documents"></a>使用功能變數對應來建立搜尋文件 

目前，Azure 搜尋無法索引任意 JSON 文件直接，因為它支援只基本資料型別、 字串陣列及 GeoJSON 點。 不過，您可以使用**功能變數對應**到挑選 JSON 文件的部分，「 請拿起 」 進行搜尋文件的最上層的欄位。 若要瞭解欄位對應的基本概念，請參閱[Azure 搜尋索引的欄位對應搭資料來源與搜尋索引之間的差異](search-indexer-field-mappings.md)。

回到我們的範例 JSON 文件︰ 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

假設您有使用下列欄位的搜尋索引︰`text`的類型 Edm.String，`date`的類型 Edm.DateTimeOffset，及`tags`類型 Collection(Edm.String)。 若要對應您 JSON 到所要的圖案，請使用下列的欄位對應︰ 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

使用[JSON 指標](http://tools.ietf.org/html/rfc6901)標記法指定來源中的欄位名稱對應。 您開始斜線參照 JSON 文件的根目錄，然後切入使用正斜線分隔路徑 （在任意巢狀層級） 所需的屬性。 

您也可以使用起始的索引以參照個別的陣列項目。 例如，若要挑選上述範例中的 「 標籤 」 陣列的第一個項目，請使用欄位對應像這樣︰

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] 如果來源欄位名稱中的欄位對應路徑參照 JSON 中不存在的屬性，該對應會略過不會產生錯誤。 這是的讓我們可支援不同的結構描述 （這是常見的使用案例） 的文件。 因為沒有驗證，您需要小心避免在您的欄位對應規格中的拼字錯誤。 

如果 JSON 文件僅包含簡單的最上層內容，您可能不需要欄位對應完全。 例如，如果您 JSON 看起來像這樣，最上層屬性 「 文字 」、 「 datePublished 」 和 「 標籤 」 會直接對應到搜尋索引中的對應欄位︰ 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>編製索引的巢狀的 JSON 陣列

如果您要編製索引 JSON 物件的陣列，但該陣列巢狀位置在文件？ 您可以挑選哪一個屬性包含陣列使用`documentRoot`設定屬性。 例如，如果您的二進位大型物件看起來像這樣︰ 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

使用此設定索引 」 level2] 屬性中包含的陣列︰ 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>要求範例

將此所有在一起，以下是完成裝載範例。 

資料來源︰ 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

索引︰

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>協助我們更有效地進行 Azure 搜尋

如果您有功能要求或想法的改良功能，請連絡我們我們[UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)上。