<properties
    pageTitle="管理使用 PowerShell 的 Azure SQL 資料庫 |Microsoft Azure"
    description="使用 PowerShell 的 azure SQL 資料庫管理。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>管理使用 PowerShell 的 Azure SQL 資料庫


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-manage-portal.md)
- [TRANSACT-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

本主題說明用來執行許多 Azure SQL 資料庫工作的 PowerShell 指令程式。 如需完整的清單，請參閱 [Azure SQL 資料庫 Cmdlet] (https://msdn.microsoft.com/library/mt574084(v=azure.300\).aspx)。


## <a name="create-a-resource-group"></a>建立資源群組

建立資源群組我們 SQL 資料庫] 及 [相關的 Azure 資源的 [新增-AzureRmResourceGroup] （https://msdn.microsoft.com/library/azure/mt759837(v=azure.300\).aspx) 指令程式。

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

如需詳細資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。
範例指令碼，請參閱[建立 SQL 資料庫 PowerShell 指令碼](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)。

## <a name="create-a-sql-database-server"></a>建立 SQL 資料庫伺服器

建立 SQL 資料庫伺服器的 [新增-AzureRmSqlServer] （https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) 指令程式。 *Server1*換成您的伺服器的名稱。 在所有 Azure SQL 資料庫伺服器，伺服器名稱必須是唯一的。 如果已經存在的伺服器名稱，您會看到的錯誤。 這個命令，可能需要幾分鐘才能完成。 在您的訂閱，必須已經存在的資源群組。

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

如需詳細資訊，請參閱[什麼是 SQL 資料庫](sql-database-technical-overview.md)。 範例指令碼，請參閱[建立 SQL 資料庫 PowerShell 指令碼](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)。


## <a name="create-a-sql-database-server-firewall-rule"></a>建立 SQL 資料庫伺服器防火牆規則

建立防火牆規則，以存取伺服器與 [新增-AzureRmSqlServerFirewallRule] （https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) 指令程式。 執行下列命令，以有效的值取代您的用戶端的開始和結束的 IP 位址。 資源群組與伺服器必須已經存在於您的訂閱。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

若要允許其他 Azure 服務存取您的伺服器，建立防火牆規則，並將兩個設定`-StartIpAddress`和`-EndIpAddress`為**0.0.0.0**。 此特殊的防火牆規則可讓所有 Azure 存取伺服器的流量。

如需詳細資訊，請參閱[Azure SQL 資料庫防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。 範例指令碼，請參閱[建立 SQL 資料庫 PowerShell 指令碼](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)。


## <a name="create-a-sql-database-blank"></a>建立 SQL 資料庫 （空白）

建立資料庫的 [新增-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) 指令程式。 資源群組與伺服器必須已經存在於您的訂閱。 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

如需詳細資訊，請參閱[什麼是 SQL 資料庫](sql-database-technical-overview.md)。 範例指令碼，請參閱[建立 SQL 資料庫 PowerShell 指令碼](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script)。


## <a name="change-the-performance-level-of-a-sql-database"></a>變更 SQL 資料庫的效能層級

調整您的資料庫向上或向下的 [設定-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) 指令程式。 資源群組、 伺服器，以及資料庫必須已經存在於您的訂閱。 設定`-RequestedServiceObjectiveName`基本層的一個空格 （例如下列程式碼片段）。 將它設定為*S0*、 *S1*、 *P1*、 *P6*等等，如前述範例其他層。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

如需詳細資訊，請參閱[SQL 資料庫選項和效能︰ 瞭解什麼是用於每個服務層級](sql-database-service-tiers.md)。 範例指令碼，請參閱[範例 PowerShell 指令碼，若要變更的服務層和效能層級 SQL 資料庫](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database)。

## <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL 資料庫複製到相同的伺服器

將 SQL 資料庫複製到相同的伺服器，使用 [新增-AzureRmSqlDatabaseCopy] （https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx) 指令程式。 設定`-CopyServerName`和`-CopyResourceGroupName`來源資料庫伺服器及資源群組為相同的值。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

如需詳細資訊，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。 範例指令碼，請參閱[複製 SQL 資料庫 PowerShell 指令碼](sql-database-copy-powershell.md#example-powershell-script)。


## <a name="delete-a-sql-database"></a>刪除 SQL 資料庫

刪除 SQL 資料庫的 [移除-AzureRmSqlDatabase] （https://msdn.microsoft.com/library/azure/mt619368(v=azure.300\).aspx) 指令程式。 資源群組、 伺服器，以及資料庫必須已經存在於您的訂閱。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>刪除 SQL 資料庫伺服器

刪除伺服器的 [移除-AzureRmSqlServer] （https://msdn.microsoft.com/library/azure/mt603488(v=azure.300\).aspx) 指令程式。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>建立及管理彈性的資料庫集區使用 PowerShell

如需建立彈性的資料庫集區使用 PowerShell 的詳細資訊，請參閱[建立新的彈性的資料庫集區使用 PowerShell](sql-database-elastic-pool-create-powershell.md)。

如需管理使用 PowerShell 彈性的資料庫資料庫的詳細資訊，請參閱[監控和管理使用 PowerShell 彈性的資料庫集區](sql-database-elastic-pool-manage-powershell.md)。



## <a name="related-information"></a>相關的資訊

- [Azure SQL 資料庫 Cmdlet](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/dn708514(v=azure.300\).aspx)
