<properties
    pageTitle="地理重複備份 (PowerShell) 還原 Azure SQL 資料庫 |Microsoft Azure"
    description="將新的伺服器從地理多餘的備份檔案還原 Azure SQL 資料庫"
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
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>使用 PowerShell 地理重複備份還原 Azure SQL 資料庫


> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [地理還原︰ Azure 入口網站](sql-database-geo-restore-portal.md)

本文將示範如何使用地理還原，將新的伺服器還原您的資料庫。 這可以透過 PowerShell。

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>地理還原至獨立資料庫資料庫

1. 取得地理多餘資料庫的備份，您想要使用 [開始-AzureRmSqlDatabaseGeoBackup] （https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) 指令程式。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 開始從地理多餘的備份還原，使用 [還原-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) 指令程式。

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>地理還原成彈性的資料庫集區資料庫

1. 取得地理多餘資料庫的備份，您想要使用 [開始-AzureRmSqlDatabaseGeoBackup] （https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) 指令程式。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 開始從地理多餘的備份還原，使用 [還原-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) 指令程式。 指定您要還原至資料庫的資料庫名稱。

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)。
- 若要瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)。
- 若要瞭解如何使用復原自動的備份，請參閱[還原服務啟動備份的資料庫](sql-database-recovery-using-backups.md)。
- 若要瞭解更快速地復原選項，請參閱[使用中地理複寫](sql-database-geo-replication-overview.md)。  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複製](sql-database-copy.md)。
