<properties
    pageTitle="Azure SQL 資料庫還原至先前的點，時間 (PowerShell) |Microsoft Azure"
    description="Azure SQL 資料庫還原至先前的點時間"
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

# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>使用 PowerShell 的時間點還原 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [時間點還原︰ Azure 入口網站](sql-database-point-in-time-restore-portal.md)

本文將說明如何從[SQL 資料庫自動備份](sql-database-automated-backups.md)時間還原資料庫至之前。 您可以使用 PowerShell 來執行這個動作。

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>將資料庫還原為獨立資料庫的時間點

1. 取得您想要使用 [開始-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) 指令程式還原的資料庫。

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 將資料庫的點還原的時間，使用 [還原-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) 指令程式。

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>彈性的資料庫集區將時間還原資料庫的點

1. 取得您想要使用 [開始-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) 指令程式還原的資料庫。

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. 將資料庫的點還原的時間，使用 [還原-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) 指令程式。

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
