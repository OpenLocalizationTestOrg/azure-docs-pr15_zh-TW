<properties
   pageTitle="監視負載平衡器作業、 活動和計數器 |Microsoft Azure"
   description="瞭解如何啟用提醒的事件，並檢查 Azure 負載平衡器的健康狀態記錄"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="log-analytics-for-azure-load-balancer-preview"></a>Azure 負載平衡器 （預覽版本） 的記錄狀況分析

您可以使用 Azure 中的不同類型的記錄，可用於管理及疑難排解負載平衡器。 部分這些記錄可以經由入口網站。 所有的記錄可以從 Azure blob 儲存體中擷取及中不同的工具，例如 Excel 和中檢視。 您可以瞭解更多關於從下列清單不同類型的記錄。

- **稽核記錄︰**若要檢視所有作業送至您 Azure 的訂閱及他們的狀態，您可以使用[Azure 稽核記錄](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md)（先前稱為作業的記錄）。 稽核記錄根據預設，啟用，並可 Azure 入口網站中檢視。
- **提醒的事件記錄檔︰**若要檢視的負載平衡器哪些提醒會引發，您可以使用這個記錄檔。 負載平衡器的狀態會收集每隔五分鐘。 如果會引發負載平衡器通知事件，只寫入此記錄。
- **狀況探查記錄︰**您可以使用此記錄，以檢查探查健康核取狀態，多少個執行個體在線上負載平衡器後端和百分比的網路流量接收負載平衡器的虛擬機器中的。 檢查狀態事件變更寫入記錄檔。

>[AZURE.IMPORTANT] 登入分析目前只適用於 Internet 對負載平衡器。 記錄只適用於部署資源管理員部署模型中的資源。 您無法使用傳統的部署模型中的資源記錄。 如需有關部署模型的詳細資訊，請參閱[瞭解資源管理員部署及傳統部署](../../articles/resource-manager-deployment-model.md)。

## <a name="enable-logging"></a>啟用記錄功能

每個資源管理員資源自動啟用稽核記錄。 您需要啟用事件，並開始收集資料可透過這些記錄的狀況探查記錄。 使用下列步驟，即可啟用記錄功能。

登入至[Azure 入口網站](http://portal.azure.com)。 如果您還沒有[建立負載平衡器](load-balancer-get-started-internet-arm-ps.md)再繼續負載平衡器。

1. 在入口網站中，按一下 [**瀏覽**]。
2. 選取 [**負載平衡器**]。

    ![入口網站-負載平衡器](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. 選取現有的負載平衡器 >>**所有設定**。
4. 在右側的負載平衡器名稱底下的對話方塊，捲動至**監控**，按一下 [**診斷**]。

    ![入口網站-負載平衡器設定](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. 在 [**診斷**] 窗格中，[**狀態**] 底下選取**上**。
6. 按一下 [**儲存帳戶**]。
7. 在 [**記錄**] 中，選取現有的儲存空間帳戶，或建立新的範本。 使用滑桿來決定多少天事件 dat 將會保留在事件記錄檔。 8。 按一下 [**儲存**]。

    ![入口網站-診斷記錄](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] 稽核記錄不需要個別的儲存空間帳戶。 使用事件和健康情況的儲存空間探查記錄會造成服務費用。

## <a name="audit-log"></a>稽核記錄

根據預設，會產生稽核記錄。 Azure 的事件記錄存放區中，記錄會保留 90 天。 進一步瞭解這些記錄以 [閱讀[檢視事件及稽核記錄檔](../../articles/monitoring-and-diagnostics/insights-debugging-with-events.md)的文件。

## <a name="alert-event-log"></a>提醒的事件記錄檔

如果您已啟用，在此記錄只會產生每負載平衡器基礎。 事件登入 JSON 格式，儲存在您啟用記錄時，您指定的儲存空間帳戶。 以下是事件的範例。

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }

JSON 輸出顯示*eventname*屬性會說明負載平衡器建立提醒的原因。 在此情況下，產生提醒都是由於 TCP 連接埠耗盡所造成的來源 IP NAT 限制 (SNAT)。

## <a name="health-probe-log"></a>狀況探查記錄

如果您已啟用，在此記錄只會產生每負載平衡器基礎詳細為上述。 資料會儲存於您啟用記錄時，您指定的儲存空間帳戶。 建立名為 「 深入見解-記錄-loadbalancerprobehealthstatus' 容器，並會記錄下列資料︰

    {
        "records":
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }
    }

在 [屬性] 欄位中顯示的 JSON 輸出探查健康狀態的基本資訊。 *DipDownCount*屬性會顯示執行個體的總數上後端的未收到失敗的探查回應因為網路流量。

## <a name="view-and-analyze-the-audit-log"></a>檢視及分析稽核記錄

您可以檢視及分析稽核記錄資料使用下列方法之一︰

- **Azure 工具︰**從透過 PowerShell 的 Azure、 Azure 命令列介面 (CLI)、 Azure REST API 或 Azure 預覽入口網站的稽核記錄檔擷取資訊。 每一種方法的逐步指示的詳細資訊，請參閱[稽核作業與資源管理員](../../articles/resource-group-audit.md)。
- **Power BI:**如果您還沒有[Power BI](https://powerbi.microsoft.com/pricing)帳戶，您也可以嘗試的可用。 使用[Azure 稽核記錄內容的 Power BI 套件](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)，您可以分析您的資料與預先設定的儀表板，或您可以自訂檢視，以符合您的需求。

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>檢視及分析的狀況檢查及事件記錄檔

您需要連線至您儲存的帳戶，並可擷取事件和健康探查記錄的 JSON 記錄項目。 一旦您下載 JSON 檔案時，您可以將它們轉換成 CSV 和在 Excel、 中或任何其他資料視覺效果工具的檢視。

>[AZURE.TIP] 如果您熟悉 Visual Studio 與變更常數及 C# 變數值的基本概念，您可以使用[記錄轉換程式工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)Github。

## <a name="additional-resources"></a>其他資源

- [視覺效果 Azure 稽核記錄使用 Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)部落格文章。
- [檢視及分析 Azure 稽核記錄在 Power BI 等](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。

## <a name="next-steps"></a>後續步驟

[了解負載平衡器探查](load-balancer-custom-probe-overview.md)
