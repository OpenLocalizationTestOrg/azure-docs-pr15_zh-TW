<properties
    pageTitle="自動調整及虛擬機器不按比例縮放設定 |Microsoft Azure"
    description="瞭解如何使用自動縮放比例設定中的 [虛擬機器的診斷與自動調整大小的資源。"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>自動調整及虛擬機器不按比例縮放設定

自動縮放比例縮放比例集中的虛擬機器是建立或刪除的電腦，在設定，視需要以符合效能需求。 當工作的音量規模擴大時，應用程式，可能需要額外的資源，讓它有效率地執行工作。

自動調整大小是可協助減輕管理負擔自動化程序。 降低成本，您不需要持續監控系統效能或決定如何管理資源。 縮放比例是彈性的程序。 載入的增加，可以新增更多資源，但與要求會減少資源可以最小化成本與維護效能層級。

設定自動縮放比例設定使用 Azure 資源管理員範本、 PowerShell 的 Azure、 Azure CLI 或 Azure 入口網站的比例。

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>設定使用資源管理員範本縮放比例

而非部署及管理應用程式的每個資源分別使用部署單一、 協同作業中的所有資源的範本。 在範本中，定義應用程式資源，及部署參數中所指定的不同環境。 範本包含 JSON 和可用來建構您的部署中的值的運算式。 若要深入瞭解，查看[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

在範本，您可以指定容量項目︰

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

容量識別虛擬機器中設定的號碼。 您可以手動變更容量部署範本具有不同的值。 如果您部署若只要變更變更容量範本，您可以包含只更新容量的 SKU 項目。

自動變更您使用 autoscaleSettings 資源和診斷副檔名設定的比例的容量。

### <a name="configure-the-azure-diagnostics-extension"></a>設定 Azure 診斷副檔名

自動縮放比例才可在縮放比例設定每個虛擬機器上成功指標集合時。 Azure 診斷副檔名提供符合自動調整大小資源的標準集合所需的監控和診斷功能。 您可以安裝副檔名為資源管理員範本的一部分。

此範例顯示變數所使用的範本，設定診斷副檔名為︰

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

部署範本時，會提供參數。 在此範例中，會提供儲存資料的儲存體帳戶名稱和收集資料的縮放設定的名稱。 也 Windows Server，在本例會收集只執行緒計數效能計數器。 在 Windows 或 Linux 的所有可用的效能計數器可以用來收集診斷資訊，可以包含在延伸設定。

此範例會顯示在範本中定義的副檔名為︰

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

診斷副檔名為執行時，資料會收集位於您指定的儲存空間帳戶中的資料表中。 在 WADPerformanceCounters 資料表中，您可以找到收集的資料︰

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>設定 autoScaleSettings 資源

AutoscaleSettings 資源使用副檔名為診斷資訊，以決定是否要增加或減少的虛擬機器中的縮放比例設定數。

此範例會顯示在範本中的資源設定︰

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

在上述範例中，會建立兩個規則定義自動縮放比例的動作。 第一個規則定義擴充動作，第二個規則定義的小數位數的動作。 在規則] 會提供這些數值︰

