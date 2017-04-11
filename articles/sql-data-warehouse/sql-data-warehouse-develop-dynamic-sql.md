<properties
   pageTitle="在 SQL Data Warehouse 動態 SQL |Microsoft Azure"
   description="使用動態 SQL Azure SQL Data Warehouse 中開發解決方案的秘訣。"
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

# <a name="dynamic-sql-in-sql-data-warehouse"></a>在 SQL Data Warehouse 動態 SQL
SQL Data Warehouse 開發應用程式碼時，您可能需要使用動態 sql 協助提供非常有彈性與一般模組化解決方案。 SQL Data Warehouse 不支援這次 blob 資料類型。 為 blob 類型包括 varchar （max） 和 nvarchar （max） 類型，這可能會限制您字串的大小。 如果您已在應用程式碼中使用這些類型，建置很大的字串時，您必須中斷成區塊的程式碼，並改用接著陳述式。

簡單範例︰

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串的簡短您可以使用[sp_executesql][]為標準。

> [AZURE.NOTE] 執行以動態 SQL 陳述式仍會受到所有 TSQL 驗證規則。

## <a name="next-steps"></a>後續步驟
如需開發秘訣，請參閱[開發概觀][]。

<!--Image references-->

<!--Article references-->
[開發概觀]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
