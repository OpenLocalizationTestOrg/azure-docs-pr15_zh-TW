<properties
    pageTitle="還原已刪除的 Azure SQL 資料庫 (PowerShell) |Microsoft Azure"
    description="還原已刪除的 Azure SQL 資料庫 (PowerShell)。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>使用 PowerShell 還原已刪除的 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [還原刪除的 DB︰ 入口網站](sql-database-restore-deleted-database-portal.md)
- [**還原刪除的 DB: PowerShell**](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]


## <a name="get-a-list-of-deleted-databases"></a>取得已刪除的資料庫的清單

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-standalone-database"></a>還原已刪除的資料庫至獨立資料庫

取得您想要使用[取得 AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387(v=azure.300/).aspx) cmdlet 來還原已刪除的資料庫備份。 然後開始從已刪除的資料庫備份還原使用[還原 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390(v=azure.300/).aspx)指令程式。

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-database-pool"></a>還原已刪除的資料庫至彈性的資料庫集區

取得您想要使用[取得 AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387(v=azure.300/).aspx) cmdlet 來還原已刪除的資料庫備份。 然後開始從已刪除的資料庫備份還原使用[還原 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390(v=azure.300/).aspx)指令程式。

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
