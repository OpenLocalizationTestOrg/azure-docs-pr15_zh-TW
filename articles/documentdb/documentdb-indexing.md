<properties 
    pageTitle="自動在 DocumentDB 編製索引 |Microsoft Azure" 
    description="深入了解中 Azure DocumentDB 如何自動編製索引的運作。" 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>
    
# <a name="automatic-indexing-in-azure-documentdb"></a>自動在 Azure DocumentDB 編製索引

本文從[「 結構描述無關編製索引作業與 Azure DocumentDB 」](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) （英文），就會顯示在[很大的資料庫 41st 內部會議](http://www.vldb.org/2015/)之間年 8 月 31-2015 年 9 月 4 日，如和如何將簡介編製索引 Azure DocumentDB 中的運作。 

讀取此後，您會回答下列問題︰

- 如何 DocumentDB 推斷的結構描述從 JSON 文件？
- 如何會 DocumentDB 建立索引跨不同的文件？
- DocumentDB 如何執行自動在編製索引？

##<a id="HowDocumentDBIndexingWorks"></a>DocumentDB 編製索引作業的運作方式

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)是 JSON 的內建的則為 true 的結構描述空閒資料庫用途。 它不預期或需要任何結構描述或在資料索引的次要索引定義。 這個選項可讓您快速定義和持續使用 DocumentDB 應用程式的資料模型。 當您新增文件集合時，DocumentDB 自動建立索引所有文件摘要資訊使其可供您要查詢。 自動編製索引作業，可讓您儲存文件屬於完全任意結構描述，而不需擔心結構描述或次要索引。

使用排除資料庫與應用程式撰寫模型之間阻抗不相符的目標，DocumentDB 利用 JSON 簡化和結構描述規格是。 它不會假設文件，並允許 DocumentDB 集合，以在結構描述，除了執行個體特定值中的文件。 相較於其他文件的資料庫，DocumentDB 的資料庫引擎的運作方式直接層級 JSON 文法檢查]，剩餘以文件結構描述的概念，以及模糊的文件的結構和執行個體的值之間的邊界。 此，依次，讓它自動索引文件而不需要結構描述或次要索引。

在 DocumentDB 編製索引會利用 JSON 文法檢查，讓文件是**以樹表示**。 表示為樹狀結構 JSON 文件，需要建立的 parents 其餘部分中的文件下方的實際節點 dummy 根節點。 包括陣列索引 JSON 文件中的每個標籤會變成樹狀節點。 下圖說明範例 JSON 文件和其對應的樹狀表示。

>[AZURE.NOTE] 由於 JSON 自我描述即每份文件包含結構描述 （中繼資料） 和資料，例如`{"locationId": 5, "city": "Moscow"}`即可在有兩個屬性`locationId`和`city`，並且具有數值和字串的屬性值。 DocumentDB 是推斷的結構描述的文件並插入或取代，不需要您曾定義的結構描述或次要索引時將這些索引。


**為樹 JSON 文件︰**

![為樹的文件](media/documentdb-indexing/DocumentsAsTrees.png)

例如，在如上所示的範例︰

- JSON 屬性`{"headquarters": "Belgium"}`上面範例中的屬性對應至路徑/總部/比利時。
- JSON 陣列`{"exports": [{"city": “Moscow"}`，`{"city": Athens"}]}`對應至路徑`/exports/[]/city/Moscow`和`/exports/[]/city/Athens`。

使用自動編製索引作業，(1) （除非開發人員已明確設定以排除特定路徑模式編製索引的原則） 編製索引的文件樹狀目錄中每個路徑。 （若要更新的索引 （亦即，原因新增或移除節點） 的結構引導 DocumentDB 集合的文件 2） 每次更新。 其中的文件自動編製索引的主要需求，確定要編製索引並查詢文件的深的巢狀結構，請說出 10 個層級成本，一般 JSON 文件包含關鍵值組只要一層的相同。 因此標準化的路徑表示是兩個自動編製索引及查詢子系統建置基礎的基礎。

將兩者很重要的含意一致地而言路徑的結構描述和執行個體的值是以邏輯方式，就像的個別文件的兩個顯示的保留路徑之間的對應的文件索引和也可以包含該路徑文件識別碼表示樹狀目錄。 DocumentDB 會使用這項事實來建立索引樹狀目錄中的不在聯集樹代表集合中的個別文件的所有公式的結構。 DocumentDB 集合中的 [索引] 樹狀目錄會隨著時間，新的文件取得新增或更新的集合。


**樹狀目錄 DocumentDB 索引︰**

![為樹狀結構的索引](media/documentdb-indexing/IndexAsTree.png)

儘管結構描述空閒，DocumentDB 的 SQL 和 JavaScript 查詢語言提供關聯式預測和篩選器、 階層式瀏覽文件、 空間作業和 Udf 完全撰寫 JavaScript 叫用。 DocumentDB 查詢執行階段是可支援下列查詢，由於它可以直接對此索引樹狀方式呈現資料。

預設編製索引原則自動建立索引的所有文件的所有內容，並提供一致的查詢 （表示文件寫入同步更新索引）。 如何 DocumentDB 支援在 [索引] 樹狀目錄一致的更新？ DocumentDB 使用寫入最佳化，免費提供鎖定和記錄結構化索引進行的維修作業技巧。 這表示 DocumentDB 可支援持續的大量的快速寫入時仍做一致的查詢。 

DocumentDB 的編製索引作業是設計用來儲存效率和處理多 tenancy。 成本效益索引的磁碟上儲存負擔是低且可預測。 每個 DocumentDB 集合配置的系統資源的預算，也會執行索引更新。

##<a name="NextSteps"></a>後續步驟
- 下載[」 結構描述無關編製索引作業與 Azure DocumentDB 」](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，呈現 41st 內部會議上很大的資料庫，在 2015 年 9 月 4 年 8 月 31。
- [有 DocumentDB SQL 查詢](documentdb-sql-query.md)
- 進一步瞭解如何自訂 DocumentDB 索引[以下](documentdb-indexing-policies.md)
 
