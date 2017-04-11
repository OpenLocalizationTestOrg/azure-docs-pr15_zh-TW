<properties
pageTitle="CSV blob 與搜尋 Azure blob 索引編製索引作業 |Microsoft Azure"
description="瞭解如何將 CSV blob Azure 搜尋與編製索引"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>使用搜尋 Azure blob 索引編製索引 CSV 二進位大型物件 

根據預設，[搜尋 Azure blob 重新](search-howto-indexing-azure-blob-storage.md)剖析會作為單一文字區塊的分隔文字二進位大型物件。 不過，含 blob 包含 CSV 資料，您通常要處理 blob 為不同的文件中的每一行。 例如，指定下列分隔的文字檔︰ 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

您可能會想要其剖析為 2 的文件，每個包含 「 識別碼 」、 「 datePublished，」 和 「 標籤 」 欄位。

本文中，您將學習如何剖析與搜尋 Azure blob 索引子 CSV 二進位大型物件。 

> [AZURE.IMPORTANT] 此功能目前預覽中。 僅在使用版本**2015年 02-28 預覽**REST API 中使用。 請記住，[預覽 Api 供測試與評估，和不應該用於生產環境中。 

## <a name="setting-up-csv-indexing"></a>設定 CSV 編製索引

編製索引 CSV 二進位大型物件、 建立或更新與重新定義`delimitedText`剖析模式︰  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

如需建立索引子 API 的詳細資訊，請參閱[建立索引](search-api-indexers-2015-02-28-preview.md#create-indexer)。

`firstLineContainsHeaders`指出 （非空白） 的第一行每個 blob 包含標題。
如果 blob 不包含初始的標題列，必須在重新設定中指定標題︰ 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

目前，只 utf-8 支援編碼。 此外，逗點`','`支援的分隔符號字元。 如果您需要的其他編碼或分隔符號的支援，請讓我們知道[我們 UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上。

> [AZURE.IMPORTANT] 當您使用分隔的文字檔剖析模式時，Azure 搜尋假設您的資料來源中的所有 blob 都會 CSV。 如果您需要相同的資料來源中支援的 CSV 和非 CSV 二進位大型物件，請讓我們知道[我們 UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上。

## <a name="request-examples"></a>要求範例

將此所有在一起，以下是完整的內容範例。 

資料來源︰ 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

索引︰

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>協助我們更有效地進行 Azure 搜尋

如果您有功能要求或想法的改良功能，請連絡我們我們[UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search/)上。