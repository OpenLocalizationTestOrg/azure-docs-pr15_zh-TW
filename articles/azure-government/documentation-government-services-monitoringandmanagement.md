<properties
    pageTitle="Azure 政府文件 |Microsoft Azure"
    description="以下提供功能及的比較開發 Azure 政府版的應用程式。"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-monitoring-and-management"></a>Azure 政府監控和管理

本文概述的監視及管理服務的變化與 Azure 政府版環境的考量。

## <a name="automation"></a>自動化

自動化是 Azure 政府推出。

### <a name="variations"></a>變化

找不到 Azure 政府目前提供的下列的自動化功能。

+ 建立的驗證服務原則認證

如需詳細資訊，請參閱[自動化公用文件](../automation/automation-intro.md)。

## <a name="log-analytics"></a>記錄狀況分析

通常 Azure 政府版中使用記錄分析。

### <a name="variations"></a>變化

下列記錄分析功能和解決方案目前不提供 Azure 政府版中。

+ Microsoft Azure 中的預覽方案包括︰
  - 網路監控解決方案
  - 應用程式相依性監控方案
  - Office 365 方案
  - Windows 10 升級分析解決方案
  - 應用程式的深入見解方案
  - Azure 網路分析解決方案
  - Azure 自動化分析解決方案
  - 索引鍵保存庫分析解決方案
+ 解決方案與需要內部部署軟體更新的功能包括︰
  - 使用系統管理中心的作業管理員 2016 （支援舊版 Operations Manager） 整合
  - 電腦群組從系統管理中心設定管理員
  - 運用中心解決方案
+ 在預覽公用 Azure 中的功能包括︰
  - 匯出資料至 Power BI
+ Azure 指標和 Azure 診斷
+ 作業管理套件行動應用程式

Azure 政府記錄分析 Url 有所不同︰

| Azure 公用 | Azure 政府版 | 備忘稿 |
|--------------|------------------|-------|
| mms.microsoft.com | oms.microsoft.us | 登入分析入口網站 |
| *workspaceId*。 ods.opinsights.azure.com | *workspaceId*。 ods.opinsights.azure.us | [資料收集 API](../log-analytics/log-analytics-data-collector-api.md) 
| \*。 ods.opinsights.azure.com | \*。 ods.opinsights.azure.us | 代理程式通訊的[防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| \*。 oms.opinsights.azure.com | \*。 oms.opinsights.azure.us | 代理程式通訊的[防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| \*。 blob.core.windows.net | \*。 blob.core.usgovcloudapi.net | 代理程式通訊的[防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |


下列記錄分析功能會有不同行為 Azure 政府版︰

+ Azure 政府版，必須從[記錄分析入口網站](https://oms.microsoft.us)下載 Windows 代理程式。
+ 若要連接記錄分析您的系統管理中心 Operations Manager 管理伺服器，您需要下載並匯入更新的管理組件。
  1. 下載並儲存的[更新管理組件](http://go.microsoft.com/fwlink/?LinkId=828749)。
  2. 解壓縮您下載的檔案。
  3. 匯入 Operations Manager 管理組件。 如需如何從光碟匯入管理套件，請參閱 Microsoft TechNet 網站上的[如何匯入作業管理員 Management Pack](http://technet.microsoft.com/library/hh212691.aspx)資訊。
  4. 若要連接記錄分析 Operations Manager，請依照下列[記錄分析來連線 Operations Manager](../log-analytics/log-analytics-om-agents.md)中的步驟進行。


## <a name="frequently-asked-questions"></a>常見問題集

+ 可以移轉資料從 Microsoft Azure 中的記錄檔分析 Azure 政府嗎？
  - [否]。 無法將資料或工作區從 Microsoft Azure 移至 Azure 政府版。
+ 可以切換 Microsoft Azure 和 Azure 政府工作區，從作業管理套件記錄分析入口網站？
  - [否]。 Microsoft Azure 和 Azure 政府版入口網站不同而不共用資訊。

如需詳細資訊，請參閱[記錄分析公用文件](../log-analytics/log-analytics-overview.md)。

## <a name="next-steps"></a>後續步驟

補充資訊，更新的訂閱<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府部落格。</a>
