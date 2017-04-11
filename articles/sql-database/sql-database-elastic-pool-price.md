<properties
    pageTitle="SQL 資料庫彈性的集區價格與效能"
    description="彈性的資料庫集區的特定價格資訊。"
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>彈性的資料庫資料庫計費和價格資訊

彈性的資料庫集區是向收費每下列特性︰

- 即使集區中沒有資料庫，是在其建立付費彈性的資料庫。
- 為每小時付費彈性的資料庫。 這是與效能層級的單一資料庫測量頻率相同。
- 如果 eDTUs 新變彈性的資料庫大小會經過調整，然後資料庫是無法付費根據新的 eDTUS 數量直到調整大小的作業完成。 這是依照變更獨立資料庫的效能層級的相同模式。


- 彈性的集區的價格根據 eDTUs 集區的數目。 彈性的集區的價格無關的數字與彈性的資料庫內的使用狀況。
- 價格計算 （數字的資料庫 eDTUs） x （單價 eDTU）。

彈性的資料庫的單價 eDTU 大於獨立資料庫中相同的服務層級的 DTU 單價。 如需詳細資訊，請參閱[SQL 資料庫價格](https://azure.microsoft.com/pricing/details/sql-database/)。 


若要瞭解 eDTUs 和服務的層級，請參閱[SQL 資料庫選項與效能](sql-database-service-tiers.md)。

## <a name="next-steps"></a>後續步驟

- [建立彈性的資料庫集區](sql-database-elastic-pool-create-portal.md)
- [監控、 管理及調整大小的彈性的資料庫集區](sql-database-elastic-pool-manage-portal.md)
- [SQL 資料庫選項和效能︰ 瞭解什麼是用於每個服務層級](sql-database-service-tiers.md)
- [識別資料庫適合彈性的資料庫資料庫的 PowerShell 指令碼](sql-database-elastic-pool-database-assessment-powershell.md)
