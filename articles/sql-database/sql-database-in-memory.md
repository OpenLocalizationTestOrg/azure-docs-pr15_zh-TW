<properties
    pageTitle="SQL 記憶體內，開始 |Microsoft Azure"
    description="SQL 記憶體內技術大幅改善效能的交易和分析的工作量。 瞭解如何利用這些技術。"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>快速入門的記憶體 （預覽版本） 中的 SQL 資料庫

記憶體內功能大幅改善效能的交易和分析工作負載的右邊的情況。

本主題強調兩個示範，一個用於記憶體內 OLTP 和記憶體內分析。 每個示範隨附完成步驟與您所需執行示範的程式碼。 您可以︰

- 使用程式碼測試效能結果; 不同的變化或
- 閱讀以瞭解此案例中，並查看如何建立和使用在記憶體物件的程式碼。

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [快速開始 1︰ 記憶體內 OLTP 技術的速度更快 T SQL 效能](http://msdn.microsoft.com/library/mt694156.aspx)-是另一篇文章可協助您開始使用。

#### <a name="in-memory-oltp"></a>記憶體內 OLTP

記憶體內[OLTP](#install_oltp_manuallink) （線上交易處理） 的功能是︰

- 記憶體最佳化的資料表。
- 原生編譯預存程序。


記憶體最佳化表格中有一個表示本身在作用中的記憶體，除了硬碟上的標準表示。 商業交易資料表執行速度更快，因為它們直接互動作用中的記憶體中表示法。

與記憶體內 OLTP，您可以設定才能 30 次交易處理量，根據工作負載的細節得到。


原本就已編譯的預存程序會要求較少機器指令執行期間非傳統解譯預存程序。 我們會是 1 的工期中看到原生編譯結果/100th 解譯工期。


#### <a name="in-memory-analytics"></a>記憶體內分析 

記憶體內[分析](#install_analytics_manuallink)的功能︰

Columnstore 索引改善效能的分析與報告查詢。 


#### <a name="real-time-analytics"></a>即時狀況分析

[即時](http://msdn.microsoft.com/library/dn817827.aspx)分析您可以結合記憶體內 OLTP 與分析，以取得︰

- 即時商務充分的操作的資料。


#### <a name="availability"></a>顯示狀態


GA，一般的顯示狀態︰

- [Columnstore 索引](http://msdn.microsoft.com/library/dn817827.aspx)的*磁碟上*。


預覽︰

- 記憶體內 OLTP
- 即時作業的狀況分析


在預覽中記憶體功能時的考量均所述[本主題中更新版本](#preview_considerations_for_in_memory)。


> [AZURE.NOTE] 預覽功能是僅適用於[*進階版*](sql-database-service-tiers.md)的資料庫不彈性的集區，並不提供任何基本或標準的資料庫。  彈性的集區中的進階版資料庫的支援即將推出。 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>答︰ 安裝在記憶體 OLTP 範例

您可以建立[Azure 入口網站](https://portal.azure.com/)中按幾下 AdventureWorksLT [V12] 範例資料庫。 然後本節中的步驟會說明如何豐富主版頁面內容與 AdventureWorksLT 資料庫︰

- 記憶體內的資料表。
- 原本就已編譯的預存程序。


#### <a name="installation-steps"></a>安裝步驟

1. 在[Azure 入口網站](https://portal.azure.com/)中，建立進階資料庫 V12 伺服器上。 設定**來源**AdventureWorksLT [V12] 範例資料庫。
 - 如需詳細指示，您可以看到[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。

2. 連線至 SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx)的資料庫。

3. 將[記憶體內 OLTP TRANSACT-SQL 指令碼](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql)複製到剪貼簿中。
 - T SQL 指令碼您在步驟 1 中建立 AdventureWorksLT 範例資料庫中建立必要記憶體內的物件。

4. T SQL 指令碼貼入 SSMS，然後再執行指令碼。
 - 重要的是`MEMORY_OPTIMIZED = ON`子句 CREATE TABLE 陳述式，如下所示︰


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>錯誤 40536


如果您收到錯誤 40536，當您執行 T SQL 指令碼時，請執行下列 T SQL 指令碼，以驗證資料庫是否支援記憶體內︰


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


結果是**0**表示記憶體內不受支援，及 1 表示支援。 診斷問題︰

- 請確定在記憶體 OLTP 功能變成作用中的預覽後所建立的資料庫。
- 確定資料庫位於進階版服務層級。


#### <a name="about-the-created-memory-optimized-items"></a>關於建立的記憶體最佳化項目

**表格**︰ 範例包含下列的記憶體最佳化表格︰

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


您可以檢查記憶體最佳化透過由 SSMS 中的**物件總管] 中**的表格︰

- 以滑鼠右鍵按一下**資料表** > **篩選** > **篩選設定** > **記憶體最佳化**等於 1。


或者，您可以查詢類別目錄檢視，例如︰


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**原本就已編譯的預存程序**︰ SalesLT.usp_InsertSalesOrder_inmem 可以檢查透過類別目錄檢視查詢︰


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>執行範例 OLTP 工作負載

下列兩個*預存程序*的唯一差異為的第一個程序使用資料表記憶體最佳化版本，第二個程序使用一般的磁碟上的表格︰

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


在此區段中，您可以瞭解如何使用方便**ostress.exe**公用程式執行壓力層級的兩個預存程序。 您可以比較兩個壓力就會執行完成所需的時間。


當您執行 ostress.exe 時，我們建議您傳遞設計兩者的參數值︰

- 執行 [大量的同時連線，以使用-n100。
- 有連線迴圈數百的次數，以使用-r500。


不過，您可能會想要從多較小的值，例如-n10 開始並-r50，以確保一切運作。


### <a name="script-for-ostressexe"></a>適用於 ostress.exe 指令碼


此區段中顯示內嵌我們 ostress.exe 命令列中的 T SQL 指令碼。 指令碼會使用您在先前安裝的 T SQL 指令碼所建立的項目。


下列指令碼會插入下列記憶體最佳化*資料表*中的範例銷售訂單的五個線條的項目︰

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


若要讓前面的 ostress.exe T-SQL _ondisk 版本，就只*_inmem*子字串的兩個項目取代*_ondisk*。 這些取代會影響表格和預存程序的名稱。


### <a name="install-rml-utilities-and-ostress"></a>安裝 RML 公用程式和 ostress


最好是您打算在 Azure VM 執行 ostress.exe。 您想要建立[Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)中 AdventureWorksLT 資料庫所在的相同 Azure 地理區域。 但您可以 ostress.exe 改為執行您的膝上型電腦上。


VM，或在上述程序主控您選擇，安裝重新執行標記語言 (RML) 公用程式，包括 ostress.exe。

- 請參閱 ostress.exe 討論[的記憶體內 OLTP] 樣本資料庫](http://msdn.microsoft.com/library/mt465764.aspx)中。
 - 或參閱[記憶體內 OLTP 範例資料庫](http://msdn.microsoft.com/library/mt465764.aspx)。
 - 請參閱[部落格安裝 ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)或



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>第一次執行 _inmem 負荷的工作量


您可以執行我們 ostress.exe 命令列使用*RML 命令提示字元*視窗。 命令列參數直接 ostress 至︰

- 並行執行 100 (-n100)。
- 有 50 次執行 T SQL 指令碼每一種連線 (-r50)。


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


若要執行上述 ostress.exe 命令列︰


1. 重設資料庫資料內容，在 SSMS，若要刪除任何先前執行已插入的所有資料執行以下命令︰
```
EXECUTE Demo.usp_DemoReset;
```

2. 剪貼簿複製上述 ostress.exe 命令列的文字。

3. 取代`<placeholders>`參數-S-U-P-d 以正確的實際值。

4. 執行您編輯的命令列 RML Cmd 視窗中。


#### <a name="result-is-a-duration"></a>結果是工期


Ostress.exe 完成時，它會寫入 RML Cmd 視窗中執行的持續時間為輸出其最後一列。 例如，為較短的測試執行持續約 1.5 分鐘的時間︰

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>重設，編輯 _ondisk，然後重新執行


結果的 _inmem 執行之後，請執行下列步驟執行 _ondisk 的︰


1. 重設資料庫中 SSMS，若要刪除已插入的上一個執行的所有資料執行以下命令︰
```
EXECUTE Demo.usp_DemoReset;
```

2. 編輯以*_ondisk*取代所有*_inmem* ostress.exe 命令列。

3. 重新執行 ostress.exe 第二次，並擷取工期結果。

4. 一次重設負責刪除何種可以大量測試資料中的資料庫。


#### <a name="expected-comparison-results"></a>預期的比較結果

我們記憶體內的測試有與在相同的資料庫 Azure 地區 Azure VM 上執行的 ostress 顯示**9 時間**的效能提升了這個簡單的工作量。



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B。 安裝在記憶體分析範例


在此區段中，您會比較 IO 和統計資料結果時使用的與傳統索引的 columnstore 索引。


在 OLTP 工作負載的即時分析，最好經常使用的非叢集 columnstore 索引。 如需詳細資訊請參閱[Columnstore 索引所述](http://msdn.microsoft.com/library/gg492088.aspx)。



### <a name="prepare-the-columnstore-analytics-test"></a>準備 columnstore 分析測試


1. 若要建立全新的 AdventureWorksLT 資料庫的範例使用 Azure 入口網站。
 - 使用相同名稱。
 - 選擇 [任何進階版服務層級]。

2. 將[sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql)複製到剪貼簿中。
 - T SQL 指令碼您在步驟 1 中建立 AdventureWorksLT 範例資料庫中建立必要記憶體內的物件。
 - [維度] 資料表中，且兩個交易資料表，就會建立指令碼。 交易資料表會填入 3.5 百萬個資料列每個。
 - 指令碼可能需要 15 分鐘才能完成。

3. T SQL 指令碼貼入 SSMS，然後再執行指令碼。
 - 重要有**COLUMNSTORE**關鍵字上**CREATE INDEX**陳述式中，為︰<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. 相容性層級 130 設定 AdventureWorksLT:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - 層級 130 沒有直接關聯在記憶體功能。 但層級 130 通常會提供更快速的查詢效能 120 一樣。


#### <a name="crucial-tables-and-columnstore-indexes"></a>重要的資料表和 columnstore 索引


- dbo。FactResellerSalesXL_CCI 是直的**columnstore**索引鍵的資料表，其中具有進階*資料*層級的壓縮。

- dbo。FactResellerSalesXL_PageCompressed 是相當於一般叢集索引鍵的資料表，其中壓縮只能在*頁面*層級。


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>若要比較 columnstore 索引的重要查詢


[以下](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql)是您可以執行數個 T SQL 查詢類型，查看改善效能。 從 T SQL 指令碼中的步驟 2] 中有一組的直接感興趣的查詢。 兩個查詢不同只在同一行︰


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


群組直的 columnstore 索引是 FactResellerSalesXL**_CCI**表格。

下列 T SQL 指令碼摘錄列印統計值為 IO 與每個資料表查詢的時間。


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>記憶體內 OLTP 預覽考量


Azure SQL 資料庫中記憶體 OLTP 功能變成[作用中的預覽，2015 年 10 月 28 日](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/)。


在目前的預覽，記憶體內 OLTP 支援的方案僅適用於︰

- 在 [*進階版*服務層級的資料庫。

- 在記憶體 OLTP 功能之後所建立的資料庫成為使用中。
 - 為新資料庫不支援在記憶體 OLTP 還原資料庫之前在記憶體 OLTP 功能成為使用中所建立。


如有疑問，您可以隨時執行下列 T SQL 選取以確定您的資料庫是否支援記憶體內 OLTP。 結果是**1**表示資料庫支援記憶體內 OLTP:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


如果查詢會傳回**1**，記憶體內 OLTP 支援此資料庫]，而任何資料庫備份和還原資料庫建立此資料庫。


#### <a name="objects-allowed-only-at-premium"></a>只允許進階版的物件


如果資料庫中包含下列類型的記憶體內 OLTP 物件或類型的任何，不支援降級服務層級，從進階基本或標準的資料庫。 若要將降級的資料庫，您必須先卸除這些物件︰

- 記憶體最佳化的表格
- 記憶體最佳化表格類型
- 原本就已編譯的模組


#### <a name="other-relationships"></a>其他的關聯


- 預覽時不支援使用記憶體內 OLTP 彈性的集區中的資料庫時的功能。
 - 若要將具有或已經有彈性的集區的記憶體內 OLTP 物件的資料庫，請遵循下列步驟︰
  - 1. 卸除資料庫中的任何記憶體最佳化的資料表、 資料表類型和原本就已編譯的 T SQL 模組
  - 2. 將資料庫的服務層變更為標準
  - 3. 將資料庫移到彈性的資料庫

- 使用記憶體內 OLTP 與 SQL Data Warehouse 不受支援。
 - SQL Data Warehouse 支援的記憶體內分析 columnstore 索引 」 功能。

- 查詢存放區不會擷取內部原本編譯模組的查詢。

- 記憶體內 OLTP 不支援某些 TRANSACT-SQL 功能。 這適用於 Microsoft SQL Server 和 Azure SQL 資料庫。 如需詳細資訊，請參閱︰
 - [記憶體內 OLTP TRANSACT-SQL 支援](http://msdn.microsoft.com/library/dn133180.aspx)
 - [不支援的記憶體內 OLTP TRANSACT-SQL 建構](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>後續步驟


- 請嘗試[現有 Azure SQL 應用程式中使用記憶體內 OLTP。](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>其他資源

#### <a name="deeper-information"></a>更深入的資訊

- [深入了解記憶體內 OLTP，適用於 Microsoft SQL Server 和 Azure SQL 資料庫](http://msdn.microsoft.com/library/dn133186.aspx)

- [深入了解即時 MSDN 上的運作狀況分析](http://msdn.microsoft.com/library/dn817827.aspx)

- 在上[的一般工作負載的圖樣與移轉考量](http://msdn.microsoft.com/library/dn673538.aspx)，說明記憶體內 OLTP 經常提供顯著的效能提升的工作量圖樣白皮書 （英文)。

#### <a name="application-design"></a>應用程式的設計

- [記憶體 OLTP （在記憶體最佳化）](http://msdn.microsoft.com/library/dn133186.aspx)

- [在現有 Azure SQL 應用程式的使用中的記憶體 OLTP。](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>工具

- [SQL Server Data Tools 預覽 (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)，每月最新版本。

- [SQL Server 的描述重新執行標記語言 (RML) 公用程式](http://support.microsoft.com/en-us/kb/944837)

- 記憶體內 OLTP[監視器記憶體內儲存空間](sql-database-in-memory-oltp-monitoring.md)。

