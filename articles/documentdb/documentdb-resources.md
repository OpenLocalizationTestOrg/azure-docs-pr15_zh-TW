<properties 
    pageTitle="DocumentDB 階層式資源模型和概念 |Microsoft Azure" 
    description="深入了解 DocumentDB 的階層式模型的資料庫、 集合、 使用者定義函數 (UDF)、 文件、 管理資源，及其他內容的權限。"
    keywords="階層式模型，documentdb azure，Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>DocumentDB 階層式資源模型和概念

DocumentDB 管理資料庫實體稱為 「**資源**」。 邏輯 URI 唯一識別每個資源。 您可以與使用標準 HTTP 動詞、 要求/回應標頭和狀態碼的資源進行互動。 

閱讀本文，您便可以回答下列問題︰

- 什麼是 DocumentDB 的資源模型？
- 什麼是系統定義而不是使用者定義資源的資源？
- 如何處理資源？
- 如何使用集合？
- 如何運作使用預存程序、 引動程序及使用者定義函數 (Udf)？

## <a name="hierarchical-resource-model"></a>階層式資源模型
如如下圖所示，DocumentDB 階層式**資源模型**所組成的資源資料庫的帳戶，為每一個定位透過邏輯化和穩定 URI 下。 資源的一組會被稱為的**摘要**本文中。 

>[AZURE.NOTE] DocumentDB 提供高效率的 TCP 通訊協定也是在其通訊模型中，可透過[.NET 用戶端 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)RESTful。

![DocumentDB 階層式資源模型][1]  
**階層式資源模型**   

若要開始使用的資源，您必須[建立 DocumentDB 資料庫帳戶](documentdb-create-account.md)使用 Azure 訂閱。 資料庫帳戶最多可以包含一組**資料庫**，每個包含多個**集合**] 中，每個包含**預存程序，觸發程序，Udf，文件**和相關的**附件**（[預覽] 功能）。 資料庫也有相關聯的**使用者**，都有一組的**權限**來存取集合、 預存程序、 引動程序、 Udf、 文件或附件。 雖然資料庫、 使用者、 權限和集合已知的結構描述系統定義的資源，文件和附件包含任意，使用者定義 JSON 內容]。  

