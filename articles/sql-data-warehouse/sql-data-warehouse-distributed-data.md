<properties
   pageTitle="資料和分佈的人連線平行處理 (MPP) SQL Data Warehouse 及系統 Parallel Data Warehouse 資料表選項 |Microsoft Azure"
   description="瞭解資料如何分散人連線平行處理 (MPP) 和發佈 Azure SQL Data Warehouse 和 Parallel Data Warehouse 中的表格的選項。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>分散的資料和分散式的表格的人連線平行處理 (MPP)

瞭解如何分佈 Azure SQL Data Warehouse 和 Parallel Data Warehouse，也就是 Microsoft 的人連線平行處理 (MPP) 系統中的使用者資料。 設計您的資料倉庫有效地使用分散式的資料可協助您達成的查詢處理 MPP 結構的優點。 幾個資料庫設計選項可以有明顯改善查詢效能的影響。  

>[AZURE.NOTE] Azure SQL Data Warehouse 和 Parallel Data Warehouse 使用相同的人連線平行處理 (MPP) 設計，但由於基礎平台有幾項差異。 SQL Data Warehouse 為服務 (PaaS) Azure 上執行的是平台。 Parallel Data Warehouse 執行，在分析平台系統 （) 這是在 Windows 伺服器執行的內部部署應用裝置。

## <a name="what-is-distributed-data"></a>什麼是分散式的資料？

在 SQL Data Warehouse 和 Parallel Data Warehouse，分散式的資料指的是儲存在多個位置 MPP 系統的使用者資料。 每個位置作為獨立的儲存空間，其部分資料上執行的查詢處理單位。 分散的資料是達到高的查詢效能的同時執行查詢的基礎。

若要散發資料，資料倉庫會將使用者資料表的每個資料列指派給分散式位置。  您可以發佈具有雜湊通訊方法或循環方法的資料表。 CREATE TABLE 陳述式中指定的通訊方法。 

## <a name="hash-distributed-tables"></a>雜湊分佈的表格
  
下圖說明如何完整 （非分散式資料表） 會儲存為雜湊分散式表格。 確定函數會指派一個分配歸屬每個資料列。 在表格定義，其中一個資料行指定為通訊群組資料行。 雜湊函數會使用 [通訊] 欄中的值分配給每個資料列。

有的通訊群組] 資料行的選取範圍的效能考量，例如 「 distinctness 與資料 skew 查詢類型的執行系統上。
  
![分散對齊表格](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分散對齊表格")  
  
-   每個資料列屬於一個通訊群組。  
  
-   確定雜湊演算法會將每個資料列指派給一個通訊群組。  
  
-   每個通訊群組的資料表資料列數目不盡相同，如下圖所示，不同的表格大小。

## <a name="round-robin-distributed-tables"></a>循環分散式的表格

循環分散式的表格指定每個資料列，讓分配連續的方式散發列。 快速將資料載入循環表格，但查詢效能可能會變慢。  加入循環表格通常需要 reshuffling 要啟用查詢產生正確的結果，需要時間的資料列。

## <a name="distributed-storage-locations-are-called-distributions"></a>稱為散佈分散式的儲存位置。

每個分散式的位置稱為通訊群組。 查詢執行時平行，每個通訊群組會在其部分資料上執行 SQL 查詢。 SQL Data Warehouse 使用 SQL 資料庫執行查詢。 Parallel Data Warehouse 使用 SQL Server 執行查詢。 此無共用架構設計是以達到擴充平行運算的基礎。

### <a name="can-i-view-the-distributions"></a>我可以檢視散佈嗎？

每個通訊群組的通訊群組識別碼，並會出現在適用於 SQL Data Warehouse 和 Parallel Data Warehouse 的系統檢視。 若要查詢的效能和其他問題的疑難排解，您可以使用的通訊群組識別碼。 [系統] 檢視的清單，請參閱[MPP 系統] 檢視](sql-data-warehouse-reference-tsql-statements.md)。

## <a name="difference-between-a-distribution-and-a-compute-node"></a>通訊與運算節點之間的差異

分配的最基本單位分散式的資料儲存及處理平行查詢。 散佈會分組為運算節點。 每個運算節點追蹤一或多個散佈。  

-   分析平台系統會使用運算節點，為硬體架構及擴充功能的主要元件。 它一律會使用每個運算節點的八個散佈雜湊分散式表格的圖表中所示。 運算節點的數字，因此數散佈，取決於您購買的應用裝置運算節點的數字。 例如，如果您購買八個運算節點，您會收到 64 散佈 （8 運算節點 x 8 散佈/節點）。 

-   SQL Data Warehouse 有固定的數目的 60 散佈和彈性運算節點的數字。 使用 Azure 運算和儲存空間的資源，來實作運算節點。 依據後端服務工作量以及您指定的資料倉庫運算功能 (DWUs)，可以變更運算節點的數字。 運算節點的數字變更時，每個計算節點散佈數目也會變更。 

### <a name="can-i-view-the-compute-nodes"></a>我可以檢視運算節點嗎？

每個運算節點的節點識別碼，並會出現在適用於 SQL Data Warehouse 和 Parallel Data Warehouse 的系統檢視。  您可以尋找 node_id 中的資料行名稱開頭 sys.pdw_nodes 系統檢視，請參閱運算節點。 [系統] 檢視的清單，請參閱[MPP 系統] 檢視](sql-data-warehouse-reference-tsql-statements.md)。

## <a name="Replicated"></a>複製表格的平行 Data Warehouse 
  
適用於︰ 平行 Data Warehouse

除了使用的分散式的表格，Parallel Data Warehouse 提供複製表格的選項。 *複製表格*是儲存在各個運算節點上完整的資料表。 複製表格，就不必再使用 [加入或彙總資料表傳送其資料表的資料列之間運算節點。 由於儲存每個運算節點的完整資料表所需的額外儲存空間，只可行的小型表格複製的資料表。  
  
下圖顯示複製的資料表儲存於每個運算節點。 複製的表格會儲存在所有分派給運算節點的磁碟。 使用 SQL Server 檔案群組實作此磁碟策略。  
  
![複寫表格](media/sql-data-warehouse-distributed-data/replicated-table.png "複寫表格") 
  
## <a name="next-steps"></a>後續步驟
  
若要有效地使用分散式的表格，請參閱[SQL Data Warehouse Distributing 資料表](sql-data-warehouse-tables-distribute.md)  
  



  
