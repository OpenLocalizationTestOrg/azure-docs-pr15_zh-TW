<properties
    pageTitle="垂直調整 Azure 虛擬機器縮放比例設定 |Microsoft Azure"
    description="垂直不按比例縮放虛擬機器來監控通知 Azure 自動化回應的方式"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>垂直的自動調整大小的虛擬機器比例設定

本文將說明如何垂直調整 Azure[虛擬機器縮放比例設定](https://azure.microsoft.com/services/virtual-machine-scale-sets/)包含或不含將。 垂直的縮放比例 Vm 沒有小數位數集，請參閱[垂直調整 Azure 自動化 Azure 虛擬機器](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md)。

垂直縮放比例，也就_放大_及_縮小_，表示增加或減少工作量來回應的虛擬機器 (VM) 大小。 比較此與[水平縮放比例](./virtual-machine-scale-sets-autoscale-overview.md)，同時也稱為_查看_和_中_，並根據工作負載受到變更 Vm 數目。

將代表移除現有的 VM 並將其取代為新。 當您增加或減少 Vm 集中 VM 小數位數的大小時，在某些情況下您想要調整大小現有 Vm 保留您的資料，同時也需要部署新 Vm 新大小的其他情況。 本文說明這兩種情況。

垂直縮放比例會有幫助的時機︰

- 內建在虛擬機器服務是下利用 （例如在週末）。 減少虛擬記憶體大小降低每月的成本。
- 增加應付較大的要求，但不建立其他 Vm 虛擬記憶體大小。

您可以從您 VM 縮放比例設定為觸發根據公制根據的通知將設定垂直縮放比例。 提醒會在啟動時，就會引發 webhook 該引動程序，您的比例的 runbook 設定向上或向下。 您可以設定垂直縮放比例，遵循下列步驟︰

1. 建立與執行方式功能的 Azure 自動化帳戶。
2. 匯入 Azure 自動化垂直刻度 runbooks VM 縮放比例設定您的訂閱。
3. 將您 runbook webhook。
4. 新增提醒您 VM 小數位數，設定使用 webhook 通知。

> [AZURE.NOTE] 垂直自動縮放只會在 VM 大小的特定範圍內。 比較的每個大小的規格之前先決定要不按比例縮放的方式 （較高的數字不一定表示變大虛擬記憶體大小）。 您可以選擇下列成對大小之間不按比例縮放︰

>| 縮放比例組 VM 大小 |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>建立 Azure 自動化帳戶的執行方式功能

首先您需要執行的建立裝載用來縮放 VM 縮放比例設定執行個體 runbooks Azure 自動化帳戶。 最近[Azure 自動化](https://azure.microsoft.com/services/automation/)推出 」 執行為帳戶 」 功能讓設定服務主體設定自動執行 runbooks 使用者的代表很容易。 您可以閱讀更多關於此，請參閱下列︰

* [Azure 執行為帳戶驗證方法 Runbooks](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Azure 自動化垂直刻度 runbooks 匯入您的訂閱

需要垂直調整您 VM 縮放比例設定 runbooks 已發佈 Azure 自動化 Runbook 圖庫中。 匯入到您的訂閱進行請遵循本文中的步驟︰

* [Azure 自動化 Runbook 和模組組件庫](../automation/automation-runbook-gallery.md)

從 Runbooks 功能表中選擇 [瀏覽] 圖庫] 選項︰

![若要匯入 Runbooks][runbooks]

需要匯入 runbooks 會顯示。 選取 [根據您是否要垂直調整包含或不含將 runbook:

![Runbooks 圖庫][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>新增您 runbook webhook

一旦您已匯入您將需要 runbooks 加入 webhook runbook，以便從 VM 縮放比例設定提醒可觸發。 本文說明您 Runbook 建立 webhook 的詳細資料︰

* [Azure 自動化 webhooks](../automation/automation-webhooks.md)

> [AZURE.NOTE] 請確定您複製 webhook URI 之前關閉 webhook] 對話方塊，您會需要此的下一節。

## <a name="add-an-alert-to-your-vm-scale-set"></a>新增提醒您 VM 縮放設定

以下是 PowerShell 指令碼，顯示如何新增提醒 VM 縮放比例設定。 請參閱下列文章，取得在啟動提醒公制的名稱︰ [Azure 監視器自動縮放常見指標](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)。

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] 建議您以避免觸發垂直縮放比例，以及任何相關聯的服務中斷，經常設定提醒合理的時間間隔。 請考慮至少 20-30 分鐘以上的視窗。 請考慮水平若要避免中斷的縮放比例。

如需有關如何建立提醒，請參閱下列文章︰

* [Azure 監視器 PowerShell 快速入門範例](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Azure 監視器跨平台 CLI 快速入門範例](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>摘要

本文將示範簡單的垂直縮放比例範例。 使用這些建置組塊-自動化帳戶、 runbooks、 webhooks，通知-您可以連線各式各樣的事件以自訂的動作。

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
