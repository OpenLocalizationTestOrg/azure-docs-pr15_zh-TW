<properties
   pageTitle="SQL Data Warehouse 中資料表的資料類型 |Microsoft Azure"
   description="開始使用 Azure SQL Data Warehouse 資料表的資料類型。"
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

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>SQL Data Warehouse 中資料表的資料類型

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [發佈][]
- [索引][]
- [分割][]
- [統計資料][]
- [暫時][]

SQL Data Warehouse 支援最常使用的資料類型。  以下是支援的 SQL Data Warehouse 的資料類型的清單。  資料類型的其他詳細資料的支援，請參閱[建立資料表][]。

|**支援的資料類型**|||
|---|---|---|
[bigint][]|[小數位數][]|[小][]|
[二進位][]|[浮動時間][]|[smallmoney][]|
[位元][]|[int][]|[role][]|
[字元][]|[金錢][]|[時間][]|
[日期][]|[nchar][]|[tinyint][]|
[日期時間][]|[nvarchar][]|[唯一][]|
[datetime2][]|[實數][]|[varbinary][]|
[datetimeoffset][]|[29.998][]|[varchar][]|


## <a name="data-type-best-practices"></a>資料類型的最佳作法

 定義您的欄類型時, 使用的最小的支援資料的資料類型會改善查詢效能。 這是特別重要的字元和 VARCHAR 資料行。 如果資料行中最長的值是 25 個字元，然後為 VARCHAR(25) 定義您的資料行。 避免字元的所有資料行定義為大型預設長度。 此外，將資料行定義 VARCHAR 時，所有的所需，而不是使用[NVARCHAR][]。  使用 NVARCHAR(4000) 或 VARCHAR(8000) 可能的話，而非 nvarchar （max） 或 varchar （max）。

## <a name="polybase-limitation"></a>Polybase 限制

如果您使用 Polybase 載入您的資料表，定義您的資料表，使最大可能的資料列的大小，包括完整變數長度欄的長度不超過 32767 個位元組。  雖然您可以定義可以超過此寬度及載入 bcp 列的長度資料列，您將無法使用 Polybase 載入此資料。  推出便會新增 Polybase 支援廣泛的資料列。

## <a name="unsupported-data-types"></a>不支援的資料類型

如果您要移轉您的資料庫從另一個 SQL 平台 Azure SQL 資料庫，例如，當您移轉中，您可能會遇到的 SQL Data Warehouse 不支援部分資料類型。  以下是不受支援的資料類型，以及您可以使用不受支援的資料類型取代一些替代方案。

|資料類型|因應措施|
|---|---|
|[幾何][]|[varbinary][]|
|[地理位置][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][](4000)|
|[圖像][ntext,text,image]|[varbinary][]|
|[文字][ntext,text,image]|[varchar][]|
|[n][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|將數個強資料行分割資料行。|
|[表格][]|轉換成暫存的表格。|
|[時間戳記][]|重做程式碼，使用[datetime2][]和`CURRENT_TIMESTAMP`函數。  支援只常數做為預設值，因此 current_timestamp 無法定義為預設限制式。 如果您要移轉的時間戳記輸入資料行的值再使用[二進位][](8) 或[VARBINARY][二進位](8) 的 NULL，或者是 NULL 列版本值。|
|[xml][]|[varchar][]|
|[使用者定義類型][]|盡可能轉換回其原始的類型|
|預設值|預設值支援常值和只常數。  不確定運算式或函數，例如`GETDATE()`或`CURRENT_TIMESTAMP`，則不受支援。|

SQL 下方可以執行您目前 SQL 資料庫來識別的資料行不受支援的 Azure SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱文章上[表格概觀][概觀]、[發佈表格][等距分佈]、[編製索引的資料表][索引]、[分割表格][的磁碟分割]、[維護資料表統計資料][的統計資料]以及[暫存資料表][暫時]。  如需瞭解最佳作法的詳細資訊，請參閱[SQL 資料倉庫最佳作法][]。

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
[建立資料表]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[二進位]: https://msdn.microsoft.com/library/ms188362.aspx
[位元]: https://msdn.microsoft.com/library/ms177603.aspx
[字元]: https://msdn.microsoft.com/library/ms176089.aspx
[日期]: https://msdn.microsoft.com/library/bb630352.aspx
[日期時間]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[小數位數]: https://msdn.microsoft.com/library/ms187746.aspx
[浮動時間]: https://msdn.microsoft.com/library/ms173773.aspx
[幾何]: https://msdn.microsoft.com/library/cc280487.aspx
[地理位置]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[金錢]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[實數]: https://msdn.microsoft.com/library/ms173773.aspx
[29.998]: https://msdn.microsoft.com/library/ms182418.aspx
[小]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[role]: https://msdn.microsoft.com/library/ms186939.aspx
[表格]: https://msdn.microsoft.com/library/ms175010.aspx
[時間]: https://msdn.microsoft.com/library/bb677243.aspx
[時間戳記]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[唯一]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[使用者定義類型]: https://msdn.microsoft.com/library/ms131694.aspx
