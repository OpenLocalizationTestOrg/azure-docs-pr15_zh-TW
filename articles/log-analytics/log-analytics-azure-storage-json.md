<properties
    pageTitle="分析使用記錄分析 Azure 診斷記錄 |Microsoft Azure"
    description="記錄分析可以讀取撰寫 Azure blob JSON 格式的儲存空間的診斷記錄的 Azure 服務的記錄。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>分析使用記錄分析 Azure 診斷記錄

記錄檔分析會收集下列 Azure 服務寫入 blob 儲存體 JSON 格式[Azure 診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)的記錄︰

+ 自動化 （預覽版本）
+ 索引鍵保存庫 （預覽版本）
+ 應用程式閘道器 （預覽版本）
+ 網路安全性群組 （預覽版本）

下列各節會引導您完成使用 PowerShell 來︰

+ 設定記錄分析，從每個資源的儲存空間收集記錄  
+ 啟用記錄分析解決方案 Azure 服務

記錄檔分析可以收集這些資源的資料之前，必須先啟用 Azure 診斷。 使用`Set-AzureRmDiagnosticSetting`cmdlet 來啟用記錄功能。

請參閱下列文章，如需有關如何啟用診斷記錄︰

+ [索引鍵保存庫](../key-vault/key-vault-logging.md)
+ [應用程式的閘道器](../application-gateway/application-gateway-diagnostics.md)
+ [網路安全性群組](../virtual-network/virtual-network-nsg-manage-log.md)

這份文件也會包含在詳細資料︰

+ 疑難排解資料收集
+ 停止資料收集

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>設定記錄分析，收集 Azure 診斷記錄

使用 PowerShell 指令碼，執行收集這些服務的記錄，以及啟用記錄以視覺化方式呈現的解決方案。

下面的範例中啟用登入所有的支援資源

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


某些資源，可能是從 Azure 入口網站使用[的 Azure 概觀診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)中所述的步驟執行上述設定。

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>設定記錄分析，收集 Azure 診斷記錄

我們提供的 PowerShell 指令碼模組匯出兩個 cmdlet，以協助您設定記錄分析︰

1. `Add-AzureDiagnosticsToLogAnalyticsUI`提示您輸入，而且可以設定簡單的設定
2. `Add-AzureDiagnosticsToLogAnalytics`會帶監控做為輸入資源，並設定記錄狀況分析  

### <a name="pre-requisites"></a>必要條件

1. 使用版本 1.0.8 azure PowerShell 或較新的操作的深入見解 cmdlet。
  - [如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)
  - 確認您的 cmdlet 版本︰`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. 診斷記錄被設定為您想要監視 Azure 資源。 使用`Set-AzureRmDiagnosticSetting`或[收集資料從 Azure 儲存體帳戶使用記錄分析](log-analytics-azure-storage.md)如何啟用診斷參照。
3. [記錄檔分析](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)工作區  
4. AzureDiagnosticsAndLogAnalytics PowerShell 模組
  - 從 PowerShell 庫下載[AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/)模組

### <a name="option-1-run-the-interactive-configuration-scripts"></a>選項 1︰ 執行互動式設定指令碼

開啟 PowerShell，並執行︰

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

您會顯示可用的選項清單，並提供的提示，讓您的選取範圍。
系統要求您選妥下列各項︰

+ 資源類型 （Azure 服務），收集在記錄檔
+ 資源執行個體，收集在記錄檔
+ 登入收集的資料分析工作區

後執行這個指令碼，您應該會看到記錄分析中的記錄資訊 30 分鐘後新診斷資料寫入儲存空間。 如果記錄後，就不提供這次請參閱疑難排解下方的章節。

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>選項 2︰ 建立資源的清單，並將其傳遞設定 cmdlet

您可以建立資源 Azure 診斷啟用，然後將資源設定指令程式的清單。

您可以藉由執行看到 cmdlet 的其他資訊`Get-Help Add-AzureDiagnosticsToLogAnalytics`。


若要尋找 [OMS[記錄分析 PowerShell Cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)上的 [更多詳細資料

>[AZURE.NOTE] 如果資源和工作區是在不同的 Azure 訂閱，切換視需要使用`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
後執行這個指令碼，您應該會看到記錄分析中的記錄資訊 30 分鐘後新診斷資料寫入儲存空間。 如果記錄後，就不提供這次請參閱疑難排解下方的章節。  

>[AZURE.NOTE] Azure 入口網站中看不到 [設定。 您可以驗證設定使用`Get-AzureRmOperationalInsightsStorageInsight`指令程式。  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>停止記錄分析收集 Azure 診斷記錄

若要刪除資源記錄分析設定，請使用`Remove-AzureRmOperationalInsightsStorageInsight`指令程式。

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>疑難排解 Azure 診斷記錄的設定

*問題*

設定登入以傳統的儲存空間的資源時，會顯示下列錯誤︰

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*解決方法*

登入的與傳統部署模型 API`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>疑難排解 Azure 診斷記錄的資料收集

如果您沒有看到您 Azure 中的資源記錄分析資料，您可以使用下列疑難排解步驟︰

+ 確認儲存的帳戶傳送的資料
+ 確認已啟用記錄分析方案的服務
+ 確認已設定記錄分析讀取儲存空間
+ 更新儲存帳戶金鑰

### <a name="verify-data-is-flowing-to-the-storage-account"></a>驗證資料儲存帳戶書寫方向

您可以檢查是否資料儲存帳戶的工具，讓您瀏覽 Azure 儲存空間 (例如 Visual Studio)，或使用 PowerShell 書寫方向。

若要尋找儲存帳戶資源設定以使用下列 PowerShell 登入︰

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

使用 Azure 診斷存放容器具有名稱的格式︰  

`insights-logs-<Category>`

請參閱[使用儲存空間 cmdlet 來檢查新的資料的容器](../storage/storage-powershell-guide-full.md)若要進一步瞭解檢視儲存帳戶的內容。

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>確認已啟用記錄分析方案的服務

如果您是使用`Add-AzureDiagnosticsToLogAnalyticsUI`，正確的記錄檔分析方案，會自動為您啟用。

若要檢查是否已啟用方案，請執行下列 PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

如果未啟用方案，您可以使用下列 PowerShell 啟用︰

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

若要啟用的每個資源類型的方案名稱，請使用下列 PowerShell （此變數是提供您匯入模組後）︰

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>確認已設定記錄分析讀取儲存空間

如果您新增其他 Azure 資源時，必須先啟用診斷記錄，並設定這些記錄分析。
若要檢查的資源，並儲存帳戶記錄分析設為收集的記錄，請使用下列 PowerShell:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>更新的儲存空間索引鍵

如果您變更儲存帳戶金鑰，記錄分析組態也必須以新的金鑰來更新。
您可以使用下列 PowerShell 以新的金鑰更新記錄分析設定︰

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

若要尋找儲存充分名稱，請使用`Get-AzureRmOperationalInsightsStorageInsight`cmdlet，先前範例所示。

## <a name="next-steps"></a>後續步驟

- [使用 blob 儲存體 iis 和資料表儲存體的事件](log-analytics-azure-storage-iis-table.md)記錄的 Azure 服務資料表儲存體或寫入 blob 儲存體 IIS 記錄的寫入診斷。
- [啟用解決方案](log-analytics-add-solutions.md)提供深入資料。
- [使用搜尋查詢](log-analytics-log-searches.md)，以分析的資料。
