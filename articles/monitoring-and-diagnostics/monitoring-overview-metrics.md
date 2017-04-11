<properties
    pageTitle="Microsoft Azure 中的指標概觀 |Microsoft Azure"
    description="指標與 Microsoft Azure 中的其用途的概觀"
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
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Microsoft Azure 中的指標的概觀 

本文將說明什麼度量是 Microsoft Azure 優點，以及如何開始使用。  

## <a name="what-are-metrics"></a>指標是什麼？

Azure 監視器可讓您使用遙測取得完善的效能和 Azure 在您工作負載的狀況。 最重要的 Azure 遙測資料類型是最 Azure 資源所發出的計量 （也稱為效能計數器）。 Azure 監視器提供數種方式可設定與使用下列指標監控和疑難排解。


## <a name="what-can-you-do-with-metrics"></a>您可以使用指標做什麼？

指標遙測寶貴來源，並允許您執行下列動作︰

- （例如 VM、 網站或邏輯應用程式） 的資源，以繪製其指標在入口網站的圖表和固定在該圖表的儀表板**追蹤效能**。
- **取得問題的通知**公制交叉特定閾值時影響您的資源的效能。
- **設定自動動作**，例如自動縮放資源或度量單位，交叉特定閾值時觸發 runbook。
- **執行進階分析**或報告您的資源的效能或使用趨勢。
- **封存**效能或狀況的歷程記錄資源**的規範/稽核**之用。

##  <a name="metric-characteristics"></a>公制特性
指標會有下列特性︰

- 所有的指標會有**1 分鐘頻率**。 您收到公制的值每分鐘從您的資源，可讓您的狀態和資源的健康情況的即時掌握附近。
- 指標會**提供--現成的並不需要選擇加入集**或其他診斷設定。
- 您可以存取的每個公制**30 天的歷程記錄**。 您可以快速查看最近及每月中的趨勢的效能或資源的狀況。

您可以︰

