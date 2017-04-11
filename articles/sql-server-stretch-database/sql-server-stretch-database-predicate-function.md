<properties
    pageTitle="選取要移轉使用篩選函數 （伸展資料庫） 的資料列 |Microsoft Azure"
    description="瞭解如何選取移轉使用篩選函數的列。"
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>選取要移轉使用篩選函數 （伸展資料庫） 的資料列

如果您在不同的資料表儲存低溫資料，您可以設定伸展資料庫移轉整個表格。 如果您的資料表包含快速鍵和低溫資料，另一方面，您可以指定以選取要移轉的資料列篩選函數。 篩選述詞是內嵌資料表\-值函數。 本主題說明如何撰寫內嵌資料表\-值函數以選取要移轉的列。

>   [AZURE.NOTE] 如果您提供執行不良的篩選函數時，資料移轉也會執行不佳。 延伸資料庫 filter 函數使用套用至表格的交互套用運算子。

如果您不指定 filter 函數，移轉整個表格。

當您執行啟用資料庫伸展精靈時，您可以將整個表格的移轉，或您可以指定 [簡易篩選函數精靈] 中。 如果您想要使用不同類型的 filter 函數以選取要移轉的列，請執行其中一個下列項目。

-   結束精靈，並執行 ALTER TABLE 陳述式以啟用伸展的資料表，並指定 filter 函數。

-   執行指定 filter 函數後結束精靈 ALTER TABLE 陳述式。

新增函數的 ALTER TABLE 語法本主題稍後的描述。

## <a name="basic-requirements-for-the-filter-function"></a>Filter 函數的基本需求
內嵌資料表\-值的函數所需的伸展資料庫篩選述詞看起來像下列範例。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
函數參數必須為資料表中的資料行的識別碼。

結構描述繫結，才能防止刪除或更改 filter 函數所使用的資料行。

### <a name="return-value"></a>傳回值
如果此函數會傳回非\-空白結果，列有合格也會移轉。 否則\-亦即，如果函數不傳回結果\-不合格也會移轉，資料列。

### <a name="conditions"></a>條件
&lt;*述詞*&gt;可以包含一個條件，或與邏輯運算子和聯結的多個條件。

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
每個條件在最多可以包含一個基本條件或多個與 OR 邏輯運算子聯結的基本條件。

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>基本的條件
基本的條件可以執行下列其中一項動作的比較。

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   比較函數參數以常數的運算式。 例如， `@column1 < 1000`。

    以下是範例，以檢查的*日期*資料行的值是否&lt;1/1/2016年。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   函數參數套用是空值或不是 NULL 運算子。

-   您可以使用 IN 運算子來比較函數參數以常值的清單。

    以下是範例，以檢查是否的值*次運送\_狀態*資料行是`IN (N'Completed', N'Returned', N'Cancelled')`。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>比較運算子
支援下列比較運算子。

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>常數運算式
您在 filter 函數中使用常數可定義的函數時可以進行評估的任何確定運算式。 常數運算式可以包含下列項目。

-   常值。 例如， `N’abc’, 123`。

-   代數運算式。 例如， `123 + 456`。

-   確定函數。 例如， `SQRT(900)`。

-   確定使用的轉換的轉換 例如， `CONVERT(datetime, '1/1/2016', 101)`。

### <a name="other-expressions"></a>其他運算式
如果結果函數符合取代 BETWEEN 之後與不 BETWEEN 運算子使用相同的 AND 與 OR 運算式此處所述的規則，您可以使用的間距及不之間運算子。

您無法使用子查詢或非\-確定函數，例如 RAND 或 GETDATE()。

## <a name="add-a-filter-function-to-a-table"></a>Filter 函數新增至資料表
執行**ALTER TABLE**陳述式，並指定現有內嵌資料表新增至表格的 filter 函數\-值函數的值為**篩選\_述詞**參數。 例如︰

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
將函數繫結到述詞資料表之後，下列項目均為 true。

-   下一步的時間資料移轉發生時，此函數傳回非的資料列\-空白值會移轉。

-   函數所使用的資料行是繫結的結構描述。 您無法變更這些資料行，只要資料表為其篩選述詞使用函數。

您不能刪除內嵌資料表\-值函數，只要資料表為其篩選述詞使用函數。

>   [AZURE.NOTE] 若要改善 filter 函數的效能，請在函數所使用的資料行建立索引。

### <a name="passing-column-names-to-the-filter-function"></a>將資料行名稱傳遞至 filter 函數
當您為表格指定 filter 函數時，指定的資料行名稱傳遞給一組件名稱的篩選函數。 如果您指定的三個部分名稱時，欄名稱，針對伸展的後續查詢\-啟用的表格會失敗。

例如，如果您指定的三個部分資料行名稱，如下列範例所示，陳述式會順利執行，但在資料表的後續查詢會失敗。

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

下列範例所示，不過，指定一組件的資料行名稱與 filter 函數。

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>執行精靈之後新增 filter 函數  

