<properties 
    pageTitle="簡介 DocumentDB，JSON 資料庫 |Microsoft Azure" 
    description="深入了解 Azure DocumentDB，NoSQL JSON 資料庫。 此文件資料庫建立大型資料、 彈性延展性及可用性。" 
    keywords="json 資料庫]，[文件資料庫"
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
    ms.topic="get-started-article" 
    ms.date="09/13/2016" 
    ms.author="mimig"/>

# <a name="introduction-to-documentdb-a-nosql-json-database"></a>DocumentDB 簡介︰ NoSQL JSON 資料庫

##<a name="what-is-documentdb"></a>什麼是 DocumentDB？

DocumentDB 是快速且可預期的效能、 可用性、 彈性的縮放比例、 全域通訊群組，且方便開發的內建的完整的管理 NoSQL 資料庫服務。 為結構描述空閒 NoSQL 資料庫，DocumentDB 會提供豐富且熟悉 SQL 查詢功能的一致的低延遲 JSON 資料-確保會提供您讀取的 99%，底下 10 的毫秒數和 99%您寫的服務底下 15 的毫秒數。 這些唯一的優點讓 DocumentDB 很好適合網頁，行動裝置，遊戲，IoT 和許多其他需要順暢的小數位數和全域複寫的應用程式。

## <a name="how-can-i-learn-about-documentdb"></a>如何瞭解 DocumentDB？ 

依照這三個步驟就能快速瞭解 DocumentDB，查看的實際操作︰ 

1. 觀看兩分鐘[DocumentDB 是什麼？](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/)視訊，使用 DocumentDB 的優點。
2. 觀看三個分鐘[Azure 上建立的 DocumentDB](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/)視訊，其中醒目提示如何開始使用 DocumentDB 使用 Azure 入口網站。
3. 請造訪[查詢遊樂場](http://www.documentdb.com/sql/demo)，您可以在其中逐步若要深入瞭解豐富的查詢功能在 DocumentDB 中使用不同的活動。 然後，至沙箱] 索引標籤上向執行您自己自訂的 SQL 查詢，並嘗試使用 DocumentDB。

然後，返回本文中，位置我們會深入瞭解。  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>哪些功能和重要功能會 DocumentDB 提供？  

Azure DocumentDB 提供下列重要功能與優點︰

-   **Elastically 調整處理量，儲存空間︰**輕鬆放大或縮小 DocumentDB JSON 資料庫以符合您的應用程式的需求。 您的資料會儲存在低如預期呈現延遲的實體狀態磁碟 (SSD)。 DocumentDB 支援 JSON 資料稱為可以幾乎無限儲存空間大小和能夠處理量不按比例縮放的集合，儲存容器。 您可以 elastically 調整 DocumentDB 如預期呈現效能流暢地為您的應用程式的規模擴大時。 

-   **多區域複寫︰**無障礙 DocumentDB 會將您的資料複製到所有您已與帳戶相關聯您 DocumentDB，讓您能夠開發應用程式時提供必須做的取捨之間的一致性、 可用性和效能，所有的相對應的保證需要通用存取資料的區域。 DocumentDB 提供透明區域的容錯移轉與多隸屬 Api，還可將 elastically 縮放地球上的 [處理量，儲存空間。 瞭解更多的[發佈 DocumentDB 全域的資料](documentdb-distribute-data-globally.md)。

-   **臨機查詢使用熟悉的 SQL 語法︰**內 DocumentDB 異質性 JSON 文件儲存在與查詢透過熟悉的 SQL 語法這些文件。 DocumentDB 使用免費的高度同時鎖定，記錄結構化編製索引的技術來自動建立索引所有文件內容]。 這會啟用 rtf 即時的查詢，而不需指定結構描述提示、 次要索引或檢視。 如需[查詢 DocumentDB](documentdb-sql-query.md)深入瞭解。 

-   **JavaScript 執行，在資料庫中︰**Express 應用程式邏輯為預存程序、 引動程序，以及使用標準 JavaScript 的使用者定義函數 (Udf)。 這個選項可讓您的應用程式邏輯，而不需擔心應用程式與資料庫結構描述不相符的資料進行運作。 DocumentDB 提供完整的交易執行 JavaScript 應用程式的邏輯直接資料庫引擎。 JavaScript 深整合啟用為隔離的交易的插入、 取代、 刪除和選取作業 JavaScript 程式中的執行。 如需[DocumentDB 伺服器端程式設計](documentdb-programming.md)深入瞭解。

-   **可微調一致性層級︰**選取四個定義良好的一致性層級，達到最佳取捨一致性與效能。 查詢和讀取的作業，DocumentDB 提供四種不同的一致性層級︰ 限制過時的工作階段，並最終。 這些細微、 明確定義的一致性層級可讓您進行之間的一致性、 可用性及延遲的音效折衷方案。 如需[使用最大化可用性和 DocumentDB 效能的一致性層級](documentdb-consistency-levels.md)深入瞭解。

-   **完全管理︰**不需要管理資料庫及電腦的資源。 為完全受管理的 Microsoft Azure 服務時，您不需要管理虛擬機器、 部署及設定軟體、 管理縮放比例，或處理複雜的資料層升級。 每個資料庫自動備份並防止地區失敗。 您可以輕鬆地新增 DocumentDB 帳戶，並視需要可讓您將焦點放在您的應用程式，而非作業系統和管理您的資料庫佈建容量。 

-   **開啟設計︰**快速入門使用現有的技巧和工具。 針對 DocumentDB 程式設計簡單、 平易近人，而不需要您採用新工具或依循 JSON 或 JavaScript 自訂擴充功能。 您可以存取的所有資料庫功能，包括 CRUD、 查詢和 JavaScript 處理簡單的 RESTful HTTP 介面上。 DocumentDB 會包含現有的格式、 語言和標準，提供高的值在其上的資料庫功能。

-   **自動編製索引作業︰**根據預設，DocumentDB[自動建立索引](documentdb-indexing.md)資料庫中的所有文件並不會發生的事或需要任何結構描述或建立次要索引。 不想要索引的所有項目嗎？ 別擔心，您可以[退出中 JSON 檔案路徑](documentdb-indexing-policies.md)太。

##<a name="data-management"></a>DocumentDB 如何管理資料？

Azure DocumentDB 管理 JSON 透過明確定義的資料庫資源的資料。 高可用性複寫這些資源，其邏輯 uri 唯一定址。 DocumentDB 提供簡單的 HTTP 基礎之所有資源的 RESTful 程式設計模型。 

DocumentDB 資料庫帳戶是唯一的命名空間，可讓您存取 Azure DocumentDB。 您可以建立資料庫帳戶之前，您必須 Azure 的訂閱，可讓您存取各種 Azure 服務。 

DocumentDB 內的所有資源的模型，並儲存成 JSON 文件。 管理資源為項目，哪些是 JSON 文件包含的中繼資料，與成摘要的項目集合。 項目集合都包含在其個別的摘要。

下圖顯示 DocumentDB 資源之間的關聯︰

![階層式關係 DocumentDB，NoSQL JSON 資料庫中的資源][1] 

資料庫帳戶所組成的一組資料庫，每個包含多個集合] 中，每一種可包含預存程序、 引動程序、 Udf、 文件和相關的附件。 資料庫也有相關聯的使用者，都有一組的權限存取各種其他集合、 預存程序、 引動程序、 Udf、 文件或附件。 雖然資料庫與使用者、 權限] 集合系統定義資源已知的結構描述-文件、 預存程序、 引動程序，Udf 和附件包含任意，使用者定義的 JSON 內容。  

