<properties
   pageTitle="設計決策與 SQL Data Warehouse 開發編碼技術 |Microsoft Azure"
   description="開發概念、 決策、 建議和 SQL Data Warehouse 編碼技巧。"
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
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>決策和 SQL Data Warehouse 的編碼技術

若要進一步瞭解 SQL Data Warehouse 的 [主要的設計決策、 建議和編碼技術這些開發文章看。

## <a name="key-design-decisions"></a>主要的設計決策
下列文章醒目提示的一些重要概念和設計決策，您必須使用 SQL Data Warehouse 您分散式的資料倉庫開發深入了解︰

- [連線][]
- [並行][]
- [交易][]
- [使用者定義的結構描述][]
- [表格通訊群組][]
- [資料表的索引][]
- [表格磁碟分割區][]
- [CTAS][]
- [統計資料][]

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和編碼技術
這些文章醒目提示特定編碼技術、 秘訣和開發您 SQL Data Warehouse 建議︰

- [預存程序][]
- [標籤][]
- [檢視][]
- [暫存資料表][]
- [動態 SQL][]
- [迴圈][]
- [群組依據] 選項][]
- [變數工作分派][]

## <a name="next-steps"></a>後續步驟
一旦您已經在開發文章看透過更多詳細資料的[SQL 參考][]頁面上支援的語法的 SQL Data Warehouse。

<!--Image references-->

<!--Article references-->
[並行]: ./sql-data-warehouse-develop-concurrency.md
[連線]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[動態 SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[群組依據] 選項]: ./sql-data-warehouse-develop-group-by-options.md
[標籤]: ./sql-data-warehouse-develop-label.md
[迴圈]: ./sql-data-warehouse-develop-loops.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md
[預存程序]: ./sql-data-warehouse-develop-stored-procedures.md
[表格通訊群組]: ./sql-data-warehouse-tables-distribute.md
[資料表的索引]: ./sql-data-warehouse-tables-index.md
[表格磁碟分割區]: ./sql-data-warehouse-tables-partition.md
[暫存資料表]: ./sql-data-warehouse-tables-temporary.md
[交易]: ./sql-data-warehouse-develop-transactions.md
[使用者定義的結構描述]: ./sql-data-warehouse-develop-user-defined-schemas.md
[變數工作分派]: ./sql-data-warehouse-develop-variable-assignment.md
[檢視]: ./sql-data-warehouse-develop-views.md
[TRANSACT-SQL 參照]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
