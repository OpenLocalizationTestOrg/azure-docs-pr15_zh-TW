<properties
   pageTitle="在 SQL 資料倉庫暫存資料表 |Microsoft Azure"
   description="開始使用中 Azure SQL Data Warehouse 暫存的資料表。"
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>在 SQL Data Warehouse 暫存的資料表

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [發佈][]
- [索引][]
- [分割][]
- [統計資料][]
- [暫時][]

處理資料-特別是在轉換中繼結果的暫時性的位置時，會很有幫助暫存資料表。 在 SQL Data Warehouse 暫存資料表存在的工作階段層級。  他們的年齡只看到他們建立與該工作階段登出時，會自動卸除工作階段。  暫存資料表提供效能提升，因為他們的結果會寫入到本機而不是遠端儲存空間。  他們可以存取的任何位置內工作階段，包括內部和外部預存程序暫存資料表是略有不同的 Azure SQL Data Warehouse Azure SQL 資料庫。

本文包含使用暫存資料表的基本指南，並醒目提示的工作階段的層級暫時資料表原則。 使用本文中的資訊可協助您模組化程式碼，同時改進性和維護程式碼的方便性。

## <a name="create-a-temporary-table"></a>建立暫時表格

只要在您使用的資料表名稱前面加上字元會建立暫存資料表`#`。  例如︰

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

暫存資料表，也可以建立使用`CTAS`使用完全相同的方法︰

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`強大的命令，而新增優點是有效的交易記錄檔空間用法。 


## <a name="dropping-temporary-tables"></a>卸除暫存資料表

建立新的工作階段時，應存在於沒有暫存的資料表。  不過，呼叫相同的預存程序，建立暫時使用相同的名稱，以確保您`CREATE TABLE`陳述式是成功使用簡單的測試是否存在檢查`DROP`可為在下方的範例︰

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

若是編碼一致性，則表格和暫存資料表使用這個模式很好的做法。  也是使用建議您先`DROP TABLE`完畢後的程式碼中，移除暫存的資料表。  預存程序會清除的開發常會若要查看的下拉命令一起結尾的程序以確保這些物件。

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing 的程式碼

由於暫存資料表能看到使用者工作階段中的任一處，可以利用這可協助您模組化應用程式碼。  例如，下列預存程序結合從上方的建議的作法產生 DDL 會依統計名稱更新資料庫中的所有統計資料。

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

在此階段中唯一發生的動作是直接將預存程序建立產生暫存的資料表，#stats_ddl，DDL 陳述式。  此預存程序會卸除 #stats_ddl，以確保它不會失敗若不只一次執行的工作階段中已經存在。  不過，由於沒有`DROP TABLE`預存程序結尾預存程序完成時，它會離開建立的資料表，讓它可以讀取以外的預存程序。  在 SQL Data Warehouse，跟其他 SQL Server 資料庫，可能是使用暫時表格以外的建立者的程序。  SQL Data Warehouse 暫存資料表可以使用**任何一處**內工作階段。 這可能會導致更多模組化且更容易管理程式碼，如下列範例︰

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>暫時資料表限制

SQL Data Warehouse 實作暫存資料表時，會強制幾個限制。  目前，只有工作階段支援範圍暫存的資料表。  不支援全域暫存的資料表。  此外，無法在暫時資料表建立檢視。

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱文章上[表格概觀][概觀]、[資料表的資料類型][的資料類型]、[發佈表格][等距分佈]、[編製索引的資料表][索引]、[分割表格][的磁碟分割]以及[維護資料表統計資料][的統計資料]。  如需瞭解最佳作法的詳細資訊，請參閱[SQL 資料倉庫最佳作法][]。

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

<!--MSDN references-->

<!--Other Web references-->
