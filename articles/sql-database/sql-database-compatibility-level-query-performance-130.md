<properties
    pageTitle="相容性層級，如何評估 |Microsoft Azure"
    description="步驟和決定的相容性層級最適合您的資料庫 Azure SQL 資料庫或 Microsoft SQL Server 上的工具"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>改良的查詢效能與 Azure SQL 資料庫中的層級 130 相容性


Azure SQL 資料庫無障礙數百個資料庫在執行許多不同的相容性層級，保留和保證對應版本的 Microsoft SQL Server 回溯相容性的所有客戶 ！

因此，不會防止變更最新的相容性層級的任何現有的資料庫的優點從新最佳化及查詢的處理器功能的客戶。 歷程記錄的提醒，預設相容性層級的 SQL 版本的對齊方式如下所示︰

- 100︰ 在 SQL Server 2008 和 Azure SQL 資料庫 V11。
- 110︰ 在 SQL Server 2012 和 Azure SQL 資料庫 V11。
- 120︰ 在 SQL Server 2014 和 Azure SQL 資料庫 V12。
- 130︰ 在 SQL Server 2016 和 Azure SQL 資料庫 V12。


> [AZURE.IMPORTANT] 啟動**mid 年 6 月 2016年**在 Azure SQL 資料庫中，預設的相容性層級會 130，而不是成 120，**新建立的**資料庫。
> 
> Mid 年 6 月 2016年之前所建立的資料庫會*不*會影響，並將維護其目前的相容性等級 （100、 110 或 120）。 從 Azure SQL 資料庫版本以 V12 V11 不會有其相容性層級移轉的資料庫變更其中。


本文中，我們會探索相容性層級 130，以及如何運用這些優點的優點。 我們地址可能的影響上現有的 SQL 應用程式的查詢效能。


## <a name="about-compatibility-level-130"></a>有關的相容性層級 130


首先，如果您想要知道目前資料庫的相容性層級，請執行下列 SQL 陳述式。


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


這項變更層級 130**新**建立資料庫的之前，現在就讓我們檢閱哪些這項變更的功能部分基本查詢範例，並查看任何人都可以助益它。

關聯式資料庫中的查詢處理可以很複雜，而且可能會導致許多電腦科學和數學瞭解既有的設計選項和行為。 在此文件中的內容已經刻意簡化以確保任何人只要有一些最小的技術背景可以有何影響的相容性層級的變更，並決定它的優點應用程式。

現在就讓快速瞭解相容性層級 130 會顯示在資料表中。  您可以在[變更資料庫相容性層級 (SQL)](https://msdn.microsoft.com/library/bb510680.aspx)，找到更多詳細資料，但有簡短摘要︰

- 插入選取陳述式的 [插入] 作業可多執行緒或可以有平行計劃，時前單一階層式這項作業。
- 記憶體最佳化表格及表格變數查詢現在可以有平行計劃，而這項作業是也單一執行緒之前。
- 現在要取樣，自動更新的記憶體最佳化表格統計資料。 請參閱[資料庫引擎中的新增功能︰ 記憶體內 OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)如需詳細資訊。
- 批次模式 v/s 的資料列模式] 會變更資料行市集索引
  - 排序的欄市集索引的資料表現在都會批次模式。
  - 批次模式，例如 TSQL 延隔時間潛在客戶陳述式現在運作視窗化彙總。
  - 具有多個不同的子句的資料行存放區資料表的查詢操作以批次模式。
  - 查詢執行 DOP = 1 或循序計劃也執行批次模式。
- 最後，基數估計改良項目實際上與相容性等級成 120，但您在執行較低的相容性等級 （也就是 100 或 110），請移至相容性層級 130 也會顯示下列改良功能，以及有這些也效益應用程式的查詢效能。


## <a name="practicing-compatibility-level-130"></a>練習相容性層級 130


