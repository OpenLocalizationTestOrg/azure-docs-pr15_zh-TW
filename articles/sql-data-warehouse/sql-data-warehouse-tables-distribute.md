<properties
   pageTitle="發送 SQL Data Warehouse 中的資料表 |Microsoft Azure"
   description="快速入門發送 Azure SQL Data Warehouse 中的資料表。"
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>發送 SQL Data Warehouse 中的資料表

> [AZURE.SELECTOR]
- [概觀][]
- [資料類型][]
- [發佈][]
- [索引][]
- [分割][]
- [統計資料][]
- [暫時][]

SQL Data Warehouse 是人連線平行處理 (MPP) 分散式資料庫系統。  將資料並處理跨多個節點的功能，SQL Data Warehouse 可以提供這些延展性-以外的任何單一系統。  決定如何發佈您的資料，在您的 SQL Data Warehouse 是一種達到最佳效能最重要的因素。   最佳效能，關鍵最小化資料移動和最小化資料移動的關鍵依序選取正確的通訊群組策略。

## <a name="understanding-data-movement"></a>了解資料移動

在 MPP 系統中，每個資料表的資料分割跨多個基本的資料庫。  在個別的分散式資料庫的任何其他資料庫間的互動上執行只要透過傳遞 MPP 系統上最最佳化的查詢。  例如，假設您有包含兩個資料表、 銷售和客戶的銷售資料的資料庫。  如果您有要加入至您的客戶資料表的銷售資料表查詢除以您銷售 」 和 「 客戶資料表設定客戶的數字，置於不同的資料庫中的每一位客戶，可以解決任何加入銷售與客戶的查詢中使用不會知道其他資料庫的每個資料庫。  相反地，如果您的銷售資料除以順序的數字與您的客戶資料，以客戶數字時，然後任何指定的資料庫不會有相對應的資料每位客戶，因此如果您想要加入您的銷售資料至您的客戶資料，您必須從其他資料庫，取得每位客戶的資料。  在第二個範例中，移動資料需要發生將客戶資料移到的銷售資料，以便可以加入兩個資料表。  

移動資料不一定都不正確的項目，有時也需要解決查詢。  但時可避免額外的步驟，符合您的查詢會執行速度更快。  聯結資料表，或執行彙總時，最常就會發生資料移動。  通常您需要執行兩個，因此您時，您可能可以最佳化以用於一種情況，例如聯結，仍需要協助您解決的其他一個案例中，例如彙總資料移動。  秘訣找出已減輕。  在大部分情況下，發佈大型交易資料表中經常聯結的資料行是減少大部分的資料移動的最有效方法。  散發資料加入資料行是更常見的方法，以減少資料移動比散發參與彙總資料行的資料。

## <a name="select-distribution-method"></a>選取 [發佈方法

在幕後 SQL Data Warehouse 會將您的資料分割成 60 的資料庫。  每個個別的資料庫稱為 「**通訊**」。  當資料載入至每個資料表時，SQL Data Warehouse 必須知道如何將您的資料分割跨這些 60 散佈。  

表格層級定義通訊方法，目前有兩個選擇︰

1. **循環**的平均但隨機散發資料。
2. **雜湊分散式**分散給根據雜湊單一資料行值的資料

根據預設，當您未定義資料通訊方法，表格會分散使用**循環**發佈方法。  不過，當您更複雜且實作中，您會要考慮使用**雜湊分散式**表格來移動資料會依序最佳化查詢效能的最小化。

### <a name="round-robin-tables"></a>循環表格

使用發佈資料的循環方法，是非常如何聽起來。  載入您的資料，每個資料列會直接傳送到下一個分配。  散發資料的這個方法會永遠隨機資料非常平均地分配所有散佈。  也就是說，有循環程序其中放置資料時所完成的任何排序。  循環分配通常稱為隨機雜湊因此。  循環分散式表格就不需要了解資料。  因此，循環表格通常進行良好的載入目標。

根據預設，如果選擇沒有通訊方法，將使用循環通訊方法。  不過，當循環表格易於使用，因為資料隨機分佈表示系統無法保證的通訊群組的系統每個資料列是。  如此一來，系統有時需要叫用資料移動作業之前，可以解決查詢更有效地組織您的資料。  這個額外的步驟可能緩慢的查詢。

