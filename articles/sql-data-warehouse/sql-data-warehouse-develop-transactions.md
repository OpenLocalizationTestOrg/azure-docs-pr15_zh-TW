<properties
   pageTitle="SQL 資料倉庫交易 |Microsoft Azure"
   description="針對開發解決方案實作 Azure SQL Data Warehouse 中的交易的秘訣。"
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
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>SQL Data Warehouse 交易

如您所預期，SQL Data Warehouse 會支援交易做為資料倉庫工作負載的一部分。 不過，以確保 SQL Data Warehouse 的效能會保留在某些功能限於相較於 SQL Server。 本文會醒目提示的差異，並列出其他人。 

## <a name="transaction-isolation-levels"></a>交易隔離層級
SQL Data Warehouse 實作 ACID 交易。 不過的交易支援隔離受限於`READ UNCOMMITTED`，這不能變更。 您可以執行程式碼來防止 dirty 讀取的資料，如果這是您的方法數的字。 最常用的方法運用 CTAS 和資料表分割切換 （通常稱為滑動視窗模式） 若要防止使用者從查詢仍準備的資料。 預先篩選資料的檢視也是常見的方法。  

## <a name="transaction-size"></a>交易的大小
單一資料修改交易的大小有限。 今天限制會套用 「 每個通訊群組]。 因此，配置可計算限制乘以通訊計數。 若要概略的交易中的列數上限除以通訊群組首字放大的每個資料列的總大小。 可變長度資料行，請考慮採取平均的資料行的長度，而不是使用的最大值。

在下列假設下表中所做︰

* 發生的資料 
* 平均資料列長度為 250 個位元組

| [DWU][]    | 每個通訊群組 （鉤） 覆蓋 | 分配的數字 | 最大值交易大小 （鉤） | # 每個通訊群組列 | 每次交易的最大資料列 |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  為 1.5 來計算                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2.25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3.75                      | 60                      |  225                       |  15,000,000             |    900,000,000           |
| DW600  |  4.5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30,000,000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11.25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60000000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2700                     | 180,000,000             | 10,800,000,000           |

交易大小限制會套用每次交易或作業。 它不會套用到所有並行交易。 因此每次交易允許此資料量寫入記錄檔。 

若要最佳化，及 [最小化的寫入記錄檔中的資料量的[交易的最佳作法][]文件，請參閱。

> [AZURE.WARNING] 最大交易大小只能達到雜湊，甚至 ROUND_ROBIN 分散式的資料表的資料分佈的在哪裡。 如果交易寫入資料扭曲的方式散發限制為可能最大交易大小之前接聽。
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>交易狀態
SQL Data Warehouse 使用 XACT_STATE() 函數來報告失敗的交易使用-2 的值。 這表示交易失敗，且標示為 [只有復原

> [AZURE.NOTE] -2 的倍數，代表失敗的交易 XACT_STATE 函數使用 SQL Server 代表不同的行為。 SQL Server 會使用的值-1 代表未認可的交易。 SQL Server 可以不將其標示為未認可有容許交易內的一些錯誤。 例如`SELECT 1/0`會造成錯誤，但強制交易未認可狀態。 SQL Server 可讀取未認可交易中。 不過，SQL Data Warehouse 不允許您執行此動作。 在 SQL Data Warehouse 交易發生錯誤時就會自動輸入的-2 的狀態，您無法將任何進一步選取陳述式，直到陳述式已復原為止。 因此務必以檢查您的應用程式程式碼，請參閱是否您使用 XACT_STATE() 可能需要修改程式碼。

例如，在 SQL Server，您可能會看到的交易，看起來像這樣︰

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

如果您離開您的程式碼，因為它是上方，您會收到下列錯誤訊息︰

訊息 111233，層級 16，狀態 1，行 1 111233;目前的交易已中止，並等待的任何變更已復原。 原因︰ 僅限復原狀態已不明確復原交易 DDL、 DML 或 SELECT 陳述式之前。

您也不會收到 ERROR_ * 函數的輸出。

在 SQL Data Warehouse 必須稍微改變程式碼︰

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

現在被上述預期的行為。 受管理的交易中的錯誤和 ERROR_ * 函數提供的值，如預期般。

所有已變更的是`ROLLBACK`交易的已發生錯誤的資訊中閱讀之前`CATCH`區塊。

## <a name="errorline-function"></a>Error_Line() 函數
值得也的 SQL Data Warehouse 不實作或支援 ERROR_LINE() 函數。 如果您有您要移除其與 SQL Data Warehouse 相容程式碼。 改為使用您的程式碼中的查詢標籤實作相等的功能。 請參閱[標籤][]文章，如需此功能的詳細資訊。

## <a name="using-throw-and-raiserror"></a>使用擲回和 RAISERROR
擲回會引發例外狀況 SQL Data Warehouse 中的最新實作但 RAISERROR 也有支援。 有值得不過應注意的一些差異。

- 使用者定義的數字不能在擲回 100000 150000 範圍中的錯誤訊息
- 在 50000 修正 RAISERROR 錯誤訊息
- 不支援的 sys.messages 使用

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse 沒有與交易相關的一些其他限制。

他們所示︰

- 沒有分散式的交易
- 不允許的巢狀的交易
- 無儲存允許的端點
- 未命名的交易
- 沒有標記的交易
- 不支援例如 DDL`CREATE TABLE`內使用者定義的交易

## <a name="next-steps"></a>後續步驟
若要進一步瞭解最佳化交易，請參閱[交易的最佳作法][]。  若要瞭解其他 SQL Data Warehouse 最佳作法，請參閱[SQL Data Warehouse 最佳作法][]。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[交易的最佳作法]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse 最佳作法]: ./sql-data-warehouse-best-practices.md
[標籤]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
