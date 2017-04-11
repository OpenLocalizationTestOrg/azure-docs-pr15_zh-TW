<properties
    pageTitle="概觀 Microsoft Azure 中監控 |Microsoft Azure"
    description="上方層級的監控和診斷中包含提醒、 webhooks、 自動調整大小等的 Microsoft Azure 概觀。"
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Microsoft Azure 中監控的概觀

本文提供監控 Azure 資源的概觀。 它提供特定類型的資源資訊的指標。  為高層級監控非 Azure 的觀點來看您的應用程式的詳細資訊，請參閱[監控和診斷指引](../best-practices-monitoring.md)。

雲端應用程式是複雜有許多移動的部分。 監視提供資料，以確保您的應用程式保持啟動和執行健全狀態。 也可協助您關閉潛在問題的洪流或疑難排解過去的項目。 此外，您可以使用監視資料至應用程式的相關獲得深入見解。 這可以協助您改善應用程式的效能或維護，或自動化需要手動操作的動作。

下圖顯示 Azure 監控的概念性檢視，包括您可以收集的記錄，該怎麼辦資料類型。   

![監視及非計算資源的診斷邏輯模型](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

圖 1︰ 監控和診斷非計算資源的概念性模型

<br/>

![監控和診斷計算資源的邏輯模型](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

圖 2︰ 監控和診斷計算資源的概念性模型


## <a name="monitoring-sources"></a>監控來源
### <a name="activity-logs"></a>活動記錄
您可以將資源所見 Azure 基礎結構的相關資訊搜尋活動記錄 （先前稱為作業或稽核記錄）。 登入包含的資訊，例如資源時所建立，或損毀的時間。  

### <a name="host-vm"></a>Host （主機) VM
**只計算**


部分計算雲端服務，虛擬機器之類的資源，而且服務布料的轉印圖樣有專用的主機 VM 互動。 Host （主機) VM 相當於的根 VM HYPER-V hypervisor 模型中。 在此情況下，您可以收集主機 VM 除了來賓 OS 指標。  

如需其他 Azure 服務，有不一定是 1:1 之間的對應程式資源與特定主機 VM 讓主機 VM 指標無法使用。


### <a name="resource---metrics-and-diagnostics-logs"></a>資源的標準和診斷記錄
固定的指標會根據的資源類型而定。 例如，虛擬機器提供的磁碟 IO 和百分比 CPU 統計資料。 但服務匯流排佇列中，請改為提供佇列大小和訊息處理能力等計量不存在的統計資料。

計算資源的您可以取得像 Azure 診斷來賓 OS 和診斷模組的指標。 Azure 診斷協助收集資料，並將傳送至其他位置，包括 Azure 儲存體收集診斷資料。

目前固定單位的清單的[支援指標](monitoring-supported-metrics.md)。

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>應用程式-診斷記錄與應用程式記錄指標
**只計算**

應用程式，可以執行計算模型中來賓 OS 上方。 他們發出記錄檔和指標自己的設定。

包含的指標類型

- 效能計數器
- 應用程式的記錄
- Windows 事件記錄檔
- .NET 事件來源
- IIS 記錄檔
- 資訊清單以 ETW
- 損毀傾印
- 客戶錯誤記錄檔


## <a name="uses-for-monitoring-data"></a>監視資料的用途

### <a name="visualize"></a>以視覺化方式呈現
視覺化監控資料的圖形和圖表，可協助您於瀏覽本身的資料尋找最更快速地找到趨勢。  

幾個視覺效果的方法如下︰

- 使用 Azure 入口網站
- Azure 應用程式獲得深入見解傳送資料
- 傳送至 Microsoft 中的資料
- 將資料傳送至協力廠商視覺效果工具使用即時串流或 [從 Azure 儲存體中封存讀取] 工具

### <a name="archive"></a>封存
監視資料通常寫入 Azure 儲存空間，而那里保留，直到您將其刪除。

使用此資料的幾種方式︰

- 撰寫之後，您可以有其他工具內或 Azure 讀取和處理。
- 您會下載至本機本機的封存的資料，或變更您在雲端，資料維持延伸一段時間的保留原則。  
- 您將資料 Azure 儲存體中永久留，但您必須支付 Azure 儲存空間，根據您要保留的資料量。
-

### <a name="query"></a>查詢
您可以使用 Azure 監視器 REST API 跨平台命令列介面 (CLI) 命令、 PowerShell cmdlet，或是.NET SDK 存取系統或 Azure 儲存體中的資料

範例包括︰

-  取得您所撰寫的自訂監視應用程式中的資料
-  建立自訂查詢並將該資料的協力廠商應用程式。

### <a name="route"></a>傳送
您可以將串流監視至其他位置中進行即時的資料。

範例包括︰

- 讓您可以那里使用視覺效果工具，傳送應用程式獲得深入見解。
- 傳送到事件，您可以將路由傳送至協力廠商工具來執行即時分析。

### <a name="automate"></a>自動化
您可以使用引動程序事件監視資料，或甚至是整個程序範例包括︰

- 使用自動調整大小的資料來計算執行個體向上或向下根據應用程式載入。
- 度量單位，交叉預定的閥值時，傳送電子郵件。
- 通話中 Azure 外部系統執行動作的 web URL (webhook)
- 在執行任何各種工作 Azure 自動化啟動 runbook

## <a name="methods-of-use"></a>使用的方法
一般而言，您可以管理資料追蹤路由]，與使用下列方法之一來擷取。 並非所有方法的所有動作或資料類型都可。

