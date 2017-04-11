<properties
   pageTitle="編製索引的資料表中 SQL Data Warehouse |Microsoft Azure"
   description="開始使用中 Azure SQL Data Warehouse 編製索引的資料表。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>在 SQL Data Warehouse 編製索引的資料表

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [發佈][]
- [索引][]
- [分割][]
- [統計資料][]
- [暫時][]

SQL Data Warehouse 提供幾個編製索引的選項，包括[叢集的 columnstore 索引][]，[叢集的索引和非叢集的索引][]。  此外，它也提供沒有索引選項也稱為[堆積][]。  本文涵蓋每個索引類型，以及秘訣，可取得最大的效能不在您索引的優點。 如何在 SQL Data Warehouse 中建立表格，請參閱更多詳細資料的 [[建立表格的語法][]。

## <a name="clustered-columnstore-indexes"></a>群組直的 columnstore 索引

根據預設，SQL Data Warehouse 時未索引指定的表格來建立直的 columnstore 索引。 群組直的 columnstore 表格提供最高層級的資料壓縮以及最佳的整體查詢效能。  群組直的 columnstore 資料表通常會勝過叢集的索引或堆積資料表，並通常大型表格的最佳選擇。  基於下列原因，直的 columnstore 是當您不確定如何編製索引的資料表時的最佳起點。  

若要建立直的 columnstore 表格，只要在與子句中，指定直 COLUMNSTORE 索引或離開與子句︰

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

有幾個案例，直的 columnstore 可能會很好的選項︰

- Columnstore 資料表不支援次要非叢集索引。  不過，請考慮堆積或叢集的索引的資料表。
- Columnstore 資料表不支援 varchar （max）、 nvarchar （max） 及 varbinary （max）。  不過，請考慮堆積或群組的索引。
- Columnstore 資料表可能效率的暫時性的資料。  請考慮堆積，甚至是暫存資料表。
- 小型小於 100 萬個列的資料表。  請考慮堆積資料表。

## <a name="heap-tables"></a>堆積表格

當您暫時在 SQL Data Warehouse 登陸資料時，您可能會發現使用堆積表格會使更快速的整體程序。  這是因為堆積的載入速度比索引的資料表，並在某些情況下，從快取進行後續讀取。  如果您正在載入資料執行更多轉換之前，先階段它，載入堆積資料表會比直的 columnstore 表格資料載入更快。 此外，[暫時資料表][暫時]資料載入也都會載入速度比載入要永久儲存的資料表。  

小型查閱表格，小於 100 萬個資料列，通常堆積表格意義。  叢集 columnstore 資料表開始超過 100 萬個資料列後，達到最佳壓縮。

若要建立堆積表格，只要有子句中指定堆積︰

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>群組與非叢集索引

需要快速擷取單一資料列時，叢集的索引可能會勝過直的 columnstore 資料表。  查詢單一或很少列查閱在哪裡才能效能與極大的速度，請考慮叢集索引或叢集次要索引。  若要使用叢集的索引缺點是助益叢集的索引資料行上使用高度選擇性篩選的查詢。  若要改善其他資料行的篩選叢集索引可以新增至其他資料行。  不過，每個索引中已新增至表格會將與處理加入載入。

若要建立叢集的索引的資料表，只要有子句中指定叢集索引︰

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

若要新增的非叢集索引的資料表，只要使用下列語法︰

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] 建立索引使用時，預設會建立非叢集的索引。 此外，在資料列的儲存空間資料表 （堆積或群組直條圖索引） 只允許非叢集的索引。 這次不允許非叢集的索引叢集 COLUMNSTORE 索引的上方。


## <a name="optimizing-clustered-columnstore-indexes"></a>最佳化直的 columnstore 索引

群組直的 columnstore 表格資料中組織成區段。  具有高區段品質很重要達到最佳查詢效能的 columnstore 表格。  壓縮的資料列群組中的資料列的數字測量區段品質。  區段品質是最有至少 100 K 每個壓縮的資料列的資料列群組，並取得為每個資料列群組的方法的列數字的效能 1048576 列，這是大部分的資料列群組可以包含的資料列。

下方檢視可以建立及使用您的系統來計算每個資料列的平均資料列組成群組和識別任何子最佳的叢集 columnstore 索引。  在此檢視上的最後一欄將會產生為可以用來重建索引的 SQL 陳述式。

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

現在，您必須建立檢視中，執行此查詢以識別資料表具有資料列群組與小於 100 的 K 列。  當然，您可能要增加 100 K 臨界值，如果您正在尋找更多的最佳區段品質。 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

一旦執行的查詢，您可以開始查詢的資料，並分析您的結果。 此表格說明如何尋找您的資料列群組分析。


