<properties
   pageTitle="最佳化 SQL Data Warehouse 交易 |Microsoft Azure"
   description="撰寫有效的交易更新中 Azure SQL Data Warehouse 的最佳作法指引"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>最佳化 SQL Data Warehouse 的交易

本文說明如何最佳化您的交易程式碼時最小化的長回復風險的效能。

## <a name="transactions-and-logging"></a>交易和記錄

交易關聯式資料庫引擎的重要元件。 SQL Data Warehouse 使用交易期間資料修改。 這些交易可以是明確或隱含。 單一`INSERT`，`UPDATE`和`DELETE`陳述式都是隱含的交易的範例。 明確交易是明確地開發人員撰寫使用`BEGIN TRAN`，`COMMIT TRAN`或`ROLLBACK TRAN`，通常用來多個修改陳述式需要在單一原子單位連結在一起。 

Azure SQL Data Warehouse 認可變更資料庫交易記錄檔。 每個通訊群組都有它自己的交易記錄檔。 寫入交易記錄會自動。 有不需要進行設定。 不過，而此程序保證寫入也會導致開銷系統中。 您可以藉由撰寫交易有效率的程式碼最小化此影響。 交易有效率的程式碼廣泛可分為兩種類別。

- 盡可能運用建構最小的記錄
- 若要避免而長時間執行交易使用範圍批次的程序資料
- 採用切換到指定的磁碟分割的大型修改模式的磁碟分割

## <a name="minimal-vs-full-logging"></a>最小與完整的記錄

與不同的完整記錄作業，請使用交易記錄檔追蹤的每個資料列的變更，最小記錄的作業追蹤的範圍配置和只中繼資料變更。 因此，最小的記錄，包括記錄所需復原交易發生故障或明確的要求的資訊 (`ROLLBACK TRAN`)。 為較交易記錄檔中的資訊會加以追蹤，最小記錄的作業會執行同樣的尺寸的完整記錄作業比更好。 此外，較少的寫入到交易記錄檔，因為較少的記錄資料就會產生還有更多 I/O 有效。

交易安全性限制只適用於完整記錄作業。

>[AZURE.NOTE] 最小記錄的作業可以參與明確交易。 為配置結構中的所有修訂會加以都追蹤，則可以回復最小記錄的作業。 請務必瞭解變更為 「 最小 」 登入並不未登入。

## <a name="minimally-logged-operations"></a>最小記錄的作業

下列作業也能最小記錄︰

- 建立表格另存新檔選取 ([CTAS][])
- 插入。選取
- 建立索引
- ALTER 重建索引
- 卸除索引
- 截斷資料表
- 卸除資料表
- ALTER 資料表切換分割

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] 內部資料移動作業 (例如`BROADCAST`和`SHUFFLE`) 不會受到交易安全性限制。

## <a name="minimal-logging-with-bulk-load"></a>使用大量載入最小的記錄

`CTAS`與`INSERT...SELECT`兩者大量載入作業。 不過，同時會受到影響目標資料表定義，取決於負載案例。 下表說明如果您大量作業會完全或最小記錄是︰  

| 主索引鍵               | 載入分析藍本                                            | 登入模式 |
| --------------------------- | -------------------------------------------------------- | ------------ |
| 堆積                        | 任何                                                      | **最小**  |
| 叢集的索引             | 空白的目標資料表                                       | **最小**  |
| 叢集的索引             | 載入資料列不會重疊的現有頁面的目標 | **最小**  |
| 叢集的索引             | 與目標的現有頁面的載入的列重疊        | 完整         |
| 叢集的 Columnstore 索引 | 批次大小 > = 102,400 每對齊分割通訊群組 | **最小**  |
| 叢集的 Columnstore 索引 | 批次大小 < 102,400 每對齊分割通訊群組  | 完整         |

值得更新次要或非叢集索引任何寫入一定會完整記錄的作業。

> [AZURE.IMPORTANT] SQL Data Warehouse 有 60 散佈。 因此，假設變異數平均分佈的所有資料列和登陸單一磁碟分割，以包含 6,144,000 列需要您批次或更大群組直條圖 Columnstore 索引撰寫時，最少記錄。 如果分割表格插入列跨越分割邊界，您將需要每分割邊界假設甚至資料分配 6,144,000 列。 在每個通訊群組中的每個資料分割獨立超出 102400 列臨界值的 [插入]，即可最小登入分配。