如果您想使用的函數，您無法建立**啟用伸展的資料庫**精靈] 中，您可以執行 ALTER TABLE 陳述式之後結束精靈指定函數。 您可以套用函數之前，不過，您可以停止正在進行中的資料移轉，並會傳回移轉的資料。 （為什麼，這是必要的詳細資訊，請參閱[取代現有的篩選函數](#replacePredicate)。  

1. 反轉移轉的方向，並開啟上一步] 已移轉資料。 啟動後，您必須取消此作業。 您也會造成成本 Azure 輸出資料傳輸\(出口\)。 取得詳細資訊，請參閱[如何 Azure 價格運作](https://azure.microsoft.com/pricing/details/data-transfers/)。  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. 等待移轉完成。 您可以檢查在 SQL Server Management Studio 中，從**伸展資料庫監視器**的狀態，或您可以查詢**sys.dm_db_rda_migration_status**檢視。 如需詳細資訊，請參閱[監視器和疑難排解資料移轉](sql-server-stretch-database-monitor.md)或[sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx)。  

3. 建立您想要套用到表格 filter 函數。  

4. 將函數加入至資料表並重新啟動 Azure 資料移轉。  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>依日期篩選資料列
下列範例會移轉位置**date** ] 資料行包含的值 2016 年 1 月 1 日之前的列。

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>[狀態] 欄中的值來篩選資料列
下列範例會移轉包含指定的值 [**狀態**] 欄的資料列。

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>篩選資料列，使用滑動] 視窗
若要使用滑動] 視窗中篩選資料列，請記住下列 filter 函數的相關需求。

-   必須確定函數。 因此您無法建立會自動重新計算經過一段時間的滑動視窗的函數。

-   此函數使用結構描述繫結。 因此您無法直接更新 」 中的位置 」 的函數每天呼叫**改變函數**來移動滑動視窗。

篩選函數，如下列範例，移轉位置**systemEndTime**資料行包含值 2016 年 1 月 1 日之前的資料列的開頭。

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

套用至表格的 filter 函數。

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

當您想要更新滑動視窗時，請執行下列項目。

1.  建立新的函數，指定新的滑動視窗。 下列範例會選取 2016 年 1 月 2 日到，而不是 2016 年 1 月 1 日之前的日期。

2.  以新呼叫**ALTER TABLE**，取代前一個篩選函數，如下列範例所示。

3. 您也可以拖放上一個篩選的函數您不再使用的呼叫**放函數**。 （此步驟沒有顯示在範例）。

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>有效的篩選功能的更多範例

-   下列範例會使用與邏輯運算子結合兩個基本的條件。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   下列範例會使用轉換多個條件和確定轉換。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   下列範例會使用數學運算子和函數。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   下列範例會使用之間並不之間運算子。 此使用方式是有效的因為結果函數符合取代 BETWEEN 之後與不 BETWEEN 運算子使用相同的 AND 與 OR 運算式此處所述的規則。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    上述函數相當於下列函數之後您使用相同的 AND 與 OR 運算式取代之間並不之間運算子。

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>無效的篩選函數的範例

-   下列函數無效，因為它包含非\-確定轉換。

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   下列函數無效，因為它包含非\-確定函數呼叫。

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   下列函數不是有效的因為其中含有子查詢。

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   下列函數無效因為使用代數運算子或內建的運算式\-函數中必須評估為常數在您定義的函數。 您無法代數運算式或函數呼叫中包含的資料行參考。

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   下列函數不是有效的因為它違反之間運算子取代與的運算式之後此處所述的規則。

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    上述函數相當於下列函數之後之間運算子取代與的運算式。 此函數不是有效的因為基本條件只能使用 OR 邏輯運算子。

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>如何伸展資料庫適用於 filter 函數
延伸資料庫套用至資料表的 filter 函數，並使用跨套用運算子來決定符合資格的列。 例如︰

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
如果此函數會傳回非\-空白列的結果，列有合格也會移轉。

## <a name="replacePredicate"></a>取代現有的篩選函數
您可以執行一次**ALTER TABLE**陳述式，並指定的新值取代先前指定的篩選函數**篩選\_述詞**參數。 例如︰

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
新的內嵌資料表\-值函式有下列需求。

-   新函數必須是較少限制比前一個函數。

-   舊函數中的所有運算子必須存都在於新的函數。

-   新函數不能包含不存在於舊函數中的運算子。

-   無法變更運算子引數的順序。

-   僅常數值屬於`<, <=, >, >=`比較可以變更的方式，可限制較少的函數。

### <a name="example-of-a-valid-replacement"></a>有效的替代的範例
假設下列函數是目前的篩選述詞。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
下列函數是有效的替代方案，因為限制較少的新日期常數 （其指定之後截止日期） 並函數。

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>無效的取代文字的範例
下列函數不是有效的替代方案，因為新的日期常數 （其指定較舊版本的截止日期） 不會使函數較少限制。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
下列函數無法有效的替代方案，因為已經移除其中一個比較運算子。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
下列函數不正確的替代方案，因為與邏輯運算子已加入新的條件。

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Filter 函數移除表格
若要移轉整個表格，而非選取的列，藉由設定移除現有函數**篩選\_述詞**為 null。 例如︰

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
移除篩選函數之後，請在表格中的所有資料列會移轉。 如此一來，您無法指定相同資料表稍後 filter 函數，除非您恢復遠端的所有資料的表格從 Azure 第一次。 若要避免這種情況位置時，不會移轉提供新的篩選函數的列已移轉至 Azure 存在這項限制。

## <a name="check-the-filter-function-applied-to-a-table"></a>核取 [套用至表格的篩選函數
若要檢查 filter 函數套用至表格，請開啟 [類別目錄檢視**sys.remote\_資料\_封存\_表格**，並檢查的值**篩選\_述詞**資料行。 如果該值為 null，整個表格的資格封存。 取得詳細資訊，請參閱[sys.remote_data_archive_tables (SQL)](https://msdn.microsoft.com/library/dn935003.aspx)。

## <a name="security-notes-for-filter-functions"></a>篩選函數的安全性附註  
Db_owner 權限洩漏的帳戶，可以執行下列項目。  

-   建立並套用資料表值函式使用大量的伺服器資源或等候長拒絕服務的結果。  

-   建立並套用資料表值函式，可讓推斷表格的使用者已明確拒絕讀取存取的內容。  

## <a name="see-also"></a>另請參閱

[變更資料表 (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
