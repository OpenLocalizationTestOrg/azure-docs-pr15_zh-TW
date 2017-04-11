<properties 
    pageTitle="複製使用 PowerShell 的 Azure SQL 資料庫 |Microsoft Azure" 
    description="建立使用 PowerShell 的 Azure SQL 資料庫的複本" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>複製使用 PowerShell 的 Azure SQL 資料庫


> [AZURE.SELECTOR]
- [概觀](sql-database-copy.md)
- [Azure 入口網站](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T SQL](sql-database-copy-transact-sql.md)

本文說明如何使用 PowerShell SQL 資料庫複製到相同的伺服器，到不同的伺服器，或將資料庫複製到[彈性的資料庫集區](sql-database-elastic-pool.md)。 資料庫複製操作使用[新增 AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx)指令程式。 


若要完成此文件，您需要下列項目︰

- Azure SQL 資料庫 （若要複製的資料庫）。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟︰[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。
- PowerShell 的 Azure 最新版本。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。


當您使用的[Azure 資源管理員部署模型](../azure-resource-manager/resource-group-overview.md)，因此範例使用[Azure SQL 資料庫 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)的資源管理員時，才支援的 SQL 資料庫的許多新功能。 現有的傳統的部署模型[Azure SQL 資料庫 （傳統） 的 cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx)支援回溯相容性，但我們建議您使用的資源管理員指令程式。


>[AZURE.NOTE] 根據您的資料庫的大小，複製作業可能需要一些時間才能完成。


## <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL 資料庫複製到相同的伺服器

若要建立相同的伺服器上的複製，省略`-CopyServerName`參數 （或將它設定為相同的伺服器）。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>將 SQL 資料庫複製到不同的伺服器

若要建立不同的伺服器上的複製，包括`-CopyServerName`參數並將它設定為不同的伺服器。 *複製*伺服器必須已存在。 如果在不同的資源] 群組中，那麼您也必須包含`-CopyResourceGroupName`參數。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>將 SQL 資料庫複製到彈性的資料庫資料庫

若要建立資料庫的 SQL 資料庫的複本中設定`-ElasticPoolName`參數至現有的資料庫。

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>解決登入

若要複製作業完成後，請解決登入，請參閱[解決登入](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>範例 PowerShell 指令碼

下列指令碼假設所有資源群組]，伺服器和集區已存在於 （將變數值取代您現有的資源）。 所有項目，必須存在，除了資料庫複本。

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>後續步驟

- 如需複製 Azure SQL 資料庫的概觀，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。
- 請參閱複製資料庫，使用 [Azure 入口網站的 [[複製使用 Azure 入口網站的 Azure SQL 資料庫](sql-database-copy-portal.md)。
- 請參閱[複製 Azure SQL 資料庫使用 T SQL](sql-database-copy-transact-sql.md)複製使用 SQL 資料庫。
- 瞭解[如何管理 Azure SQL 資料庫安全性損毀修復後的](sql-database-geo-replication-security-config.md)，若要深入瞭解管理使用者與登入，複製到不同的邏輯伺服器的資料庫時。


## <a name="additional-resources"></a>其他資源

- [新 AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [取得 AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [管理登入](sql-database-manage-logins.md)
- [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)
- [匯出 BACPAC 資料庫](sql-database-export.md)
- [業務連續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫文件](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL 資料庫 PowerShell Cmdlet 參考](https://msdn.microsoft.com/library/mt574084.aspx)
