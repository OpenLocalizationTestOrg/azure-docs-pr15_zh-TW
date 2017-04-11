<properties
   pageTitle="建立資料表中 SQL Data Warehouse 選取 (CTAS) |Microsoft Azure"
   description="建立資料表選取 (CTAS) 陳述式中 Azure SQL Data Warehouse 編碼開發解決方案的秘訣。"
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

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>SQL 資料倉庫以建立資料表，以選取 (CTAS)
建立與選取的資料表或`CTAS`有最重要的 T SQL 功能。 建立 SELECT 陳述式的輸出所根據的新表格的完整平行化的作業則。 `CTAS`已建立一份資料表的簡單且最快速的方法。 您可以將其視為 supercharged 的版本以`SELECT..INTO`如果您想要。 這份文件提供範例和的最佳作法`CTAS`。

## <a name="using-ctas-to-copy-a-table"></a>使用 CTAS 複製表格

其中一個最常用的使用也許`CTAS`，好讓您可以變更 DDL 就建立表格的複本。 如果您原本建立為表格，例如`ROUND_ROBIN`且要將其變更為 [分佈的欄，表格目前`CTAS`是您想要如何變更 [通訊] 欄。 `CTAS`也可以用來變更分割、 編製索引作業，或資料行的類型。

假設您建立此表格，使用預設的通訊類型的`ROUND_ROBIN`分散式因為沒有通訊群組資料行中指定`CREATE TABLE`。

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

現在您要建立具有群組直條圖 Columnstore 索引的新複本的這個資料表，好讓您可以利用群組直條圖 Columnstore 表格的效能。 您也想要散發 ProductKey 此資料表，因為您所預期對此欄的連接，而且想要避免資料移動連接上 ProductKey 期間。 最後也要新增，好讓您快速可以刪除舊的資料拖放舊的磁碟分割區來分割 OrderDateKey 上。 以下是要將舊表格複製到新的資料表的 CTAS 陳述式。

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

最後您可以重新命名您的資料表，若要在新的資料表交換然後舊表格。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Azure SQL Data Warehouse 尚未支援自動建立，或自動更新統計資料。  若要取得最佳效能，從您的查詢，請務必所有資料表的所有資料行建立統計資料之後第一個載入, 或出現在資料中的任何大幅變更。  統計資料的詳細說明，請參閱[統計資料][]主題開發] 群組中的主題。

## <a name="using-ctas-to-work-around-unsupported-features"></a>若要解決不支援的功能使用 CTAS

`CTAS`也可以用來解決不支援的功能下方所列的數字。 這可以通常證明 win/win 情況不只將您的程式碼會相容，但它會經常執行速度更快 SQL Data Warehouse 上。 這是當做其完整平行化的設計。 可以使用 CTAS 解決的情況包括︰

- 選取。將
- 更新的 ANSI 連接
- 在刪除 ANSI 連接
- 合併陳述式

> [AZURE.NOTE] 請試著認為 「 CTAS 第一 」。 如果您認為您可以解決問題，使用`CTAS`然後，通常是最佳方法，即使您正在撰寫更多資料的結果。
>

## <a name="selectinto"></a>選取。將
您可能會發現`SELECT..INTO`會出現在您的方案中的位置數字。

以下是範例`SELECT..INTO`陳述式︰

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

若要將上述轉換`CTAS`是相當直接的︰

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS 目前需要指定分配資料行。  如果您刻意沒有若要變更 [通訊] 欄中，您`CTAS`如果您選取通訊群組的資料行的相同基礎資料表為這種方法可避免移動資料最快執行。  如果您正在建立位置效能並不是因子變異數，然後您可以指定的小型表格`ROUND_ROBIN`避免依據通訊群組資料行。

## <a name="ansi-join-replacement-for-update-statements"></a>Update 陳述式的 ANSI 加入取代

您可能會發現您有兩個以上的資料表一起使用聯結語法的 ANSI 執行更新或刪除連接的複雜更新。