- 輕鬆地探索、 存取及 [透過 Azure 入口網站，當您選取資源，並繪製圖表的 [**檢視所有的指標**。 
- 公制交叉您所設定的臨界值時，請設定公制**警示規則會傳送通知或會自動的動作的**。 自動調整大小是一種特殊的自動的動作，可讓您將您認識傳入要求或載入在您的網站或計算資源的資源。 您可以設定自動調整大小設定規則來縮放出/根據交叉臨界值的度量。
- [再**封存**指標或用於離線報告。 您可以路由傳送您的指標設定診斷設定資源時 blob 儲存體。
- 將指標**串流**到事件集線器，讓您能夠然後將其傳送至 Azure 資料流分析或附近進行即時分析自訂應用程式。 您可以使用執行診斷設定。
- **傳送**至 OMS 記錄分析，若要解除鎖定立即分析資料、 搜尋及從您的資源的標準資料的自訂提醒的所有指標。
- **消耗**透過新 Azure 監視器 REST Api 指標。
- 使用 PowerShell Cmdlet 或跨平台 REST API**查詢**指標。

 ![路由 Azure 監視器的指標](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>透過入口網站的存取指標
以下是快速建立公制圖表透過 Azure 入口網站的逐步解說

### <a name="view-metrics-after-creating-a-resource"></a>建立資源之後的檢視指標
1. 開啟 Azure 入口網站
2. 建立新的應用程式服務-的網站。
3. 建立網站之後，請前往網站的概觀刀。
4. 您可以檢視新的指標為 「 監控 」 方塊。 您也可以編輯磚，並選取 [更多的指標

 ![資源在 Azure 監視器上的指標](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>存取在單一位置中的所有指標
1. 開啟 Azure 入口網站 
2. 瀏覽至新的 [監視器] 索引標籤，然後選取 [公制] 選項 
3. 您可以從下拉式清單選取您的訂閱資源] 群組中，與資源的名稱。 
4. 您現在可以檢視可用的標準清單。 
5. 選取 [的公制您感興趣，並繪製它。 
6. 您可以至儀表板固定，即可在右上角的 pin。

 ![存取在單一位置中 Azure 監視器中所有的指標](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] 您可以存取主機層級指標從 Vm （根據 Azure 資源管理員） 和 VM 比例組沒有任何其他的診斷設定。 這些新的主層級計量是適用於 Windows 和 Linux 的執行個體。 這些計量都不會與您有權存取，當您開啟 Azure 診斷 Vm 或 VMSS 上的來賓 OS 層級計量混淆。 若要瞭解更多關於設定 Azure 診斷程式，請參閱[什麼是 Microsoft Azure 診斷](../azure-diagnostics.md)。

## <a name="access-metrics-via-rest-api"></a>透過 REST API 的存取指標
Azure 指標可透過 Azure 監視器 Api 加以存取。 有兩個 Api 可協助您探索及存取指標。 使用: 

- [Azure 監視器公制定義 REST API](https://msdn.microsoft.com/library/mt743621.aspx)來存取指標適用於服務的清單。
- [Azure 監視器指標 REST API](https://msdn.microsoft.com/library/mt743622.aspx)來存取實際指標資料

>[AZURE.NOTE] 本文涵蓋透過 Azure 資源的[新的 API 度量](https://msdn.microsoft.com/library/dn931930.aspx)單位。 新的公制定義 API API 版 2016年-03-01，而指標 API 的版本會 2016年-09-01。 舊版公制定義和度量，都可以存取 api 版本 2014年-04-01。

如需詳細的逐步解說，使用 Azure 監視器 REST Api，請參閱[Azure 監視器 REST API 逐步解說](monitoring-rest-api-walkthrough.md)。

## <a name="export-options-for-metrics"></a>匯出指標的選項
您可以移至 [診斷記錄刀監視器] 索引標籤下，並檢視的指標的 [匯出] 選項。 您可以選取路由傳送至 Blob 儲存體，事件集線器或 OMS 記錄分析指標 （和診斷記錄） 的使用案例先前本文所述。 

 ![匯出計量 Azure 監視器的選項](./media/monitoring-overview-metrics/MetricsOverview3.png)   

您可以透過資源管理員範本， [PowerShell](insights-powershell-samples.md)、 [CLI](insights-cli-samples.md)或[REST Api](https://msdn.microsoft.com/library/dn931943.aspx)來設定此設定。 

## <a name="take-action-on-metrics"></a>指標採取行動
您可以收到通知，或執行公制資料自動化的動作。 您可以設定提醒的規則] 或 [自動調整大小設定執行此作業。

### <a name="alert-rules"></a>通知的規則
您可以設定度量通知的規則。 如果度量單位，有超過特定閾值，通知您透過電子郵件或啟動然後可以用來執行任何自訂指令碼 webhook，可以檢查這些通知的規則。 您也可以使用 webhook 設定第 3 產品整合。

 ![指標和通知的規則，在 Azure 監視器](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>自動調整大小
Azure 資源支援縮放或中處理您的工作負載的多個執行個體。 自動調整大小適用於應用程式服務 （Web 應用程式） 虛擬機器縮放比例設定 (VMSS) 與傳統的雲端服務。 您可以設定不按比例縮放出或特定度量單位，影響您的工作量交叉您指定的臨界值時自動調整大小規則。 如需詳細資訊，請參閱[自動縮放的概觀](monitoring-overview-autoscale.md)。

 ![指標和 Azure 監視器中的自動調整大小](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>支援的服務和指標
Azure 監視器是新的度量基礎結構。 它會提供下列 Azure 入口網站和 Azure 監視器 API 新版 Azure 服務的支援︰

- Vm （根據 Azure 資源管理員）
- VM 縮放設定
- 批次
- 事件中心命名空間 
- 服務匯流排命名空間 （僅限進階 SKU）
- SQL （版本 12）
- 彈性的 SQL 資料庫
- 網站
- 網頁伺服器陣列
- 邏輯應用程式
- IoT 集線器
- Redis 快取
- 網路︰ 應用程式的閘道器
- 搜尋

您可以檢視所有的支援的服務和[Azure 監視器指標的每個資源類型的支援指標](monitoring-supported-metrics.md)在其指標的詳細的清單。 


## <a name="next-steps"></a>後續步驟

請參閱本文中的連結。 此外，了解︰  

- 關於[自動調整常見的指標](insights-autoscale-common-metrics.md)
- 如何[建立提醒的規則](insights-alerts-portal.md)