第一個就讓我們開始部分的表格、 索引和練習某些新功能建立的隨機資料。 SQL Server 2016，或下 Azure SQL 資料庫，則可以執行 TSQL 指令碼範例。 不過，建立 Azure SQL 資料庫時，請確定您選擇 [最小的 P2 資料庫因為您需要至少有幾個核心允許多執行緒處理，因此受益這些功能。


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


現在，我們有一些即將與相容性等級 130 的查詢處理功能的外觀。


## <a name="parallel-insert"></a>平行插入


執行下列 TSQL 陳述式執行相容性層級底下 120 和 130 分別執行插入作業，在單一階層式模型 (120)，及多執行緒模型 (130) 中插入作業。


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


要求的實際查詢計劃，查看其圖形表示或 XML 內容，您可以決定函數位於播放哪些基數估計。 圖 1 上查看計劃-並存，我們可以清楚看到的欄市集插入執行進入從序列中 120 平行 130 中。 此外，請注意，iterator 圖示，顯示兩個平行箭號，說明 fact 現在 iterator 執行 130 計劃中的變更確實平行。 如果您有大型的插入作業，完成時，平行執行，連結到您有一種方式的資料庫，核心數會執行效能較佳。最多 100 次更快速地視情況 ！


*圖 1︰ 插入作業會變更從循序平行相容性層級 130。*


![圖 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>序列批次模式


同樣地，移動相容性層級 130 處理資料列時，可讓批次處理模式。 首先，批次模式作業時，可只資料行存放區索引的位置。 第二，以批次通常表示 ~ 900 列，使用最佳化多核心 CPU，較高的記憶體處理量的程式碼邏輯，並直接運用壓縮的欄市集盡可能的資料。 若符合下列條件，SQL Server 2016 可以一次，處理 ~ 900 資料列，而不是 1 列在時間]，然後整體負荷作業的結果，現在共用整個批次，減少整體成本循列。 此共用的作業基本上合併使用欄市集壓縮量減少選取批次模式作業中的延遲。 您可以尋找欄存放區的相關的更多詳細資料，並批次以[Columnstore 索引指南](https://msdn.microsoft.com/library/gg492088.aspx)模式。


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


為 [顯示] 下方，觀察查詢方案並排顯示上圖 2，我們可以處理模式的相容性層級，已變更，因此，當完全兩個相容性層級中執行查詢時，我們可以看到該大部分的處理時間，請參閱所花的相較於批次模式 （14%)，其中已處理 2 批次的資料列模式 （86%)。 增加資料集，就會增加獲得的好處。


*圖 2︰ 選取作業變更循序到相容性層級 130 批次模式。*


