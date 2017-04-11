<properties 
    pageTitle="使用 PowerShell 的 Azure SQL 資料庫啟動計畫之內的容錯移轉 |Microsoft Azure" 
    description="使用 PowerShell 的 Azure SQL 資料庫啟動計畫之內的容錯移轉" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>使用 PowerShell 的 Azure SQL 資料庫啟動計畫之內的容錯移轉



> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T SQL](sql-database-geo-replication-failover-transact-sql.md)


本文將示範如何開始使用 PowerShell SQL 資料庫的計畫之內的容錯移轉。 若要設定地理試驗，請參閱[設定地理複寫 Azure SQL 資料庫](sql-database-geo-replication-powershell.md)。



## <a name="initiate-a-planned-failover"></a>啟動計劃的容錯移轉

使用**設定 AzureRmSqlDatabaseSecondary** cmdlet 和**容錯**參數來宣傳次要成為新的主要資料庫，降級成為第二個現有的主要資料庫。 此功能針對計劃故障，例如期間損毀復原切入，並需要主要資料庫可以使用。

執行下列工作流程︰

1. 暫時切換複寫到同步的模式。 這會導致次要以清除所有未完成的交易。

2. 切換地理複寫合作關係的兩個資料庫的角色。  

此順序保證角色切換，因此沒有資料會遺失前兩個資料庫會同步處理。 有短時間內的兩個資料庫無法使用 （於 0 到 25 秒） 時切換角色。 整個作業應該採取一分鐘之內完成標準的情況下。 如需詳細資訊，請參閱[設定 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx)。




切換至主索引的次要資料庫的程序完成時，會傳回這個指令程式。

下列命令會切換名為 「 mydb 」 下資源群組 」 rg2 」 為主要伺服器 「 srv2 」 上的資料庫的角色。 原始的主要的 「 db2 「 已連線至會次要之後切換兩個資料庫完全同步處理。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] 在某些情況可能是作業無法完成，且可能會出現短時間無反應。 在此情況下使用者可以撥打強制容錯移轉] 命令 （意外的容錯移轉），並接受資料遺失。


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>啟動意外的容錯移轉從主要資料庫至次要資料庫


您可以使用**設定 AzureRmSqlDatabaseSecondary** cmdlet 和**– 錯誤移轉**及**-AllowDataLoss**參數升級成為新的主要資料庫尚未計劃的方式，是次要資料庫強制變成次要時的主要資料庫已不再提供一次現有的主要的降級。

此功能的設計損毀修復時還原的資料庫可用性非常重要，且是可接受的部分資料遺失。 叫用強制容錯移轉時，所指定的第二個資料庫立即會變成主要的資料庫，並開始接受寫入交易。 原始的主要資料庫是無法使用此新的主要資料庫重新連線，強制容錯移轉作業後，當累加備份在原始的主要資料庫並在舊的主要資料庫中建立新的主要資料庫; 次要資料庫接下來，則只是新的主要的複本。

但因為將還原的時間點上不支援次要資料庫，如果您想要復原認可舊的主要資料庫有不已複製到新的主要資料庫的資料，您應該加入 CSS 已知的記錄檔備份還原資料庫。

> [AZURE.NOTE] 如果舊的主要會變成主要和次要是線上時發出命令立即沒有資料同步處理新的次要。 如果主要可能發生認可交易時發出部分資料遺失的命令。


如果主要資料庫有多個命令將會部分成功的次要連結。 執行命令的次要會變成主要。 舊的主要不過仍會維持主索引，也就是兩個主運算最後會在不一致的狀態，並已擱置的複寫連結連接。 使用者必須以手動方式修復使用其中一個主要資料庫的 [移除次要] API 此設定。


下列命令會切換主要無法使用時，名為 「 mydb 」 為主要資料庫的角色。 原始的主要的 「 mydb 「 已連線至會次要之後切換回線上。 此時，同步處理可能會導致資料遺失。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>後續步驟   

- 後移轉後，請確定在新的主要上設定您的伺服器及資料庫的驗證需求。 如需詳細資訊，請參閱[SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md)。
- 瞭解使用 Active 地理複寫，包括之前損毀後的復原並修復後的步驟執行損毀復原向下切入，請參閱[損毀復原切入](sql-database-disaster-recovery.md)
- Sasha Nosov 部落格文章瞭解作用中地理複寫，請參閱[聚焦地理複寫的新功能](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- 設計要使用作用中的地理複寫雲端應用程式的相關資訊，請參閱[設計雲端應用程式的使用地理複寫的業務連續性](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- 使用作用中的地理複寫彈性的資料庫集區的相關資訊，請參閱[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
- 商務 continurity 的概觀，請參閱[業務連續性概觀](sql-database-business-continuity.md)
