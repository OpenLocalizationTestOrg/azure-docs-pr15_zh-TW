<properties
   pageTitle="從 SQL Server 資料載入 Azure SQL Data Warehouse (PolyBase) |Microsoft Azure"
   description="使用 bcp SQL Server 資料匯出到一般檔案，以匯入資料至 Azure blob 儲存體，AZCopy 和 PolyBase 至 Azure SQL Data Warehouse 將內嵌的資料。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>從 SQL Server 資料載入 Azure SQL Data Warehouse (AZCopy)

若要從 SQL Server 資料載入 Azure blob 儲存體使用 bcp 及 AZCopy 命令列公用程式。 然後，將資料載入 Azure SQL Data Warehouse 使用 PolyBase 或 Azure 資料工廠。 


## <a name="prerequisites"></a>必要條件

若要在逐步執行本教學課程中，您需要︰

- SQL Data Warehouse 資料庫
- 安裝 bcp 命令列公用程式
- 安裝 SQLCMD 命令列公用程式

>[AZURE.NOTE] 您可以從[Microsoft 下載中心][]下載 bcp 和 sqlcmd 公用程式。

## <a name="import-data-into-sql-data-warehouse"></a>將 SQL Data Warehouse 匯入資料

在本教學課程中，您將 Azure SQL Data Warehouse 中建立表格，並將資料表匯入資料。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>步驟 1: Azure SQL Data Warehouse 中建立表格

在命令提示字元中，請執行下列查詢您的執行個體建立資料表使用 sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] 如需有關建立資料表 SQL Data Warehouse 和與子句中可用的選項，請參閱[表格概觀][]或[建立表格的語法][]。

### <a name="step-2-create-a-source-data-file"></a>步驟 2︰ 建立來源資料檔案

開啟 [記事本] 和將下列行的資料複製到新的文字檔案，然後將此檔案儲存到您的本機 temp 目錄，C:\Temp\DimDate2.txt。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [AZURE.NOTE] 請務必記住該 bcp.exe 不支援 utf-8 檔案編碼。 請在使用 bcp.exe 時使用 ASCII 檔案或 utf-16 編碼檔案。

### <a name="step-3-connect-and-import-the-data"></a>步驟 3︰ 連線及匯入資料
使用 bcp，您可以在簡報中連線及匯入使用下列命令取代為適當的值的資料︰

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

您可以驗證來執行下列查詢使用 sqlcmd 已載入資料︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

這應該會傳回下列結果︰

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>步驟 4︰ 建立新載入資料的統計資料

Azure SQL Data Warehouse 尚未支援自動建立，或自動更新統計資料。 若要取得最佳效能，從您的查詢，請務必所有資料表的所有資料行建立統計資料之後第一個載入, 或出現在資料中的任何大幅變更。 統計資料的詳細說明，請參閱[統計資料][]主題開發] 群組中的主題。 以下是如何建立資料表的統計資料載入在此範例中的簡單範例

Sqlcmd 提示字元中，執行下列建立統計資料陳述式︰

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>從 SQL Data Warehouse 匯出資料。
在本教學課程中，您將建立的表格 SQL Data Warehouse 中的資料檔案。 我們會將我們先前所建立的資料匯出至稱為 DimDate2_export.txt 的新資料檔案。

### <a name="step-1-export-the-data"></a>步驟 1︰ 將資料匯出

使用 bcp 公用程式，您可以在簡報中連線及匯出資料使用下列命令取代為適當的值︰

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
您可以驗證正確開啟新的檔案匯出資料。 檔案中的資料應符合下列文字︰

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE] 因分散式系統的性質，而資料順序可能不是相同 SQL Data Warehouse 資料庫。 另一個選項是 bcp 的使用**queryout**函數撰寫查詢解壓縮，而不是 bcp 的匯出整個表格。

## <a name="next-steps"></a>後續步驟
如需載入的概觀，請參閱[將 SQL Data Warehouse 的載入資料][]。
如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。

<!--Image references-->

<!--Article references-->

[將資料載入至 SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse 開發概觀]: ./sql-data-warehouse-overview-develop.md
[表格概觀]: ./sql-data-warehouse-tables-overview.md
[統計資料]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[建立表格的語法]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft 下載中心]: https://www.microsoft.com/download/details.aspx?id=36433