##<a name="develop"></a>我要如何開發應用程式搭配 DocumentDB？

Azure DocumentDB 公開可以呼叫簡訊提出 HTTP/HTTPS 要求的任何語言 REST API 透過資源。 此外，DocumentDB 提供數種常見語言的程式設計文件庫。 這些文件庫簡化各種使用 Azure DocumentDB 處理詳細資料，例如地址快取、 例外狀況管理，自動重試等等。 文件庫會目前提供下列語言與平台︰  

下載 | 文件
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET 文件庫](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js 文件庫](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java 文件庫](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript 文件庫](http://azure.github.io/azure-documentdb-js/)
n/a | [伺服器端 JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python 文件庫](http://azure.github.io/azure-documentdb-python/)

進階基本建立、 讀取、 更新及刪除作業、 DocumentDB 提供豐富的 SQL 查詢介面擷取 JSON 文件和伺服器端支援交易執行 JavaScript 應用程式邏輯。 查詢和指令碼執行介面，可透過所有的平台文件庫，以及 REST Api。 

### <a name="sql-query"></a>SQL 查詢
Azure DocumentDB 支援查詢中使用關聯式、 階層和空間查詢的根目錄 JavaScript 型別系統，並使用支援的運算式中的 SQL 語言的文件。 DocumentDB 查詢語言是簡單而強大的介面，來查詢 JSON 文件。 語言支援的 ANSI SQL 文法子集，並新增 JavaScript 物件、 陣列、 物件建構和函數引動深度整合。 DocumentDB 開發人員提供沒有任何明確結構描述] 或 [編製索引的提示它查詢的模型。

使用者定義函數 (Udf) 可以使用 DocumentDB 註冊並參照屬於 SQL 查詢，藉此延伸支援自訂應用程式邏輯文法。 這些 Udf 為 JavaScript 程式撰寫及執行資料庫中。 

對於.NET 開發人員，DocumentDB 也會提供 LINQ 查詢提供者[.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx)的一部分。 

### <a name="transactions-and-javascript-execution"></a>交易和 JavaScript 執行
DocumentDB 可讓您將應用程式邏輯寫成完全撰寫 JavaScript 命名程式。 這些應用程式所註冊的集合，也可以發行指定集合中的文件的資料庫作業。 JavaScript 註冊為觸發程序、 預存程序或使用者定義函數執行。 引動程序與預存程序可以建立、 讀取、 更新及刪除文件，而使用者定義的函數執行查詢執行邏輯沒有寫入存取權的集合的一部分。

支援關聯式資料庫系統，做為新的 TRANSACT-SQL 取代 JavaScript 的概念會以模型內 DocumentDB JavaScript 執行。 快照集隔離環境 ACID 交易中執行所有 JavaScript 邏輯。 執行期間，如果 JavaScript 擲回例外狀況，然後整個交易中止。

## <a name="next-steps"></a>後續步驟
您已經有 Azure 帳戶？ 然後您可以開始使用 DocumentDB [Azure 入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)中建立[DocumentDB 資料庫帳戶](documentdb-create-account.md)。

沒有 Azure 帳戶？ 您可以︰

- 註冊[Azure 免費試用版](https://azure.microsoft.com/free/)，其中會提供您 30 天和 $200 嘗試所有 Azure 服務。 
- 如果您有訂閱 MSDN，您會有資格申請[中每個月免費 Azure 貸項總計 $150](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)上任何 Azure 服務使用。 

接著，當您準備好要瞭解詳細資訊，請造訪我們[學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)，瀏覽您可以使用所有學習資源。 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 
