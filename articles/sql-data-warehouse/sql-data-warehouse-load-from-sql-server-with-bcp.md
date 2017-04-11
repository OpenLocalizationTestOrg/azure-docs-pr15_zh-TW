<properties
   pageTitle="從 SQL Server 資料載入 Azure SQL Data Warehouse (bcp) |Microsoft Azure"
   description="對於較小的資料的大小，使用 bcp 資料匯出到一般檔案的 SQL Server，並直接將 Azure SQL Data Warehouse 匯入資料。"
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
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>從 SQL Server 資料載入 Azure SQL Data Warehouse （一般檔案）

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

小型資料集，您可以使用 bcp 命令列公用程式，若要將資料匯出從 SQL Server，然後將它載入直接對 Azure SQL Data Warehouse。

在此教學課程中，您將使用 bcp:

- 匯出表格與從 SQL Server 使用命令 bcp （或建立簡單的範例檔案）
- 從一般檔案匯入資料表，以 SQL Data Warehouse。
- 建立統計資料載入資料。

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>開始之前

### <a name="prerequisites"></a>必要條件

若要在逐步執行本教學課程中，您需要︰

- SQL Data Warehouse 資料庫
- 安裝 bcp 命令列公用程式
- 安裝 sqlcmd 命令列公用程式

您可以從[Microsoft 下載中心][]下載 bcp 和 sqlcmd 公用程式。

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 或 utf-16 格式的資料

如果您是本教學課程中使用您自己的資料，您的資料必須使用 ASCII 或 utf-16 編碼由於 bcp 不支援 utf-8。 

PolyBase 支援 utf-8，但尚不支援 utf-16。 請注意，是否您想要與 PolyBase 合併 bcp 您需要將資料轉換為 utf-8 之後則匯出從 SQL Server。 


## <a name="1-create-a-destination-table"></a>1.建立目的資料表

定義表格中的 SQL Data Warehouse 會載入的目的資料表。 在資料表中的資料行必須對應到資料檔的每個資料列中的資料。

若要建立表格，開啟命令提示字元中，使用 sqlcmd.exe 來執行下列命令︰


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

## <a name="4-create-statistics"></a>4.建立統計資料

SQL Data Warehouse 尚未支援自動建立或自動更新統計資料。 若要取得最佳的查詢效能，請務必建立的所有資料表的所有資料行的統計資料的第一個負載或之後出現在資料中的任何大幅變更。 統計資料的詳細說明，請參閱[統計資料][]。 

執行下列命令以建立新載入資料表的統計資料。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5。 從 SQL Data Warehouse 匯出資料
玩看，您可以匯出您 SQL Data Warehouse 不在重新載入資料。  [匯出] 命令會完全相同匯出從 SQL Server。

不過，就是在結果中的差異。 當您將資料匯出資料儲存在分散式的位置，在 SQL Data Warehouse，因為每個運算節點其資料寫入輸出檔案。 輸出檔案中的資料的順序有可能不同的順序，輸入檔案中的資料。

### <a name="export-a-table-and-compare-exported-results"></a>將表格匯出，並且比較匯出的結果

若要查看匯出的資料，請開啟命令提示字元執行此命令，使用您自己的參數。 伺服器名稱是 Azure 邏輯的 SQL Server 的名稱。

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
您可以驗證正確開啟新的檔案匯出資料。 檔案中的資料應符合下列的文字，但可能會以不同的順序排序︰

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

### <a name="export-the-results-of-a-query"></a>匯出查詢的結果

您可以使用**queryout**函數的 bcp 匯出，而不是匯出整個資料表查詢的結果。 

## <a name="next-steps"></a>後續步驟
如需載入的概觀，請參閱[將 SQL Data Warehouse 的載入資料][]。
如需開發秘訣，請參閱[SQL Data Warehouse 開發概觀][]。
如需有關在 SQL Data Warehouse 建立表格，請參閱[表格概觀][]或[建立表格的語法][]。

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
