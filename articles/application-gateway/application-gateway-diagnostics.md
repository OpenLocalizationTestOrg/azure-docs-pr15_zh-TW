<properties 
   pageTitle="監視應用程式的閘道器存取和效能的記錄檔和指標 |Microsoft Azure"
   description="瞭解如何啟用並管理應用程式的閘道器存取和效能的記錄"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amitsriva" />

# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>診斷記錄及應用程式的閘道器的指標

Azure 提供這項功能可以監視與記錄指標的資源

[**記錄**](#enable-logging-with-powershell)-記錄允許效能、 存取和其他記錄檔儲存或取用監控的資源。

[**指標**](#metrics)應用程式閘道器目前有一個公制。 此公制測量的位元組秒的應用程式閘道器。

您可以使用 Azure 中的不同類型的記錄，可用於管理及疑難排解應用程式的閘道器。 部分這些記錄可以透過入口網站，來存取所有的記錄可以從 Azure blob 儲存體中擷取及和中不同的工具，例如[記錄分析](../log-analytics/log-analytics-azure-networking-analytics.md)、 Excel 和中檢視。 您可以瞭解更多不同類型的記錄從下列清單︰

- **稽核記錄︰**若要檢視所有作業送至 Azure 訂閱及他們的狀態，您可以使用[Azure 稽核記錄](../monitoring-and-diagnostics/insights-debugging-with-events.md)（先前稱為作業的記錄）。 稽核記錄根據預設，啟用，並可 Azure 預覽入口網站中檢視。
- **存取記錄︰**您可以使用此記錄，來檢視應用程式的閘道器存取模式和分析重要資訊包括來電者 IP，URL 要求，回應延遲和縮小傳回程式碼，位元組。 Access 記錄會收集每 300 秒。 此記錄包含應用程式閘道執行個體每一筆記錄。 應用程式閘道執行個體可識別 「 執行個體識別碼] 屬性。
- **效能記錄︰**若要檢視應用程式閘道執行個體的執行方式，您可以使用這個記錄檔。 此記錄擷取每個執行個體的基準包括 served 的總要求位元組處理量的效能資訊、 總要求提供，失敗的要求計數，健全和不健全的後端執行個體計數。 效能記錄會收集每隔 60 秒。
- **防火牆記錄︰**若要檢視記錄透過偵測或防止模式應用程式閘道設定 web 應用程式防火牆的要求，您可以使用這個記錄檔。

>[AZURE.WARNING] 記錄只適用於部署資源管理員部署模型中的資源。 您無法使用傳統的部署模型中的資源記錄。 進一步了解的兩種模式，參考[瞭解資源管理員部署及傳統部署](../resource-manager-deployment-model.md)文章。

## <a name="enable-logging-with-powershell"></a>啟用記錄使用 PowerShell

每個資源管理員資源自動啟用稽核記錄。 您必須啟用存取和效能開始收集資料可透過這些記錄的記錄。 若要啟用記錄功能，請參閱下列步驟︰ 

1. 請注意您儲存的帳戶的資源識別碼，儲存記錄檔資料。 這是表單的︰ /subscriptions/\<subscriptionId\>/resourceGroups/\<資源群組名稱\>/providers/Microsoft.Storage/storageAccounts/\<儲存體帳戶名稱\>。 可在您的訂閱中的任何儲存帳戶。 若要尋找這項資訊，您可以使用 [預覽] 入口網站。

    ![預覽入口網站的應用程式閘道器診斷](./media/application-gateway-diagnostics/diagnostics1.png)

2. 筆記記錄應用程式閘道器的資源識別碼會啟用。 這是表單的︰ /subscriptions/\<subscriptionId\>/resourceGroups/\<資源群組名稱\>/providers/Microsoft.Network/applicationGateways/\<應用程式的閘道器名稱\>。 若要尋找這項資訊，您可以使用 [預覽] 入口網站。

    ![預覽入口網站的應用程式閘道器診斷](./media/application-gateway-diagnostics/diagnostics2.png)

3. 啟用診斷記錄使用下列 powershell 指令程式︰

        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

>[AZURE.INFORMATION] 稽核記錄不需要個別的儲存空間帳戶。 使用儲存空間，存取和效能記錄必須支付服務費用。

## <a name="enable-logging-with-azure-portal"></a>啟用記錄 Azure 入口網站

### <a name="step-1"></a>步驟 1

瀏覽至您在 Azure 入口網站的資源。 按一下 [**診斷記錄**]。 如果這是第一次設定診斷刀看起來像下圖中︰

應用程式的閘道器，3 的記錄可供使用。

- Access 記錄
- 效能記錄
- 防火牆記錄

按一下 [**開啟 [診斷**開始收集資料]。

![診斷設定刀][1]

### <a name="step-2"></a>步驟 2

在**診斷程式設定**刀，如何設定診斷記錄是設定。 在此範例中，記錄分析用來儲存記錄。 按一下 [**記錄分析**，設定您的工作區底下的 [**設定**]。 事件集線器和儲存帳戶可用來儲存診斷記錄檔。

![診斷刀][2]

### <a name="step-3"></a>步驟 3

選擇現有的 OMS 工作區或建立新的項目。 此範例會使用現有的項目。

![oms 工作區][3]

### <a name="step-4"></a>步驟 4

完成時，請確認設定，然後按一下 [**儲存**] 儲存設定。

![確認選取項目][4]

## <a name="audit-log"></a>稽核記錄

這個記錄 （先前稱為 「 作業記錄 」） 會產生 Azure 預設。  Azure 的事件記錄存放區中，記錄會保留 90 天。 進一步瞭解這些記錄以 [閱讀[檢視事件及稽核記錄檔](../monitoring-and-diagnostics/insights-debugging-with-events.md)的文件。

## <a name="access-log"></a>Access 記錄

如果您已啟用，在此記錄只會產生每個應用程式的閘道器為上述步驟所述的基礎。 資料會儲存於您啟用記錄時，您指定的儲存空間帳戶。 每個 access 應用程式的閘道器已有登入 JSON 格式，如下列範例所示︰

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>效能記錄

如果您已啟用，在此記錄只會產生每個應用程式的閘道器為上述步驟所述的基礎。 資料會儲存於您啟用記錄時，您指定的儲存空間帳戶。 下列資料記錄中︰

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>防火牆記錄

如果您已啟用，在此記錄只會產生每個應用程式為上述步驟所述的閘道器基礎。 此記錄也會需要的 web 應用程式防火牆設定應用程式閘道。 資料會儲存於您啟用記錄時，您指定的儲存空間帳戶。 下列資料記錄中︰

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>檢視及分析稽核記錄

您可以檢視及分析稽核記錄資料使用下列方法之一︰

- **Azure 工具︰**從透過 PowerShell 的 Azure、 Azure 命令列介面 (CLI)、 Azure REST API 或 Azure 預覽入口網站的稽核記錄檔擷取資訊。  每一種方法的逐步指示的詳細資訊，請參閱[稽核作業與資源管理員](../resource-group-audit.md)。
- **Power BI:**如果您還沒有[Power BI](https://powerbi.microsoft.com/pricing)帳戶，您也可以嘗試的可用。 使用[Azure 稽核記錄內容的 Power BI 套件](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)您可以分析您的資料使用，您可以使用為預先設定的儀表板-，或自訂。

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>檢視及分析 access、 效能及防火牆記錄

Azure[記錄分析](../log-analytics/log-analytics-azure-networking-analytics.md)可以收集計數器及事件記錄檔 Blob 儲存體帳戶，並包含視覺效果和分析您的記錄功能強大的搜尋功能。

您也可以連線至您儲存的帳戶，並擷取存取和效能記錄的 JSON 記錄項目。 一旦您下載 JSON 檔案時，您可以將它們轉換成 CSV 和在 Excel、 中或任何其他資料視覺效果工具的檢視。

>[AZURE.TIP] 如果您熟悉 Visual Studio 與變更常數及 C# 變數值的基本概念，您可以使用[記錄轉換程式工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)Github。

## <a name="metrics"></a>指標

指標是讓您可以在入口網站檢視效能計數器特定 Azure 資源的功能。 應用程式閘道一個公制會出現在撰寫本文的時間。 此計量處理量，而能夠看到在入口網站。 瀏覽至 [應用程式閘道器，然後按一下 [**度量**。  若要檢視之值的 [**可用的指標**] 區段中選取處理量。 在下圖中，您可以看到範例，可用來顯示不同的時間範圍中的資料篩選。

若要查看目前的支援指標的清單，請造訪[Azure 監視器支援指標](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![公制的檢視][5]

## <a name="alert-rules"></a>通知的規則

通知規則可以開始根據的資源上的指標。 代表應用程式的閘道器，提醒可以呼叫 webhook 或電子郵件系統管理員，如果應用程式閘道器的上方、 下方或臨界值的時間在指定期間內。

下列範例會逐步引導您完成建立提醒的規則，會將電子郵件傳送給系統管理員之後不良處理量閥值。

### <a name="step-1"></a>步驟 1

按一下 [啟動 [**新增公制的提醒**]。 此刀也可以從指標刀連線到。

![通知規則刀][6]

### <a name="step-2"></a>步驟 2

在 [**新增規則**刀中，輸入名稱，條件，通知的節，並按一下**[確定]**完成。

[**條件**選擇器可讓 4 值、**大於**、**大於或等於**、**小於**，或**小於或等於**。

**期間**選擇器可讓您挑選的 5 分鐘到 6 小時的期間。

選取**電子郵件的擁有人、 參與者及讀者**電子郵件可動態根據有權存取該資源的使用者。 否則使用者的逗點分隔清單會提供**其他管理員 email(s)** ] 文字方塊中。

![新增規則刀][7]

如果破壞閥值，電子郵件送達類似下圖中的項目︰

![閥值違反電子郵件][8]

提醒的清單會顯示後，已建立公制的通知，並提供通知的所有規則的概觀。

![通知規則檢視][9]

若要進一步瞭解提醒通知，請造訪[接收提醒通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

若要瞭解 webhooks 和您能夠如何使用這些通知的詳細資訊，請造訪[webhook 上 Azure 公制通知設定](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>後續步驟

- 以視覺化方式呈現計數器和與[記錄分析](../log-analytics/log-analytics-azure-networking-analytics.md)的事件記錄檔 
- [視覺效果 Azure 稽核記錄使用 Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)部落格文章。
- [檢視及分析 Azure 稽核記錄在 Power BI 等](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png