將資料載入叢集索引的非空白資料表通常可以包含混合的完整記錄] 及 [最小記錄列。 叢集的索引是對稱樹狀結構 （b 樹狀結構） 的頁面。 如果寫入頁面已包含的另一個交易資料列，然後這些寫入會完全記錄。 不過，如果是空白的頁面然後寫入到該頁面會最小記錄。

## <a name="optimizing-deletes"></a>最佳化刪除

`DELETE`是完整記錄的作業。  如果您要刪除大量的磁碟分割或表格中的資料，請將其通常較為合理`SELECT`資料您想要保留，可以執行為最小記錄的作業。  若要這麼做，請使用[CTAS][]建立新的資料表。  建立後，使用[重新命名][]交換舊表格與新建立的資料表。

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>最佳化更新

`UPDATE`是完整記錄的作業。  如果您需要更新大量的表格中的列或磁碟分割，通常會更有效率使用最小記錄的作業，例如[CTAS][]執行此作業。

在全在表格下方的範例中更新已轉換成`CTAS`，讓最小的記錄有可能是。

在此情況下我們 retrospectively 新增折扣量表中銷售︰

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] 重新建立大型表格的效益使用 SQL Data Warehouse 工作量管理功能。 如需詳細資訊，請參閱[並行][]文件中的工作量管理一節。

## <a name="optimizing-with-partition-switching"></a>最佳化的磁碟分割切換

當面臨[的磁碟分割表格][]中的大型修改內容，切換圖樣的磁碟分割可讓您很實用。 如果資料修改很重要，且橫跨多個資料分割，然後只需逐一磁碟分割區已達到相同的結果。

若要執行的磁碟分割切換參數的步驟如下所示︰
1. 建立分割出的空白
2. 以 CTAS 執行 「 更新 」
3. 現有的資料切換至 out 資料表
4. 在新的資料中切換
5. 清除資料

不過，若要協助您識別要切換的磁碟分割我們必須先建立協助程序，例如下列項目。 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

此程序會最大化重複使用的程式碼，並保留切換範例更精簡的磁碟分割。

下列程式碼示範前述達成切換常式的完整的磁碟分割的五個步驟。

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>最小化小型批次的記錄

為大型資料修改作業，會提供將作業分成區塊或批次來設定範圍的工時單位。

一個作業範例如下所示。 批次大小已經在少數設定以醒目提示技巧。 事實上批次的大小會變得很大。 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>暫停和縮放比例指示

Azure SQL Data Warehouse 可讓您將游標暫停、 繼續及縮放視您的資料倉庫。 當您將游標暫停或請務必瞭解任何 「 執行中 」 的交易終止立即; 您 SQL Data Warehouse 不按比例縮放造成任何開啟的交易復原。 如果您的工作量有發出長時間執行和未完成的資料修改 [暫停] 或 [小數位數] 作業之前，這項工作必須要復原。 這可能會影響來暫停或 Azure SQL Data Warehouse 資料庫不按比例縮放的時間。 

> [AZURE.IMPORTANT] 同時`UPDATE`和`DELETE`完整記錄的作業而作業，讓這些復原/取消復原會大幅超過等於最小登入作業。 

讓航班資料修改交易完成之前暫停或縮放 SQL Data Warehouse 可最佳的案例。 不過，這可能不一定是實用。 若要降低長復原的風險，請考慮下列選項︰

- 重新寫入很長的作業使用[CTAS][]
- 作業劃分成區塊。作業系統上所列的子集

## <a name="next-steps"></a>後續步驟

請參閱[SQL Data Warehouse 交易][]若要進一步瞭解隔離層級和交易的限制。  如需其他的最佳作法的概觀，請參閱[SQL 資料倉庫最佳作法][]。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse 交易]: ./sql-data-warehouse-develop-transactions.md
[資料表分割]: ./sql-data-warehouse-tables-partition.md
[並行]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL 資料倉庫最佳作法]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[重新命名]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

