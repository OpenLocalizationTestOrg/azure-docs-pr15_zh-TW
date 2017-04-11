<properties
   pageTitle="[群組依據在 SQL Data Warehouse 選項 |Microsoft Azure"
   description="適用於開發解決方案實作 Azure SQL Data Warehouse 中的 [選項] 群組中的秘訣。"
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

# <a name="group-by-options-in-sql-data-warehouse"></a>群組依據在 SQL Data Warehouse 中的選項

[GROUP BY][]子句會用於彙總資料摘要的一組資料列。 也都有擴充的功能，必須使用周圍，只要 Azure SQL Data Warehouse 不直接支援的幾個選項。

這些選項
- 群組依據的彙總套件
- 群組設定
- 群組依據 cube

## <a name="rollup-and-grouping-sets-options"></a>彙總套件及群組設定選項
最簡單的選項就是使用`UNION ALL`改為執行彙總套件而不是仰賴明確的語法。 結果是完全相同

以下是使用陳述式群組的範例`ROLLUP`選項︰

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

使用彙總套件我們要求動作的彙總︰
- 國家和地區
- 國家/地區
- 總計

若要取代您需要使用此`UNION ALL`;指定彙總所需明確傳回相同的結果︰

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

所有要做的分組集是採用相同的本金，但只建立 UNION ALL 章節以供我們想要查看的彙總層級

## <a name="cube-options"></a>Cube 選項
若要建立群組，以 CUBE 使用 UNION ALL 方法可能是。 問題是麻煩且龐大，可以快速會變成程式碼。 若要解決這個您可以使用這個更進階的方法。

讓我們來使用上述範例。

第一步是定義 'cube' 定義的彙總，我們要為其建立的所有層級。 請務必記下兩個衍生資料表的交叉聯結。 這會產生所有層級給我們。 其他程式碼會很有來設定格式。

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

CTAS 的結果都可以如下所示︰

![][1]

指定目標表格中期結果儲存為第二個步驟︰

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

第三個步驟是針對我們 cube 執行彙總資料行的循環播放。 查詢會執行一次 #Cube 暫時資料表中每個資料列，並將結果儲存在 #Results temp 表格

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

最後我們可以傳回的結果，只要閱讀從 #Results 暫時資料表

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

的程式碼分成章節，並產生迴圈建構程式碼會變成更容易管理且易於維護。


## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[群組依據]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
