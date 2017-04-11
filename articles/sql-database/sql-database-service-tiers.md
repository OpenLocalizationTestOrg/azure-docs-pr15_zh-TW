<properties
    pageTitle="SQL 資料庫效能與選項︰ 服務層 |Microsoft Azure"
    description="提供的服務層来平衡成本及功能，當您不按比例縮放的 SQL 資料庫效能與商務連續性功能比較。"
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
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL 資料庫選項和效能︰ 瞭解什麼是用於每個服務層級

[Azure SQL 資料庫](sql-database-technical-overview.md)提供三種服務層處理不同的工作負載的多個效能層級。 每個效能層級提供增加的設計進行越來越處理量的資源。 您可以管理自己的[服務層級](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels)與它自己的效能層級的每個資料庫。 您也可以管理多個以共用資源的[彈性的資料庫](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)中的資料庫。 獨立資料庫資源資料庫交易單位 (DTUs) 和彈性的集區彈性 DTUs 或 eDTUs 表示。 如需 DTUs 和 eDTUs 的詳細資訊，請參閱[什麼是 DTU](sql-database-what-is-a-dtu.md)。 

在這兩種情況下，服務層級會包含**基本**、**標準**、 和**進階版**。 這些層資料庫選項為類似於獨立資料庫及彈性的資料庫，但有彈性的集區的其他考量。 本文提供服務層級的詳細資料的獨立資料庫及彈性的資料庫。

## <a name="service-tiers-and-database-options"></a>服務層和資料庫選項
基本、 標準、 進階版服務層所有有 SLA 99.99%的執行時間並提供可預期的效能、 彈性的業務連續性選項、 安全性功能，以及每小時帳單。 下表提供適用於不同的應用程式工作負載的最佳層範例。

| 服務層級 | 目標負載 |
|---|---|
| **基本** | 最適合的小型的資料庫，支援通常是單一作用中作業在指定的時間。 範例包括資料庫用於開發或測試，或小型不常使用的應用程式。 |
| **標準** | 移至選項大部分的雲端應用程式，支援多個同時查詢。 範例包括工作群組或 web 應用程式。 |
| **進階版** | 設計高交易大量支援許多使用者同時，並要求最高層級的業務連續性功能。 範例是支援重要的應用程式的資料庫。 |

>[AZURE.NOTE] 網頁版和企業版的版本是已停用。 如果您想要繼續使用網頁版和企業版，請閱讀[日落常見問題集](https://azure.microsoft.com/pricing/details/sql-database/web-business/)。

## <a name="standalone-database-service-tiers-and-performance-levels"></a>獨立資料庫服務層和效能層級
獨立的資料庫，有多個效能層級中每個服務層級。 您可以選擇最符合您的工作量需求的層級彈性。 如果您需要調整向上或向下，您可以輕鬆地變更層的資料庫。 如需詳細資訊，請參閱[變更資料庫服務層和效能層級](sql-database-scale-up.md)。

列於此處的效能特性套用到使用[SQL 資料庫 V12](sql-database-v12-whats-new.md)所建立的資料庫。 資料庫裝載於數，無論您的資料庫取得保證的一組資源，並不會影響您的資料庫的預期的效能特性。

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] 此服務層表格中的所有資料列的詳細說明，請參閱[服務層功能和限制](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)。

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>彈性的資料庫服務層和 eDTUs 效能
除了建立和縮放比例獨立的資料庫，您也可以管理[彈性的資料庫](sql-database-elastic-pool.md)中的多個資料庫的選項。 彈性的資料庫中的所有資料庫都共用一組通用的資源。 *彈性的資料庫交易單位*(eDTUs) 測量的效能特性。 為獨立的資料庫時，資料庫有三種服務層︰**基本**、**標準**、 和**進階版**。 集區，這些三種服務層仍會定義的整體效能限制和有幾種功能。

集區允許共用與使用 DTU 資源，並不需要指派特定的效能等級集區中每個資料庫的資料庫。 例如，標準的資料庫中的獨立資料庫可以移使用以設定當您設定集區最大資料庫 eDTU 0 eDTUs。 集區允許有效率地使用整個資料庫 eDTU 資源可用的各種工作負載的多個資料庫。 如需詳細資訊，請參閱[彈性的集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

下表描述資料庫服務層的特性。

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

集區中的每個資料庫也會遵循該層的獨立資料庫特性。 例如基本集區每個集區的 4800 28800，具有最大的工作階段的限制，但基本的資料庫中的個別資料庫有 300 工作階段的資料庫限制。

## <a name="choosing-a-service-tier"></a>選擇服務層級

若要決定服務層上，先決定資料庫應該是獨立的資料庫或一部分彈性的資料庫。 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>選擇獨立資料庫服務層級

若要決定獨立資料庫服務層上，先決定您需要選擇 SQL 資料庫版本的資料庫功能︰

- 資料庫大小 （2 GB 最大值的標準，Basic 250 GB 最大值和 500 GB，以 1 TB 的最大值的進階版-根據效能層級）
- 資料庫備份的保留期間 （7 天的基本、 標準，35 天的時間，35 天進階版）

一旦您決定 SQL 資料庫 edition，就可以開始判斷效能層級的資料庫 （DTUs 的數量）。 您可以猜測然後[不按比例縮放上或往下動態](sql-database-scale-up.md)根據實際的體驗。 您也可以使用[DTU 計算機](http://dtucalculator.azurewebsites.net/)估算 DTUs 所需的數目。 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>選擇彈性的資料庫集區服務層級。

若要決定服務層彈性的資料庫集區上，先決定您需要選擇 [程式集區的服務層資料庫功能。

- 資料庫大小 （2 GB 基本、 標準，250 GB 和 500 GB 進階版）
- 資料庫備份的保留期間 （7 天的基本、 標準，35 天的時間，35 天進階版）
- 數字的每個資料庫 （基本 400、 400 標準，以及 50 進階版） 的資料庫
- 最大值的儲存空間，每個集區 (117 GB 的基本、 標準，1200年到 750 進階版)

一旦您決定服務層級的 [程式集區，您準備判斷資料庫 (eDTUs) 的效能層級。 您可以猜測，然後[放大或縮小動態](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool)基礎實際的體驗。 您可以使用[DTU 計算機](http://dtucalculator.azurewebsites.net/)估算 DTUs 幫助您判斷資料庫的上限集區中每個資料庫所需的數目。

## <a name="next-steps"></a>後續步驟
- 瞭解更多有關[SQL 資料庫](https://azure.microsoft.com/pricing/details/sql-database/)價格層價格。
- 瞭解[彈性的資料庫](sql-database-elastic-pool-guidance.md)及[彈性的集區的價格和效能考量](sql-database-elastic-pool-guidance.md)的詳細資料。
- 瞭解如何[監視器管理及調整大小彈性的集區](sql-database-elastic-pool-manage-portal.md)及[監視器獨立資料庫的效能](sql-database-single-database-monitor.md)。
- 現在，您知道關於 SQL 資料庫層級，請試試看使用[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)，並瞭解[如何建立您的第一個 SQL 資料庫](sql-database-get-started.md)。

## <a name="additional-resources"></a>其他資源

- [多租用戶 SaaS 應用程式使用 Azure SQL 資料庫的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Microsoft 虛擬學院視訊課程 Azure SQL 資料庫中的彈性的資料庫功能](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
