<properties
    pageTitle="使用 PowerShell 來建立提醒 Azure 服務 |Microsoft Azure"
    description="使用 PowerShell 來建立 Azure 通知，可以觸發通知或自動化符合您指定的條件時。"
    authors="rboucher"
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>使用 PowerShell 來建立 Azure 服務的提醒

> [AZURE.SELECTOR]
- [入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## <a name="overview"></a>概觀

本文將說明如何設定使用 PowerShell 的 Azure 提醒。  

您會收到通知監控指標，或 Azure 服務上的事件。

- **度量值**-的度量單位，指定值交叉您指派以任意方向臨界值時的通知觸發程序。 就是它觸發兩時條件符合時第一次，然後之後時的條件不符合。    
- **活動記錄事件**的*每個*事件，或，只在特定數目的事件發生時，才可以觸發程序通知。

您可以設定提醒觸發時，請執行下列動作︰

- 傳送電子郵件通知給服務管理員和共同管理員
- 傳送電子郵件至您指定的其他電子郵件。
- 通話 webhook
- 開始執行 Azure runbook （僅從 Azure] 入口網站）

您可以設定，並取得通知使用的規則的相關資訊

- [Azure 入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
- [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


如需詳細資訊，您可以隨時輸入```get-help```，然後要說明的 PowerShell 命令。

## <a name="create-alert-rules-in-powershell"></a>在 PowerShell 中建立提醒的規則

1. 登入 Azure。   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. 取得清單的訂閱您有提供。 請確認您使用的訂閱。 如果不是，將它設定為使用的輸出正確無誤`Get-AzureRmSubscription`。

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  若要現有的規則，在 [資源群組] 清單中，使用下列命令︰

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. 若要建立規則，您需要先有幾項重要的資訊。 
    - 您想要設定的提醒和資源**資源識別碼**
    - **公制定義**該資源可用的

    若要取得資源識別碼的其中一個方法是使用 Azure 入口網站。 已經建立假設資源，在入口網站中加以選取。 然後在下一個刀中，選取 [*內容*在 [*設定*] 區段底下。 資源識別碼是下一個刀的欄位。 另一個方法是使用[Azure 資源檔案總管](https://resources.azure.com/)]。

    範例資源識別碼 web 應用程式

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    您可以使用`Get-AzureRmMetricDefinition`若要檢視特定資源的所有公制定義的清單。

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    下列範例會產生公制名稱的資料表及該公制的單位。

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    取得 AzureRmMetricDefinition 可用選項的完整清單，請執行取得 MetricDefinitions。


5. 下列範例會設定網站資源的警示。 通知的觸發程序時持續收到任何流量 5 分鐘與一次時收到任何流量 5 分鐘。

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. 若要建立 webhook 或提醒觸發時，傳送電子郵件，請先建立電子郵件及/或 webhooks。 然後立即建立規則之後-動作標籤，如下列範例所示。 您無法建立關聯 webhook 或以電子郵件建立規則，透過 PowerShell。


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. 若要建立活動記錄中的特定條件的觸發程序的提醒，請使用下列格式的命令

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName 會對應到一種活動記錄中的事件。 範例包括*Microsoft.Compute/virtualMachines/delete*和*microsoft.insights/diagnosticSettings/write*。

    您可以使用 PowerShell 命令[取得 AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx)以取得可能 operationNames 的清單。 或者，您可以使用 [Azure 入口網站查詢活動記錄，並尋找特定過您想要建立提醒的作業。 顯示圖形的記錄檔的檢視中的好記的名稱作業。 查看項目的 JSON，並拉出 OperationName 值。   

8. 請確認您的通知有已正確建立個別的規則。

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. 刪除您的通知。 這些命令刪除本文先前所建立的規則。

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>後續步驟

* 您可以[概略 Azure 監控](monitoring-overview.md)包括類型的資訊收集並監控。
* 深入瞭解[設定 webhooks 中通知](insights-webhooks-alerts.md)。
* 進一步瞭解[Azure 自動化 Runbooks](..\automation\automation-starting-a-runbook.md)。
* 前往[概觀收集診斷記錄](monitoring-overview-of-diagnostic-logs.md)您的服務收集詳細的頻率高的度量。
* 取得[指標集合的概觀](insights-how-to-customize-monitoring.md)，請確定您的服務是可用和回應。