請考慮使用循環分配表格在以下情況︰

- 當簡單的起點快速入門
- 如果沒有明顯聯結索引鍵
- 如果沒有適合雜湊發佈表格欄
- 如果資料表不共用與其他資料表的常見的聯結索引鍵
- 如果聯結不嚴重比其他查詢中的聯結
- 資料表時暫時臨時資料表

兩個範例會建立循環資料表︰

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] 循環時要明確的 DDL 中的預設表格類型最佳作法是，讓清除給其他人的 [表格版面配置。

### <a name="hash-distributed-tables"></a>雜湊分散式的表格

使用**雜湊分散式**演算法來發佈您的資料表可以改善效能許多案例在查詢時減少移動資料。  分散式雜湊資料表為分開的分散式資料庫時使用雜湊演算法您選取的單一資料行的資料表。  散發] 資料行是什麼決定資料分割跨分散式資料庫的方式。  雜湊函數會使用通訊群組資料行，來指定列至散佈。  雜湊演算法產生的通訊群組，請確定。  這是相同的通訊群組會永遠都使用相同的資料類型的值相同。    

此範例會建立分散式 id 上的資料表︰

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>選取 [發佈欄

當您選擇**雜湊發佈**表格時，您必須選取一個通訊群組資料行。  當選取通訊群組資料行，有三個主要考量的因素。  

選取 [將的單一資料行︰

1. 不會更新
2. 資料平均地分配，避免 skew 的資料
3. 最小化資料移動

### <a name="select-distribution-column-which-will-not-be-updated"></a>選取 [將不會更新通訊群組資料行

發佈欄為不可更新，因此，請選取具有靜態值的欄。  如果資料行需要更新，不通常是很好的通訊群組適用。  如果您必須在此更新通訊群組資料行的情況下，進行第一次刪除資料列，然後插入新的資料列。

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>選取平均地分配資料的通訊群組資料行

由於分散式的系統執行只一樣快速其最慢的通訊群組，請務必以達到平衡的執行整個系統將平均分配給工作。  工作的除數分散式系統的方式根據所在的每個散發資料。  如此可讓您選取正確的通訊群組資料行散發資料，使每個通訊群組有相同的工作，並會同時完成其工作的部分很重要。  當工作以及的除數至整個系統時，資料是通過平衡散佈。  當資料不平均平衡時、 我們呼叫此**資料扭曲**。  

若要平均分割資料並避免 skew 的資料，請考慮下列選取您的通訊群組資料行時︰

1. 選取包含唯一值的有效數字的資料行。
2. 避免發送上有幾個唯一值的資料行的資料。 
3. 避免發佈具有經常性 null 值的資料行的資料。
4. 避免發佈的日期資料行上的資料。

1 的 60 散佈雜湊每個值，因為達成平均分配您要選取一欄是唯一的高度，其中包含超過 60 的唯一值。  若要說明，請考慮欄僅有 40 的唯一值的大小寫。  通訊群組索引鍵] 選取此欄該資料表的資料會讓登陸 40 散佈最離開 20 散佈沒有資料與不應如何處理。  相反地，其他 40 散佈必須執行這項作業，如果資料的平均散佈超過 60 散佈將更多工作。  這種情況下是 skew 資料的範例。

MPP 系統中每個查詢步驟等待所有散發完成其工作的共用。  如果一個通訊群組會比其他更多工作，然後的其他散佈資源是基本上浪費只要等候忙碌的通訊群組。  當工作不平均分散所有散發時，我們呼叫此**處理 skew**。  處理 skew 會導致查詢執行速度比如果工作量可以平均分散到散佈。  處理 skew 會導致 skew 的資料。

避免發送高度 nullable 欄為 null 值會開啟上相同的通訊群組。 發佈的日期資料行上可能也會導致處理 skew 因為給定日期的所有資料將會都開啟上相同的通訊群組。 多個使用者執行查詢所有篩選在相同的日期，然後會進行所有的工作指定的日期之後的 60 散佈 1 時，才會在一個通訊群組。 在此案例中，查詢可能會執行 60 次低於如果資料平均分佈所有散佈。 

已存在的不適合資料行，然後請考慮使用循環的通訊方法。

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>將 [最小化資料移動選取的通訊群組資料行

選取正確的通訊群組資料行]，將資料移動最小化是一種最重要的策略最佳化您的 SQL Data Warehouse 的效能。  聯結資料表，或執行彙總時，最常就會發生資料移動。  使用中的資料行`JOIN`， `GROUP BY`， `DISTINCT`，`OVER`和`HAVING`子句所有讓的**好**雜湊分配候選項目。 

另一方面中的資料行`WHERE`子句執行**不**讓的好的雜湊欄候選項目限制哪些散佈參與查詢，因為造成處理扭曲。  使用例子欄可能會想要散發，但通常可能會導致 skew 此處理的是日期資料行。

一般而言，如果您經常相關的兩個大交易資料表中加入，您會取得最大的效能發送兩個資料表中的其中一個加入資料行。  如果您有永遠不會加入另一個大型的交易資料表的資料表，然後查看常見問題所在的資料行`GROUP BY`子句。

有幾個重要準則必須符合若要避免資料移動期間聯結︰

1. 聯結中所包含的資料表必須是雜湊分佈的上**一個**參與聯結的資料行。
2. 加入資料行資料類型必須符合兩個資料表之間。
3. 必須使用等號運算子加入資料行。
4. 連接類型可能無法`CROSS JOIN`。


## <a name="troubleshooting-data-skew"></a>疑難排解 skew 的資料

使用雜湊發佈方法分佈表格資料時有一些散佈會偏態比例詳細資料，與其他人有機會。 過多資料 skew 會影響查詢效能，因為最終查詢結果的分散式必須等到完成的最長執行分配。 根據資料扭曲的程度而定，您可能需要地址。

### <a name="identifying-skew"></a>識別 skew

為準識別資料表的簡單方法是使用`DBCC PDW_SHOWSPACEUSED`。  這是非常快速又簡單的方式，以查看儲存於每個 60 散佈資料庫的資料表資料列數目。  請記住，最平衡效能，分散式表格中的資料列應該平均分散到所有散佈。

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

不過，如果您在查詢 Azure SQL Data Warehouse 動態管理檢視上，您可以執行更詳細的分析。  若要開始，建立使用[表格概觀][概觀]文章 SQL 檢視[dbo.vTableSizes][]檢視。  檢視建立後，請執行此查詢以識別哪些資料表已超過 10%資料 skew。

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>解決 skew 的資料

不所有 skew 即足以保證修正。  在某些情況下，某些查詢中的表格的效能將超越 skew 資料的傷害。  若要決定是否您應該解決資料 skew 在資料表中，您應該瞭解盡可能資料區與查詢的相關在您的工作量。   若要查看影響的其中一個方法是傾斜的使用[查詢監控][]文件中的步驟，來監控的 skew 查詢效能，尤其是傾斜的個別散佈完成需要多久查詢影響的影響。

散發資料是尋找最小化資料 skew 之間移動資料最小化的正確平衡的問題。 這些可以反向目標，並有時您會想要保留資料 skew 以減少移動資料。 例如，當通訊群組資料行經常連接和彙總 [共用] 欄，您會會最小化移動資料。 擁有的最小的資料移動所帶來的影響扭曲的資料。 

若要解決資料 skew 一般的方式是重新建立不同的通訊群組資料行的資料表。 由於沒有方法可以變更現有的資料表，若要變更的表格的方式的通訊群組資料行以重新建立 [CTAS] []。  以下是兩個範例如何解決 skew 資料︰

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>範例 1︰ 重新建立新的通訊群組資料行的資料表

此範例使用 [CTAS] 的 [重新建立不同的雜湊通訊群組資料行的資料表。 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>範例 2︰ 重新建立資料表使用循環通訊群組

此範例使用 [CTAS] 的 [重新建立 [循環，而不是雜湊通訊群組的資料表。 這項變更會產生更犧牲提高的資料移動資料通訊群組。 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>後續步驟

若要進一步瞭解表格設計，請參閱[等距分佈][]、 [Index][]、[分割][]、[資料類型][]、[統計資料][]和[暫存資料表][暫時]文章。

如需最佳作法的概觀，請參閱[SQL 資料倉庫最佳作法][]。


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
[查詢監控]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