![圖 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>在排序執行批次模式


類似於上方，但套用排序作業，從 [資料列模式 （相容性層級 120） 轉換為批次模式 （相容性層級 130） 可改善效能的理由相同的排序作業。


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


顯示--並排上圖 3，我們可以看到的資料列模式中的 [排序] 作業代表 81%的成本時批次模式僅代表 19 的 %成本 （分別 81%和排序本身 56%）。


*圖 3︰ 排序作業會變更列相容性層級 130 批次模式。*


![圖 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


當然，這些範例只包含百的資料列，其沒有時查看用於大部分的 SQL Server 資料這幾天。 相反地，只要專案這些針對數百萬個資料列，這可以在幾分鐘的時間執行一命擱置的工作量自然每天的翻譯。


## <a name="cardinality-estimation-ce-improvements"></a>估計 (CE) 基數的改良功能


介紹與 SQL Server 2014，任何資料庫執行相容性級 120 或上方會使您使用新的基數估計的功能。 基本上，基數評估為邏輯用來決定如何 SQL server 將執行其估計成本為基礎的查詢。 估計的計算是採用輸入物件參與該查詢的相關統計資料。 實際上，高階基數估計函數是列計數預計及散發相異值計算值的相關資訊，重複計算中包含的資料表和查詢中參考的物件。 取得這些評估錯誤，可能會導致不必要的磁碟 I/O，因為沒有足夠的記憶體授權 （亦即 TempDB 圖示之下），或選取範圍的序列計劃執行平行計劃執行，透過等等。 結束時，查詢執行的整體效能降低會導致不正確的估計值。 另一側，更好的估計值，提供更精確的估計值，會導致更好的查詢執行 ！

如之前所述，查詢最佳化和評估複雜的重要性，但如果您想要進一步瞭解查詢計劃和基數估算程式，您可以參考的更深入的不動產[最佳化您的查詢計劃 SQL Server 2014 基數估算程式](https://msdn.microsoft.com/library/dn673537.aspx)在文件。


## <a name="which-cardinality-estimation-do-you-currently-use"></a>哪些基數評估您目前使用？


若要判斷底下執行查詢的基數評估，我們就是使用下列查詢範例。 請注意，第一個範例會執行相容性層級 110 意味著使用舊的基數估計函數。


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


執行完成後，請按一下 [XML] 連結，並查看的第一個 iterator 屬性，如下所示。 請注意稱為 CardinalityEstimationModelVersion 上 [70 目前設定的屬性名稱。 它不表示，資料庫的相容性層級設定為 [SQL Server 7.0 版本 （設為在上述 TSQL 陳述式中可見的 110 上），但 [70] 的值只會表示 SQL Server 7.0，直到 SQL Server 2014 （其隨附 120 相容性層級） 有沒有主要修訂後，您可以使用的舊版基數估計功能。


*圖 4: CardinalityEstimationModelVersion 設定為 [70 時使用的相容性層級的 110 或下方。*


![圖 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


或者，您可以變更 130 相容性層級，並使用設定為 [開啟] 以[變更資料庫範圍設定](https://msdn.microsoft.com/library/mt629158.aspx)LEGACY_CARDINALITY_ESTIMATION 來停用新的基數估計函數使用。 這是完全相同使用最新的查詢處理相容性層級時使用從基數估計函數的觀點，110。 如此一來，您就可以受益新的查詢處理即將使用最新的相容性層級 （亦即批次模式） 的功能，但仍依賴舊的基數估計功能，如有必要。


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


只移動 120 或 130 相容性層級，可讓基數估計的新功能。 在這種情況下，預設 CardinalityEstimationModelVersion 會設定會相應地 120 或 130 為 [顯示] 下方。


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*圖 5: CardinalityEstimationModelVersion 會設定為 130 時使用 130 相容性層級。*


![圖 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Witnessing 基數估計的差異


現在，讓我們來執行稍有更複雜涉及使用一些述詞，以 WHERE 子句的內部聯結的查詢，然後我們來看看資料列計數估計值從舊的基數估計函數第一次。


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


有效率地執行此查詢會傳回 200,704 列，而列估計值，以舊的基數估計功能宣告 194,284 列。 當然，說過之前，這些資料列計數結果也取決於頻率執行前一個範例，其中填入不斷地在每次執行範例資料表。 當然，您在查詢中的述詞也會有影響實際的估計除了表格資料的圖形，內容]，及如何此資料實際相互關聯彼此。


*圖 6︰ 資料列計數估計值超出 194,284 或 6000 列從 200,704 預期的列。*


![圖 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


以相同的方式，讓我們來立即執行相同的查詢以新的基數估計功能。


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


查看以下，我們現在看到資料列評估 202,877，更深入瞭解或高於舊的基數估計。

*圖 7︰ 資料列計數估計值現在 202,877，而不是 194,284。*


![圖 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


實際上，結果集是 200,704 列 （但所有項取決頻率您已執行的查詢的前一個範例中，但更重要的是，因為 TSQL 使用 RAND 陳述式，傳回的實際值變更一次執行到下一步）。 因此，在這個範例，新的基數估計在評估的列數，因為 202,877 更近 200,704，比 194,284 更好 ！ 最後，如果您要變更 WHERE 子句述詞以等號 (而非 「 > 「 執行個體)，這可能會使之間舊的估計能更而有所不同，多少符合您的新基數函數。

當然，在此情況下，從實際次數，關閉正在 ~ 6000 列不代表大量資料，在某些情況下。 現在，此數百萬跨多個資料表和更複雜的查詢，以及在估計值的資料列可關閉數百萬個資料列，因此，挑選錯誤執行計劃，或要求 TempDB 用，並更多內容 I/O，讓前置零的記憶體不足，無法授與的風險的轉置會更高。

如果您有機會，在練習與您最常見的查詢和資料集，此比較，並查看您自己的剩餘部分的舊和新的估計會受到影響，同時一些可能只會關閉現實情況下，從更多或一些其他人只只要往實際的資料列計算實際傳回結果集。 取決於其所有的查詢與 Azure SQL 資料庫特性、 性質的資料集，並提供其相關統計資料大小的圖案。 如果您只是建立您的 Azure SQL 資料庫執行個體，最佳化必須建立其知識庫中從頭開始，而不是重複使用的上一個查詢會執行所做的統計資料。 因此，評估是非常關聯式和幾乎特定每個伺服器和應用程式的情況。 這是要記住的重要 ！


## <a name="some-considerations-to-take-into-account"></a>若要考慮的一些考量


雖然多數負載會因之前採用生產環境的相容性層級的相容性層級 130 基本上有 3 個選項︰

1. 您會移到 [相容性層級 130，並請參閱如何執行項目。 萬一您會注意到部分回復您只層級的相容性設定回其原始的層級，或按一下 [保留 130，只反向基數估計返回傳統模式 （如上所述，此單獨無法解決問題）。
2. 您徹底測試您現有的應用程式，類似生產負載微調，與驗證後，再進行正式的效能。 若有任何問題，同一個上方，您可以隨時回到原始的相容性層級，或只要回到傳統模式反向基數估計。
3. 為完稿的選項，[解決這些問題的最新的方法，是使用查詢存放區。 這是今天建議的選項 ！ 若要協助您在相容性的查詢的分析層級 120 或下方與 130，我們無法鼓勵您不夠使用查詢存放區。 查詢市集提供最新版 Azure SQL 資料庫 V12，而它的設計是可協助您處理查詢效能疑難排解。 將查詢市集視為航班資料的錄製器資料庫來收集及簡報的所有查詢歷程記錄的詳細的資訊。 這可以大幅簡化效能鑑識來減少診斷和解決問題的時間。 您可以找到詳細的資訊，在[查詢市集︰ 資料庫的航班資料錄製器](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)。


將 at 高層級，如果您已有一組在相容性層級 120 或下方，執行的資料庫，計劃一些它們移到 130，或您的工作量自動佈建新的資料庫，因為推出可設定預設值，130，請考慮下列︰

- 變更之前生產環境中的新相容性層級，讓查詢存放區。 如需詳細資訊，您可以參考[變更資料庫相容性模式](https://msdn.microsoft.com/library/bb895281.aspx)，並使用查詢存放區。
- 接下來，測試所有的要徑負載使用代表資料和類似生產環境和比較時發生效能，並為查詢儲存報告查詢。 如果您遇到一些回復時，您可以識別 regressed 的查詢儲存查詢，並使用強制查詢存放區中的選項的計劃 （又稱方案釘選）。 在這種情況下，您肯定保留相容性層級 130，並將用離職查詢計劃建議來查詢存放區。
- 如果您想要運用的新功能和 Azure SQL 資料庫 （這執行 SQL Server 2016） 的功能，但會區分變更的相容性層級 130 的最後一個步驟，您可以考慮強制相容性層級返回適合您的工作量使用 ALTER DATABASE 陳述式的階層。 但是，首先，請注意，釘選選項的查詢市集計劃是您最佳的選項，因為功能層級的是舊版的 SQL Server 基本上保持不使用 130。
- 如果您有 multitenant 橫跨多個資料庫的應用程式時，可能需要更新您的資料庫，以確保所有資料庫; 的一致的相容性層級的佈建邏輯新能夠，舊的項目。 應用程式的工作量效能可能以不同的相容性層級執行某些資料庫交易機密，因此，相容性層級的一致性任何資料庫可能是給您的客戶提供相同的使用體驗，所有全盤必要項目。 請注意，它不法令，它實際上取決於應用程式會如何影響相容性層級。
- 最後，關於基數評估，和就像變更相容性層級，才能繼續實際建議來測試您生產的工作量來判斷您的應用程式是否受益的基數估計改良功能在新的情況下。


## <a name="conclusion"></a>結束時


使用 Azure SQL 資料庫受益所有的 SQL Server 2016 增強功能可以清楚地改善您的查詢執行。 如同-是 ！ 當然，任何新的功能，例如適當的評估必須進行，以決定要您資料庫工作負載的運作方式的最佳的確切條件。 經驗大部分的工作量都必須至少有無障礙底下執行相容性層級 130 時充分使用新的查詢處理函數和新的基數估計。 所實際上，然而，有一些例外狀況，並執行適當的到期日注意重要的評估，以判斷多少好處這些增強功能。 並再次查詢市集可幫中執行此工作的 ！

隨著 SQL Azure 發展，您可以在未來預期的相容性層級 140。 時間時，我們就會開始討論此未來的相容性層級 140 顯示什麼，我們簡要討論相容性層 130 攜帶今天一樣。

現在，我們別忘了，開始年 6 月 2016年，Azure SQL 資料庫會預設的相容性層級從變更成 120 130 新建立的資料庫。 請注意 ！


## <a name="references"></a>參照


- [資料庫引擎中的新功能](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [部落格︰ 查詢市集︰ 資料庫，Borko Novakovic，年 6 月 8 2016年航班資料錄製器](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [ALTER 資料庫相容性層級 (TRANSACT-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTER 資料庫範圍設定](https://msdn.microsoft.com/library/mt629158.aspx)

- [相容性層級 130 Azure SQL 資料庫 V12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [最佳化查詢計劃與 SQL Server 2014 基數估算程式](https://msdn.microsoft.com/library/dn673537.aspx)

- [Columnstore 索引指南](https://msdn.microsoft.com/library/gg492088.aspx)

- [部落格︰ 改良的查詢效能與相容性等級 130 Azure SQL 資料庫中的 Alain Lissoir 可能 6 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
