<properties
    pageTitle="使用 PowerShell 新增 SQL 資料庫設定 |Microsoft Azure"
    description="立即瞭解如何使用 PowerShell 建立 SQL 資料庫。 管理資料庫設定的一般工作時，可以透過 PowerShell cmdlet。"
    keywords="建立新的 sql 資料庫，資料庫設定"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>建立 SQL 資料庫及執行常見的資料庫設定工作，透過 PowerShell cmdlet


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



瞭解如何使用 PowerShell cmdlet 來建立 SQL 資料庫。 （如建立彈性的資料庫，請參閱[建立新的彈性的資料庫集區使用 PowerShell](sql-database-elastic-pool-create-powershell.md)）。


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>資料庫設定︰ 建立資源群組、 伺服器，以及防火牆規則

一旦您有權存取您所選的 Azure 訂閱對執行 cmdlet 下, 一步建立包含將會建立資料庫伺服器的資源群組。 您可以編輯您選擇下一個命令，使用任何有效的位置。 執行**(取得 AzureRmLocation |位置物件 {$_。提供者-eq 「 Microsoft.Sql 」})。位置**以取得正確的位置清單。

執行下列命令以建立資源群組︰

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>建立伺服器

SQL 資料庫會建立內 Azure SQL 資料庫伺服器。 執行 [新增-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) 建立伺服器。 您的伺服器的名稱必須是唯一的所有 Azure SQL 資料庫伺服器。 如果已經存在的伺服器名稱，您會看到的錯誤。 也值得是這個命令可能需要幾分鐘才能完成。 您可以編輯] 命令，使用任何有效的位置選擇，但您應該使用您用來建立一個步驟中的資源群組的同一個位置。

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

當您執行此命令時，系統會提示您的使用者名稱和密碼。 不輸入 Azure 認證。 不過，輸入使用者名稱和密碼，才能建立伺服器系統管理員的身分。 在本文底部的指令碼會顯示如何設定程式碼中的伺服器認證。

伺服器成功建立之後，就會出現伺服器詳細資料。

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>設定伺服器防火牆規則，允許伺服器的存取權

若要存取伺服器，必須先建立防火牆規則。 執行 [新增-AzureRmSqlServerFirewallRule] （https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)] 命令，以您的電腦的有效數值取代的開始和結束的 IP 位址。

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

已成功建立規則之後，就會出現的防火牆規則詳細資料。

若要讓其他 Azure 服務存取伺服器，請新增防火牆規則，StartIpAddress 和 EndIpAddress] 設定為 0.0.0.0。 此規則可讓您存取伺服器的任何 Azure 訂閱 Azure 流量。

如需詳細資訊，請參閱[Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)。


## <a name="create-a-sql-database"></a>建立 SQL 資料庫

現在您擁有資源群組與伺服器，您就可以存取的伺服器設定防火牆規則。

下列 [新增 AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx)] 命令會建立一個 （空白） SQL 資料庫在標準服務層，S1 效能等級︰


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


資料庫已成功建立之後，就會出現資料庫的詳細資訊。

## <a name="create-a-sql-database-powershell-script"></a>建立 SQL 資料庫 PowerShell 指令碼

SQL 資料庫，且及其相關的資源，就會建立下列 PowerShell 指令碼。 全部取代`{variables}`與您的訂閱和資源 （當您設定您的值時，請移除**{}** ） 的特定的值。

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>後續步驟
建立 SQL 資料庫後，當您執行基本的資料庫設定工作時，您準備好進行下列動作︰

- [管理使用 PowerShell SQL 資料庫](sql-database-manage-powershell.md)
- [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>其他資源

- [Azure SQL 資料庫 Cmdlet](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Azure SQL 資料庫](https://azure.microsoft.com/documentation/services/sql-database/)
