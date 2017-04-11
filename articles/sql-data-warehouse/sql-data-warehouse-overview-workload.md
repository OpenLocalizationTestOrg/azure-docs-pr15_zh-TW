<properties
   pageTitle="資料倉庫工作量"
   description="SQL Data Warehouse 的 elasticity 可讓您放大、 縮小，或使用的資料倉庫單位 (DWUs) 滑動色階來暫停運算能力。 本文說明資料倉庫指標，並以 DWUs 如何建立關聯。 "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/25/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# <a name="data-warehouse-workload"></a>資料倉庫工作量
資料倉庫工作量指的是所有經過針對資料倉庫的作業。 倉庫工作負載的資料包含資料載入倉庫、 執行分析與報告資料倉庫、 管理資料倉庫中的資料和資料匯出資料倉庫整個程的序。 深度和幅度這些元件通常是 parallel 到期層級的資料倉庫。


## <a name="new-to-data-warehousing"></a>新資料倉儲嗎？
資料倉庫是從一或多個資料來源會載入和用來執行商務智慧工作，例如報告及資料分析的資料集合。

資料倉儲被特性掃描較大的數字的資料列，大範圍的資料，並在進行分析與報告，可能會傳回較大的結果的查詢。 資料倉儲也是較大的資料載入小型交易層級插入、 更新和刪除與特性。

- 資料倉庫最佳執行時的資料儲存在最佳化需要將大量的資料列或大範圍的資料掃描的查詢的方式。 這類掃描最適合資料是在儲存時搜尋欄，而不是依列。

>[AZURE.NOTE] 記憶體內 columnstore 索引中，使用欄的儲存空間，提供最多 10 x 壓縮獲得和 100 x 查詢效能提升在傳統的二進位樹報告及分析資料的查詢。 我們來儲存及瀏覽大量資料中的資料倉庫標準考慮 columnstore 索引。

- 資料倉庫需求有不同於最佳化的線上交易處理 (OLTP) 系統。 OLTP 系統有許多插入、 更新及刪除作業。 這些作業，設法資料表中的特定列。 表格搜尋時所列的方式儲存資料的最佳方式執行。 資料排序快速搜尋與除以並征服稱為二進位樹狀目錄或 btree 搜尋的方法。


## <a name="data-loading"></a>載入資料
載入資料是大資料倉庫工作量。 企業通常只有追蹤客戶時產生商業交易日常變更為忙碌 OLTP 系統。 定期，通常晚上進行的維修作業期間，交易移動或複製到資料倉庫。 在資料倉庫資料後，分析師就可以執行分析，並在資料進行商業決策。

- 一般而言，載入的程序稱為 ETL 解壓縮、 轉換，以及負載。 通常必須，讓它與其他資料倉庫中的資料轉換資料。 先前，企業用於專用的 ETL 伺服器執行轉換。 現在，例如快速人連線平行處理中，您可以先將資料載入 SQL Data Warehouse，然後執行轉換。 此程序稱為解壓縮、 載入和轉換 (ELT)，然後現在改稱為資料倉庫工作負載的新標準。

> [AZURE.NOTE] 使用 SQL Server 2016，您現在可以執行分析中的即時 OLTP 資料表。 這不會取代需要使用資料倉庫來儲存及分析資料，但它會提供即時執行分析。

### <a name="reporting-and-analysis-queries"></a>報告及分析的查詢
報告及分析查詢通常會分類成小型、 中型及大型根據準則、 數字，但通常根據時間。 在大部分的資料倉儲有混合工作負載的快速執行與長期執行查詢。 每一種情況，請務必判斷此混合並決定其頻率 （每小時每日、 月端，當季端等等）。 請務必瞭解混合的查詢負載量，搭配並行，會導致規劃資料倉庫適當的容量。

- 容量計劃可以是混合式的查詢工作負載的複雜的工作，尤其是您需要將容量新增至資料倉庫長的前置重疊時間。 SQL Data Warehouse 移除只要容量計劃，因為您可以放大和縮小計算容量，隨時和自儲存空間，計算容量獨立調整大小。

### <a name="data-management"></a>資料管理
管理資料時，很重要，特別是您知道您可能會在不久的將來執行磁碟空間。 資料倉儲通常會將分成有意義的範圍，儲存為資料表中的磁碟分割區的資料。 SQL Server 為基礎的所有產品可都讓您移動磁碟分割區與表格登出。 磁碟分割切換可讓您將較舊的資料移到較便宜的儲存空間，並最新的資料上保留線上儲存空間。

- Columnstore 索引支援分割的資料表。 Columnstore 索引分割的資料表可用的資料管理，並保存。 儲存資料表資料列列的磁碟分割區，請在 [查詢效能中有較大的角色。  

- PolyBase 播放在管理資料相當重要的角色。 使用 PolyBase，您可以封存較舊的資料至 Hadoop 或 Azure blob 儲存體。  由於資料仍線上提供許多選項。  可能需要較長的時間從 Hadoop，擷取資料，但擷取時間的取捨所帶來的儲存空間的成本。

### <a name="exporting-data"></a>匯出資料
以供報告及分析資料的其中一個方法是將資料從傳送資料倉庫伺服器專用執行報告及分析。 這些伺服器稱為資料超市。 例如，您可以預先處理報表資料，然後匯出其資料倉庫全球，使其客戶和分析師廣泛提供許多的伺服器。

- 產生報表，每個晚上您可能會填入唯讀狀態報表伺服器使用每日資料的快照。 這同時降低計算資源需求，對資料倉庫會較高的頻寬提供的客戶。 從安全性長寬資料超市可讓您以減少資料倉庫擁有權限的使用者數目。
- 分析，您可以建立對資料倉庫 analysis cube 和對資料倉庫中，執行分析或預先處理資料，並將其匯出到進一步分析的分析伺服器。

## <a name="next-steps"></a>後續步驟
現在，您知道有點 SQL Data Warehouse，瞭解如何快速[建立 SQL Data Warehouse][]並[載入範例資料][]。

<!--Image references-->

<!--Article references-->
[載入範例資料]: ./sql-data-warehouse-load-sample-databases.md
[建立 SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->
