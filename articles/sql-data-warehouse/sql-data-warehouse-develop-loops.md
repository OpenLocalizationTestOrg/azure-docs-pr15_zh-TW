<properties
   pageTitle="在 SQL Data Warehouse 迴圈 |Microsoft Azure"
   description="TRANSACT-SQL 迴圈及取代的資料指標中 Azure SQL Data Warehouse 開發解決方案的秘訣。"
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="loops-in-sql-data-warehouse"></a>在 SQL Data Warehouse 迴圈
SQL Data Warehouse 支援[時][]循環播放，重複執行陳述式區塊。 這會繼續，只要在指定的條件為真，或直到程式碼特別終止循環播放使用的`BREAK`關鍵字。 迴圈是適合用來取代 SQL 程式碼中定義的游標。 所幸，幾乎所有游標撰寫的 SQL 程式碼的快速轉寄，都讀取只各種不同。 如果您需要將其中一個，因此[時]迴圈是絕佳的替代方案。

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>運用迴路和取代在 SQL Data Warehouse 游標
不過之前先深入不對, 您應該要求您自己的下列問題: 「 無法此游標重新寫入使用集作業嗎？ 」。 在許多情況下回答 [是]，通常是最好的方法。 設定以作業通常會比反覆運算、 列的方法更快執行。

向前唯讀狀態指標可以輕鬆地取代迴圈建構。 以下是一個簡單的範例。 此範例中的程式碼更新資料庫中的每個資料表的統計資料。 逐一查看循環播放中的資料表我們的順序執行每一個命令。

首先，建立暫存的資料表，其中包含用來識別的個別陳述式的唯一資料列數︰

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

第二，初始化執行循環播放所需的變數︰

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

現在迴圈陳述式執行一次︰

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後放在第一個步驟所建立的暫時表格

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[時]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
