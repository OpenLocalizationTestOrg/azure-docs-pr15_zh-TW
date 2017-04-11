<properties 
    pageTitle="啟動與 SQL Azure SQL 資料庫計畫之內的容錯移轉 |Microsoft Azure" 
    description="使用 SQL Azure SQL 資料庫啟動計畫之內的容錯移轉" 
    services="sql-database" 
    documentationCenter="" 
    authors="CarlRabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>啟動計畫之內的容錯移轉與 SQL Azure SQL 資料庫


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T SQL](sql-database-geo-replication-failover-transact-sql.md)


本文將示範如何啟動容錯移轉到第二個 SQL 資料庫使用 TRANSACT-SQL。 若要設定地理試驗，請參閱[設定地理複寫 Azure SQL 資料庫](sql-database-geo-replication-transact-sql.md)。



若要啟動容錯移轉，您需要下列項目︰

- DBManager 位於主要，登入有 db_ownership 您將會地理複寫的本機資料庫，在您要設定地理複寫合作夥伴伺服器上是 DBManager。
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>啟動升級成為新的主要次要資料庫計劃容錯移轉

若要提高成為新的主要資料庫計劃的方式，是次要資料庫降級成為第二個現有的主要，您可以使用**ALTER DATABASE**陳述式。 主要資料庫的地理複寫次要資料庫正在升階存在於 Azure SQL 資料庫邏輯伺服器上執行此陳述式。 需要主要資料庫可以使用和計劃的容錯移轉，例如設計期間的 DR 切入，這項功能。

執行下列工作流程︰

1. 暫時同步模式，導致次要以清除所有未完成的交易並封鎖所有新的交易; 切換複寫

2. 切換的兩個資料庫中的地理複寫合作關係的角色。  

此順序保證角色切換，因此沒有資料會遺失前兩個資料庫會同步處理。 有短時間內的兩個資料庫無法使用 （於 0 到 25 秒） 時切換角色。 如果主要資料庫有多個次要資料庫，[] 命令會自動重新設定連線到新的主要其他次要連結。  整個作業應該採取一分鐘之內完成標準的情況下。 如需詳細資訊，請參閱[變更資料庫 (SQL)](https://msdn.microsoft.com/library/mt574871.aspx)和[服務層](sql-database-service-tiers.md)。


使用下列步驟進行計劃的容錯移轉。

1. 在 [Management Studio 中連線到 Azure SQL 資料庫邏輯伺服器地理複製的第二個資料庫存在於。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 使用下列**ALTER DATABASE**陳述式，切換次要資料庫至主要的角色。

        ALTER DATABASE <MyDB> FAILOVER;

4. 按一下 [**執行**] 來執行查詢。

>[AZURE.NOTE] 在某些情況中很可能無法完成作業，而且可能會出現停留。 在此情況下，使用者可以執行強制容錯移轉命令並接受資料遺失。


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>啟動意外的容錯移轉從主要資料庫至次要資料庫

您可以使用**ALTER DATABASE**陳述式升級成為新的主要資料庫尚未計劃的方式，是次要資料庫強制變成次要時主要資料庫已不再提供一次現有的主要降級。 主要資料庫的地理複寫次要資料庫正在升階存在於 Azure SQL 資料庫邏輯伺服器上執行此陳述式。

此功能的設計損毀修復時還原的資料庫可用性非常重要，且是可接受的部分資料遺失。 叫用強制容錯移轉時，所指定的第二個資料庫立即會變成主要的資料庫，並開始接受寫入交易。 原始的主要資料庫是無法使用此新的主要資料庫重新連線，當累加備份在原始的主要資料庫並在舊的主要資料庫中建立新的主要資料庫; 次要資料庫接下來，則只是新的主要的同步複本。

不過，因為將還原的時間點上不支援次要資料庫中，如果使用者想要復原認可舊的主要資料庫有不已複製到新的主要資料庫前強制容錯移轉發生資料，使用者必須加入支援，以復原此遺失資料。

如果主要資料庫有多個次要資料庫，[] 命令會自動重新設定連線到新的主要其他次要連結。

使用下列步驟進行意外的容錯移轉。

1. 在 [Management Studio 中連線到 Azure SQL 資料庫邏輯伺服器地理複製的第二個資料庫存在於。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 使用下列**ALTER DATABASE**陳述式，切換次要資料庫至主要的角色。

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. 按一下 [**執行**] 來執行查詢。

>[AZURE.NOTE] 如果舊的主要會變成主要和次要是線上時發出命令立即沒有資料同步處理新的次要。 如果主要可能發生認可交易時發出部分資料遺失的命令。



## <a name="next-steps"></a>後續步驟   

- 後移轉後，請確定在新的主要上設定您的伺服器及資料庫的驗證需求。 如需詳細資訊，請參閱[SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md)。
- 若要瞭解復原之後使用 Active 地理複寫，包括之前損毀修復後的步驟，並執行損毀復原向下切入，請參閱[損毀修復](sql-database-disaster-recovery.md)
- Sasha Nosov 部落格文章瞭解作用中地理複寫，請參閱[聚焦地理複寫的新功能](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- 設計要使用作用中的地理複寫雲端應用程式的相關資訊，請參閱[設計雲端應用程式的使用地理複寫的業務連續性](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- 使用作用中的地理複寫彈性的資料庫集區的相關資訊，請參閱[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
- 商務 continurity 的概觀，請參閱[業務連續性概觀](sql-database-business-continuity.md)