|資源   |描述
|-----------|-----------
|資料庫帳戶   |資料庫帳戶是與資料庫及固定的量的 blob 儲存體附件 （[預覽] 功能） 的一組相關聯。 您可以建立一或多個資料庫帳戶使用 Azure 訂閱。 如需詳細資訊，請造訪我們的[價格頁面](https://azure.microsoft.com/pricing/details/documentdb/)。
|資料庫   |資料庫是邏輯的分割整個集合的文件存放容器。 也是使用者容器。
|使用者   |設定權限的範圍邏輯命名空間。 
|權限 |關聯與特定的資源的存取權的使用者授權權杖。
|集合 |集合是 JSON 文件和相關聯的 JavaScript 應用程式邏輯的容器。 集合是計費的實體[成本](documentdb-performance-levels.md)由集合相關聯的效能層級的位置。 集合可以延伸一或多個磁碟分割區/伺服器，且可以調整，以處理幾乎沒有限制的區的儲存空間或處理量。
|預存程序   |撰寫 JavaScript 登錄的集合，並在資料庫引擎交易執行應用程式邏輯。
|觸發程序    |JavaScript 執行之前或之後 [插入]，以撰寫的應用程式邏輯取代或刪除作業。
|UDF    |撰寫 JavaScript 應用程式邏輯。 Udf 可讓您模型自訂查詢運算子，藉此延伸核心 DocumentDB 查詢語言。
|文件   |使用者定義的 （任意） JSON 內容。 根據預設，沒有結構描述，必須定義，也可提供給新增至集合的所有文件需要次要索引。
|（預覽版本）附件   |附件的方式是包含參照與相關聯的中繼資料的外部的 blob 媒體的特殊文件。 開發人員可以選擇以管理 DocumentDB blob 或將其儲存的外部 blob 服務提供者，例如 OneDrive 或 Dropbox 等。 


## <a name="system-vs-user-defined-resources"></a>使用者定義的資源與系統
資源，例如帳戶資料庫、 資料庫、 集合、 使用者、 權限、 預存程序、 引動程序及 Udf-所有已固定的結構描述，以及稱為系統資源。 相反地，資源，例如文件和附件有無限制的結構描述，然後為使用者定義資源的範例。 在 DocumentDB 系統和使用者定義的資源是表示和管理標準相容 json。 所有的資源，系統或使用者定義，有下列通用屬性。

> [AZURE.NOTE] 所有系統都產生的屬性資源中的筆記會加上底線 (_) 在其 JSON 表示。

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>屬性</strong></p></td>
            <td valign="top"><p><strong>使用者可設定或系統產生？</strong></p></td>
            <td valign="top"><p><strong>用途</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>系統產生</p></td>
            <td valign="top"><p>系統產生，唯一和階層式資源的識別碼</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>系統產生</p></td>
            <td valign="top"><p>etag 樂觀控制項所需的資源</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>系統產生</p></td>
            <td valign="top"><p>上次更新的時間戳記的資源</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>系統產生</p></td>
            <td valign="top"><p>唯一定位的資源 URI</p></td>
        </tr>
        <tr>
            <td valign="top"><p>識別碼</p></td>
            <td valign="top"><p>系統產生</p></td>
            <td valign="top"><p>使用者定義的唯一的名稱 （以相同的磁碟分割關鍵值） 的資源。 如果使用者未指定的識別碼，識別碼會產生系統</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>資源線表示法
DocumentDB 不強迫 JSON 標準或特殊編碼; 以採取任何專屬的副檔名其運作方式與標準相容的 JSON 文件。  
 
### <a name="addressing-a-resource"></a>處理資源
所有資源都是 URI 定址。 資源的**_self**屬性的值代表資源相對 URI。 包含的 URI 格式 /\<摘要\>/ {_rid} 路徑區段︰  

|_Self 的值 |描述
|-------------------|-----------
|/dbs   |資料庫的帳號] 之下的資料庫的摘要
|/dbs/ {dbname 引數。  |具有相符的值 {dbname 引數} 識別碼
|{dbname 引數} /dbs/ /colls/   |在 [資料庫的集合的摘要
|{dbname 引數} /dbs/ /colls/ {collName} |比對值 {collName} 識別碼的集合
|{dbname 引數} /dbs/ /colls/ {collName} / 文件    |文件集合下的摘要
|/dbs/ {dbname 引數} {collName} /colls/ /docs/ {docId}    |[文件識別碼比對值 {文件}
|{dbname 引數} /dbs/ /users/   |使用者在資料庫的摘要
|{dbname 引數} /dbs/ /users/ {使用者識別碼}   |使用者識別碼比對值 {使用者}
|{dbname 引數} /dbs/ /users/ {使用者識別碼} / 權限   |在 [使用者的權限的摘要
|/dbs/ {dbname 引數} {使用者識別碼} /users/ /permissions/ {permissionId}    |比對值 {權限} 識別碼的權限
  
每個資源都有唯一的使用者定義名稱公開透過 [識別碼] 屬性。 附註︰ 文件，如果使用者未指定的識別碼，我們支援的 Sdk 會自動產生的唯一識別碼，文件。 Id 是使用者定義字串中的特定的父項資源內容都是唯一的達 256 個字元。 

每個資源也有系統產生的階層式資源識別項 （也稱為 RID），可透過 _rid 屬性。 RID 編碼整個指定資源的階層，用於分散式的方式強迫參考完整性的方便內部表示法。 RID 是唯一的資料庫帳戶內，它內部用 DocumentDB 有效路由，而不需要交叉磁碟分割的查閱。 _Self 和 _rid 屬性的值是資源的替代和標準表示。 

DocumentDB REST Api 支援資源的處理，以及識別碼] 和 [_rid 屬性路由的要求。

## <a name="database-accounts"></a>資料庫帳戶
您可以提供使用 Azure 訂閱的一或多個 DocumentDB 資料庫帳戶。

您可以[建立及管理 DocumentDB 資料庫帳戶](documentdb-create-account.md)透過在[http://portal.azure.com/](https://portal.azure.com/)Azure 入口網站。 建立及管理資料庫帳戶需要系統管理存取權，而且只能 Azure 訂閱底下執行。 

### <a name="database-account-properties"></a>資料庫帳戶內容
佈建與管理資料庫帳戶的一部分，您可以設定，並閱讀下列屬性︰  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>屬性名稱</strong></p></td>
            <td valign="top"><p><strong>描述</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>一致性原則</p></td>
            <td valign="top"><p>設定此屬性，以設定您的資料庫帳號] 之下的所有集合的預設一致性層級。 您可以在每一個要求來使用的 [x-ms-一致性-層級] 要求標頭上的一致性層級會覆寫。 <p><p>請注意，此屬性僅適用於<i>使用者定義的資源</i>。 已定義的資源設定為支援的所有系統讀取/查詢強式的一致性。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>授權索引鍵</p></td>
            <td valign="top"><p>這些是主要和次要母片及唯讀的按鍵提供的所有資源資料庫的帳號] 之下的管理權限。</p></td>
        </tr>
    </tbody>
</table>

請注意，除了佈建、 設定和管理您的資料庫帳戶從 Azure 入口網站，您也以程式設計方式可以建立及使用[Azure DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx) ，以及[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)管理 DocumentDB 資料庫帳戶。  

## <a name="databases"></a>資料庫
DocumentDB 資料庫會是邏輯容器的一個或多個集合，使用者，如下圖所示。 您可以建立資料庫 DocumentDB 資料庫帳戶主旨優惠限制的任何數字。  

![資料庫帳戶和集合階層式模型][2]  
**資料庫是邏輯的使用者和集合容器**

資料庫可以包含分割表單的文件，其中包含的交易網域的集合，幾乎所有不受限制的文件儲存空間。 

### <a name="elastic-scale-of-a-documentdb-database"></a>彈性的小數位數 DocumentDB 資料庫
依預設 – 範圍到 petabytes SSD 基礎的文件儲存和能夠處理量的幾個 GB 彈性 DocumentDB 資料庫。 

與傳統 RDBMS 中的資料庫，不同的資料庫中 DocumentDB 不限定為一部電腦。 使用 DocumentDB，應用程式的縮放比例需求的成長，您可以建立更多的集合、 資料庫或兩者。 實際上，Microsoft 內各種第一方應用程式已經使用 DocumentDB 消費者的縮放比例使用的文件儲存空間 tb 建立太大 DocumentDB 資料庫集合中的每個含有千分位。 您可以放大或縮小資料庫藉由新增或移除以符合您的應用程式的縮放比例需求的集合。 

您可以建立集合主旨優惠資料庫中的任何數字。 每個集合有 SSD 備份的儲存空間，根據所選的效能層為您佈建處理量。

DocumentDB 資料庫也是容器的使用者。 為使用者，請在 [開啟] 會提供更細緻的授權和存取集合、 文件和附件的權限集邏輯的命名空間。  
 
就跟 DocumentDB 資源模型中其他資源資料庫可以建立、 取代、 刪除、 閱讀或列舉輕鬆地使用[Azure DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)或任何[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 DocumentDB 保證供讀取或查詢資料庫資源的中繼資料的強式一致性。 自動刪除資料庫，可確保您無法存取的任何集合或其包含的使用者。   

## <a name="collections"></a>集合
DocumentDB 集合是 JSON 文件的容器。 集合也是單位的交易和查詢的小數位數。 

### <a name="elastic-ssd-backed-document-storage"></a>彈性 SSD 基礎的文件儲存空間
集合實際上彈性-其自動越來越淡化顯示，當您新增或移除文件。 集合邏輯的資源，而且可以跨越一或多個實體磁碟分割區或伺服器。 根據儲存空間大小和集合的能夠處理量 DocumentDB 取決於集合中的分割的數目。 DocumentDB 中的每個資料分割有固定的 SSD 備份，相關聯的儲存空間量，而且複寫的可用性。 磁碟分割管理完全受 Azure DocumentDB，而且您不需要撰寫複雜的程式碼或管理您的分割。 DocumentDB 集合是**幾乎沒有限制**儲存及處理量。 

### <a name="automatic-indexing-of-collections"></a>自動編製索引作業的集合
DocumentDB 是不是則為 true 的結構描述空閒資料庫系統。 它不假設或需要任何結構描述 JSON 文件。 當您新增文件集合時，會 DocumentDB 自動索引並有可供您的查詢。 自動編製索引的文件而不需要結構描述或次要索引是 DocumentDB 重要功能，而且會啟用寫入最佳化、 鎖定空閒和記錄結構化索引進行的維修作業技巧。 DocumentDB 支援持續的大量的極快速寫入時仍做一致的查詢。 文件和 [索引的儲存空間用來計算每個集合所使用的儲存空間。 您可以控制儲存和效能折衷方案與編製索引設定集合編製索引的原則相關聯。 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>設定集合的編製索引的原則
編製索引的每個集合原則可讓您進行效能及儲存折衷方案與編製索引作業相關聯。 下列選項可提供給您屬於編製索引的設定︰  

-   選擇是否集合會自動建立索引的文件或不。 根據預設，所有文件會自動建立索引。 您可以選擇要關閉自動編製索引作業，然後選擇性地將特定的文件新增至索引。 相反地，您可以選擇性地選擇排除特定的文件。 您可以設定為 true 或 false 的集合 indexingPolicy 上的 [自動] 屬性，然後插入、 取代或刪除文件時使用的 [x-ms-indexingdirective] 要求標頭達到此目標。  
-   選擇是否要包含或排除在索引的特定路徑或您的文件中的模式。 您可以達到此目標設定 includedPaths excludedPaths 集合的 indexingPolicy 上，分別。 您也可以設定儲存和效能折衷方案的特定路徑模式的範圍和雜湊查詢。 
-   選擇同步 （一致） 和非同步 （延遲） 索引更新。 根據預設，更新索引同步每個插入、 取代或刪除文件集合。 這可讓執行相同的一致性層級的文件讀取的查詢。 當 DocumentDB 寫入最佳化，且支援持續的區的文件寫入並同步索引維護與伺服一致的查詢時，您可以設定某些延遲更新其索引的集合。 延遲編製索引作業提升寫入效能進一步和非常適合用於主要閱讀大量集合的大量 ingestion 案例。

變更編製索引的原則可執行放入集合上。 如下透過用[戶端 SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)， [Azure 入口網站](https://portal.azure.com)或[Azure DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)。

### <a name="querying-a-collection"></a>查詢集合
集合中的文件可以有任意結構描述，您可以查詢集合中的文件，但不提供任何結構描述或事前次要索引。 您可以查詢使用的[DocumentDB SQL 語法](https://msdn.microsoft.com/library/azure/dn782250.aspx)，提供的 rtf 階層式、 關聯性，並空間運算子和擴充透過 JavaScript 型 Udf 的集合。 模型 JSON 的文件為樹的樹狀節點標籤可讓您 JSON 文法。 這被利用都 DocumentDB 的自動編製索引技巧，以及 DocumentDB 的 SQL 方言。 DocumentDB 查詢語言是由三個主要層面所組成︰   

1.  對應 naturally 包括階層式的查詢和預測樹狀結構的查詢作業一小群。 
2.  關聯式作業包括撰寫、 篩選、 計帳、 彙總及自我聯結子集。 
3.  純粹 JavaScript 依據 Udf 搭配使用的 （1） 和 (2)。  

DocumentDB 查詢模型嘗試之間的功能與效率簡易性取得平衡。 原生 DocumentDB 資料庫引擎編譯，並執行 SQL 查詢陳述式。 您可以查詢使用[Azure DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)或其他[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)的集合。 .NET SDK 隨附 LINQ 提供者。

> [AZURE.TIP] 您可以嘗試 DocumentDB，並執行 SQL 查詢中[查詢遊樂場](https://www.documentdb.com/sql/demo)我們資料集。

### <a name="multi-document-transactions"></a>多重文件的交易
資料庫交易提供安全且可預測的程式設計模型用來處理資料的同時變更。 在 RDBMS，撰寫商務邏輯的傳統方式是撰寫**預存程序**及/或**引動程序**，並將它運送到交易執行的資料庫伺服器。 在 [RDBMS，應用程式設計者，才能處理兩種不同的程式設計語言︰ 

- （非交易） 程式設計語言 （例如 JavaScript、 Python、 C#、 Java 等） 的應用程式
- T-交易程式設計語言的原生資料庫執行 SQL

深層承諾 JavaScript 及 JSON 直接在資料庫引擎，一定 DocumentDB 提供直覺式的程式設計模型執行 JavaScript 基礎應用程式邏輯直接預存程序和引動程序的集合。 如此一來下列其中一項︰

- 控制有效率的並行實作，復原自動編製索引的 JSON 物件圖形直接在資料庫引擎
- 自然表達控制流程、 變數範圍、 工作分派和整合的例外處理直接而言程式設計語言 JavaScript 資料庫交易的基本項目

註冊集合層級的 JavaScript 邏輯然後可以發行指定集合的文件的資料庫作業。 DocumentDB 隱含換行根據 JavaScript 程序和引動程序中快照集隔離環境 ACID 交易儲存過的文件集合中。 執行期間，如果 JavaScript 擲回例外狀況，然後整個交易中止。 產生的程式設計模型是非常簡單尚未強大。 JavaScript 開發人員會收到 「 長期 」 的程式設計模型時仍在使用其熟悉的語言建構和文件庫的基本項目。   

效能和交易的執行文件集合的資料庫作業，可讓直接在資料庫引擎緩衝集區相同位址空間中執行 JavaScript 的能力。 此外，DocumentDB 資料庫引擎進行 JSON 深承諾，並 JavaScript 排除任何阻抗不相符的應用程式類型系統和資料庫。   

建立之後的集合，您可以使用[Azure DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)或其他[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)集合註冊預存程序，Udf 引動程序。 註冊後，您可以參照，並執行它們。 請考慮下列預存程序完全撰寫 JavaScript、 以下程式碼使用兩個引數 （活頁簿名稱和作者名稱） 建立新的文件、 查詢文件和更新它 – 所有隱含 ACID 交易中。 在任何點執行期間，如果 JavaScript 例外狀況時，整個交易中止。

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

用戶端可以 「 出貨 「 資料庫交易執行透過 HTTP 張貼的上述 JavaScript 邏輯。 如需有關使用 HTTP 方法的詳細資訊，請參閱[使用 DocumentDB 資源 RESTful 互動](https://msdn.microsoft.com/library/azure/mt622086.aspx)。 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


請注意，JSON 和 JavaScript，這些了解資料庫，因為沒有任何類型系統不相符，」 或對應 」 或所需的程式碼產生個神奇。   

預存程序與觸發程序互動的集合，並透過明確定義的物件模型，其會顯示目前的集合內容集合中的文件。  

集合 DocumentDB 可以建立、 刪除、 閱讀或列舉輕鬆地使用[Azure DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)或任何[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 DocumentDB 一律會提供強式一致性供讀取或查詢集合的中繼資料。 自動刪除集合，可確保您不能存取任何文件、 附件、 預存程序引動程序，並 Udf 內。   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>預存程序、 引動程序及使用者定義函數 (UDF)
上一節所述，您可以撰寫應用程式直接在資料庫引擎交易中執行的邏輯。 應用程式邏輯可以完全撰寫 JavaScript，並可以模型預存程序、 觸發程序或 UDF。 JavaScript 程式碼中的預存程序或觸發程序可以插入、 取代、 刪除已讀取] 或 [查詢集合中的文件。 另一方面，UDF 中的 JavaScript 無法插入、 取代或刪除文件。 Udf 列舉查詢的結果集的文件，並產生另一個結果集。 為多 tenancy，DocumentDB 會強制執行嚴格保留依據資源管理。 每個預存程序，觸發程序或 UDF 取得固定的配作業系統資源執行其工作。 此外，預存程序、 引動程序或無法針對外部 JavaScript 文件庫連結，並會黑名單超出配置給他們的資源預算。 您可以註冊，請使用 REST Api 移除預存程序、 引動程序或註冊集合。  在註冊時預存程序、 觸發程序或 UDF 預先編譯並儲存為稍後取得執行位元組程式碼。 下一節說明如何使用 DocumentDB JavaScript SDK 註冊、 執行、 和解除登錄預存程序與觸發程序，UDF。 JavaScript SDK [DocumentDB REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)是簡單的包裝紙。 

### <a name="registering-a-stored-procedure"></a>登錄預存程序
註冊預存程序建立新的預存程序資源透過 HTTP 張貼集合上。  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>執行預存程序
發行 HTTP 張貼現有的預存程序資源針對邀請內文中的程序傳遞的參數是預存程序執行。

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>取消註冊預存程序
只要完成發出針對現有的預存程序資源 HTTP 刪除解除登錄預存程序。   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>註冊測試觸發程序
註冊的觸發程序是透過 HTTP 張貼集合上建立新的觸發程序資源。 您可以指定觸發程序是否前置或文章觸發程序和作業的類型，可與 （例如建立、 取代、 刪除或全部） 相關聯。   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>執行測試觸發程序
執行的觸發程序是由時發出文章/放入刪除要求的文件資源透過要求標頭指定現有的觸發程序的名稱。  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>取消註冊測試觸發程序
只要完成透過發行針對現有的觸發程序資源 HTTP 刪除解除登錄觸發程序。  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>註冊 UDF
註冊 UDF 的方法是透過 HTTP 張貼集合上建立新的 UDF 資源。  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>執行查詢的一部分 UDF
UDF 可以指定 SQL 查詢的一部分，而且是用來延伸核心[DocumentDB 的 SQL 查詢語言](https://msdn.microsoft.com/library/azure/dn782250.aspx)。

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>取消註冊 UDF 
只要完成發出針對現有的 UDF 資源 HTTP 刪除解除登錄 UDF。  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

雖然上述的程式碼片段顯示註冊 （文章）、 移除註冊 （放入），讀取清單 （取得） 和執行 （文章） 透過[DocumentDB JavaScript SDK](https://github.com/Azure/azure-documentdb-js)，您也可以使用[REST Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)或其他[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 

## <a name="documents"></a>文件
您可以插入、 取代、 刪除、 讀取、 列舉及查詢集合中的任意 JSON 文件。 DocumentDB 不強制任何結構描述，而且不需要次要索引才支援查詢整個集合中的文件。   

正在真正開啟的資料庫服務，DocumentDB 設計任何特殊的資料類型 （例如 「 日期時間） 不或特定編碼 JSON 文件。 請注意，DocumentDB 不需要任何特殊 JSON 慣例，若要編訂各種不同的文件; 間的關聯DocumentDB 的 SQL 語法提供強大不含任何特殊的註釋或需要編訂文件使用之間的關聯性的查詢和專案文件的運算子辨別屬性的階層式和關聯式查詢。  
 
為所有其他的資源，文件可以建立，取代、 刪除、 讀取、 列舉和查詢輕鬆地使用 REST Api 或任何[用戶端 Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 刪除文件立即釋放空間對應到所有的巢狀附件的配額。 讀取的一致性層級的文件後面的一致性原則資料庫帳戶。 可覆寫此原則，根據您的應用程式的資料一致性需求每個要求基礎。 查詢時的文件，了解的一致性遵循設定集合上的索引模式。 針對 「 一致 」，這是依照帳戶的一致性原則。 

## <a name="attachments-and-media"></a>附件和媒體
>[AZURE.NOTE] 附件和媒體的資源可預覽功能。
 
DocumentDB 可讓您儲存二進位 blob/媒體 DocumentDB 或遠端媒體存放區。 您也可以代表而言特殊的文件，稱為附件媒體的中繼資料。 以 DocumentDB 附件的方式是參照媒體/blob 儲存在其他位置的特殊 (JSON) 文件。 附件的只是選擇性的文件會擷取儲存在遠端媒體儲存媒體的中繼資料 （例如位置、 作者等）。 

請考慮使用 DocumentDB 儲存筆跡標註社交讀取應用程式及中繼資料，包括註解，醒目提示，書籤、 評等讚 dislikes 等相關聯的特定使用者的電子書。   

-   活頁簿本身的內容儲存媒體存放區中提供的 DocumentDB 資料庫帳戶的組件] 或 [遠端媒體市集。 
-   應用程式可能會將每個使用者的中繼資料儲存為不同的文件--例如 /colls/joe/docs/book1 參照文件中儲存於 book1 王的中繼資料。 
-   指向 [指定活頁簿的使用者內容頁面的附件會儲存在對應的文件例如 /colls/joe/docs/book1/chapter1 /colls/joe/docs/book1/chapter2 等。 

請注意，上述範例會使用易記的識別碼，在傳達資源階層。 資源被存取透過 REST Api，透過唯一識別碼。 

由 DocumentDB 的媒體，附件的 _media 屬性會參考其 uri 的媒體。 可確保 DocumentDB 回收來收集媒體時的所有未完成的參考會遭到捨棄。 DocumentDB 自動產生附件，當您上傳的新媒體，並填入指向要新增媒體 _media。 如果您選擇要儲存由您 （例如 OneDrive、 Azure 儲存體，DropBox 等） 管理遠端 blob 儲存體中的媒體，還是可以使用附件參照媒體。 在此情況下，您會建立您自己的附件，並填入其 _media 屬性。   

與其他資源，附件可以建立、 取代、 刪除、 閱讀或列舉輕鬆地使用 REST Api 或任何用戶端 Sdk。 與文件附件的讀的一致性層級會遵循一致性原則資料庫帳戶。 可覆寫此原則，根據您的應用程式的資料一致性需求每個要求基礎。 查詢時的附件，了解的一致性遵循設定集合上的索引模式。 針對 「 一致 」，這是依照帳戶的一致性原則。 
 
## <a name="users"></a>使用者
DocumentDB 使用者代表邏輯的命名空間群組權限。 使用者可能對應 DocumentDB 使用者的身分識別管理系統或預先定義的應用程式的角色。 DocumentDB，為使用者只要表示抽象，將一組權限] 之下的資料庫。   

實作多 tenancy 應用程式中，您可以建立使用者在對應的 DocumentDB 您實際的使用者或應用程式的租用戶。 然後，您可以建立針對指定的使用者會對應到 access 會控制各種集合、 文件、 附件等的權限。   

為您的應用程式需要不按比例縮放與您的使用者等比級數，您可以採用晶怪幾種方式可以在您的資料。 您可以建立模型每位使用者，如下所示︰   

-   每個使用者會對應至資料庫。
-   每位使用者對應至集合。 
-   對應到多個使用者的文件移至專屬的集合。 
-   對應到多個使用者的文件移至一組的集合。   

無論您選擇的特定 sharding 策略，您可以為 DocumentDB 資料庫中的使用者模型實際使用者，並建立關聯資訊粗略每位使用者的權限。  

![使用者的集合][3]  
**Sharding 策略和模型使用者**

所有其他的資源，例如 DocumentDB 中的使用者可以建立、 取代、 刪除、 閱讀或列舉輕鬆地使用 REST Api 或任何用戶端 Sdk。 DocumentDB 一律會提供強式一致性供讀取或查詢的 [使用者資源中繼資料。 很重要的一點，會自動刪除使用者可確保您無法存取其包含的權任何的限。 雖然 DocumentDB 回收的權限的配額做為背景中已刪除的一部分，刪除權限是使用者的提供立即再次供您使用。  

## <a name="permissions"></a>權限
從 access 控制項觀點來看，例如資料庫帳戶時，資源資料庫使用者與權限會被視為*管理*資源因為這些需要系統管理權限。 另一方面，包括集合、 文件、 附件、 預存程序、 引動程序，以及 Udf 的資源範圍限定在指定的資料庫，而且視為*應用程式資源*。 對應至兩種類型的資源和存取 （也就是系統管理員和使用者） 的角色，授權模型定義兩種類型的*便捷鍵*︰*主索引鍵*和*資源鍵*。 主索引鍵是資料庫帳戶，並提供給開發人員 （或系統管理員） 誰佈建資料庫帳戶。 此主索引鍵有管理員語意可用於授權存取系統管理和應用程式的資源。 相反地，資源金鑰是允許存取*特定*的應用程式資源細緻的便捷鍵。 因此，它會擷取資料庫的使用者與權限的使用者有特定的資源 （例如集合、 文件、 附件、 預存程序、 觸發程序或 UDF） 之間的關係。   

若要取得資源金鑰唯一的方法是建立在 [指定的使用者權限資源。 請注意，才能建立或擷取權限，必須在授權標頭中呈現主索引鍵。 權限資源繫結資源與使用者存取。 建立權限資源之後, 使用者只需呈現相關聯的資源金鑰，才能存取相關資源。 因此，資源鍵可視為權限資源的邏輯化和精簡表示法。  

與其他資源，DocumentDB 中的權限可建立、 取代、 刪除、 閱讀或列舉輕鬆地使用 REST Api 或任何用戶端 Sdk。 DocumentDB 一律會提供強式一致性供讀取或查詢的中繼資料的權限。 

## <a name="next-steps"></a>後續步驟
進一步瞭解使用中[RESTful 互動 DocumentDB 資源](https://msdn.microsoft.com/library/azure/mt622086.aspx)HTTP 命令來使用的資源。


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

