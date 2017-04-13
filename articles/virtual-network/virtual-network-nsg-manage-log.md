<properties
   pageTitle="監視 NSGs 作業、 活動和計數器 |Microsoft Azure"
   description="瞭解如何啟用計數器、 活動和 NSGs 作業的記錄"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="jdial" />

#<a name="log-analytics-for-network-security-groups-nsgs"></a>網路安全性群組 (NSGs) 記錄狀況分析

您可以使用 Azure 中的不同類型的記錄，可用於管理及疑難排解 NSGs。 部分這些記錄可以透過入口網站，來存取所有的記錄可以從 Azure blob 儲存體中擷取及和中不同的工具，例如[記錄分析](../log-analytics/log-analytics-azure-networking-analytics.md)、 Excel 和中檢視。 您可以瞭解更多關於從下列清單不同類型的記錄。

- **稽核記錄︰**若要檢視所有作業送至您 Azure 的訂閱及他們的狀態，您可以使用[Azure 稽核記錄](../monitoring-and-diagnostics/insights-debugging-with-events.md)（先前稱為作業的記錄）。 稽核記錄根據預設，啟用，並可 Azure 預覽入口網站中檢視。
- **事件記錄檔︰**若要檢視哪些 NSG 規則套用至 Vm 和根據 MAC 地址的執行個體角色，您可以使用這個記錄檔。 這些規則的狀態會收集每隔 60 秒。
- **計數器記錄︰**若要檢視每個 NSG 規則已套用至拒絕或允許流量的次數，您可以使用這個記錄檔。

>[AZURE.WARNING] 記錄只適用於部署資源管理員部署模型中的資源。 您無法使用傳統的部署模型中的資源記錄。 進一步了解的兩種模式，參考[瞭解資源管理員部署及傳統部署](../resource-manager-deployment-model.md)文章。

##<a name="enable-logging"></a>啟用記錄功能
已啟用稽核記錄會自動在所有的時間每個資源管理員資源。 必須先啟用 [事件] 和 [計數器開始收集資料可透過這些記錄的記錄。 若要啟用記錄功能，請遵循下列步驟。

1.  登入至[Azure 入口網站](https://portal.azure.com)。 如果您還沒有現有的網路安全性群組，[建立 NSG](virtual-networks-create-nsg-arm-ps.md)再繼續進行。

2.  在 [預覽] 入口網站中，按一下 [**瀏覽** >> **網路安全性群組**。

    ![預覽入口網站-網路安全性群組](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. 選取現有的網路安全性群組。

    ![預覽入口網站-網路安全性群組設定](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. **設定**刀中，按一下 [**診斷**，，然後在 [**診斷**] 窗格的 [**狀態**] 旁按一下 [**上**
5. 在**設定**刀中，按一下 [**儲存帳戶**，然後選擇現有的儲存空間帳號，或建立新的範本。  

>[AZURE.INFORMATION] 稽核記錄不需要個別的儲存空間帳戶。 使用的儲存空間事件規則記錄會造成服務費用。

6. 在下拉式清單中只**儲存空間的帳號**] 之下，選取是否要記錄事件]、 [計數器，或兩項目，然後按一下 [**儲存**。

    ![預覽入口網站-診斷記錄](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## <a name="audit-log"></a>稽核記錄
這個記錄 （先前稱為 「 作業記錄 」） 會產生 Azure 預設。  Azure 的事件記錄存放區中，記錄會保留 90 天。 進一步瞭解這些記錄以 [閱讀[檢視事件及稽核記錄檔](../monitoring-and-diagnostics/insights-debugging-with-events.md)的文件。

## <a name="counter-log"></a>[計數器記錄
如果您已啟用，如上述以每個 NSG 為基礎，此記錄只會產生。 資料會儲存於您啟用記錄時，您指定的儲存空間帳戶。 套用到資源的每個規則會記錄 JSON 格式，如下所示。

    {
        "time": "2015-09-11T23:14:22.6940000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupRuleCounter",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupCounters",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"DenyAllOutBound",
            "direction":"Out",
            "type":"block",
            "matchedConnections":0
            }
    }

## <a name="event-log"></a>事件記錄檔
如果您已啟用，如上述以每個 NSG 為基礎，此記錄只會產生。 資料會儲存於您啟用記錄時，您指定的儲存空間帳戶。 下列資料記錄中︰

    {
        "time": "2015-09-11T23:05:22.6860000Z",
        "systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
        "category": "NetworkSecurityGroupEvent",
        "resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
        "operationName": "NetworkSecurityGroupEvents",
        "properties": {
            "vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
            "subnetPrefix":"10.0.0.0/24",
            "macAddress":"001517D9C43C",
            "ruleName":"AllowVnetOutBound",
            "direction":"Out",
            "priority":65000,
            "type":"allow",
            "conditions":{
                "destinationPortRange":"0-65535",
                "sourcePortRange":"0-65535",
                "destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
                "sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
            }
        }
    }

## <a name="view-and-analyze-the-audit-log"></a>檢視及分析稽核記錄
您可以檢視及分析稽核記錄資料使用下列方法之一︰

- **Azure 工具︰**從透過 PowerShell 的 Azure、 Azure 命令列介面 (CLI)、 Azure REST API 或 Azure 預覽入口網站的稽核記錄檔擷取資訊。  每一種方法的逐步指示的詳細資訊，請參閱[稽核作業與資源管理員](../resource-group-audit.md)。
- **Power BI:**如果您還沒有[Power BI](https://powerbi.microsoft.com/pricing)帳戶，您也可以嘗試的可用。 使用[Azure 稽核記錄內容的 Power BI 套件](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)您可以分析您的資料使用，您可以使用為預先設定的儀表板-，或自訂。

## <a name="view-and-analyze-the-counter-and-event-log"></a>檢視及分析計數器和事件記錄檔

Azure[記錄分析](../log-analytics/log-analytics-azure-networking-analytics.md)可以收集計數器及事件記錄檔 Blob 儲存體帳戶，並包含視覺效果和分析您的記錄功能強大的搜尋功能。

您也可以連線至您儲存的帳戶，並擷取事件和計數器記錄檔的 JSON 記錄項目。 一旦您下載 JSON 檔案時，您可以將它們轉換成 CSV 和在 Excel、 中或任何其他資料視覺效果工具的檢視。

>[AZURE.TIP] 如果您熟悉 Visual Studio 與變更常數及 C# 變數值的基本概念，您可以使用[記錄轉換程式工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)Github。

## <a name="next-steps"></a>後續步驟

- 以視覺化方式呈現計數器和與[記錄分析](../log-analytics/log-analytics-azure-networking-analytics.md)的事件記錄檔
- [視覺效果 Azure 稽核記錄使用 Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)部落格文章。
- [檢視及分析 Azure 稽核記錄在 Power BI 等](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。