| 資料行                             | 如何使用此資料                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | 如果已分割表格，您可能會看到較高的開啟列群組計算。 發佈中的每個資料分割理論可能有與其相關聯的開啟列群組。 此因素分析。 已分割的小型表格無法最佳化移除所有分割這會改善壓縮。                                                                        |
| [row_count_total]                  | 資料表的 [合計] 列計數。 例如，您可以使用此值來計算百分比的壓縮狀態中的資料列。                                                                      |
| [row_count_per_distribution_MAX]   | 如果平均分佈的所有資料列有目標每個通訊群組的列數。 比較此值與 compressed_rowgroup_count。                                 |
| [COMPRESSED_rowgroup_rows]         | 總 columnstore 格式為表格的列數。                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | 如果資料列的平均數目會大幅小於列群組的資料列的最大的 #，然後請考慮使用 CTAS 或變更重建索引，重新資料                     |
| [COMPRESSED_rowgroup_count]        | 資料列中的群組 columnstore 格式的數字。 如果這個數字相對於表格非常高則 columnstore 密度很低標記。                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | 列會以邏輯方式刪除 columnstore 格式。 如果 number 高相對於表格大小，請考慮重新建立磁碟分割或重建索引，因為這會將其移除實際。 |
| [COMPRESSED_rowgroup_rows_MIN]     | 使用此 [平均] 和 [最大值欄搭配若要瞭解您 columnstore 中的資料列群組的值的範圍。 小的數字，透過載入閥值 (102,400 每對齊分割通訊群組) 建議最佳化可在資料載入                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | 為上方                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | 開啟列群組是一般資訊。 合理預期表格分配 (60) 每一個開啟的資料列群組。 過多的數字建議載入跨磁碟分割的資料。 點兩下檢查分割的策略，確保它已音效                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | 每個列群組可以做為 1048576 列的最大值。 若要查看如何完整開啟列群組是目前使用這個值                                                                 |
| [OPEN_rowgroup_rows_MIN]           | 開啟群組表示的資料或提升速度載入到資料表的前一個載入溢出此列群組剩餘的資料列。 最大值使用最小值]，若要查看的資料量 sat 在開啟的平均欄列的群組。 小型表格的可能是 100%的所有資料 ！ 在這種情況下變更要強制資料至 columnstore 重建索引。                                                                       |
| [OPEN_rowgroup_rows_MAX]           | 為上方                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | 為上方                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | 為有效檢查查看隱藏的列資料列組成群組。                                                                                                                                       |
| [CLOSED_rowgroup_count]            | 關閉的列群組的數目應該低如果任何完全看到。 關閉資料列群組可以轉換成壓縮的 rowg roups 使用變更索引...REORGANISE] 命令。 不過，這通常不需要。 已關閉的群組會自動轉換為 columnstore 列群組背景 」 tuple 搬運 「 處理程序。                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | 關閉資料列群組應有更高的填滿工資率。 關閉的列群組的填滿率很低，然後進一步分析的 columnstore 需要。                                   |
| [CLOSED_rowgroup_rows_MAX]         | 為上方                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | 為上方                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL 重建 columnstore 索引的資料表                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>不佳的 columnstore 索引品質的原因

如果您已識別資料表與品質不佳的區段，您會想要識別根本原因。  以下是不佳的區段 quaility 的一些其他常見原因︰

1. 記憶體壓力時建立索引
2. 大量的 DML 作業
3. 小型或 trickle 載入作業
4. 太多的磁碟分割區

小於最佳有 1 百萬個列的每個資料列群組的有大幅 columnstore 索引，可能會導致這些因素。  他們也可能會導致以移至 [delta] 資料列群組，而不是壓縮的列群組的資料列。 

### <a name="memory-pressure-when-index-was-built"></a>記憶體壓力時建立索引

資料列和記憶體用來處理資料列群組數量的寬度直接相關的每個壓縮的列群組的列數。  當列寫記憶體壓力下 columnstore 資料表之後時，可能會降低 columnstore 區段品質。  因此，最佳作法是讓您 columnstore 索引的資料表存取多記憶體盡可能寫入工作階段。  由於取捨記憶體和並行、 右記憶體配置指引取決於您的表格、 DWU 您已配置給您的系統的量和並行位置，您可以授權讓寫入表格資料的工作階段的每個資料列中的資料。  最佳作法是我們建議您開始使用 xlargerc，如果您使用的 DW300 或更少，largerc，如果您使用的 DW600，並 mediumrc DW400 如果您使用的 DW1000 與上方。

### <a name="high-volume-of-dml-operations"></a>大量的 DML 作業

大量更新及刪除列 DML 作業可能引入 columnstore 效率。 修改大部分的資料列群組中的資料列時，這是如此。

