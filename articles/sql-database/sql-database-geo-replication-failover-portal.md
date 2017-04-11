<properties 
    pageTitle="啟動 Azure SQL 資料庫的計畫之內的容錯移轉，Azure 入口網站與 |Microsoft Azure" 
    description="使用 [Azure 入口網站的 Azure SQL 資料庫啟動計畫之內的容錯移轉" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>啟動 Azure SQL 資料庫的計畫之內的容錯移轉，與 Azure 入口網站


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T SQL](sql-database-geo-replication-failover-transact-sql.md)


本文將示範如何啟動容錯移轉到第二個 SQL 資料庫與[Azure 入口網站](http://portal.azure.com)。 若要設定地理試驗，請參閱[設定地理複寫 Azure SQL 資料庫](sql-database-geo-replication-portal.md)。


## <a name="initiate-a-failover"></a>啟動容錯移轉

次要資料庫可以切換成為主要。  

1. 在 [主要資料庫中的地理複寫合作關係[Azure 入口網站](http://portal.azure.com)瀏覽]。
2. 在 [SQL 資料庫刀中，選取 [**所有設定** > **地理複寫**。
3. 在**次要連結**清單中，選取您想要成為新的主要，然後按一下 [**容錯移轉**的資料庫。

    ![容錯移轉][2]

4. 按一下 [ **]**以開始容錯移轉。

命令立即會切換次要資料庫至主要的角色。 

有短時間內的兩個資料庫無法使用 （於 0 到 25 秒） 時切換角色。 如果主要資料庫有多個次要資料庫，[] 命令會自動重新設定連線到新的主要其他次要連結。 整個作業應該採取一分鐘之內完成標準的情況下。 

>[AZURE.NOTE] 如果主要處於線上狀態時發出命令部分資料遺失，請先測試交易可能會發生。


## <a name="next-steps"></a>後續步驟   

- 後移轉後，請確定在新的主要上設定您的伺服器及資料庫的驗證需求。 如需詳細資訊，請參閱[SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md)。
- 瞭解使用 Active 地理複寫，包括之前損毀後的復原並修復後的步驟執行損毀復原向下切入，請參閱[損毀復原切入](sql-database-disaster-recovery.md)
- Sasha Nosov 部落格文章瞭解作用中地理複寫，請參閱[聚焦地理複寫的新功能](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- 設計要使用作用中的地理複寫雲端應用程式的相關資訊，請參閱[設計雲端應用程式的使用地理複寫的業務連續性](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- 使用作用中的地理複寫彈性的資料庫集區的相關資訊，請參閱[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
- 商務 continurity 的概觀，請參閱[業務連續性概觀](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
