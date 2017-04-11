<properties 
    pageTitle="使用 PowerShell 的 Azure SQL 資料庫設定作用中的地理複寫 |Microsoft Azure" 
    description="使用 PowerShell 的 Azure SQL 資料庫設定作用中的地理複寫" 
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
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>使用 PowerShell 的 Azure SQL 資料庫設定地理複寫

> [AZURE.SELECTOR]
- [概觀](sql-database-geo-replication-overview.md)
- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T SQL](sql-database-geo-replication-transact-sql.md)

本文將說明如何設定使用中的地理複寫使用 PowerShell SQL 資料庫。

若要開始使用 PowerShell 容錯移轉，請參閱[啟動計畫之內的容錯移轉，使用 PowerShell 的 Azure SQL 資料庫](sql-database-geo-replication-failover-powershell.md)。

>[AZURE.NOTE] 現在使用的所有服務層中的所有資料庫作用中地理複寫 （可讀取次要連結）。 在年 4 月 2017年無法讀取副類型會停用，無法讀取中現有的資料庫會自動升級到可讀的次要連結。



若要設定作用中的地理複寫使用 PowerShell，您需要下列動作︰

- Azure 的訂閱。 
- Azure SQL 資料庫-您想要複製的主要資料庫。
- Azure PowerShell 1.0 或更新版本。 您可以下載並安裝下列[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)Azure PowerShell 模組。


## <a name="configure-your-credentials-and-select-your-subscription"></a>設定您的認證，然後選取您的訂閱

您必須先建立 access Azure 帳戶，因此啟動 PowerShell，並執行下列 cmdlet。 在登入畫面中輸入相同的電子郵件與您用來登入 Azure 入口網站的密碼。


    Login-AzureRmAccount

順利登入後您會看到包含您登入 [識別碼] 和 [Azure 訂閱您有權存取的畫面上的部分資訊。


### <a name="select-your-azure-subscription"></a>選取您 Azure 的訂閱

若要選取訂閱，必須訂閱識別碼。 您可以將訂閱 Id 複製上一個步驟中，從顯示的資訊，或如果您有多個訂閱，需要更多詳細資料您可以執行**取得 AzureRmSubscription**指令程式，並將結果集，複製所要的訂閱資訊。 下列指令程式會使用訂閱識別碼來設定目前的訂閱︰

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行**選取 AzureRmSubscription**之後，您會傳回 powershell 提示。


## <a name="add-secondary-database"></a>新增第二個資料庫


下列步驟會建立新的次要資料庫地理複寫合作。  
  
若要啟用次要您必須是訂閱擁有者或共同擁有者。 

若要新增至您的伺服器上的本機資料庫合作夥伴伺服器的次要資料庫連接 （主要資料庫），您可以使用**新增 AzureRmSqlDatabaseSecondary**指令程式。 

這個 cmdlet 和**– IsContinuous**參數取代**開始 AzureSqlDatabaseCopy** 。  它會輸出**AzureRmSqlDatabaseSecondary**物件，可用於其他 cmdlet 來明確的特定複寫連結。 建立並完全定位次要資料庫時，會傳回這個指令程式。 根據資料庫大小可能需要分鐘的時間。

在第二個伺服器上複製的資料庫會在主要的伺服器上有相同的資料庫名稱，並會根據預設，有相同的服務層級。 次要資料庫可以是讀取或未讀取，而且可以是單一資料庫或彈性的資料庫。 如需詳細資訊，請參閱[新增 AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)和[服務層](sql-database-service-tiers.md)。
建立和定位次要後，資料就會開始到新的次要資料庫複寫從主要資料庫。 下列步驟說明如何完成這項工作使用 PowerShell 來建立無法讀取且容易閱讀次要連結，可以使用單一資料庫或彈性的資料庫。

如果已經存在 （例如-當做結束前一個地理複寫關聯性） 的合作夥伴資料庫的命令會失敗。



### <a name="add-a-non-readable-secondary-single-database"></a>新增非可讀的次要 （單一資料庫）

下列命令會建立資料庫的伺服器 「 srv2 」 資源群組 」 rg2 」 中的 「 mydb 「 無法讀取次要︰

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>新增可讀取次要 （單一資料庫）

下列命令會建立資料庫的伺服器 「 srv2 」 資源群組 」 rg2 」 中的 「 mydb 」 可讀取次要︰

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>新增非可讀的次要 （彈性的資料庫）

下列命令會無法讀取次要資料庫 」 mydb 」 的建立名為 「 ElasticPool1 」 的伺服器 「 srv2 」 資源群組 」 rg2 」 中的彈性的資料庫集區中︰

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>新增可讀的次要 （彈性的資料庫）

下列命令會讀取次要資料庫 」 mydb 」 的建立名為 「 ElasticPool1 」 的伺服器 「 srv2 」 資源群組 」 rg2 」 中的彈性的資料庫集區中︰

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>移除次要資料庫

您可以使用**移除 AzureRmSqlDatabaseSecondary** cmdlet，將會永久終止其主要與次要資料庫複寫合作關係。 關聯性終止之後, 第二個資料庫會變成讀寫資料庫。 如果第二個資料庫的連線中斷命令順利完成，但次要後恢復連線時，會變成讀寫。 如需詳細資訊，請參閱[移除 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx)和[服務層](sql-database-service-tiers.md)。

這個指令程式會取代停駐點 AzureSqlDatabaseCopy 複寫。 

此移除相當於強制終止中移除複製連結並保留離職次要為沒有完全複寫終止之前的獨立資料庫。 如果或可用時所有連結資料將會同時在先前的主要和次要，前者都清理。 移除複寫連結時，會傳回這個指令程式。 


若要移除次要，使用者應有根據 RBAC 主要和次要資料庫的寫入權限。 請參閱角色型存取控制，如需詳細資訊。

下列移除名為 「 mydb 」 至伺服器 「 srv2 」 的資源群組 」 rg2 「 資料庫複寫連結。 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>監視地理複寫組態和健康狀況

監視工作包括地理複寫設定的監視及監視資料複寫健康狀況。  

[取得 AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)可用來擷取轉寄複寫連結 sys.geo_replication_links 類別目錄檢視] 中顯示的相關資訊。

下列命令會擷取複寫和之間的連結 」 mydb 」 的主要資料庫伺服器 「 srv2 」 的資源群組 」 rg2 」 上的次要的狀態。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解使用地理複寫，請參閱-[作用中的地理複寫](sql-database-geo-replication-overview.md)
- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)

