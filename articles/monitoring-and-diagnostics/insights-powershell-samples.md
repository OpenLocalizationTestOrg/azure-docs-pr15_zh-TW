<properties
    pageTitle="Azure 監視器 PowerShell 快速入門範例。 |Microsoft Azure"
    description="您可以使用 PowerShell 來存取 Azure 監視器功能，例如自動調整大小、 通知、 webhooks 及搜尋活動記錄。"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure 監視器 PowerShell 快速入門範例

此範例可協助您存取 Azure 監視器功能的 PowerShell 命令文章會顯示。 Azure 監視器可讓您自動調整大小雲端服務，虛擬機器 Web 應用程式並傳送提醒通知或通話期間，設定的遙測資料的數值為根據的 web Url。

>[AZURE.NOTE] Azure 監視器 2016 年 9 月 25，直到是稱為 「 Azure 深入資訊 」 的新名稱。 不過，命名空間，因此下列命令仍然包含 「 深入資訊]。

## <a name="set-up-powershell"></a>設定 PowerShell
如果您尚未選取，請在您的電腦上執行設定 PowerShell。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell](../powershell-install-configure.md) 。

## <a name="examples-in-this-article"></a>本文中的範例

文件中的範例說明如何使用 Azure 監視器 cmdlet。 您也可以檢閱監視器 PowerShell 的 Azure cmdlet [Cmdlet Azure 監視器 （獲得深入見解）](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx)在整份清單。


## <a name="sign-in-and-use-subscriptions"></a>登入並使用訂閱

首先，請登入您 Azure 的訂閱。

```PowerShell
Login-AzureRmAccount
```

這需要您登入。 一旦您這麼做，您的帳戶，TenantId 與預設訂閱識別碼會顯示。 所有的 Azure 指令程式會在您預設的訂閱的內容。 若要檢視您有權存取的訂閱清單，請使用下列命令。

```PowerShell
Get-AzureRmSubscription
```

若要變更您使用的內容，以不同的訂閱，請使用下列命令。

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>擷取訂閱的稽核記錄
使用`Get-AzureRmLog`指令程式。  以下是一些常見的範例。

從這個時間/日期簡報中取得記錄項目︰

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

取得記錄項目之間的時間/日期範圍︰

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

從特定的資源群組取得記錄項目︰

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

從特定的資源提供者之間的時間/日期範圍中取得記錄項目︰

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得特定的來電者之間的所有記錄項目︰

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

下列命令會擷取稽核記錄的最後一個 1000年的事件︰

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`支援許多其他參數。 請參閱`Get-AzureRmLog`參照，如需詳細資訊。

>[AZURE.NOTE] `Get-AzureRmLog`僅提供 15 天的歷程記錄。 使用**-MaxEvents**參數，可讓您查詢的最後一個 N 事件，超過 15 天。 若要存取事件 15 天之前，使用 REST API 或 SDK （C# 範例使用 SDK）。 如果您不包含**開始時間**，預設值會是**結束時間**減去一小時。 如果您不包含**結束時間**，則預設值為目前的時間。 所有時間都都以 utc 表示。

## <a name="retrieve-alerts-history"></a>擷取通知歷程記錄
若要檢視所有提醒的事件，您可以查詢使用下列範例 Azure 資源管理員記錄。

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

若要檢視其歷程記錄的特定的通知規則，您可以使用`Get-AzureRmAlertHistory`cmdlet，傳入警示規則的資源識別碼。

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzureRmAlertHistory` Cmdlet 支援各種不同的參數。 詳細資訊，請參閱[取得 AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)。


## <a name="retrieve-information-on-alert-rules"></a>擷取通知規則的詳細資訊
資源群組名稱 「 montest 」 執行下列命令的所有動作。

檢視所有屬性的警示規則︰

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

擷取資源群組的所有通知︰

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

