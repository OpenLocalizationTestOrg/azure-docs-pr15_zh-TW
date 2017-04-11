<properties
    pageTitle="管理使用 Azure 入口網站的 Azure SQL 資料庫 |Microsoft Azure"
    description="瞭解如何使用 Azure 入口網站管理中使用 [Azure 入口網站的雲端關聯式資料庫。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>使用 [Azure 入口網站管理 Azure SQL 資料庫


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

[Azure 入口網站](https://portal.azure.com/)可讓您建立、 監控及管理 Azure SQL 資料庫伺服器。 本文提供的快速描述與其他一般工作的詳細資料的連結。

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>檢視 Azure SQL 資料庫、 伺服器和集區

若要檢視可用的 SQL 資料庫服務，請按一下 [**更多服務**]，並在 [搜尋] 方塊中輸入**SQL** :

![SQL 資料庫](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>如何建立或檢視 Azure SQL 資料庫？

若要開啟 [ **SQL 資料庫**刀，請按一下 [ **SQL 資料庫**]，然後按一下您想要使用的資料庫，或按一下 [ **+ 新增**來建立 SQL 資料庫。 如需詳細資訊，請參閱[建立 SQL 資料庫中使用 Azure 入口網站的分鐘](sql-database-get-started.md)。


![SQL 資料庫](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>如何建立或檢視 Azure SQL 伺服器？

若要開啟 [ **SQL server**刀，按一下 [ **SQL server**，然後按一下您想要使用的伺服器，或按一下 [ **+ 新增**來建立 SQL server。 如需詳細資訊，請參閱[建立 SQL 資料庫中使用 Azure 入口網站的分鐘](sql-database-get-started.md)。

![SQL server](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>如何建立或檢視 SQL 彈性資料庫？

若要開啟**SQL 彈性集區**刀，按一下 [ **SQL 彈性的資料庫**，然後按一下您想要使用的資料庫，或按一下 [ **+ 新增**建立資料庫。 如需詳細資訊，請參閱[建立 Azure 入口網站的彈性的資料庫集區](sql-database-elastic-pool-create-portal.md)。

![SQL 彈性集區](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>如何更新或檢視 SQL 資料庫設定？

若要檢視或更新您的資料庫設定，請按一下 [SQL 資料庫刀在想要的設定︰


![SQL 資料庫設定](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>我要如何找到 SQL 資料庫的完整伺服器名稱？

若要檢視您的資料庫伺服器名稱，按一下 [ **SQL 資料庫**刀上的 [**概觀**，並記下的伺服器名稱︰


![SQL 資料庫設定](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>我要如何管理防火牆規則來控制對我的 SQL server 與資料庫的存取權？

若要檢視、 建立或更新防火牆規則，按一下 [**設定伺服器防火牆**上**SQL 資料庫**刀。 如需詳細資訊，請參閱[設定使用 Azure 入口網站的 Azure SQL 資料庫伺服器層級的防火牆規則](sql-database-configure-firewall-settings.md)。


![防火牆規則](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>我要如何變更我 SQL 資料庫服務層] 或 [效能層級？


若要更新的 SQL 資料庫的服務層] 或 [效能層級，按一下 [ **SQL 資料庫**刀中的 [**價格層 （縮放 DTUs）** 。 如需詳細資訊，請參閱[變更服務層和效能的層級 （價格層） SQL 資料庫](sql-database-scale-up.md)。


![價格層](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>如何設定稽核及威脅偵測 SQL 資料庫？

若要設定稽核及威脅偵測 SQL 資料庫，按一下 [ **SQL 資料庫**刀中的 [**稽核及威脅偵測**。 如需詳細資訊，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing-get-started.md)和[SQL 資料庫威脅偵測快速入門](sql-database-threat-detection-get-started.md)。


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>我要如何設定 masking SQL 資料庫的動態資料？

若要設定動態 masking SQL 資料庫的資料，按一下 [ **SQL 資料庫**刀中的 [**遮罩的動態資料**]。 如需詳細資訊，請參閱[開始使用 SQL 資料庫動態資料遮罩](sql-database-dynamic-data-masking-get-started.md)。


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>我要如何設定透明資料加密 (TDE) SQL 資料庫？

若要設定為 SQL 資料庫的透明資料加密，按一下 [ **SQL 資料庫**刀中的 [**透明資料加密**]。 如需詳細資訊，請參閱[啟用資料庫，使用入口網站上的 TDE](https://msdn.microsoft.com/library/dn948096#Anchor_1)。

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>如何檢視或變更 SQL 資料庫的最大的大小嗎？

若要檢視或變更 SQL 資料庫的大小，請按一下 [ **SQL 資料庫**刀中的 [**資料庫大小**。 藉由變更服務層] 或 [效能層級更新資料庫的最大的大小。 如需詳細資訊，請參閱[變更服務層和效能的層級 （價格層） SQL 資料庫](sql-database-scale-up.md)。

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>如何監視及改善 SQL 資料庫的效能？

若要監視並改善效能特性 SQL 資料庫，按一下**SQL 資料庫**刀**效能概觀**。 如需詳細資訊，請參閱[SQL 資料庫效能充分](sql-database-performance.md)。


## <a name="how-do-i-configure-geo-replication"></a>如何設定地理試驗？

若要設定地理複寫 SQL 資料庫，按一下 [ **SQL 資料庫**刀中的 [**地理複寫**。 如需詳細資訊，請參閱[設定地理複寫 Azure 入口網站的 Azure SQL 資料庫](sql-database-geo-replication-portal.md)。


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>我要如何容錯移轉到地理複製 SQL 資料庫？

若地理複寫次要的容錯移轉時，按一下**地理複寫**上**SQL 資料庫**刀，然後按一下**容錯移轉**。 如需詳細資訊，請參閱[啟動 Azure SQL 資料庫 Azure 入口網站的計畫之內容錯移轉](sql-database-geo-replication-failover-portal.md)。


## <a name="how-do-i-copy-a-sql-database"></a>如何複製 SQL 資料庫？

若要複製 SQL 資料庫，按一下 [ **SQL 資料庫**刀中的 [**複製**]。 如需詳細資訊，請參閱[複製使用 Azure 入口網站的 Azure SQL 資料庫](sql-database-copy-portal.md)。


![SQL 資料庫設定](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>我要如何封存 Azure SQL 資料庫 BACPAC 檔案？

若要建立 SQL 資料庫的 BACPAC，按一下 [ **SQL 資料庫**刀上的 [**匯出**]。 如需詳細資訊，請參閱[封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫](sql-database-export.md)。


![SQL 資料庫匯出](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>如何還原 SQL 資料庫的上一個點時間？

若要還原的 SQL 資料庫，按一下 [ **SQL 資料庫**刀上的 [**還原**]。 如需詳細資訊，請參閱[還原至先前的 Azure 入口網站的時間點 Azure SQL 資料庫](sql-database-point-in-time-restore-portal.md)。


![SQL 資料庫設定](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>如何從 BACPAC 檔案建立 Azure SQL 資料庫？

若要建立 SQL 資料庫 BACPAC 檔案中，按一下 [ **SQL server**刀中的 [**匯入資料庫**]。 如需詳細資訊，請參閱[匯入 BACPAC 檔案，以建立 Azure SQL 資料庫](sql-database-import.md)。


![SQL server](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>我要如何還原已刪除的 SQL 資料庫？

若要還原已刪除的 SQL 資料庫，按一下 [**刪除資料庫**的**SQL server**刀 (SQL server，包含已遭刪除的資料庫)。 如需詳細資訊，請參閱[還原已刪除的 Azure SQL 資料庫，使用 [Azure 入口網站](sql-database-restore-deleted-database-portal.md)。

## <a name="how-do-i-delete-a-sql-database"></a>我要如何刪除 SQL 資料庫？

若要刪除的 SQL 資料庫，按一下 [**刪除** **SQL 資料庫**刀上。 

![SQL 資料庫設定](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>其他資源

- [SQL 資料庫](sql-database-technical-overview.md)
- [監控和管理 Azure 入口網站的彈性的資料庫集區](sql-database-elastic-pool-manage-portal.md)
