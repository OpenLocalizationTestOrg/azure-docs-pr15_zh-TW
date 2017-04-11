<properties
    pageTitle="SQL 資料庫︰ 什麼是 DTU？ |Microsoft Azure"
    description="了解哪些 Azure SQL 資料庫交易單位是。"
    keywords="資料庫選項，資料庫效能"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>說明資料庫交易單位 (DTUs) 和彈性的資料庫交易單位 (eDTUs)

本文說明資料庫交易單位 (DTUs) 彈性的資料庫交易單位 (eDTUs) 並按下 [最大值 DTUs 或 eDTUs 時，會發生什麼情況。  

## <a name="what-are-database-transaction-units-dtus"></a>什麼是資料庫交易單位 (DTUs)

DTU 是度量的資源可供使用特定的效能層級內[獨立資料庫服務層](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels)的獨立 Azure SQL 資料庫保證單位。 DTU 是混合的 CPU、 記憶體和 I/O 和交易記錄檔 I/O 比率，取決於專為實際 OLTP 工作負載的典型 OLTP 事先工作負載的資料。 資料庫的效能層級增加加倍 DTUs 等於加倍資源才能使用該資料庫的設定。 例如 1750 DTUs 的進階版 P11 資料庫會提供更多 DTU x 350 計算電源超過 5 DTUs 的基本資料庫。 若要瞭解前用來決定 DTU 混合 OLTP 事先工作負載的方法，請參閱[SQL 資料庫事先概觀](sql-database-benchmark-overview.md)。

![SQL 資料庫簡介︰ 單一層級，資料庫 DTUs](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

您可以[變更服務層](sql-database-scale-up.md)隨時最小中斷的應用程式 （通常平均在四秒）。 適用於眾多企業及應用程式，也可以建立資料庫，並視需要撥號單一資料庫效能向上或向下即足以，特別是如果使用模式是相當如預期呈現。 但如果您有無法預期的使用模式時，它可以很難管理成本和商務模型。 此案例中，您可以使用的彈性的集區 eDTUs 以特定數字。

## <a name="what-are-elastic-database-transaction-units-edtus"></a>彈性的資料庫交易單位 (eDTUs) 是什麼

EDTU 是度量的集合的資源 (DTUs)，可以共用一組 Azure SQL 伺服器-稱為[彈性的集區](sql-database-elastic-pool.png)上的資料庫之間單位。 彈性的集區提供管理有差距的多個資料庫及模式發生無法預期的效能目標簡單成本有效的解決方案。 [彈性的資料庫及服務層級](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)的詳細資訊，請參閱。

![SQL 資料庫簡介︰ eDTUs 層和層級](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

集區提供的 eDTUs，設定價格。 集區中個別的資料庫會自動調整設定參數中指定彈性。 經常負載資料庫可以使用多個 eDTUs 以符合需求。 在精簡負載資料庫取用小於，與在沒有負載資料庫使用沒有 eDTUs。 佈建資源的整個資料庫，而不是單一資料庫的簡化管理工作。 此外，您還可預測預算集區。

其他 eDTUs 可以新增至現有的集區沒有資料庫停機時間或不會影響彈性的資料庫中的資料庫]。 同樣地，如果不再需要額外 eDTUs 他們可以從移除任何現有的集區時間。 您可以加上或減去的集區的資料庫。 如果資料庫預測下-利用的資源，將其移。

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>如何判斷我的工作負載所需的 DTUs 數？

如果您想要移轉現有的內部部署或到 Azure SQL 資料庫的 SQL Server 虛擬機器工作量，您可以使用[DTU 計算機](http://dtucalculator.azurewebsites.net/)估算 DTUs 所需的數目。 現有的 Azure SQL 資料庫工作負載，您可以使用[SQL 資料庫查詢效能充分](sql-database-query-performance.md)了解您資料庫資源使用量 (DTUs)，以取得更深入瞭解如何最佳化您的工作量。 您也可以使用[sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV 取得最後一小時資源消耗資訊。 或者，類別目錄檢視[sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)可以也查詢相同的資料取得最近 14 天，但在較低的精確度的五分鐘平均值。

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>如何知道我無法受益資源彈性資料庫？

集區很適合大量以特定的使用情況圖樣的資料庫。 為指定的資料庫，此模式被特性低平均使用率較不常用的使用情況波峰。 SQL 資料庫會自動評估中現有的 SQL 資料庫伺服器的資料庫的歷程記錄的資源使用狀況，並建議 Azure 入口網站中適當的集區設定。 如需詳細資訊，請參閱[何時彈性的資料庫集區使用？](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>當我叫用我最大 DTUs 時，會發生什麼情況

校正效能層級，而且提供所需的資源執行您資料庫的工作量進位到最大可容許的選取的服務層效能層級的限制權限所規範。 如果您的工作量正中 CPU/資料 IO/記錄 IO 限制的其中一種限制，您繼續收到的資源，最大的可容許層級，但可能若要查看查詢增加的延遲。 這些限制不會造成任何錯誤，但而降低工作量，除非降低變成嚴重的查詢開始時間。 如果您正中限制的最大可容許的使用者同時工作階段/要求 （執行緒），您會看到明確的錯誤。 請參閱[Azure SQL 資料庫資源限制](sql-database-resource-limits.md)的 CPU、 記憶體資料 I/O 以外的資源限制的詳細資訊和交易記錄檔 I/O。

## <a name="next-steps"></a>後續步驟

- DTUs 和 eDTUs 使用獨立的資料庫，彈性的集區的相關資訊，請參閱[服務層級](sql-database-service-tiers.md)。
- 請參閱[Azure SQL 資料庫資源限制](sql-database-resource-limits.md)的 CPU、 記憶體資料 I/O 以外的資源限制的詳細資訊和交易記錄檔 I/O。
- 請參閱[SQL 資料庫查詢效能充分](sql-database-query-performance.md)了解您 (DTUs) 消耗。
- 請參閱[SQL 資料庫事先概觀](sql-database-benchmark-overview.md)，瞭解前用來決定 DTU 混合 OLTP 事先工作負載的方法。