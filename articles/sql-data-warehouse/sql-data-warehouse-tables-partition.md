<properties
   pageTitle="分割表格中 SQL Data Warehouse |Microsoft Azure"
   description="開始使用資料表中 Azure SQL Data Warehouse 分割。"
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
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>在 SQL 資料倉庫分割表格

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [發佈][]
- [索引][]
- [分割][]
- [統計資料][]
- [暫時][]

分割支援所有的 SQL Data Warehouse 表格類型;包括叢集的 columnstore、 叢集的索引及堆積。  所有的通訊群組類型，包括雜湊] 或 [循環分散式也支援分割。  進行磁碟分割可讓您將您的資料分成較小的群組的資料，並在大部分情況下，分割的日期資料行。

## <a name="benefits-of-partitioning"></a>資料分割的好處

分割的效益資料進行的維修作業] 與 [查詢效能。  它好處或只是載入資料的方式，與相同的資料行是否可以使用這兩種用途，因為分割才可在一欄。

### <a name="benefits-to-loads"></a>若要載入的優點

在 SQL Data Warehouse 分割的主要優點是提升的載入資料，使用的磁碟分割刪除、 切換及合併的效能。  在大多數情況下資料分割上與資料載入至資料庫的順序密切相關的日期資料行。  最大使用磁碟分割區以維護資料的優點之一，避免交易記錄。  只要插入、 更新或刪除資料可以是最簡單的方式，與一些想法，請使用 [分割載入程序期間可以大幅改善效能。

分割切換可用來快速移除或取代表格的區段。  例如，銷售交易資料表可能包含過去 36 月的只的資料。  每個月的結尾，從表格刪除最舊月份的銷售資料。  刪除舊的月份的資料使用的刪除陳述式都可以刪除這個資料。  不過，刪除大量的資料列列以刪除陳述式可以花費很長的時間，以及建立大型交易發生錯誤時，可能需要較長的時間要復原的風險。  更好的方法是直接放舊的磁碟分割的資料。  位置刪除個別的資料列花時間，刪除整個的磁碟分割花秒數。

### <a name="benefits-to-queries"></a>若要查詢的優點

分割也可以用來提升查詢效能。  如果查詢將篩選套用分割資料行，這可能會限制掃描只合格的磁碟分割可能會更小的資料子集，避免完整的資料表。  隨著直的 columnstore 索引，述詞雜訊效能優點較有幫助，但在某些情況下可查詢的優勢。  比方說，如果銷售資料表會分割成 36 個月，使用 [銷售日期] 欄位中，然後查詢篩選的銷售日期可以略過不符合篩選的磁碟分割區中搜尋。

## <a name="partition-sizing-guidance"></a>分割縮放指南

分割可以用來改善效能某些情況下，使用**太多**的磁碟分割區建立資料表可以傷害在某些情況下的效能。  這些問題並尤其直的 columnstore 資料表。  分割很有幫助，請務必瞭解何時要使用分割的磁碟分割區以建立數。  為多少磁碟分割區是太多沒有難快速規則、，取決於您的資料，分割多少會載入至同時。  但是一般法則，為想新增 10s 到 100 s 的磁碟分割區，不 1000s年。

建立分割**直的 columnstore**表格，時一定要考慮如何多個資料列會讓每個資料分割。  最佳的壓縮和直的 columnstore 表格的效能，需要有 1 百萬個資料列，每個通訊和分割的最小值。  建立分割之前，SQL Data Warehouse 已將每個資料表分成 60 分散式資料庫。  新增表格至任何分割是除了在幕後建立散佈。  如果銷售資料表包含每月的磁碟分割區 36，而且的 SQL Data Warehouse 有 60 散佈，然後銷售資料表包含 60 百萬個資料列，每個月或 21 億個資料列所有月份已填都妥時，請使用此範例中。  如果資料表包含大幅少於每個資料分割列建議最小值的資料列，請考慮使用較少的磁碟分割區設增加的每個資料分割的列數。  也請參閱[編製索引][索引]包括 SQL Data Warehouse 來評估叢集 columnstore 索引品質您可以執行的查詢。

## <a name="syntax-difference-from-sql-server"></a>從 SQL Server 的語法差異

SQL Data Warehouse 介紹這是從 SQL Server 稍有不同的磁碟分割區的簡化的定義。  分割函數及配置中未使用 SQL Data Warehouse 就和在 SQL Server 中。  不過，您只需要是找出分割資料行和邊界點。  分割的語法可能稍有不同，從 SQL Server 時, 的基本概念都相同。  SQL Server 和 SQL Data Warehouse 支援每個資料表，距攻擊的磁碟分割的一個資料分割資料行。  若要進一步瞭解分割，請參閱[分割資料表和索引][]。

下列 SQL Data Warehouse 分割[建立表格][]的陳述式的範例資料分割 OrderDateKey 欄的 FactInternetSales 資料表︰

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>移轉分割從 SQL Server

只要將 SQL Server 分割定義移轉到 SQL Data Warehouse:

- 排除 SQL Server[資料分割配置][]。
- 建立資料表中加入[partition 函數][]定義。

如果您要移轉的分割的資料表從 SQL Server 執行個體下方 SQL 可協助您詢問中每個資料分割的資料列數目。  請記住，如果在 SQL Data Warehouse 使用相同的分割資料粒度，則每個資料分割的資料列數目會減少 60 倍。  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>工作量管理

若要納入表格的磁碟分割決策的一個最後考量是[工作量管理][]。  在 SQL Data Warehouse 的工作量管理是主要的記憶體和並行管理。  在 SQL Data Warehouse 配置給每個查詢執行期間的通訊群組的最大記憶體會是受控的資源類別。  最好是您的磁碟分割區將 in consideration of 建置直的 columnstore 索引的記憶體需要的其他因素調整大小。  群組直條圖 columnstore 索引的優勢大幅配置更多記憶體時會。  因此，您會要確保重建索引分割區不渴望的記憶體。 可從預設角色，smallrc，切換到其中一個其他角色，例如 largerc 達到增加查詢可用的記憶體。

每個通訊群組的記憶體配置的詳細資訊，請查詢資源管理員動態管理檢視。 事實上您記憶體授與會小於下方的數字。 不過，這是提供程度縮放您分割的資料管理作業時，您可以使用的指導方針。  嘗試避免縮放超出超大型資源類別提供的記憶體授與您分割。 如果您分割成長限制，超出此圖您執行記憶體壓力最佳壓縮小於依次帶來的風險。

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>分割切換

SQL Data Warehouse 支援分割分割、 合併及切換。 每個這些函數是 excuted 使用[ALTER TABLE][]陳述式。

若要切換兩個資料表之間的磁碟分割區中，您必須確定磁碟分割區的對齊其個別的界限及表格定義符合。 核取的限制式無法使用強制執行的表格中的值範圍的來源資料表必須包含相同的磁碟分割界限與目標資料表。 如果這不是大小寫，然後將會失敗的磁碟分割切換時不會同步處理的磁碟分割中繼資料。

### <a name="how-to-split-a-partition-that-contains-data"></a>如何分割包含資料的磁碟分割

分割已經包含資料的磁碟分割的最有效方法是使用`CTAS`陳述式。 如果分割的資料表是群組的 columnstore 然後資料表分割必須先空白可使用分割。

以下是包含每個資料分割的一列的範例分割的 columnstore 表格︰

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] 藉由建立統計物件，我們確定該資料表中繼資料提供更精確。 如果我們省略建立統計資料時，SQL Data Warehouse 會使用預設值。 如統計資料的詳細資訊，請檢閱[統計資料][]。

我們可以接著查詢資料列計數使用`sys.partitions`類別目錄檢視︰

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

如果我們嘗試此資料表分割，我們將會看到的錯誤︰

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

訊息 35346，層級 15 狀態 1，變更的磁碟分割陳述式的線條 44 分割子句失敗，因為不是空的磁碟分割。  Columnstore 索引存在於資料表時，可以分割只空白的磁碟分割區中。 請考慮停用 columnstore 索引之前發行改變分割陳述式，然後變更磁碟分割完成後，重建 columnstore 索引。

不過，我們可以使用`CTAS`若要建立新的資料表，如要保留我們的資料。

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

允許切換為分割邊界會對齊。 這會讓我們可以接著分割一個空的分割的來源資料表。

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

所有就是我們的資料，以使用新的分割邊界對齊`CTAS`並切換至主要資料表回我們的資料

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

當您完成的資料移動最好重新整理目標資料表，以確保他們正確反映新的通訊群組，其個別的磁碟分割區中的資料的統計資料︰

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>資料表資料分割來源控制

若要避免從**rusting**您表格定義來源控制系統中，您可能要考慮下列方法︰

1. 為分割表格，但沒有磁碟分割的值建立資料表

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`部署程序的一部分資料表︰

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

此方法在來源控制項的程式碼仍會保留靜態，分割的邊界值允許動態;倉庫與發展一段時間。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱文章上[表格概觀][概觀]、[資料表的資料類型][的資料類型]、[發佈表格][等距分佈]、[編製索引的資料表][索引]、[維護資料表統計資料][的統計資料]以及[暫存資料表][暫時]。  如需瞭解最佳作法的詳細資訊，請參閱[SQL 資料倉庫最佳作法][]。

<!--Image references-->

<!--Article references-->
[概觀]: ./sql-data-warehouse-tables-overview.md
[資料類型]: ./sql-data-warehouse-tables-data-types.md
[發佈]: ./sql-data-warehouse-tables-distribute.md
[索引]: ./sql-data-warehouse-tables-index.md
[分割]: ./sql-data-warehouse-tables-partition.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[暫時]: ./sql-data-warehouse-tables-temporary.md
[工作量管理]: ./sql-data-warehouse-develop-concurrency.md
[SQL 資料倉庫最佳作法]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[分割的資料表和索引]: https://msdn.microsoft.com/library/ms190787.aspx
[變更資料表]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[建立資料表]: https://msdn.microsoft.com/library/mt203953.aspx
[partition 函數]: https://msdn.microsoft.com/library/ms187802.aspx
[分割配置]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
