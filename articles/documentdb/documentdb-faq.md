<properties 
    pageTitle="DocumentDB 資料庫問題的常見的問題 |Microsoft Azure" 
    description="取得 Azure DocumentDB 常見問題的解答 JSON NoSQL 文件資料庫服務。 回答資料庫問題容量、 效能層級，以及縮放比例。" 
    keywords="資料庫問題、 問題、 documentdb、 azure、 Microsoft azure 的常見問題集"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>關於 DocumentDB 常見問題集

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>資料庫疑問 Microsoft Azure DocumentDB 基本概念

### <a name="what-is-microsoft-azure-documentdb"></a>什麼是 Microsoft Azure DocumentDB？ 
Microsoft Azure DocumentDB 是熾熱快速的世界各地縮放比例 NoSQL 文件資料庫為-的-服務，提供豐富的查詢，無結構描述的資料，可協助提供可設定及可靠的效能，並可讓快速開發，面對受管理的平台由 power 並連接的 Microsoft Azure 中。 DocumentDB 是正確的網頁，行動裝置，遊戲，而且 IoT 應用程式時如預期呈現處理量、 可用性、 低延遲和結構描述免費的資料模型按鍵需求。 DocumentDB 提供結構描述彈性與豐富的原生 JSON 資料模型，透過編製索引作業，並且包含多重文件使用整合式 JavaScript 的交易支援。  
  
