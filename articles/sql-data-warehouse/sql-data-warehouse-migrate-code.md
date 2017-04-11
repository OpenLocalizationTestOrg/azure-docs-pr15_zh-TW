<properties
   pageTitle="將您的 SQL 程式碼移轉到 SQL Data Warehouse |Microsoft Azure"
   description="適用於開發解決方案，移轉至 Azure SQL Data Warehouse 的 SQL 程式碼的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>將您的 SQL 程式碼移轉到 SQL Data Warehouse

當移轉從另一個資料庫的程式碼，以 SQL Data Warehouse，很可能是您必須變更您的程式碼基底。 為它們專為搭配分散式方式，某些 SQL Data Warehouse 功能可以大幅改善效能。 不過，若要維持效能和小數位數，某些功能都也無法使用。

## <a name="common-t-sql-limitations"></a>一般 T SQL 限制

下列清單摘要列出最常見 Azure SQL Data Warehouse 不支援的功能。 連結可讓您不受支援功能的因應措施︰

- [更新的 ANSI 連接][]
- [在刪除 ANSI 連接][]
- [合併陳述式][]
- 跨資料庫連接
- [游標][]
- [選取。將][]
- [插入。接著][]
- 輸出子句
- 內嵌使用者定義函數
- 多陳述式函數
- [常見的資料表運算式](#Common-table-expressions)
- [遞迴通用資料表運算式 (CTE)](#Recursive-common-table-expressions-(CTE)
- CLR 函數與程序
- $partition 函數
- 資料表變數
- 資料表值參數
- 分散式的交易
- 認可 / 復原工時
- 儲存交易
- 執行內容 （執行另存新檔）
- [group by 子句的彙總套件 / cube / 群組設定選項][]
- [除了 8 的巢狀層級][]
- [更新透過檢視][]
- [使用可變的工作分派選取][]
- [沒有最大值的資料輸入動態 SQL 字串][]

所幸可以使用大部分的這些限制，周圍。 上述的相關開發文章提供說明。

## <a name="supported-cte-features"></a>支援的 CTE 功能

SQL Data Warehouse 部分支援常見的資料表運算式 (Cte)。  目前支援下列 CTE 功能︰

- 可以在 SELECT 陳述式中指定 CTE。
- CREATE VIEW 陳述式中，就可以指定 CTE。
- CTE 可以建立表格另存新檔選取 (CTAS) 陳述式中指定。
- CTE 可以建立遠端表格另存新檔選取 (CRTAS) 陳述式中指定。
- 建立外部表格另存新檔選取 (CETAS) 陳述式中，可以指定 CTE。
- 遠端資料表可以從 CTE 參考。
- 外部資料表可以從 CTE 參考。
- 可以在 CTE 定義多個 CTE 查詢定義。

## <a name="cte-limitations"></a>CTE 限制

常見的資料表運算式有一些限制 SQL Data Warehouse 包括︰

- CTE 必須後面單一的 SELECT 陳述式。 [插入]，更新、 刪除及合併陳述式不受支援。
- 常見的資料表運算式本身 （遞迴一般資料表運算式） 的參照包含不受支援 （請參閱] 區段下方）。
- 不允許指定超過一個具有 CTE。 例如，如果 CTE_query_definition 包含子查詢，該子查詢不能包含巢狀子句定義另一個 CTE。
- ORDER BY 子句無法用於 CTE_query_definition，除了指定子句時。
- 屬於一批陳述式中使用 CTE 時，它之前的陳述式必須後面跟著分號。
- 準備 sp_prepare 陳述式中使用時，Cte 將相同的行為為 PDW 中其他 SELECT 陳述式。 不過，如果 Cte CETAS 由 sp_prepare 準備的一部分，行為可以延遲從 SQL Server 和其他 PDW 陳述式由於 sp_prepare 實作繫結的方式。 如果 CTE 使用的不存在於 CTE 錯誤資料行的參考資料]、 sp_prepare 會通過偵測錯誤，但錯誤就會擲回期間 sp_execute 改為選取這個選項。

## <a name="recursive-ctes"></a>遞迴 Cte

SQL Data Warehouse 不支援遞迴 Cte。  遞迴 CTE 的 migraion 可能較為完成和最佳的程序是劃分成多個步驟。 您通常可以使用循環播放，並填入暫存的資料表，當您逐一遞迴中期查詢。 填入暫時資料表後，您就可以為單一結果集傳回的資料。 已使用類似的方法，以解決`GROUP BY WITH CUBE` [group by 子句的彙總套件 / cube / 群組設定選項][]的文件中。

## <a name="unsupported-system-functions"></a>不支援的系統函數

還有一些系統功能不受支援。 通常您會發現倉儲的資料中所使用的主要項目如下︰

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

這些問題可以使用周圍。

## <a name="rowcount-workaround"></a>@@ROWCOUNT因應措施

若要解決不支援@@ROWCOUNT,建立的預存程序會從 sys.dm_pdw_request_steps 擷取姓氏的列數，然後執行`EXEC LastRowCount`DML 陳述式之後。

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>後續步驟
所有支援的 T SQL 陳述式的完整清單，請參閱[TRANSACT-SQL 主題][]。

<!--Image references-->

<!--Article references-->
[更新的 ANSI 連接]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[在刪除 ANSI 連接]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[合併陳述式]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[插入。接著]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[TRANSACT-SQL 主題]: ./sql-data-warehouse-reference-tsql-statements.md

[游標]: ./sql-data-warehouse-develop-loops.md
[選取。將]: ./sql-data-warehouse-develop-ctas.md#selectinto
[group by 子句的彙總套件 / cube / 群組設定選項]: ./sql-data-warehouse-develop-group-by-options.md
[除了 8 的巢狀層級]: ./sql-data-warehouse-develop-transactions.md
[更新透過檢視]: ./sql-data-warehouse-develop-views.md
[使用可變的工作分派選取]: ./sql-data-warehouse-develop-variable-assignment.md
[沒有最大值的資料輸入動態 SQL 字串]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