- 邏輯上只刪除從壓縮的列群組的資料列標示的列，為已刪除。 資料列仍會保留在壓縮的列群組，直到重建的磁碟分割或表格。
- 插入列新增至內部 rowstore 資料表稱為 delta 列群組的列。 插入的列不會轉換為 [columnstore，直到 delta 列群組已滿被標示為已關閉。 資料列群組會關閉後達到 1048576 列的最大容量。 
- 更新 columnstore 格式的資料列做為邏輯的刪除，再插入處理。 插入的列可能會儲存在 delta 存放區。

批次更新並插入超過大量閾值的每個對齊的通訊群組會直接寫入 columnstore 格式的磁碟分割 102400 列的作業。 不過，假設平均分配，您必須修改一次這個發生的多個 6.144 百萬個資料列。 如果指定的磁碟分割的資料列數目對齊分配是 [小於 102,400 列就會前往差異存放區，而且會保留有直到足夠的列已插入或修改，以關閉 [列] 群組或在重建索引。

### <a name="small-or-trickle-load-operations"></a>小型或 trickle 載入作業

小型載入的流程到 SQL Data Warehouse，則有時也稱為 trickle 載入。 它們通常表示近期穩定的正 ingested 系統的資料。 不過，此資料流現狀附近連續的列不特別大。 多資料低於大幅 columnstore 格式直接載入所需的臨界值。

在下列情況下，最好通常 Azure blob 儲存體中的第一次開啟資料，並讓其累積載入之前。 這項技巧通常稱為*微批次*。

### <a name="too-many-partitions"></a>太多的磁碟分割區

將另一個是分割上直的 columnstore 表格的影響。  前分割，SQL Data Warehouse 已將您的資料分成 60 的資料庫。  分割進一步會將您的資料。  如果您分割的資料，您會想要考慮的**每個**資料分割都需要有受益直的 columnstore 索引中至少 1 百萬個資料列。  如果您分割表格到 100 的磁碟分割區時，您的資料表必須擁有至少 6 億列受益的直的 columnstore 索引 （60 散佈*100 的磁碟分割區*有 1 百萬個資料列）。 如果您有 100 的磁碟分割表格沒有 6 億列，減少磁碟分割區的數目，或請考慮改用堆積表格。

後表格已載入含有一些資料，請依照下列步驟來找出並重新建立包含子最佳的叢集 columnstore 索引的資料表。

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>若要改善區段品質重建索引

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>步驟 1︰ 找出，或建立使用者使用正確的資源類別

若要立即改善區段品質其中一個快速的方法是重建索引。  上述檢視傳回 SQL 會傳回這可以用來重建索引的變更索引重建陳述式。  當重建索引，請確定您配置記憶體不足，無法將重建索引工作階段。  若要這麼做，請增加資源類別有重建索引的建議的最小此資料表上的權限的使用者。  無法變更使用者的資料庫擁有者的資源類別，因此如果您沒有建立使用者系統上，您將需要執行此作業。  我們建議您的最小值是 xlargerc 如果您使用的 DW300 或更少，largerc 如果您使用的 DW600，並 mediumrc DW400 如果您使用的 DW1000 與上方。

以下是如何配置給使用者的更多記憶體增加其資源類別的範例。  如需有關資源的類別，以及如何建立新的使用者，請參閱[並行及工作量管理][Concurrency]文章。

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>步驟 2︰ 與較高的資源類別使用者重建直的 columnstore 索引
登入步驟 1 中的使用者 (例如 LoadUser) 使用較高的資源類別，且會執行改變 INDEX 陳述式。  請確定此使用者已變更權限正在重建索引的資料表。  以下範例將示範如何重建整個 columnstore 索引或如何重新建立單一磁碟分割。 在大型的資料表，則同時更多實用重建索引的單一資料分割。

或者，，而不是重建索引，您無法將表格複製到使用[CTAS][]的新資料表。  最好的方式？ 大量資料中，針對[CTAS][]會通常會比快[變更索引][]。 為較小的大量資料內容，[變更索引][]更輕鬆地使用，而不會要求您更換表格。  如需如何重建索引 CTAS 的詳細資訊，請參閱下方的**Rebuilding 索引與 CTAS 分割切換**。

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

重建索引中 SQL Data Warehouse 是離線作業。  如需有關重建索引的詳細資訊，請參閱[Columnstore 索引重組][]和語法主題[變更索引][]中的 [變更索引重建] 區段。
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>步驟 3︰ 驗證直的 columnstore 區段品質改善
改善重新執行查詢區段品質不佳的識別的資料表，並確認區段品質。  如果無法改善區段品質，可能是在表格中的資料列會額外寬。  請考慮使用較高的資源類別或 DWU 重建索引。

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>重建索引與 CTAS 分割切換

此範例使用[CTAS][]並切換到重建資料表分割的磁碟分割。 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

如需詳細資訊重新建立使用磁碟分割`CTAS`，請參閱[分割][]文件。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱文章上[表格概觀][概觀]、[資料表的資料類型][的資料類型]、[發佈表格][等距分佈]、[分割表格][的磁碟分割]、[維護資料表統計資料][的統計資料]以及[暫存資料表][暫時]。  若要進一步瞭解最佳作法，請參閱[SQL 資料倉庫最佳作法][]。

<!--Image references-->

<!--Article references-->
[概觀]: ./sql-data-warehouse-tables-overview.md
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[發佈]: ./sql-data-warehouse-tables-distribute.md
[索引]: ./sql-data-warehouse-tables-index.md
[分割]: ./sql-data-warehouse-tables-partition.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[暫時]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL 資料倉庫最佳作法]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER 索引]: https://msdn.microsoft.com/library/ms188388.aspx
[堆積]: https://msdn.microsoft.com/library/hh213609.aspx
[叢集的索引和非叢集的索引]: https://msdn.microsoft.com/library/ms190457.aspx
[建立表格的語法]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore 索引重組]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[群組直的 columnstore 索引]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