更多資料庫問題、 的答案及部署，並使用這項服務的指示，請參閱[DocumentDB 文件的頁面](https://azure.microsoft.com/documentation/services/documentdb/)。

### <a name="what-kind-of-database-is-documentdb"></a>哪一類是資料庫的 DocumentDB？
DocumentDB 是 NoSQL 文件導向的資料庫的 receive JSON 格式的資料。  DocumentDB 支援可透過豐富的 DocumentDB [SQL 查詢文法檢查](documentdb-sql-query.md)查詢的巢狀、 自動 contained 資料結構。 DocumentDB 提供高效能交易處理伺服器端 JavaScript 透過[預存程序、 引動程序，以及使用者定義的函數](documentdb-programming.md)。 資料庫也支援與相關聯的[效能層級](documentdb-performance-levels.md)的開發人員可微調一致性層級。
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>DocumentDB 資料庫有資料表，例如關聯式資料庫 (RDBMS) 嗎？
否，DocumentDB 會將資料儲存在 JSON 文件的集合。  DocumentDB 資源的相關資訊，請參閱[DocumentDB 資源模型和概念](documentdb-resources.md)。 如需有關如何 NoSQL 解決方案，例如 DocumentDB 異關聯式解決方案的詳細資訊，請參閱[NoSQL 與 SQL](documentdb-nosql-vs-sql.md)。

### <a name="do-documentdb-databases-support-schema-free-data"></a>DocumentDB 資料庫支援沒有結構描述的資料？
是的 DocumentDB 可讓應用程式沒有結構描述定義] 或 [提示任意 JSON 文件儲存在。 立即使用透過 DocumentDB SQL 查詢介面查詢的資料。   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB 是否支援 ACID 交易？
是的 DocumentDB 支援跨文件交易以 JavaScript 預存程序及引動程序。 交易被每個集合中的單一資料分割的範圍，以及執行所有 ACID 語意與或執行任何動作隔離從其他並行執行的程式碼和使用者要求。  例外狀況透過伺服器端執行 JavaScript 應用程式碼，如果被復原整個交易。 如需有關交易的詳細資訊，請參閱[資料庫程式交易](documentdb-programming.md#database-program-transactions)。

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>什麼是 DocumentDB 一般情況？  
DocumentDB 適合新的網頁，行動裝置，遊戲，而 IoT 應用程式自動縮放比例，如預期呈現效能，位置快速順序毫秒回應的時間，還可將查詢無結構描述的資料是很重要。 DocumentDB 本身快速開發和支援的應用程式的資料模型的連續重複項目。 管理使用者產生的內容和資料的應用程式是[DocumentDB 的常見使用案例](documentdb-use-cases.md)。  

### <a name="how-does-documentdb-offer-predictable-performance"></a>如何 DocumentDB 會提供可預期的效能？
[要求單位](documentdb-request-units.md)是 DocumentDB 中處理量的量值。 1 RU 對應到的 1 的知識庫文件的 [開始。 DocumentDB 中的每一項作業，包括讀取與寫入、 SQL 查詢預存程序的確定 RU 值以完成作業所需的處理能力。 而不是思考 CPU、 IO 和記憶體和各個如何影響您的應用程式處理量，您可以將而言單一 RU 量值。

每個 DocumentDB 集合可以保留與處理量秒的 RUs 就能夠處理量。 對於任何縮放比例的應用程式，您可以基準測試個別要求測量其 RU 值和佈建集合的所有要求處理要求單位的總和。 您也可以放大或縮小您的集合處理量，為您的應用程式 evolve 的需求。 如需要求單位和說明判斷您的集合需要請閱讀[管理效能與容量](documentdb-manage.md)並嘗試[處理量計算機](https://www.documentdb.com/capacityplanner)。 

### <a name="is-documentdb-hipaa-compliant"></a>與 DocumentDB HIPAA 相容？
是的 DocumentDB 是 HIPAA 相容。 HIPAA 建立使用，公開、 需求與個別辨識的健康狀態資訊的保護。 如需詳細資訊，請參閱[Microsoft 信任中心](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)。

### <a name="what-are-the-storage-limits-of-documentdb"></a>什麼是 DocumentDB 的儲存空間限制？ 
有總數集合可以儲存在 DocumentDB 中的資料沒有理論限制。 如果您想要儲存超過 250 GB 的單一集合內的資料，請[連絡支援人員](documentdb-increase-limits.md)以讓您更高的帳戶配額。 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>什麼是 DocumentDB 的處理限制？ 
如果您的工作量可以會大致平均分佈的磁碟分割金鑰夠大的數字之間，會在 DocumentDB，可支援集合的處理量的總數量沒有理論限制。 如果您想要超過 250000 要求單位/第二個每個集合或帳戶，請[連絡支援人員](documentdb-increase-limits.md)以讓您更高的帳戶配額。 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Microsoft Azure DocumentDB 費用是多少？
請參閱[DocumentDB 定價詳細資料](https://azure.microsoft.com/pricing/details/documentdb/)頁面，如需詳細資訊。 DocumentDB 使用費用由數集合中使用的時數集合在線上，以及使用的儲存和每個集合能夠處理量。 

### <a name="is-there-a-free-account-available"></a>有免費的帳戶使用嗎？
如果您是新 Azure，您可以註冊[Azure 免費的帳戶](https://azure.microsoft.com/free/)，這會提供您 30 天和 $200 嘗試所有 Azure 服務。 或者，如果您有 Visual Studio 訂閱時，您有資格申請[中每個月免費 Azure 貸項總計 $150](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)上任何 Azure 服務使用。  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>如何取得其他協助 DocumentDB？
如果您需要協助，請連絡我們的[堆疊溢位](http://stackoverflow.com/questions/tagged/azure-documentdb)， [Azure DocumentDB MSDN 開發人員論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB)，在或排程[與 DocumentDB 工程團隊的 1:1 交談](http://www.askdocdb.com/)。 若要保持最新的最新的 DocumentDB 新聞和功能，請遵循我們[Twitter](https://twitter.com/DocumentDB)。

## <a name="set-up-microsoft-azure-documentdb"></a>設定 Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>我要如何註冊為 Microsoft Azure DocumentDB？
Microsoft Azure DocumentDB 是[Azure 入口網站]提供[azure-portal]。  首先您必須註冊 Microsoft Azure 訂閱。  當您註冊 Microsoft Azure 訂閱時，您可以新增 DocumentDB 帳戶 Azure 訂閱。 如需新增 DocumentDB 帳戶的指示，請參閱[建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)。   

### <a name="what-is-a-master-key"></a>什麼是主索引鍵？
主索引鍵是存取帳戶的所有資源的安全性權杖。 與索引鍵的個人已閱讀和寫入存取權的所有資源在資料庫帳戶。 請謹慎發佈主索引鍵。 母片的主索引鍵和次要主索引鍵可在[Azure 入口網站]的**按鍵**刀[azure-portal]。 如需關於鍵的詳細資訊，請參閱[檢視、 複製與重新產生便捷鍵](documentdb-manage-account.md#keys)。

### <a name="how-do-i-create-a-database"></a>如何建立資料庫？
您可以建立使用[Azure 入口網站]()中[建立 DocumentDB 資料庫](documentdb-create-database.md)，其中一個[DocumentDB Sdk](documentdb-sdk-dotnet.md)，或是透過[REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)所述的資料庫。  

### <a name="what-is-a-collection"></a>什麼是集合？
集合是 JSON 文件和相關聯的 JavaScript 應用程式邏輯的容器。 集合是計費實體，[成本](documentdb-performance-levels.md)由處理量及 storaged 使用位置。 集合可以延伸一或多個磁碟分割區/伺服器，且可以調整，以處理幾乎沒有限制的區的儲存空間或處理量。

集合，也會 DocumentDB 帳單實體。 每個集合被付費每小時為能夠處理量及使用的儲存空間。 如需詳細資訊，請參閱[DocumentDB 價格](https://azure.microsoft.com/pricing/details/documentdb/)。  

### <a name="how-do-i-set-up-users-and-permissions"></a>如何設定使用者與權限？
您可以建立使用者與權限使用其中一個[DocumentDB Sdk](documentdb-sdk-dotnet.md) ，或是透過[REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)。   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>關於針對 Microsoft Azure DocumentDB 開發的資料庫問題

### <a name="how-to-do-i-start-developing-against-documentdb"></a>如何執行開始開發 DocumentDB 嗎？
[Sdk](documentdb-sdk-dotnet.md)的.NET、 Python、 Node.js、 JavaScript] 和 Java 可。  開發人員也可以使用[RESTful HTTP Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)與從各種不同的平台和語言 DocumentDB 資源互動。 

DocumentDB [.NET](documentdb-dotnet-samples.md)、 [Java](https://github.com/Azure/azure-documentdb-java)、 [Node.js](documentdb-nodejs-samples.md)，及[Python](documentdb-python-samples.md) Sdk 的範例，可在 GitHub 上。

### <a name="does-documentdb-support-sql"></a>DocumentDB 是否支援 SQL？
DocumentDB SQL 查詢語言是受支援的 SQL 查詢功能的增強型的子集。 DocumentDB SQL 查詢語言提供階層式 rtf 和關聯式運算子和擴充功能，透過 JavaScript 以使用者定義函數 (Udf)。 允許模型 JSON 文件為樹狀目錄與標籤為樹狀節點，這用 DocumentDB 自動編製索引技術以及 DocumentDB 的 SQL 查詢方言 JSON 文法。  如需如何使用 SQL 文法檢查的詳細資訊，請參閱[查詢 DocumentDB]的[query]文章。

### <a name="what-are-the-data-types-supported-by-documentdb"></a>什麼是 DocumentDB 所支援的資料類型？
支援的 DocumentDB 基本資料類型是 JSON 相同。 JSON 具有簡單類型系統組成的字串、 數字 （IEEE754 雙精確度） 及布林值-為 true，則為 false 及 Null。  更複雜的資料類型，例如日期時間、 Guid、 Int64，與幾何可以 JSON 和 DocumentDB 表示透過使用 {} 運算子和陣列，使用 [運算子的巢狀物件的建立。 

### <a name="how-does-documentdb-provide-concurrency"></a>如何 DocumentDB 提供並行？
DocumentDB 支援透過 HTTP 實體標籤或 etag 樂觀控制項 (OCC)。 每個 DocumentDB 資源有 etag，和 etag 設定伺服器上每次更新文件。 Etag 標題和目前的值會包含所有回覆郵件中。 Etag 可以搭配 If 標頭允許決定資源應該要更新的伺服器。 如果符合值是經過 etag 值。 如果 etag 值符合伺服器 etag 值，就會更新資源。 如果 etag 不再是目前日期，伺服器拒絕用 「 HTTP 412 前置條件失敗 」 回應程式碼。 用戶端就必須來重新提取取得目前 etag 值資源的資源。 此外，etag 都可以使用 If 無-標頭來判斷是否需要重新擷取資源。 

若要使用樂觀.NET 中，使用[AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx)類別。 .NET 範例中，請參閱[Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) github DocumentManagement 範例。

### <a name="how-do-i-perform-transactions-in-documentdb"></a>如何執行 DocumentDB 中的交易？
DocumentDB 支援語言整合的交易，透過 JavaScript 預存程序及引動程序。 範圍的集合是單一資料分割集合，或以相同的磁碟分割關鍵值的集合，內文件如果集合已分割的快照集隔離下執行指令碼內的所有資料庫作業。 文件版本 (Etag) 的快照是做交易的開頭，且已確認指令碼順利完成時，才。 如果 JavaScript 擲回錯誤，被復原交易。 如需詳細資訊，請參閱[DocumentDB 伺服器端程式設計](documentdb-programming.md)。

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>如何將 DocumentDB 大量插入文件？ 
有三種方法可以將 DocumentDB 大量插入文件︰

- 資料移轉工具，[將資料匯入至 DocumentDB](documentdb-import-data.md)所述。
- [大量新增文件檔案總管的文件](documentdb-view-json-document-explorer.md#BulkAdd)中所述 Azure 入口網站中的文件檔案總管。
- 預存程序， [DocumentDB 伺服器端程式設計](documentdb-programming.md)中所述。

### <a name="does-documentdb-support-resource-link-caching"></a>不支援資源連結快取 DocumentDB 嗎？
是，因為 DocumentDB RESTful 服務，資源連結是不變，可快取。 針對文件或集合及更新其本機複製只變更的伺服器版本具有等任何資源讀取 DocumentDB 用戶端可以指定 「 If 無-比對 「 頁首。 

### <a name="is-a-local-instance-of-documentdb-available"></a>提供 DocumentDB 的本機執行個體？
這一次執行 DocumentDB 無法使用。 您可以追蹤本機模擬器] 和 [投票的[意見反應](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance)上的狀態。


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