- [Azure 入口網站](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [跨平台的命令列介面 (CLI)](insights-cli-samples.md)
- [REST API](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Azure 的監控方案
Azure 有可用的監控至應用程式遙測從答對金屬基礎結構服務方案。 監控策略最佳便會使用掌握全面涵蓋所有內容，詳細的服務健康狀況的三個。

- [Azure 監視器](http://aka.ms/azmondocs)– 優惠視覺效果、 查詢、 路由、 提醒、 自動調整大小，與自動化資料同時從 Azure 基礎結構 （活動記錄） 和每個個別的 Azure 資源 （診斷記錄）。 本文是 Azure 監視器文件的一部分。 在 Ignite 2016 25 年 9 月發行 Azure 監視器名稱。  上一個名稱是 「 Azure 深入資訊]。  
- [應用程式的深入見解](https://azure.microsoft.com/documentation/services/application-insights/)– 提供豐富偵測和診斷服務，從 Azure 監控資料最上方完善整合式應用程式層的問題。 則應用程式服務 Web 應用程式的預設診斷平台。  您可以路由傳送至其他服務的資料。  
- [記錄分析](https://azure.microsoft.com/documentation/services/log-analytics/)[作業管理套件](https://www.microsoft.com/cloud-platform/operations-management-suite)-一部分提供全方位的 IT 管理解決方案內部部署和協力廠商雲端基礎結構 （例如 AWS) 除了 Azure 資源兩者均適用。  從 Azure 監視器的資料可以會直接路由至記錄的分析，讓您可以在同一個地方整個環境的看到指標和記錄。     


## <a name="next-steps"></a>後續步驟
深入瞭解︰

- [從 Ignite 2016 視訊中的 azure 監視器](https://myignite.microsoft.com/videos/4977)
- [快速入門 Azure 監視器](monitoring-get-started.md)
- [Azure 診斷](../azure-diagnostics.md)如果您正嘗試診斷雲端服務、 虛擬機器或服務布料的轉印圖樣應用程式中的問題。
- [應用程式深入資訊](https://azure.microsoft.com/documentation/services/application-insights/)如果您在應用程式服務 Web 應用程式中嘗試診斷問題。
- [疑難排解 Azure 儲存體](../storage/storage-e2e-troubleshooting.md)時使用 Blob 儲存體]、 [目錄] 或 [佇列
- [記錄檔分析](https://azure.microsoft.com/documentation/services/log-analytics/)及[作業管理套件](https://www.microsoft.com/cloud-platform/operations-management-suite)
