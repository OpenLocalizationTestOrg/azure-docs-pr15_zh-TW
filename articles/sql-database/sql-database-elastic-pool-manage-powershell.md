<properties 
    pageTitle="管理彈性的資料庫資料庫 (PowerShell) |Microsoft Azure" 
    description="瞭解如何使用 PowerShell 來管理彈性的資料庫資料庫。"  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>監控和管理使用 PowerShell 彈性的資料庫集區 

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T SQL](sql-database-elastic-pool-manage-tsql.md)

管理[彈性的資料庫集區](sql-database-elastic-pool.md)使用 PowerShell cmdlet。 

常見的錯誤代碼，請參閱[SQL 資料庫用戶端應用程式的 SQL 錯誤碼︰ 資料庫連線錯誤及其他問題](sql-database-develop-error-messages.md)。

可以在[eDTU，儲存空間限制](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)找到集區的值。 

## <a name="prerequisites"></a>必要條件

* Azure PowerShell 1.0 或更新版本。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。
* 彈性的資料庫集區只可與 SQL 資料庫 V12 伺服器。 如果您有 SQL 資料庫 V11 伺服器，一個步驟中的 [[使用 PowerShell 來 V12 升級，並建立資料庫](sql-database-upgrade-server-portal.md)。


## <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移到彈性的資料庫

進或移出[設定 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx)集區，您可以移動資料庫。 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>變更集區的效能設定

當犧牲效能時，您可以變更集區的設定，以配合等比級數。 使用[設定 AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx)指令程式。 -Dtu 將參數設定為每個集區 eDTUs。 請參閱的可能值的[eDTU，儲存空間限制](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)。  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>取得資料庫作業的狀態

建立資料庫花費的時間。 若要追蹤的資料庫作業包括建立與更新的狀態，請使用[取得 AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx)指令程式。

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>取得資料庫進出移動彈性的資料庫的狀態

移動資料庫花費的時間。 追蹤移動狀態使用[取得 AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx)指令程式。

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>取得集區的 [資源使用狀況

可擷取的資源集區限制的百分比的指標︰   


| 單位名稱 | 描述 |
| :-- | :-- |
| cpu\_百分比 | 平均值計算使用率百分比的集區的限制。 |
| 實體\_資料\_閱讀\_百分比 | 平均 I/O 使用率百分比根據集區的限制。 |
| 記錄檔\_撰寫\_百分比 | 平均功能集區的限制百分比撰寫資源使用狀況。 | 
| DTU\_消耗\_百分比 | 平均 eDTU 使用率百分比的集區 eDTU 限制 | 
| 儲存\_百分比 | 平均利用存放區中的儲存空間限制，集區的百分比。 |  
| 同事\_百分比 | 最大同時工作者 （要求），根據集區的限制的百分比。 |  
| 工作階段\_百分比 | 最大百分比根據集區的限制同時工作階段。 | 
| eDTU_limit | 目前最大的彈性資料庫 DTU 設定這個彈性的集區在這段期間。 |
| 儲存\_限制 | 在這段期間設定以百萬位元組這個彈性集區目前最大的彈性資料庫儲存空間限制。 |
| eDTU\_使用 | 使用此時間間隔的集區的平均 eDTUs。 |
| 儲存\_使用 | 平均儲存使用中位元組此間隔的資料庫 |

**指標資料粒度/保持期限︰**

* 資料將會傳回在 5 分鐘資料粒度。  
* 資料保留是 35 天。  

這個 cmdlet 和 API 限制可以擷取一個呼叫 1000 個資料列 （關於 3 天的資料在 5 分鐘資料粒度） 中的資料列數目。 但是可以使用不同的開始/結束時間間隔，取得更多資料多次呼叫此命令 

若要擷取的指標︰

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>取得彈性的資料庫中的資源使用狀況

這些 Api 是資料庫的用來監視獨立，除了下列語意差異的資源使用量的目前 (V12) Api 相同。 

針對此 API，擷取的指標會以百分比表示的每個最大 eDTUs （或對等的首字放大的基礎公制像 CPU、 IO 等） 為該資料庫。 例如，50%的任何這些計量的使用狀況表示特定資源消耗位於 50%的每個資料庫首字放大的上層集區中的資源。 

若要擷取的指標︰

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>新增資料庫資源的提醒

您可以新增提醒的規則傳送電子郵件通知或提醒的字串[URL 端點](https://msdn.microsoft.com/library/mt718036.aspx)當資源設定使用閾值的資源。 使用新增 AzureRmMetricAlertRule 指令程式。 

> [AZURE.IMPORTANT]監控彈性的集區的資源使用量有至少 20 分鐘的延遲。 目前不支援的彈性的集區設定通知的 30 分鐘。 任何通知彈性的集區設定以英文句點 (稱為參數 」-WindowSize 」 中 PowerShell API) 30 分鐘的可能不會觸發。 請確定您定義彈性的集區的任何通知使用 30 分鐘或更一段 (WindowSize)。

此範例中加入集區 eDTU 消耗超過特定閾值時收到通知的提醒。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>新增提醒至資料庫中的所有資料庫

您可以新增提醒的規則傳送電子郵件通知彈性資料庫中的所有資料庫或通知字串[URL 端點](https://msdn.microsoft.com/library/mt718036.aspx)時資源使用量臨界值設定提醒。 

> [AZURE.IMPORTANT] 監控彈性的集區的資源使用量有至少 20 分鐘的延遲。 目前不支援的彈性的集區設定通知的 30 分鐘。 任何通知彈性的集區設定以英文句點 (稱為參數 」-WindowSize 」 中 PowerShell API) 30 分鐘的可能不會觸發。 請確定您定義彈性的集區的任何通知使用 30 分鐘或更一段 (WindowSize)。

本範例會新增至該資料庫 DTU 消耗超過特定閾值時收到通知的集區中的資料庫的各個通知。

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>收集並監控跨多個訂閱集區的 [資源使用狀況

當您在訂閱中有大量的資料庫時，將會很難分別監控每個彈性的資料庫。 不過，SQL 資料庫 PowerShell cmdlet T SQL 查詢可以結合多個集區和其資料庫的監控和資源使用狀況分析收集資源使用狀況。 設定的 powershell 指令碼[範例實作](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)可以找到 GitHub SQL Server 範例存放庫內，以及相關文件功能，以及如何使用它。

若要使用執行此範例，請遵循下列下方所列的步驟。


1. 下載[指令碼和文件](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)︰
2. 修改您的環境的指令碼。 指定一個或多個伺服器裝載彈性的資料庫。
3. 指定的度量收集的資訊會儲存在哪裡遙測資料庫。 
4. 自訂指令碼來指定執行指令碼的持續時間。

在高的層級，指令碼執行下列動作︰

*   列舉所有伺服器中指定的 Azure 訂閱 （或指定的伺服器清單）。
*   執行每個伺服器的背景工作。 工作循環執行定期間隔，收集遙測資料的伺服器中的所有集區。 然後會收集的資料載入到指定的遙測資料庫。
*   列舉收集資料庫資源使用狀況資料每個資料庫中的資料庫的清單。 然後會收集的資料載入到遙測資料庫。

您可以監視彈性的資料庫及資料庫的狀況分析遙測資料庫中收集的指標。 指令碼也會安裝的預先定義的資料表值函式 (TVF) 可協助彙總遙測資料庫中指定的時間視窗的指標。 TVF 的結果，例如，可以用來顯示 「 上方 N 彈性集區與指定的時間視窗中的最大 eDTU 使用率。 」 您也可以使用查詢並分析收集的資料分析工具，例如 Excel 或 Power BI。

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>範例︰ 擷取集區和其資料庫的資源消耗指標

此範例中擷取耗用的指標彈性集和其所有資料庫。 收集的資料設定格式，而寫入.csv 格式的檔案。 使用 Excel 可以瀏覽檔案。

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>彈性的資料庫作業的延遲

- 在 5 分鐘之內，通常變更每個資料庫的資料庫或最大值 eDTUs 每 min eDTUs 會完成。
- 變更每個集區 eDTUs，則使用集區中的所有資料庫的空間的總數量而定。 變更計算平均值 90 分鐘或更少每 100 GB。 例如，如果所使用的總空間集區中的所有資料庫都是 200 GB，變更每個集區資料庫 eDTU 預期的延遲為 3 個小時之內。

## <a name="migrate-from-v11-to-v12-servers"></a>從 V11 移轉到 V12 伺服器

PowerShell cmdlet 可啟動、 停止或監控升級至 Azure SQL 資料庫 V12，從 V11 或其他類型的前置 V12 版本。

- [使用 PowerShell 以 SQL 資料庫 V12 升級](sql-database-upgrade-server-powershell.md)

如需瞭解這些 PowerShell cmdlet 參照文件，請參閱︰


- [取得 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [開始 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [停止 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


取消、 暫停，就表示停駐點指令程式。 有任何繼續升級時，不是一次從頭開始。 停止指令程式會，然後釋放適當的所有資源。

## <a name="next-steps"></a>後續步驟

- [建立彈性的工作](sql-database-elastic-jobs-overview.md)彈性的工作可讓您執行的資料庫在資料庫中的任何數字 T SQL 指令碼。
- 請參閱[Azure SQL 資料庫時的縮放比例](sql-database-elastic-scale-introduction.md)︰ 若要向外，移動資料，請使用彈性的資料庫工具查詢，或建立交易。
