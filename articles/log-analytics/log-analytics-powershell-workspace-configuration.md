<properties
    pageTitle="使用 PowerShell 來建立及設定記錄分析工作區 |Microsoft Azure"
    description="記錄檔分析會使用資料中您的內部部署伺服器或雲端基礎結構。 您可以從 Azure 儲存體時產生的 Azure 診斷收集機器資料。"
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>管理記錄分析使用 PowerShell

您可以使用 [記錄分析 PowerShell cmdlet] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) 記錄分析中執行各種功能，從命令列或指令碼的一部分。  您可以使用 PowerShell 執行的工作的範例包括︰

+ 建立工作區
+ 新增或移除解決方案
+ 匯入及匯出儲存的搜尋
+ 建立電腦群組
+ 啟用的集合 IIS 記錄從電腦上已安裝 Windows 代理程式
+ 從 Linux 和 Windows 的電腦收集正在
+ 收集 Linux 電腦從系統事件 
+ 從 Windows 事件記錄檔收集事件
+ 收集自訂事件記錄檔
+ 將記錄分析代理程式新增至 Azure 虛擬機器
+ 設定記錄使用 Azure 診斷收集資料索引的狀況分析


本文將說明一些您可以從 PowerShell 執行的函數的兩個程式碼範例。  您可以參考 [記錄分析 PowerShell cmdlet 參考] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) 其他函數。

> [AZURE.NOTE] 記錄檔分析先前稱為操作觀點，這是為什麼 cmdlet 中使用的名稱。

## <a name="prerequisites"></a>必要條件

若要使用 PowerShell 與您記錄分析工作區，您必須︰

+ Azure 的訂閱，和 
+ 連結至 Azure 訂閱您 Azure 記錄分析工作區。

如果您已經建立 OMS 工作區中，但沒有尚未連結它 Azure 訂閱，您可以建立連結︰

+ Azure 入口網站中
+ 在 OMS 入口網站或 
+ 使用取得 AzureRmOperationalInsightsLinkTargets 和新增 AzureRmOperationalInsightsWorkspace cmdlet。


## <a name="create-and-configure-a-log-analytics-workspace"></a>建立及設定記錄分析工作區

下列指令碼範例說明如何︰

1.  建立工作區
2.  清單的可用的解決方案
3.  新增至工作區的解決方案
4.  匯入儲存搜尋
5.  儲存匯出搜尋
6.  建立電腦群組
7.  啟用的集合 IIS 記錄從電腦上已安裝 Windows 代理程式
8.  從 Linux 電腦收集邏輯磁碟效能計數器 (%使用 Inodes;免費 Mb。%使用空間。磁碟轉送/sec;磁碟讀取/秒。磁碟寫入/秒）
9.  從 Linux 電腦收集系統事件
10. 從應用程式事件記錄檔，從 Windows 電腦上收集錯誤及警告事件
11. 從 Windows 的電腦收集記憶體可用 Mb 效能計數器
12. 收集自訂的記錄檔 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>設定索引 Azure 診斷記錄狀況分析 

無代理程式監控 Azure 資源的資源需要有 Azure 診斷啟用和設定寫入到儲存的帳戶。 然後您可以設定記錄分析收集記錄從儲存的帳戶。 您需要執行先前設定的資源包括︰

+ 傳統的雲端服務 （web 及工作者角色）
+ 服務布料的轉印圖樣叢集
+ 網路安全性群組
+ 重要保存庫和 
+ 應用程式的閘道器

您也可以使用 PowerShell 來設定記錄分析工作區中，從不同的 Azure 訂閱收集記錄的一個 Azure 訂閱。

下列範例所示的方式︰

1.  列出現有的儲存空間帳戶和記錄分析會索引資料的位置
2.  建立讀取儲存帳戶設定
3.  更新索引資料的新建立的設定，從其他位置
4.  刪除新建立的設定

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>後續步驟

- 如需有關使用 PowerShell 設定的記錄檔分析[檢閱記錄分析 PowerShell cmdlet](http://msdn.microsoft.com/library/mt188224.aspx) 。

