<properties 
    pageTitle="DocumentDB 小數位數和效能測試 |Microsoft Azure" 
    description="瞭解如何執行縮放比例和效能與 Azure DocumentDB 測試"
    keywords="效能測試"
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="performance-and-scale-testing-with-azure-documentdb"></a>效能和 Azure DocumentDB 以測試的小數位數
效能和縮放比例測試是應用程式開發的主要步驟。 許多應用程式資料庫層會影響的整體效能與延展性，因此效能測試的重要元件。 [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)特殊用途的彈性的小數位數，如預期呈現效能，而因此很好適合需要高效能資料庫層的應用程式。 

本文是實作效能測試套件的他們 DocumentDB 的工作量，或評估 DocumentDB 高效能應用程式案例的開發人員參考。 主要是解釋隔離的效能測試的資料庫，但也包含產品應用程式的最佳作法。

閱讀本文之後，您將可以回答下列問題︰   

- 哪裡可以找到效能測試 Azure DocumentDB 範例.NET 用戶端應用的程式？ 
- 如何從我的用戶端應用程式中獲得高處理量，層級的 Azure DocumentDB？

若要開始使用程式碼，請從[DocumentDB 效能測試範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)下載專案。 

> [AZURE.NOTE] 這個應用程式的目標是示範含少量的用戶端電腦擷取登出 DocumentDB 較佳的效能的最佳作法。 這不進行示範的服務，可以不按比例縮放 limitlessly 的最大使用量容量。

如果您正在尋找的用戶端設定選項，以提升 DocumentDB 效能，請參閱[DocumentDB 效能的秘訣](documentdb-performance-tips.md)。

## <a name="run-the-performance-testing-application"></a>執行效能測試應用程式
開始使用最快速的方法是編譯和執行.NET 範例] 下方，執行下列步驟所述。 您也可以檢閱的程式碼，並實作類似設定用戶端應用程式。

**步驟 1:**從[DocumentDB 效能測試範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)，下載專案或分叉 Github 存放庫。

**步驟 2:**修改 EndpointUrl 與 AuthorizationKey，CollectionThroughput DocumentTemplate （選擇性） 在 App.config 的設定。

> [AZURE.NOTE] 前佈建與高處理量的集合，請參閱[價格] 頁面](https://azure.microsoft.com/pricing/details/documentdb/)來估計的成本，每個集合。 DocumentDB 帳單儲存及處理量獨立地在每小時，因此您可以刪除或降低您 DocumentDB 集合的測試後儲存成本。

**步驟 3:**編譯，並從命令列執行主控台應用程式。 您應該會看到如下所示的輸出︰

    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://docdb-scale-demo.documents.azure.com:443/
    Collection : db.testdata at 50000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: 500
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Creating database db
    Creating collection testdata
    Creating metric collection metrics
    Retrying after sleeping for 00:03:34.1720000
    Starting Inserts with 500 tasks
    Inserted 661 docs @ 656 writes/s, 6860 RU/s (18B max monthly 1KB reads)
    Inserted 6505 docs @ 2668 writes/s, 27962 RU/s (72B max monthly 1KB reads)
    Inserted 11756 docs @ 3240 writes/s, 33957 RU/s (88B max monthly 1KB reads)
    Inserted 17076 docs @ 3590 writes/s, 37627 RU/s (98B max monthly 1KB reads)
    Inserted 22106 docs @ 3748 writes/s, 39281 RU/s (102B max monthly 1KB reads)
    Inserted 28430 docs @ 3902 writes/s, 40897 RU/s (106B max monthly 1KB reads)
    Inserted 33492 docs @ 3928 writes/s, 41168 RU/s (107B max monthly 1KB reads)
    Inserted 38392 docs @ 3963 writes/s, 41528 RU/s (108B max monthly 1KB reads)
    Inserted 43371 docs @ 4012 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 48477 docs @ 4035 writes/s, 42282 RU/s (110B max monthly 1KB reads)
    Inserted 53845 docs @ 4088 writes/s, 42845 RU/s (111B max monthly 1KB reads)
    Inserted 59267 docs @ 4138 writes/s, 43364 RU/s (112B max monthly 1KB reads)
    Inserted 64703 docs @ 4197 writes/s, 43981 RU/s (114B max monthly 1KB reads)
    Inserted 70428 docs @ 4216 writes/s, 44181 RU/s (115B max monthly 1KB reads)
    Inserted 75868 docs @ 4247 writes/s, 44505 RU/s (115B max monthly 1KB reads)
    Inserted 81571 docs @ 4280 writes/s, 44852 RU/s (116B max monthly 1KB reads)
    Inserted 86271 docs @ 4273 writes/s, 44783 RU/s (116B max monthly 1KB reads)
    Inserted 91993 docs @ 4299 writes/s, 45056 RU/s (117B max monthly 1KB reads)
    Inserted 97469 docs @ 4292 writes/s, 44984 RU/s (117B max monthly 1KB reads)
    Inserted 99736 docs @ 4192 writes/s, 43930 RU/s (114B max monthly 1KB reads)
    Inserted 99997 docs @ 4013 writes/s, 42051 RU/s (109B max monthly 1KB reads)
    Inserted 100000 docs @ 3846 writes/s, 40304 RU/s (104B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 100000 docs @ 3834 writes/s, 40180 RU/s (104B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.


**（如有需要） 的步驟 4:**處理量報告 (RU/s) 從工具應相同或更高的集合能夠處理量。 如果沒有，增加小幅度地 DegreeOfParallelism 可能會協助您達成限制。 如果您用戶端應用程式的處理能力富饒啟動多個執行個體的相同或不同的電腦上的應用程式可協助您達成能夠的限制在不同的執行個體。 如果您需要此步驟的說明，請撰寫電子郵件以askdocdb@microsoft.com或填滿的支援票證。

執行的應用程式之後，您可以嘗試不同[編製索引的原則](documentdb-indexing-policies.md)和[一致性層級](documentdb-consistency-levels.md)若要瞭解其處理量及延遲的影響。 您也可以檢閱的程式碼，並實作類似的設定，您自己的測試套件或實際執行應用程式。

## <a name="next-steps"></a>後續步驟
本文中，我們會看如何執行效能和測試 DocumentDB 使用.NET 主控台應用程式的小數位數。 請參閱以下連結 DocumentDB 所使用的其他相關資訊。

* [DocumentDB 效能測試範例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [若要改善 DocumentDB 效能的用戶端設定選項](documentdb-performance-tips.md)
* [伺服器端 DocumentDB 中分割](documentdb-partition-data.md)
* [DocumentDB 集合和效能層級](documentdb-performance-levels.md)
* [DocumentDB.NET SDK MSDN 上的文件](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [DocumentDB.NET 範例](https://github.com/Azure/azure-documentdb-net)
* [DocumentDB 部落格上效能的秘訣](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)
