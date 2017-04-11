<properties
    pageTitle="DocumentDB NoSQL Node.js 範例 |Microsoft Azure"
    description="尋找 NoSQL Node.js 範例上 github DocumentDB，包括 NoSQL 資料庫中的 CRUD 作業 JSON 文件中的一般工作。"
    keywords="node.js 範例"
    services="documentdb"
    authors="moderakh"
    manager="jhubbard"
    editor="monicar"
    documentationCenter="nodejs"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="moderakh"/>


# <a name="documentdb-nodejs-examples"></a>DocumentDB Node.js 範例

> [AZURE.SELECTOR]
- [.NET 範例](documentdb-dotnet-samples.md)
- [Node.js 範例](documentdb-nodejs-samples.md)
- [Python 範例](documentdb-python-samples.md)
- [Azure 程式碼範例圖庫](https://azure.microsoft.com/documentation/samples/?service=documentdb)

[Azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) GitHub 存放庫中包含範例解決方案執行 CRUD 作業和 Azure DocumentDB 資源其他共同作業。 本文提供︰

- 連結至每個 Node.js 範例專案檔案中的工作。
- 連結到相關的 API 參考內容。

**必要條件**

1. 您必須使用 Azure 帳戶才能使用這些 Node.js 範例︰
    - 您可以[開啟 Azure 帳戶免費](https://azure.microsoft.com/pricing/free-trial/)︰ 取得貸項總計試用付費 Azure 服務，您可以使用和即使使用這些之後最多，您可以將該帳戶，然後使用免費 Azure 服務，例如網站。 您的信用卡不需要付費，除非您明確地變更您的設定，並要求付費。
   - 您可以[啟動 Visual Studio 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)︰ 您的 Visual Studio 訂閱可讓您貸項總計付費 Azure 服務，您可以使用每個月。
2. 您也需要[Node.js SDK](documentdb-sdk-node.md)。

    > [AZURE.NOTE] 每個範例是獨立，自行設定，並清除之後本身。 因此，範例議題[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)多次呼叫。 這是您的訂閱每次計費 （1 小時） 的每個集合建立的效能層的使用方式。

## <a name="database-examples"></a>資料庫範例

[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement)專案檔案會顯示如何執行下列工作。

任務 | API 參考
--- | ---
[建立資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[查詢資料庫的帳戶](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases)
[閱讀識別碼資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[帳戶清單資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[刪除資料庫](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## <a name="collection-examples"></a>集合範例

[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement)專案檔案會顯示如何執行下列工作。

任務 | API 參考
--- | ---
[建立集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[閱讀的資料庫中的所有集合清單](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections)
[取得 _self 集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[取得識別碼集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[取得集合的效能層](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[變更集合的效能層](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[刪除集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## <a name="document-examples"></a>文件範例

[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement)專案檔案會顯示如何執行下列工作。

任務 | API 參考
--- | ---
[建立文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[閱讀文件摘要集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[閱讀文件識別碼](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[只有當文件已變更，請閱讀文件](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[文件的查詢](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments)
[取代文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[取代條件 ETag 核取的文件](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[刪除文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## <a name="indexing-examples"></a>編製索引的範例

[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement)專案檔案會顯示如何執行下列工作。

任務 | API 參考
--- | ---
建立具有預設的索引的集合 | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[手動編製索引特定的文件](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: [包含]](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[手動將特定的文件排除索引](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[使用大量匯入的麻煩索引或閱讀大量的集合](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode)
[編製索引中包含特定的文件的路徑](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[排除特定路徑編製索引](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[範圍作業期間，字串路徑上允許掃描](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[字串路徑上建立範圍索引](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[建立與預設 indexPolicy 的集合，然後更新此線上](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

如需有關編製索引作業的詳細資訊，請參閱[DocumentDB 編製索引的原則](documentdb-indexing-policies.md)。

## <a name="server-side-programming-examples"></a>伺服器端程式設計範例

[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts)專案檔案會顯示如何執行下列工作。

任務 | API 參考
--- | ---
[建立預存程序](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[執行預存程序](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

如需有關伺服器端程式設計的詳細資訊，請參閱[DocumentDB 伺服器端程式設計︰ 預存程序、 資料庫引動程序，以及 Udf](documentdb-programming.md)。

## <a name="partitioning-examples"></a>分割的範例

[App.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) [Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning)專案檔案會顯示如何執行下列工作。

任務 | API 參考
--- | ---
[使用 HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html)

如需有關如何分割 DocumentDB 中的資料的詳細資訊，請參閱[中 DocumentDB 的資料分割和小數位數](documentdb-partition-data.md)。