假設您有更新此資料表︰

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

原始的查詢可能會有看起來應該像這樣︰

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

ANSI SQL Data Warehouse 不支援由於中的聯結`FROM`子句的`UPDATE`陳述式中，無法複製在這段程式碼，而不變更該稍微。

您可以使用的組合`CTAS`和隱含聯結將此程式碼︰

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI 加入取代刪除陳述式
刪除資料的最佳方式是使用有時`CTAS`。 而不只刪除資料中，選取您想要保留的資料。 此尤其`DELETE`陳述式的使用中的聯結語法，因為 SQL Data Warehouse 不支援 ANSI 聯結的 ansi`FROM`子句的`DELETE`陳述式。

已轉換的刪除陳述式的範例是如下所示︰

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>取代合併陳述式
合併陳述式，至少組件，以取代使用`CTAS`。 您可以合併彙算`INSERT`和`UPDATE`到單一陳述式。 若要關閉在第二個陳述式中所需任何已刪除的記錄。

範例`UPSERT`如下所示︰

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>CTAS 建議︰ 明確敘述資料類型與輸出的 null 屬性

移轉程式碼時，您可能會發現您在此類型的程式撰寫模式執行︰

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinctively，您可能認為應該將此程式碼移轉到 CTAS，而您可能會正確。 不過，就是隱藏的問題。

下列程式碼不會產生相同的結果︰

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

請注意欄 」 結果 」 轉寄執行運算式的資料類型與 null 屬性值。 這會導致細微變異數的值，如果您不小心。

請嘗試下列動作，作為範例︰

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

已儲存的結果值會不同。 為其他運算式中使用保存的欄中的值結果錯誤會變成更有效。

![][1]

這是資料移轉時特別重要。 即使第二個查詢可說更精確有問題。 資料不同比較來源系統，移轉的完整性之問題的潛在客戶。 這是其中一個這些少見的情況下，「 錯誤 」 的答案是正確的項目 ！

我們看到此工之間的兩個結果的原因是下隱含型別轉換。 在第一個範例表格定義資料行定義。 插入列時，會隱含類型轉換。 在第二個範例沒有隱含類型轉換為運算式定義資料類型的資料行。 請注意，第二個範例中的資料行定義為 Null 資料行而在第一個範例中沒有。 明確定義時建立資料表中第一個範例資料行 null 屬性。 在第二個範例只從運算式，並依預設這會導致 NULL 定義。  

若要解決這些問題您必須明確設定的類型轉換及 null 值屬性中的`SELECT`部分`CTAS`陳述式。 您無法建立表格的組件中設定這些屬性。

下列範例會示範如何修正程式碼︰

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

請注意下列事項︰
- 轉換無法使用
- ISNULL 用來強制 null 屬性不聯合
- ISNULL 是最外層函數
- ISNULL 第二部分是常數，也就是 0

> [AZURE.NOTE] Null 正確設定其屬性是相當使用`ISNULL`並不`COALESCE`。 `COALESCE`不確定函數和運算式的結果，因此一律為 Null。 `ISNULL`有不同。 確定是。 因此當第二部分`ISNULL`函數是常數或常值，然後將會產生的值不 NULL。

此提示不只適用於確保您計算的完整性。 請務必也資料表分割切換。 假設您有定義為您的交易這個資料表︰

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

不過，[值] 欄位是不是來源資料的組件的計算結果的運算式。

若要建立您分割的資料集，您可能會想要執行這項操作︰

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

查詢會執行最適合的。 您嘗試執行分割切換時，就會很問題。 表格定義不相符。 需要修改以符合 CTAS 表格定義。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

您可以看到因此類型一致性和維護 CTAS null 屬性內容絕佳工程最佳作法。 它有助於維護您計算的完整性，也可確保分割切換可能。

請參閱 MSDN 如需有關使用[CTAS][]。 是的最重要的陳述式中 Azure SQL Data Warehouse。 請確定您完全瞭解。

## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
