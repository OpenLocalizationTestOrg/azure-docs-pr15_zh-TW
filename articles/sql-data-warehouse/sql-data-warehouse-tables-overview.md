<properties
   pageTitle="在 SQL Data Warehouse 表格概觀 |Microsoft Azure"
   description="開始使用 Azure SQL 倉庫資料表。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>在 SQL Data Warehouse 表格概觀

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [發佈][]
- [索引][]
- [分割][]
- [統計資料][]
- [暫時][]

快速入門 SQL Data Warehouse 中建立表格很簡單。  基本[建立表格][]語法遵循最可能的常見語法已經熟悉使用其他資料庫。  若要建立表格，您只需要命名表格、 欄的名稱和定義的每個資料行資料類型。  如果您已在其他資料庫中建立表格，看起來應該不陌生給您。

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

上述範例會建立 Customers 的資料表與兩個資料行，名字和姓氏。  每個資料行定義一個 VARCHAR(25)，限制資料以 25 個字元的資料類型。  這些基本屬性的資料表，以及其他人，通常是其他資料庫相同。  資料類型所定義的每個欄，並確保資料的完整性。  可以新增索引以提升效能來減少 I/O。  分割可以新增以提升效能，當您需要修改資料。

[重新命名][ RENAME] SQL Data Warehouse 表格看起來像這樣︰

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>分散的表格

新的基本屬性，例如 SQL Data Warehouse 分散式系統簡略為**通訊群組資料行**。  [通訊] 資料行會非常什麼聽起來。  它會決定如何發佈，或除法，請在幕後資料欄。  當您未指定的通訊群組資料行建立表格時，表格會自動使用分佈的**循環**。  雖然循環資料表可能是足夠在某些情況下，定義通訊群組資料行可大幅降低移動資料查詢，因此最佳化效能期間。  請參閱[發佈表格][等距分佈]]若要進一步瞭解如何選取一個通訊群組資料行。

## <a name="indexing-and-partitioning-tables"></a>編製索引和分割表格

當您在使用 SQL Data Warehouse 變得更多進階，並且想要最佳化效能，您需要進一步瞭解表格設計。  若要深入瞭解，請參閱在[資料表的資料類型][的資料類型]、[發佈表格][等距分佈]、[編製索引的資料表][索引]和[分割表格][的磁碟分割]。

## <a name="table-statistics"></a>表格統計資料

統計資料，相當重要登出您的 SQL Data Warehouse 取得最佳效能。  由於 SQL Data Warehouse 不會還會自動建立和更新統計資料，您，如可能會發生的事 Azure SQL 資料庫，閱讀我們[的統計資料][]上的文件中有可能是最重要的文章，您閱讀以確保您獲得最佳效能，從您的查詢。

## <a name="temporary-tables"></a>暫存資料表

暫時資料表為只存在於您的登入期間，並無法看到其他使用者的資料表。  暫時表格可防止其他人看到暫存的結果，也會減少清理需要的好方法。  暫時表格也會利用本機的儲存空間，因為他們可以提供更快速地在某些作業的效能。  請參閱[暫時資料表][暫時]如需詳細資訊暫存資料表。

## <a name="external-tables"></a>外部資料表

外部資料表，也稱為 Polybase 資料表，為它可以從 SQL Data Warehouse，但點查詢從 SQL Data Warehouse 的外部資料的資料表。  例如，您可以建立外部資料表的指向 Azure Blob 儲存體上的檔案。  如需如何建立和查詢外部表格的詳細資訊，請參閱[Polybase 載入資料][]。  

## <a name="unsupported-table-features"></a>不支援的表格功能

雖然 SQL Data Warehouse 包含許多相同的表格功能所提供的其他資料庫，有一些並不支援的功能。  以下是一些資料表功能並不支援的清單。

| 不支援的功能 |
| --- |
|[識別屬性][]（請參閱[指派 surrogate 索引鍵因應措施][]）|
|主索引鍵、 唯一及核取[表格的限制式][]的外部索引鍵|
|[唯一索引][]|
|[計算資料行][]|
|[疏鬆資料行][]|
|[使用者定義類型][]|
|[順序][]|
|[觸發程序][]|
|[索引的檢視][]|
|[同義字][]|

## <a name="table-size-queries"></a>表格大小查詢

找出空間和列由資料表中每個 60 散佈，最簡單方式是使用[DBCC PDW_SHOWSPACEUSED][]。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

不過，使用 DBCC 命令可以相當限制。  動態管理檢視 (Dmv) 可讓您以查看更多詳細資料，以及讓您的查詢結果更大的控制權。  開始建立此檢視，將由我們的範例，這和其他文件中的參照。

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>表格空間摘要

此查詢會傳回表格列和空格。  很好的查詢，若要查看哪些資料表是您最大的資料表，以及其為循環或分散式雜湊。  雜湊分散式表格也會顯示 [通訊] 欄。  在大多數情況下您最大的表格應雜湊以直的 columnstore 索引。

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>依通訊類型的資料表空間

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>依索引類型的資料表空間

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>分配空間摘要

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱文章上[表格的資料類型][的資料類型]、[發佈表格][等距分佈]、[編製索引的資料表][索引]、[分割表格][的磁碟分割]、[維護資料表統計資料][的統計資料]以及[暫存資料表][暫時]。  如需瞭解最佳作法的詳細資訊，請參閱[SQL 資料倉庫最佳作法][]。

<!--Image references-->

<!--Article references-->
[概觀]: ./sql-data-warehouse-tables-overview.md
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[發佈]: ./sql-data-warehouse-tables-distribute.md
[索引]: ./sql-data-warehouse-tables-index.md
[分割]: ./sql-data-warehouse-tables-partition.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[暫時]: ./sql-data-warehouse-tables-temporary.md
[SQL 資料倉庫最佳作法]: ./sql-data-warehouse-best-practices.md
[載入 Polybase 的資料]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[建立資料表]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[識別屬性]: https://msdn.microsoft.com/library/ms186775.aspx
[指派 surrogate 索引鍵的因應措施]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[表格的限制式]: https://msdn.microsoft.com/library/ms188066.aspx
[計算資料行]: https://msdn.microsoft.com/library/ms186241.aspx
[疏鬆資料行]: https://msdn.microsoft.com/library/cc280604.aspx
[使用者定義類型]: https://msdn.microsoft.com/library/ms131694.aspx
[順序]: https://msdn.microsoft.com/library/ff878091.aspx
[觸發程序]: https://msdn.microsoft.com/library/ms189799.aspx
[索引的檢視]: https://msdn.microsoft.com/library/ms191432.aspx
[同義字]: https://msdn.microsoft.com/library/ms177544.aspx
[唯一索引]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
