<properties
    pageTitle="建立新的彈性的資料庫集區使用 PowerShell |Microsoft Azure"
    description="瞭解如何使用 PowerShell 來擴充 Azure SQL 資料庫來建立管理多個資料庫可調整彈性資料庫集區的資源。"
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>使用 PowerShell 來建立新的彈性的資料庫資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


瞭解如何建立[彈性的資料庫集區](sql-database-elastic-pool.md)使用 PowerShell cmdlet。 

常見的錯誤代碼，請參閱[SQL 資料庫用戶端應用程式的 SQL 錯誤碼︰ 資料庫連線錯誤及其他問題](sql-database-develop-error-messages.md)。

> [AZURE.NOTE] 彈性的集區北美美國中部和位置正在中預覽西印度以外的所有 Azure 區域是推出 (GA)。  在下列區域內的彈性集區 GA 會越快越提供。 此外，彈性的集區目前不支援使用[記憶體內 OLTP 或記憶體內分析](sql-database-in-memory.md)的資料庫。


您需要執行 Azure PowerShell 1.0 或更新版本。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

## <a name="create-a-new-pool"></a>建立新的資料庫

[新增 AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx)指令程式會建立新的資料庫。 [服務] 層值 （基本、 標準、 或進階） 會受限於 eDTU 每個資料集區，最大 Dtus 最小值] 的值。 請參閱[eDTU，儲存空間限制的彈性的資料庫及彈性的資料庫](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)。

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>在資料庫中建立新的彈性資料庫

使用[新增 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet 和**ElasticPoolName**參數設定至目標資料庫。 若要將現有的資料庫至資料庫，請參閱[移動到彈性的集區資料庫](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool)。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>建立資料庫，並填入多個新的資料庫 

建立大量集區中的資料庫會花費時間完成使用入口網站或一次建立單一資料庫的 PowerShell 指令程式。 若要自動建立到新的資料庫，請參閱[CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。   

## <a name="example-create-a-pool-using-powershell"></a>範例︰ 建立資料庫，使用 PowerShell 

這個指令碼會建立新的 Azure 資源群組和新的伺服器。 出現提示時，提供系統管理員的使用者名稱和密碼，為新的伺服器 （不 Azure 認證）。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>後續步驟

- [管理您的資料庫](sql-database-elastic-pool-manage-powershell.md)
- [建立彈性的工作](sql-database-elastic-jobs-overview.md)彈性的工作可讓您執行的資料庫在資料庫中的任何數字 T SQL 指令碼。
- [Azure SQL 資料庫時的縮放比例](sql-database-elastic-scale-introduction.md)︰ 使用 [擴充的彈性的資料庫工具]。

