<properties
    pageTitle="Azure 診斷記錄概觀 |Microsoft Azure"
    description="了解 Azure 診斷記錄是什麼，以及如何使用這些瞭解 Azure 資源內所發生的事件。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="johnkem; magoedte"/>

# <a name="overview-of-azure-diagnostic-logs"></a>Azure 診斷記錄的概觀
**Azure 診斷記錄**是資源所發出的記錄，提供豐富的經常性該資源的作業相關的資料。 這些記錄的內容會因資源類型 （例如，Windows 事件系統記錄是一個類別的 [診斷記錄 Vm 和 blob、 表格且佇列中記錄儲存帳戶診斷記錄的類別） 及異[活動記錄 （先前稱為稽核記錄檔或作業的記錄）](monitoring-overview-activity-logs.md)，提供深入了解您的訂閱中的資源執行的作業。 並非所有資源都支援新此處所述的診斷記錄類型。 下面的支援服務] 清單會顯示哪些資源類型支援新診斷記錄。

![邏輯的 [診斷記錄的位置](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>您可以使用 [診斷記錄做什麼
以下是一些您可以執行診斷記錄與項目︰

- 將其儲存至稽核或手動檢查的**儲存空間的帳戶**。 您可以指定保留時間 （以天數），使用**診斷設定**。
- 協力廠商服務或自訂分析解決方案，例如中 ingestion，[串流**事件集線器**即可](monitoring-stream-diagnostic-logs-to-event-hubs.md)。
- 分析[OMS 記錄狀況分析](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>診斷設定
使用 [診斷設定設定非計算資源的診斷記錄。 資源控制項**診斷設定**︰

- 診斷記錄的傳送位置 （儲存帳戶、 事件集線器，及/或 OMS 記錄分析）。
- 傳送記錄檔分類。
- 每個記錄類別多久保留儲存帳戶 – 保留的零天數表示記錄會保持不限次數。 否則，此值可以範圍是介於 1 到 2147483647。 如果保留原則設定，但已停用 [儲存記錄檔儲存帳戶 (例如如果僅選取事件集線器或 OMS 選項)，保留原則有任何效果。

透過 Azure 入口網站中的資源診斷刀、 透過 PowerShell 的 Azure 和 CLI] 命令，或透過[Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)，輕鬆設定這些設定。

> [AZURE.WARNING] 診斷記錄及計算資源 （例如，Vm 或服務布料的轉印圖樣） 的指標使用[另一個設定] 及 [選取範圍的輸出機制](../azure-diagnostics.md)。

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>如何啟用診斷記錄的集合
建立資源，或透過資源的刀入口網站中建立資源後，您可以啟用診斷記錄的集合。 您也可以在任何時候使用 PowerShell 的 Azure 或 CLI 命令，或使用 Azure 監視器 REST API 啟用診斷記錄。

> [AZURE.TIP] 直接對每個資源，可能不適用這些指示。 請在此瞭解特殊的步驟，可能會套用至特定的資源類型] 頁面底部的結構描述連結。

[本文將示範如何使用資源範本建立資源時，啟用診斷設定](./monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>入口網站中啟用診斷記錄
讓 Windows 或 Linux Azure 診斷副檔名以建立計算資源類型時，您可以在 Azure 入口網站中啟用診斷記錄︰

1.  移至 [**新增]** ，然後選擇您感興趣的資源。
2.  基本的設定，選取 [**設定**刀，底下**監控**中的 [大小]，選取 [**啟用]** ，然後選擇您想要儲存診斷記錄的位置以儲存帳戶。 當您儲存帳戶傳送診斷時，您會儲存與交易的一般資料工資率。

    ![在 [資源建立啟用診斷記錄](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.  按一下**[確定]** ，並建立資源。

非計算資源的您可以 Azure 入口網站中啟用診斷記錄之後已建立資源執行下列動作︰

1.  移至 [資源刀，然後開啟 [**診斷**刀。
2.  **按一下**，然後選擇 [儲存帳戶及/或事件中心。

    ![資源建立後啟用診斷記錄](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.  在 [**記錄**] 中，選取您想要收集或串流哪些**記錄的類別**。
4.  按一下 [**儲存**]。

### <a name="enable-diagnostic-logs-via-powershell"></a>啟用診斷記錄透過 PowerShell
若要啟用透過 Azure PowerShell Cmdlet 診斷記錄，請使用下列命令。

若要啟用診斷記錄在儲存帳戶中的儲存空間，請使用這個命令︰

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

儲存帳戶 ID 是您要傳送記錄檔儲存帳戶的資源識別碼。

若要啟用串流的診斷記錄事件集線器，請使用這個命令︰

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

服務匯流排規則識別碼是使用此格式字串︰ `{service bus resource ID}/authorizationrules/{key name}`。

若要啟用診斷記錄傳送記錄檔分析工作區，請使用這個命令︰

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [AZURE.NOTE] WorkspaceId 參數不適用於年 10 月發行。 將會變成年 11 月版本中可用。

您可以取得您 Azure 入口網站中的記錄檔分析工作區識別碼。

您可以結合下列參數，若要啟用多個輸出選項。

### <a name="enable-diagnostic-logs-via-cli"></a>啟用透過 CLI 診斷記錄
若要啟用透過 Azure CLI 診斷記錄，請使用下列命令︰

若要啟用診斷記錄在儲存帳戶中的儲存空間，請使用這個命令︰

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

儲存帳戶 ID 是您要傳送記錄檔儲存帳戶的資源識別碼。

若要啟用串流的診斷記錄事件集線器，請使用這個命令︰

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

服務匯流排規則識別碼是使用此格式字串︰ `{service bus resource ID}/authorizationrules/{key name}`。

若要啟用診斷記錄傳送記錄檔分析工作區，請使用這個命令︰

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [AZURE.NOTE] WorkspaceId 參數不適用於年 10 月發行。 將會變成年 11 月版本中可用。

您可以取得您 Azure 入口網站中的記錄檔分析工作區識別碼。

您可以結合下列參數，若要啟用多個輸出選項。

### <a name="enable-diagnostic-logs-via-rest-api"></a>啟用診斷記錄透過 REST API
若要變更使用 Azure 監視器 REST API 診斷設定，請參閱[這份文件](https://msdn.microsoft.com/library/azure/dn931931.aspx)。

## <a name="manage-diagnostic-settings-in-the-portal"></a>管理入口網站中的 [診斷設定

為了確保的所有資源已正確設定診斷設定，您可以瀏覽至**監控**刀，在入口網站，並開啟 [**診斷記錄**刀。

![診斷記錄刀在入口網站](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

您可能必須按一下 [更多服務] 來尋找監控刀。

在此刀，您可以檢視及篩選支援診斷記錄，看看是否有啟用診斷和哪些儲存帳戶、 事件] 中心內，及/或記錄分析工作區來傳送這些記錄的所有資源。

![診斷記錄刀結果，在入口網站](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

按一下資源上會顯示所有記錄已經儲存在儲存帳戶，讓您開啟或修改診斷設定的選項。 按一下 [下載] 圖示，以下載特定期間的記錄。

![診斷記錄刀某個資源](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [AZURE.NOTE] 診斷記錄只會出現在此檢視，並可供下載如果您已設定診斷將檔案儲存到儲存的帳戶。

按一下 [**診斷**設定] 連結會顯示診斷設定防禦，您可以在其中啟用、 停用或修改您所選取資源的診斷設定。

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>支援的服務和診斷記錄的結構描述
診斷記錄的結構描述異資源和記錄的類別。 以下是支援的服務 」 和 「 其結構描述。

| 服務                       | 結構描述與文件                                                                                                   |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
|    軟體負載平衡器     |    [Azure 負載平衡器 （預覽版本） 的記錄狀況分析](../load-balancer/load-balancer-monitor-log.md)             |
|    網路安全性群組    |    [網路安全性群組 (NSGs) 記錄狀況分析](../virtual-network/virtual-network-nsg-manage-log.md)     |
|    應用程式的閘道器       |    [應用程式的閘道器的診斷記錄](../application-gateway/application-gateway-diagnostics.md)     |
|    索引鍵保存庫                  |    [Azure 鍵保存庫記錄](../key-vault/key-vault-logging.md)                                                 |
|    Azure 搜尋               |    [啟用與使用搜尋流量分析](../search/search-traffic-analytics.md)                         |
|    資料湖存放區            |    [存取 Azure 資料湖市集診斷記錄](../data-lake-store/data-lake-store-diagnostic-logs.md) |
|    資料湖狀況分析        |    [Azure 資料湖分析存取診斷記錄](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
|    邏輯應用程式                 |    沒有可用的結構描述。                                                                                         |
|    Azure 批次                |    [Azure 批次診斷記錄](../batch/batch-diagnostics.md)                                              |
|    Azure 自動化           |    [Azure 自動化記錄狀況分析](../automation/automation-manage-send-joblogs-log-analytics.md)          |
|    事件中心                  |    沒有可用的結構描述。                                                                                         |
|    服務匯流排                |    沒有可用的結構描述。                                                                                         |
|    資料流狀況分析           |    沒有可用的結構描述。                                                                                         |

## <a name="supported-log-categories-per-resource-type"></a>支援的每個資源類型的記錄檔分類

|資源類型|類別|類別的顯示名稱|
|---|---|---|
|Microsoft.Automation/automationAccounts|JobLogs|工作記錄檔|
|Microsoft.Automation/automationAccounts|JobStreams|工作資料流時|
|Microsoft.Batch/batchAccounts|ServiceLog|服務記錄檔|
|Microsoft.DataLakeAnalytics/accounts|稽核|稽核記錄|
|Microsoft.DataLakeAnalytics/accounts|要求|要求記錄檔|
|Microsoft.DataLakeStore/accounts|稽核|稽核記錄|
|Microsoft.DataLakeStore/accounts|要求|要求記錄檔|
|Microsoft.EventHub/namespaces|ArchiveLogs|封存記錄|
|Microsoft.EventHub/namespaces|OperationalLogs|作業的記錄|
|Microsoft.KeyVault/vaults|AuditEvent|稽核記錄|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流程執行階段診斷事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|網路安全性群組事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|網路安全性群組規則計數器|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|網路安全性群組規則流程事件|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|負載平衡器提醒的事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|負載平衡器探查健康狀態|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|應用程式閘道器存取記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|應用程式閘道效能記錄檔|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|應用程式閘道器防火牆記錄|
|Microsoft.Search/searchServices|OperationLogs|作業的記錄|
|Microsoft.ServerManagement/nodes|RequestLogs|要求記錄檔|
|Microsoft.ServiceBus/namespaces|OperationalLogs|作業的記錄|
|Microsoft.StreamAnalytics/streamingjobs|執行|執行|
|Microsoft.StreamAnalytics/streamingjobs|撰寫|撰寫|

## <a name="next-steps"></a>後續步驟
- [串流到**事件**的診斷記錄](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [變更使用 Azure 監視器 REST API 診斷設定](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [分析 OMS 記錄分析的記錄](../log-analytics/log-analytics-azure-storage-json.md)
