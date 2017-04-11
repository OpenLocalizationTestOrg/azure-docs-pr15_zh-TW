<properties
    pageTitle="使用 PowerShell 來 Azure SQL 資料庫 V12 升級 |Microsoft Azure"
    description="說明如何升級至 Azure SQL 資料庫 V12 包括如何升級網站和商務資料庫，以及如何升級 V11 伺服器直接將使用 PowerShell 彈性的資料庫資料庫移轉的資料庫。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>使用 PowerShell 來 Azure SQL 資料庫 V12 升級


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


建議您升級至 SQL 資料庫 V12 SQL 資料庫 V12 是最新版本。
SQL 資料庫 V12 具有多個[前一版的優點](sql-database-v12-whats-new.md)包括︰

- 提高使用 SQL Server 相容。
- 改良的進階版效能與新的效能層級。
- [彈性的資料庫集區](sql-database-elastic-pool.md)。

本文提供指示以 SQL 資料庫 V12 升級現有的 SQL 資料庫 V11 伺服器和資料庫。

期間的升級至 V12 程序，讓升級網站和商務資料庫的說明都包含在內 」，任何網站和商務資料庫升級至新的服務層級。

此外，移轉至[彈性的資料庫資料庫](sql-database-elastic-pool.md)可成本比更有效單一資料庫升級個別的效能層級 （價格層）。 集區也簡化管理資料庫，因為您只需要管理資料庫，而不是個別管理的個別資料庫的效能層級的效能設定。 如果您有多個伺服器上的資料庫，請考慮將它們移到相同的伺服器，並利用將它們放入集區。

您可以依照本文中，直接將彈性的資料庫集區輕鬆地將從 V11 伺服器的資料庫中的步驟進行。

請注意，資料庫會保持線上狀態，繼續處理整個升級作業。 在新的效能層級暫時卸除資料庫連線的實際的轉場的時間便會發生這通常是大約 90 秒的很小持續時間內，但可以約 5 分鐘。 如果您的應用程式有[暫時性錯誤的處理連線終止](sql-database-connectivity-issues.md)它能充分防範首字結尾的升級的連線。

升級至 SQL 資料庫 V12 無法復原。 升級後伺服器無法還原成 V11。

升級至 V12 之後，[服務層建議](sql-database-service-tier-advisor.md)和[彈性的資料庫建議](sql-database-elastic-pool-create-portal.md)會立即無法使用直到服務有時間進行評估您新的伺服器上的工作量。 V11 伺服器建議記錄不適用於 V12 伺服器，讓它不會保留。  

## <a name="prepare-to-upgrade"></a>準備升級

