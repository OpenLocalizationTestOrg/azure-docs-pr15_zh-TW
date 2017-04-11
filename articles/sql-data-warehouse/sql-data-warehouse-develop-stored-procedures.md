<properties
   pageTitle="預存程序 SQL Data Warehouse |Microsoft Azure"
   description="秘訣實作預存程序 Azure SQL Data Warehouse 開發解決方案。"
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>在 SQL Data Warehouse 預存程序

SQL Data Warehouse 支援許多在 SQL Server 中找到的 TRANSACT-SQL 功能。 更重要的是沒有小數位數，我們會想要使用您的方案效能最大化的特定功能。

不過，若要維持比例和效能 SQL Data Warehouse 有，也會一些功能及行為差異和其他人不支援的功能。

本文說明如何實作 SQL Data Warehouse 內的預存程序。

## <a name="introducing-stored-procedures"></a>介紹預存程序
預存程序很適合用於封裝 SQL 程式碼。您可以將其關閉資料倉庫中的資料。 藉由壓縮分成可管理的單位的程式碼預存程序協助開發人員模組化解決方案;促進大於 re-usability 的程式碼。 每個預存程序也可以接受參數，使其更有彈性。

SQL Data Warehouse 提供簡體與精簡預存程序實作。 最大的差異比較 SQL Server 是預存程序不預先編譯程式碼。 在資料倉儲我們是通常考慮使用編譯的時間。 請務必更多操作針對資料時，正確 optimised 預存程序程式碼。 目標是儲存小時、 分鐘和秒鐘不毫秒為單位。 因此，這是更好的搜尋預存程序為容器的 SQL 邏輯。     

當 SQL Data Warehouse 執行您的預存程序會剖析 SQL 陳述式，翻譯和最佳化在執行階段。 此程序期間每個陳述式會轉換成分散式查詢。 實際執行資料的 SQL 程式碼是不同提交的查詢。

## <a name="nesting-stored-procedures"></a>巢狀預存程序
當預存程序呼叫其他預存程序，或執行動態 sql，然後內部預存程序或程式碼引動稱為巢狀。

SQL Data Warehouse 支援 8 巢狀層級的最大值。 這是以 SQL Server 稍有不同。 在 SQL Server 中的巢狀層級為 32。

在上方層級的預存程序呼叫等於巢狀層級 1

```sql
EXEC prc_nesting
```
如果預存程序也可讓您的通話的另一個接著再這會增加為 2 的巢狀層級
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
如果第二個程序，然後執行某些動態 sql 然後這會增加 3 巢狀層級
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

目前不支援筆記 SQL Data Warehouse @@NESTLEVEL。 您必須自行保留您的巢狀層級的追蹤。 不太就會叫用 8 巢狀層級限制，但如果您執行的動作您必須重新處理您的程式碼，並 」 簡維 」 它，使其符合這項限制。

## <a name="insertexecute"></a>插入。執行
SQL Data Warehouse 不允許您使用預存程序以插入陳述式的結果集。 不過，就是您可以使用另一種方法。

請參閱下列文章[暫存資料表]，如需如何執行此動作的範例。

## <a name="limitations"></a>限制

有一些層面的 SQL Data Warehouse 並未實作 TRANSACT-SQL 預存程序。

它們是︰

- 暫時預存程序
- 編號的預存程序
- 擴充的預存程序
- CLR 預存程序
- 加密] 選項
- 複寫選項
- 資料表值參數
- 唯讀的參數
- 預設參數
- 執行內容
- 傳回陳述式

## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[暫存資料表]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[開發概觀]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
