<properties
   pageTitle="還原 Azure SQL Data Warehouse (PowerShell) |Microsoft Azure"
   description="還原 Azure SQL Data Warehouse PowerShell 任務。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>還原 Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [其餘][]

在本文中，您將學習如何還原使用 PowerShell 的 Azure SQL Data Warehouse。

## <a name="before-you-begin"></a>開始之前

**請確認您 DTU 容量。** 每個 SQL Data Warehouse 主控具有預設 DTU 配額的 SQL server (例如 myserver.database.windows.net)。  您可以還原 SQL Data Warehouse 之前，請確認您的 SQL server 有足夠剩餘的 DTU 配額要還原的資料庫。 若要瞭解如何計算所需的 DTU 或邀請更多 DTU，請參閱[要求 DTU 配額變更][]。

### <a name="install-powershell"></a>安裝 PowerShell

使用 PowerShell 的 Azure 與 SQL Data Warehouse，您需要安裝 PowerShell 的 Azure 1.0 或更大的版本。  您可以執行，以檢查您的版本**取得模組 ListAvailable-名稱 AzureRM**。  從[Microsoft 網站的平台安裝程式][]，可以安裝最新版本。  如需有關如何安裝最新版本的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure][]。

## <a name="restore-an-active-or-paused-database"></a>還原使用中] 或 [暫停的資料庫

若要還原快照的資料庫，請使用[還原 AzureRmSqlDatabase][] PowerShell 指令程式。

1. 開啟 Windows PowerShell。
2. 連線至 Azure 帳戶，並讓您的帳戶相關聯的所有訂閱。
3. 選取包含要還原的資料庫的訂閱。
4. 列出資料庫還原點。
5. 挑選您要的還原點使用 RestorePointCreationDate。
6. 還原資料庫至您要的還原點。
7. 確認已還原的資料庫在線上。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] 還原完畢後，您可以設定下列[設定您的資料庫復原之後][]，復原的資料庫。


## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫

若要還原已刪除的資料庫，請使用[還原 AzureRmSqlDatabase][]指令程式。

1. 開啟 Windows PowerShell。
2. 連線至 Azure 帳戶，並讓您的帳戶相關聯的所有訂閱。
3. 選取包含要還原已刪除的資料庫的訂閱。
4. 取得特定已刪除的資料庫。
5. 還原已刪除的資料庫。
6. 確認已還原的資料庫在線上。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] 還原完畢後，您可以設定下列[設定您的資料庫復原之後][]，復原的資料庫。


## <a name="restore-from-an-azure-geographical-region"></a>從 Azure 的地理區域還原

若要復原資料庫，請使用[還原 AzureRmSqlDatabase][]指令程式。

1. 開啟 Windows PowerShell。
2. 連線至 Azure 帳戶，並讓您的帳戶相關聯的所有訂閱。
3. 選取包含要還原的資料庫的訂閱。
4. 取得您想要復原的資料庫。
5. 建立資料庫的復原要求。
6. 確認地理還原資料庫的狀態。

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] 若要還原完畢後，請設定您的資料庫，請參閱[設定您復原後的資料庫][]。 


復原的資料庫會 TDE 啟用如果來源資料庫 TDE 啟用。


## <a name="next-steps"></a>後續步驟
若要瞭解 Azure SQL 資料庫版本的業務連續性功能，請閱讀[Azure SQL 資料庫 business 連續性概觀][]。

<!--Image references-->

<!--Article references-->
[Azure SQL 資料庫 business 連續性概觀]: sql-database-business-continuity.md
[要求 DTU 配額變更]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[復原後，設定您的資料庫]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[如何安裝和設定 PowerShell 的 Azure]: powershell-install-configure.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[其餘]: ./sql-data-warehouse-restore-database-rest-api.md
[復原後，設定您的資料庫]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[還原 AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web 平台安裝程式]: https://aka.ms/webpi-azps
