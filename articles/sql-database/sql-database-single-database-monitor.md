<properties
    pageTitle="監視 Azure SQL 資料庫中的資料庫效能 |Microsoft Azure"
    description="深入了解監控 Azure 工具與動態管理檢視資料庫的選項。"
    keywords="監控雲端資料庫效能的資料庫"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>監視 Azure SQL 資料庫中的資料庫效能
監控效能中 Azure SQL 資料庫的第一句是監控資源使用狀況相對於您選擇的資料庫效能的層級。 監控可協助您判斷是否有多餘的容量資料庫，或有問題，因為資源超量，並決定是否以調整效能層級和資料庫的[服務層級](sql-database-service-tiers.md)的時間。 您可以監視使用[Azure 入口網站](https://portal.azure.com)中的圖形化的工具，或使用 SQL[動態管理檢視](https://msdn.microsoft.com/library/ms188754.aspx)您的資料庫。

## <a name="monitor-databases-using-the-azure-portal"></a>監視使用 Azure 入口網站的資料庫

在[Azure 入口網站](https://portal.azure.com/)，您可以監視單一資料庫的使用情況，請選取您的資料庫並按一下**監控**圖表。 如此會開啟 [**公制**] 視窗，您可以按一下 [**編輯圖表**] 按鈕。 新增下列指標︰

- CPU 百分比
- DTU 百分比
- 資料 IO 百分比
- 資料庫大小的百分比

一旦您已新增下列指標，您可以繼續在**監控**圖表與更多詳細資料，在 [**公制**] 視窗中檢視。 所有的四個指標顯示平均使用率百分比相對於**DTU**的資料庫。 請參閱[服務層級](sql-database-service-tiers.md)的詳細資料 DTUs。

![服務資料庫效能監視層。](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

您也可以在效能指標設定通知。 按一下 [**公制**] 視窗中的 [**新增提醒**] 按鈕。 遵循精靈來設定您的提醒。 如果計量超過特定閾值或低於特定閾值的度量，您會有 [通知] 選項。

例如，如果您希望工作負載提升您資料庫時，您可以選擇設定電子郵件提醒，每當您資料庫達到效能指標的任何 80%。 您可以為最早的警告使用此選項時，您可能需要切換到下一個較高的效能層級，找出。

效能指標也可協助您判斷您是否能夠降級至較低的效能層級。 假設您使用的標準的 S2 資料庫，而且所有效能指標都顯示平均資料庫不會在任何時候使用超過 10%。 有可能在標準 S1 運作到資料庫。 不過，請注意工作負載的特殊或波動以移至較低的效能層級決定。

## <a name="monitor-databases-using-dmvs"></a>使用 Dmv 監視器資料庫

公開入口網站中的相同計量也都可以透過系統檢視︰ [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)您的伺服器，以及[sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)使用者資料庫中的邏輯**主**資料庫中。 如果您需要監視較粗略資料到較長的時間，請使用**sys.resource_stats** 。 如果您需要監控微調較小的時間範圍內的資料，請使用**sys.dm_db_resource_stats** 。 如需詳細資訊，請參閱[Azure SQL 資料庫效能指引](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats)。

>[AZURE.NOTE] **sys.dm_db_resource_stats**會傳回空白設定用於網頁和商務版資料庫，這已停用時的結果。

彈性的資料庫集區，您可以監視集區中的個別資料庫與本節所述的技巧。 但您也可以監視整個資料庫。 資訊，請參閱[監控和管理的彈性的資料庫集區](sql-database-elastic-pool-manage-portal.md)。