- **metricName** -這個值是您在診斷分機 wadperfcounter 變數中定義的效能計數器相同。 在上述範例中，用於執行緒計數計數器。  
- **metricResourceUri**此值是資源識別碼虛擬機器縮放比例設定。 此識別碼包含資源群組的名稱、 資源提供者的名稱和縮放比例設定為 [不按比例縮放的名稱。
- **timeGrain** – 此值會收集指標的方式。 在上述範例中，資料會收集上一分鐘的時間間隔。 此值 timeWindow 搭配使用。
- **統計資料**-此值會決定計量組合以容納自動縮放比例動作的方式。 可能的值︰ 平均值、 最小值] 的最大值。
- **timeWindow** – 此值會的收集執行個體資料時的範圍。 它必須介於 5 到 12 小時。
- **timeAggregation** – 此值會決定如何是否會收集的資料合併一段時間。 預設值是平均值。 可能的值︰ 平均值、 最小值、 最大值，最後一個、 總計、 計數。
- **運算子**– 這個值是用來比較公制資料和臨界值的運算子。 可能的值︰ NotEquals GreaterThan、 GreaterThanOrEqual、 LessThan、 LessThanOrEqual 等於。
- **閥值**– 這個值是觸發比例巨集指令的值。 請務必提供擴充動作的臨界值和小數位數的巨集指令的臨界值足夠差異。 如果您設定的相同的值時，系統不常變更，以防止實作縮放比例的動作。 例如，設定為 600 執行緒在上述範例中無法運作。
- **方向**– 此值會決定達成臨界值時應該採取的動作。 可能的值會增加或減少。
- **輸入**– 這個值是動作應該發生，必須設定為 [ChangeCount 類型。
- **值**– 這個值是新增到清單或從中縮放比例設定的虛擬機器數目。 此值必須大於或等於 1。
- **cooldown** – 這個值是時間的下一個動作前，自上次的縮放比例動作等待量。 此值必須是一分鐘之間一週。

視您使用的效能計數器，而定，某些範本設定中的項目會使用不同。 在上述範例中，效能計數器執行緒計數、 臨界值是 650 擴充的動作，而臨界值會 550 小數位數的動作。 如果您使用的計數器 %處理器時間例如，臨界值的決定縮放比例的動作的 CPU 使用率百分比設定。

觸發縮放比例的巨集指令的虛擬機器上建立載入時，設定的容量會增加根據範本中的值。 例如中縮放比例, 設定位置容量設為 3 和縮放比例動作值設為 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

載入建立時導致到上方了 650 的臨界值的平均執行緒計算︰

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

擴充動作觸發導致集的容量，以增加 1:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

然後虛擬機器會新增至縮放比例設定︰

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

一段 cooldown 五分鐘，如果在電腦上的對話的平均數目保持超過 600、 另一部電腦會新增至設定。 如果平均執行緒計數保持 550] 下方的縮放比例設定容量減少一個，然後電腦已從設定]。

## <a name="set-up-scaling-using-azure-powershell"></a>設定使用 PowerShell 的 Azure 縮放比例

若要查看範例使用 PowerShell 來自動縮放設定，請查看[監視器 PowerShell 的 Azure 快速開始範例](../monitoring-and-diagnostics/insights-powershell-samples.md)。

## <a name="set-up-scaling-using-azure-cli"></a>設定使用 Azure CLI 縮放比例

若要查看使用 Azure CLI 設定自動縮放的範例，請查看[Azure 監視器跨平台 CLI 快速啟動範例](../monitoring-and-diagnostics/insights-cli-samples.md)。

## <a name="set-up-scaling-using-the-azure-portal"></a>設定縮放使用 Azure 入口網站

若要設定自動縮放使用 Azure 入口網站的範例，請查看[建立虛擬機器縮放比例設定使用 Azure 入口網站](virtual-machine-scale-sets-portal-create.md)。

## <a name="investigate-scaling-actions"></a>調查縮放比例的動作

- [Azure 入口網站]()-目前，就可以使用入口網站的資訊的一段有限。
- [Azure 資源檔案總管]()這項工具最適合探索您的縮放設定的目前狀態。 請遵循此路徑，您應該會看到您所建立的縮放比例設定的執行個體檢視︰ 訂閱 > {您的訂閱} > resourceGroups > {資源群組} > 提供者 > Microsoft.Compute > virtualMachineScaleSets > {縮放比例設定} > virtualMachines
- Azure PowerShell-使用此命令，以取得一些資訊︰

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- 連線到 jumpbox 虛擬機器，就像您想要的任何其他電腦，然後您可以從遠端存取縮放比例設定為監視個別的程序中的虛擬機器。

## <a name="next-steps"></a>後續步驟

- 看看如何建立比例設定自動縮放設定的範例，請參閱 [[自動調整機器集中虛擬機器小數位數](virtual-machine-scale-sets-windows-autoscale.md)。
- 尋找監控功能[監視器 PowerShell 的 Azure 快速啟動範例](../monitoring-and-diagnostics/insights-powershell-samples.md)Azure 監視器的範例
- 深入了解中[使用自動調整大小動作來傳送電子郵件和 webhook Azure 監視器的提醒通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)的通知功能。
- 進一步瞭解如何[使用稽核記錄以傳送電子郵件和 webhook Azure 監視器的提醒通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- 深入了解[進階自動調整大小的案例](./virtual-machine-scale-sets-advanced-autoscale.md)。
