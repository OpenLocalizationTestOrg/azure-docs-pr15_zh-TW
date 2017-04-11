<properties 
    pageTitle="變更使用 PowerShell 的 Azure SQL 資料庫的服務層和效能層級 |Microsoft Azure" 
    description="變更服務層級和 Azure SQL 資料庫的效能層級會顯示如何使用 PowerShell 向上或向下調整 SQL 資料庫。 變更使用 PowerShell 的 Azure SQL 資料庫的價格層。" 
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
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>變更服務層和效能的層級 （價格層） 使用 PowerShell SQL 資料庫


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


服務層效能層級描述的功能和 SQL 資料庫的資源，並可以更新為應用程式進行變更的需求。 如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。

請注意，變更層及/或資料庫的效能層級會在新的效能層級，建立原始資料庫的複本，然後在複本的切換連線。 此程序期間會遺失任何資料，但期間長的時間，當我們切換至複本，資料庫的連線會停用，因此航班的一些交易可能復原。 這個視窗不盡相同，但平均底下 4 秒鐘，而在 99%以上的情況下會小於 30 秒。 很少，尤其是如果有大型的交易航班森積差連線的數字會停用，這個視窗可能會更長的時間。  

整個升級程序的工期而定資料庫的大小和服務層之前和之後的變更。 例如，250 GB 資料庫會變更到、，或在標準的服務層級，應該 6 小時內完成。 為相同大小會變更內進階版服務層級的效能層級的資料庫，它應該 3 小時內完成。


- 若要降級資料庫，資料庫應小於目標服務層級的最大可容許大小。 
- 升級時資料庫[地理複寫](sql-database-geo-replication-portal.md)啟用，您必須先升級其次要資料庫至想要的效能層之前升級的主要資料庫。
- 當降級從進階版服務層級，您必須先終止所有地理複寫關聯。 您可以遵循[從中斷復原](sql-database-disaster-recovery.md)主題停止之間的主要及作用中的次要資料庫複寫程序中所述的步驟。
- 還原服務方案是不同的各服務層。 如果您降級您可能會遺失能夠還原到時間，或有較低的備份保留期間。 如需詳細資訊，請參閱[Azure SQL 資料庫備份與還原](sql-database-business-continuity.md)。
- 變更完成前，不會套用新資料庫的屬性。



**若要完成這份文件您需要下列項目︰**

- Azure 的訂閱。 如果您需要的 Azure 訂閱，只要按一下 [**免費的帳戶**] 頁面的頂端，然後回到完成這份文件。
- Azure SQL 資料庫。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟︰[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。
- Azure PowerShell。


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>變更您的 SQL 資料庫的服務層和效能層級

執行 [設定-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet 與設定**-RequestedServiceObjectiveName**效能層級的所需的價格層; 例如*S0* *S1*、 *S2*、 *S3*、 *P1*、 *P2*，...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>若要變更的服務層和效能層級 SQL 資料庫的 PowerShell 指令碼的範例

取代```{variables}```與您的值 （不包含大括號）。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>後續步驟

- [不按比例縮放看]，並在](sql-database-elastic-scale-get-started.md)
- [連接及查詢 SSMS 的 SQL 資料庫](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL 資料庫](sql-database-export-powershell.md)

## <a name="additional-resources"></a>其他資源

- [業務連續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫文件](http://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL 資料庫 Cmdlet](http://msdn.microsoft.com/library/azure/mt574084https:/ / msdn.microsoft.com/文件庫/azure/mt619433(v=azure.300\).aspx.aspx)