擷取目標資源設定的所有通知規則。 例如，通知的所有規則都設定 VM。

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`支援其他參數。 如需詳細資訊，請參閱[取得 AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) 。

## <a name="create-alert-rules"></a>建立提醒的規則
您可以使用`Add-AlertRule`cmdlet 來建立、 更新或停用通知的規則。

您可以建立使用的電子郵件和 webhook 屬性`New-AzureRmAlertRuleEmail`和`New-AzureRmAlertRuleWebhook`分別。 在通知規則 cmdlet，指派為動作給警示規則中的 [**動作**] 屬性。

下一節中包含的範例將示範如何使用各種不同的參數建立提醒的規則。

### <a name="alert-rule-on-a-metric"></a>在 [度量單位，通知規則
下表說明的參數和用來建立通知使用的度量值。


|參數|值|
|---|---|
|名稱|  simpletestdiskwrite|
|此通知規則的位置|   設定適用於美國|
|ResourceGroup| montest|
|TargetResourceId|  /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName 的建立提醒|   \PhysicalDisk (_Total) \Disk 寫入/秒。 請參閱`Get-MetricDefinitions`有關如何擷取的確切的單位名稱下方的指令程式|
|運算子|  GreaterThan|
|臨界值 （計數秒中的此公制）|    1|
|WindowSize （ss 格式）|  00:05:00|
|彙總 （統計的度量，在此情況下使用平均值的計算，）|  平均值|
|自訂電子郵件 （字串陣列）|'foo@example.com','bar@example.com'|
|擁有人、 參與者及讀者傳送電子郵件|    -SendToServiceOwners|

建立電子郵件的動作

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

建立 Webhook 動作

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

在傳統 VM CPU %公制上建立提醒的規則

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

擷取通知的規則

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

新增提醒指令程式也會更新規則如果警示規則已存在指定的屬性。 若要停用通知的規則，包括參數**-DisableRule**。

### <a name="alert-on-audit-log-event"></a>稽核記錄事件的提醒

>[AZURE.NOTE] 此功能仍在預覽中。

在此案例中，您會在我的訂閱，在 [資源群組*abhingrgtest123*順利啟動網站時，傳送電子郵件。

設定電子郵件規則

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

設定 webhook 規則

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

建立規則的事件

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

擷取通知的規則

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

`Add-AlertRule`指令程式可讓您各種其他參數。 詳細資訊，請參閱[新增 AlertRule](https://msdn.microsoft.com/library/mt282468.aspx)。

## <a name="get-a-list-of-available-metrics-for-alerts"></a>可用的標準清單取得通知
您可以使用`Get-AzureRmMetricDefinition`cmdlet 來檢視特定資源的所有指標的清單。

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

下列範例會產生公制名稱的表格和它的單位。

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

完整的可用選項清單`Get-AzureRmMetricDefinition`網址[取得 MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)。


## <a name="create-and-manage-autoscale-settings"></a>建立及管理自動縮放設定
資源，例如在 Web 應用程式，VM、 雲端服務或 VM 縮放比例設定可以讓，只有一個自動縮放設定。
不過，自動調整大小的每個設定可以有多個設定檔。 例如，一個效能為基礎的縮放比例設定檔和排程的第二個為主的設定檔。 每個設定檔可以有多個設定的規則。 如需有關自動調整大小的詳細資訊，請參閱[如何自動調整大小的應用程式](../cloud-services/cloud-services-how-to-scale.md)。

以下是我們將使用的步驟︰

1. 建立規則。
2. 建立設定檔將您建立的規則先前對應至 [設定檔。
3. 可省略︰ 建立自動調整大小的通知設定 webhook 和電子郵件的內容。
4. 對應的設定檔及上一個步驟中所建立的通知上目標資源的名稱與建立自動縮放設定。

下列範例會顯示如何建立自動調整大小的設定為 Windows 作業系統使用的 CPU 使用率公制 VM 小數位數。

首先，建立規則，以向外，執行個體計算增加。

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

接下來，請建立規則以比例增益集與執行個體計算減少。

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

然後，建立規則的設定檔。

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

建立 webhook 屬性。

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

建立自動縮放設定，包括電子郵件和 webhook 您先前建立的 [通知] 屬性。

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

最後，建立 [自動調整大小] 設定，若要新增您先前所建立的設定檔。

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

如需有關管理自動縮放設定的詳細資訊，請參閱[取得 AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)。

## <a name="autoscale-history"></a>自動調整大小的歷程記錄
下列範例會顯示如何檢視最近的自動調整大小和通知的事件。 您可以使用稽核記錄檔搜尋來檢視自動調整大小歷程記錄。

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

您可以使用`Get-AzureRmAutoScaleHistory`cmdlet 來擷取自動調整大小的歷程記錄。

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

如需詳細資訊，請參閱[取得 AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)。

### <a name="view-details-for-an-autoscale-setting"></a>檢視詳細資料以自動縮放設定
您可以使用`Get-Autoscalesetting`cmdlet 來擷取自動縮放設定的詳細資訊。

下列範例會顯示在 [資源群組 'myrg1' 自動調整大小的所有設定的詳細資料。

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

下列範例會顯示在 [資源群組 」 myrg1 」 中的所有自動調整大小設定和特別名為 「 MyScaleVMSSSetting 」 自動調整大小設定的詳細資訊。

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>移除自動調整大小的設定
您可以使用`Remove-Autoscalesetting`cmdlet 來刪除自動調整大小的設定值。

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>管理記錄設定檔的稽核記錄

您可以建立*記錄檔的設定檔*，並從您的稽核記錄的資料匯出到儲存的帳戶，您可以用來設定資料保留資料。 或者，您也可以將資料串流到您的事件集線器。 請注意︰ 此功能目前預覽和您僅可以建立一個記錄設定檔，每個訂閱。 您可以使用下列 cmdlet，與您目前的訂閱，來建立和管理記錄設定檔。 您也可以選擇特定的訂閱。 雖然 PowerShell 預設為最新訂閱，您可以隨時變更該使用`Set-AzureRmContext`。 您可以設定該訂閱中的傳送資料至任何儲存的帳戶或事件中樞的稽核記錄。 為 blob JSON 格式的檔案會寫入資料。

### <a name="get-a-log-profile"></a>取得記錄檔
若要擷取您現有的記錄設定檔，使用`Get-AzureRmLogProfile`指令程式。

### <a name="add-a-log-profile-without-data-retention"></a>新增不資料保留記錄檔

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>移除記錄檔

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>新增資料保留記錄設定檔

您可以指定**-RetentionInDays**屬性的天數，為正數的整數，資料會保留在數字。

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>新增記錄保留與 EventHub 的設定檔
除了路由儲存帳戶的 [資料]，也可以將它串流到事件集線器。 請注意，此預覽版本和儲存空間中設定帳戶是強制事件中心設定為選用步驟。

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>設定診斷記錄
許多 Azure 服務會提供其他記錄檔和遙測，包括 Azure 網路安全性群組、 軟體負載平衡器、 金鑰保存庫、 Azure 搜尋服務，然後邏輯應用程式，而且可以設定儲存在您 Azure 儲存體帳戶中的資料。 只在資源層級執行的作業，然後儲存帳戶應該出現在相同地區做為目標資源經診斷程式設定。

### <a name="get-diagnostic-setting"></a>取得診斷設定

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

停用診斷設定

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

啟用診斷設定不保留

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

啟用與保留診斷設定

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

啟用診斷設定與特定的記錄檔類別的保留

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
