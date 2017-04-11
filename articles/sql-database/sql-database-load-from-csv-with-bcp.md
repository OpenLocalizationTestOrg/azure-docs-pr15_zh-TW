<properties
   pageTitle="從 CSV 檔案的資料載入 Azure SQL Databaase (bcp) |Microsoft Azure"
   description="對於較小的資料的大小，使用 bcp Azure SQL 資料庫將資料匯入。"
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>從 CSV 載入 Azure SQL Data Warehouse （一般檔案）

您可以使用 bcp 命令列公用程式，將資料匯入從 CSV 檔案 Azure SQL 資料庫。

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

若要在逐步執行本教學課程中，您需要︰

- Azure SQL 資料庫邏輯伺服器及資料庫
- 安裝 bcp 命令列公用程式
- 安裝 sqlcmd 命令列公用程式

您可以從[Microsoft 下載中心][]下載 bcp 和 sqlcmd 公用程式。

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 或 utf-16 格式的資料

如果您是本教學課程中使用您自己的資料，您的資料必須使用 ASCII 或 utf-16 編碼由於 bcp 不支援 utf-8。 

## <a name="1-create-a-destination-table"></a>1.建立目的資料表

定義 SQL 資料庫中的資料表與目的資料表。 在資料表中的資料行必須對應到資料檔的每個資料列中的資料。

若要建立表格，開啟命令提示字元中，使用 sqlcmd.exe 來執行下列命令︰


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2.建立來源資料檔案

開啟 [記事本] 和將下列行的資料複製到新的文字檔案，然後將此檔案儲存到您的本機 temp 目錄，C:\Temp\DimDate2.txt。 此資料是 ASCII 格式。

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

（選用）匯出您自己的資料從 SQL Server 資料庫，請開啟命令提示字元中，執行下列命令。 取代您自己的資訊中的表格名稱、 伺服器名稱、 資料庫名稱、 使用者名稱和密碼。

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```

## <a name="3-load-the-data"></a>3.載入資料
將資料載入開啟命令提示字元中，執行下列命令，使用您自己的資訊取代伺服器名稱、 資料庫名稱、 使用者名稱和密碼的值。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

使用這個命令來確認已正確地載入資料

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

結果看起來應該像這樣︰

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


## <a name="next-steps"></a>後續步驟

若要移轉的 SQL Server 資料庫，請參閱[SQL Server 資料庫移轉](sql-database-cloud-migrate.md)。

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft 下載中心]: https://www.microsoft.com/download/details.aspx?id=36433
