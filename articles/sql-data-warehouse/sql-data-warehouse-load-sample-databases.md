<properties
   pageTitle="範例資料載入 SQL Data Warehouse |Microsoft Azure"
   description="將 SQL Data Warehouse 載入範例資料"
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
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>將 SQL Data Warehouse 載入範例資料

請遵循這些簡單步驟載入並查詢 Adventure Works 範例資料庫。 這些指令碼第一次使用 sqlcmd 執行 SQL 這將會建立資料表和檢視表。 只要建立資料表之後，指令碼會使用 bcp 載入資料。  如果您還沒有 sqlcmd 和 bcp 安裝，請遵循這些連結來[安裝 bcp][]並[安裝 sqlcmd][]。

##<a name="load-sample-data"></a>載入範例資料

1. 下載[Adventure Works 範例指令碼的 SQL Data Warehouse][] zip 檔案。

2. 您的本機電腦上的目錄中解壓縮從下載 zip 檔案。

3. 編輯 [擷取的檔案 aw_create.bat 並設定下列變數找到頂端的檔案。  請確定之間不能有空白 「 = 」 與參數。  以下是範例編輯可能的外觀。

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. 從 Windows 命令提示字元中，執行編輯的 aw_create.bat。  請確定您是在您儲存 aw_create.bat 您編輯的過的目錄。
這個指令碼會...
    * 卸除任何 Adventure Works 資料表或已存在於資料庫中的檢視
    * 建立 Adventure Works 資料表及檢視
    * 載入使用 bcp 每個 Adventure Works 資料表
    * 確認每個 Adventure Works 資料表的資料列計數
    * 收集針對每個 Adventure Works 資料表的每個資料行的統計資料


##<a name="query-sample-data"></a>查詢範例資料

一旦您已載入您的 SQL Data Warehouse 的一些範例資料，您可以快速地執行幾個查詢。  若要執行查詢，連線到您新建立的 Adventure Works 資料庫中使用 Visual Studio 與 SSDT，Azure SQL DW [Visual Studio 的查詢][]文件中所述。

若要取得員工的所有資訊的簡單 select 陳述式的範例︰

```sql
SELECT * FROM DimEmployee;
```

若要查看所有銷售額的總數量上每日使用建構，例如 [群組依據較複雜的查詢範例︰

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

選取 [使用 WHERE 子句，篩選出訂單從特定日期之前的範例︰

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse 支援執行幾乎所有 T SQL 建構 SQL Server 支援。  任何差異會記錄在我們[移轉程式碼][]的說明文件。

## <a name="next-steps"></a>後續步驟
既然您已有一些範例資料的查詢，嘗試，查看如何[開發][]、[載入][]時，或[移轉][]到 SQL Data Warehouse。

<!--Image references-->

<!--Article references-->
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[使用 Visual Studio 的查詢]: sql-data-warehouse-query-visual-studio.md
[移轉程式碼]: sql-data-warehouse-migrate-code.md
[安裝 bcp]: sql-data-warehouse-load-with-bcp.md
[安裝 sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[冒險適合 SQL Data Warehouse 指令碼範例]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