- **升級 Web 和企業版的所有資料庫**︰ 使用入口網站，或使用[PowerShell 來都升級資料庫及伺服器](sql-database-upgrade-server-powershell.md)。
- **檢閱及擱置地理複寫**︰ 如果 Azure SQL 資料庫設為地理複寫您應該將文件其目前的設定與[停止地理複寫](sql-database-geo-replication-portal.md#remove-secondary-database)。 升級完成之後重新設定您的資料庫，以進行地理複寫。
- **開啟下列連接埠，如果您的用戶端上 Azure VM**︰ 如果您的用戶端程式連線到 SQL 資料庫 V12 時您的用戶端執行 Azure 虛擬機器 (VM) 上時，您必須開啟連接埠範圍 11000 11999 和 14000-14999 VM 上。 如需詳細資訊，請參閱[SQL 資料庫 V12 的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。


## <a name="prerequisites"></a>必要條件

若要使用 PowerShell V12 升級伺服器，您需要有最新的 Azure PowerShell 安裝及執行。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。


## <a name="configure-your-credentials-and-select-your-subscription"></a>設定您的認證，然後選取您的訂閱

您必須先 Azure 帳戶建立 access 對 Azure 訂閱執行 PowerShell cmdlet。 執行下列動作，然後輸入您的認證登入畫面會顯示。 使用相同電子郵件與您用來登入 Azure 入口網站的密碼。

    Add-AzureRmAccount

順利登入後您應該會看到包含您登入 [識別碼] 和 [Azure 訂閱您有權存取的畫面上的部分資訊。

若要選取您想要使用與您的訂閱，需要您的訂閱 Id (**-SubscriptionId**) 或訂閱命名 (**-SubscriptionName**)。 您可以複製上一個步驟，或者如果您有多個訂閱，您就可以執行**取得 AzureRmSubscription**指令程式，並從結果集複製您要的訂閱資訊。

使用您的訂閱資訊 」 來設定您目前的訂閱中執行下列 cmdlet:

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

下列命令會針對來執行訂閱，您只要選取上方。

## <a name="get-recommendations"></a>取得的建議

若要取得伺服器的建議升級，請執行下列 cmdlet:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

如需詳細資訊，請參閱[建立彈性的資料庫集區](sql-database-elastic-pool-create-portal.md)和[Azure SQL 資料庫價格層建議](sql-database-service-tier-advisor.md)。



## <a name="start-the-upgrade"></a>開始升級

若要開始升級的伺服器執行下列 cmdlet:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


當您執行時就會開始此命令升級程序。 您可以自訂的建議輸出，並提供此 cmdlet 編輯的建議。


## <a name="upgrade-a-server"></a>升級伺服器


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>自訂升級對應

如果不適合您的伺服器和商務案例的建議，然後您可以選擇資料庫升級的方式，並可以對應到單一或彈性的資料庫。

ElasticPoolCollection 和 DatabaseCollection 參數是選擇性的︰

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>監控資料庫升級為 SQL 資料庫 V12 之後


升級之後，建議監控資料庫主動確認應用程式執行的在想要的效能，並視需要最佳化使用方式。

此外，若要監視個別的資料庫，您可以監視彈性的資料庫集區[使用入口網站](sql-database-elastic-pool-manage-portal.md)，或使用[PowerShell](sql-database-elastic-pool-manage-powershell.md)


**資源消耗資料︰**基本、 標準和進階版的資料庫的資源消耗資料是透過[sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV 使用者資料庫中。 這個 DMV 提供附近即時資源消耗資訊在 15 的第二個資料粒度作業的前一個小時。 時間間隔 DTU 百分比消耗計算方式 [最大百分比耗用的 CPU、 IO 和記錄維度。 以下是查詢來計算平均 DTU 百分比消耗移到最後一個小時︰

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

監控的其他資訊︰

- [Azure SQL 資料庫效能指導單一的資料庫](http://msdn.microsoft.com/library/azure/dn369873.aspx)。
- [彈性的資料庫資料庫的價格和效能考量](sql-database-elastic-pool-guidance.md)。
- [使用動態管理檢視監控 Azure SQL 資料庫](sql-database-monitoring-with-dmvs.md)



**通知︰**「 通知 」 中設定 Azure 入口網站升級後的資料庫 DTU 消耗接近特定高層級時通知您。 資料庫通知可以設定 DTU、 CPU、 IO 及記錄等各種效能眼 Azure 入口網站中。 瀏覽至您的資料庫，然後選取 [**設定**刀中的 [**提醒的規則**。

例如，您可以設定 「 DTU 百分比 」 的電子郵件警示如果 DTU 百分比值與平均值超過 75%移到最後一個 5 分鐘。 若要進一步瞭解如何設定提醒通知，請參閱接收[提醒通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。



## <a name="next-steps"></a>後續步驟

- [建立彈性的資料庫資料庫](sql-database-elastic-pool-create-portal.md)並將部分或所有資料庫都新增到資料庫。
- [變更您的資料庫的服務層和效能層級](sql-database-scale-up.md)。



## <a name="related-information"></a>相關的資訊

- [取得 AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [開始 AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [停止 AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
