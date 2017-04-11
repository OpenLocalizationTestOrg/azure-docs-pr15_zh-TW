<properties
    pageTitle="Microsoft Azure 虛擬機器、 雲端服務及 Web 應用程式中的自動調整大小的概觀 |Microsoft Azure"
    description="Microsoft Azure 中的自動調整大小的概觀。 適用於虛擬機器、 雲端服務及 Web 應用程式。"
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
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Microsoft Azure 虛擬機器、 雲端服務及 Web 應用程式中的自動調整大小的概觀

本文將說明何種 Microsoft Azure 自動調整大小，它的優點，以及如何開始使用。  

Azure 監視器自動調整大小僅適用於[虛擬機器縮放比例設定](https://azure.microsoft.com/services/virtual-machine-scale-sets/)」、 「[雲端服務](https://azure.microsoft.com/services/cloud-services/)與 「[應用程式服務-Web 應用程式](https://azure.microsoft.com/services/app-service/web/)。

>[AZURE.NOTE] Azure 有兩種自動調整大小的方法。 自動調整大小的較舊版本適用於虛擬機器 （可用性組）。 此功能的支援有限，我們建議您移轉至 VM 比例集更快且更可靠的自動調整大小支援。 如何使用較舊的技術上的連結會包含在此文件。  


## <a name="what-is-autoscale"></a>自動調整大小是什麼？

自動調整大小可讓您有資源執行處理您的應用程式的負載的資料量。 其可讓您新增資源可處理增加載入及移除之資源的兩人坐也節省金錢閒置。 您指定要執行及新增或移除 Vm 會根據一組規則，自動執行個體的最小值和最大值數字。 有最小值會讓確定應用程式隨時執行即使在沒有載入下。 具有最大值限制每小時成本您總計可能。 您會自動調整之間使用您所建立的規則下列兩個多種。

 ![說明自動調整大小。 新增和移除 Vm](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

規則條件符合時，就會觸發一或多個自動調整大小的動作。 您可以新增及移除 Vm，或執行其他動作。 下列的概念性圖表會顯示此程序。  

 ![概念性自動調整大小的流程圖](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>自動調整大小的程序說明
下列說明適用於設備組件的上一個圖表。   

### <a name="resource-metrics"></a>資源指標
資源發出指標，會由規則稍後處理。 指標放在透過不同的方法。
VM 縮放比例設定使用 Azure 診斷代理程式的遙測資料，而 Web 應用程式和雲端服務的遙測直接來自 Azure 基礎結構。 一些常用的統計資料包含 CPU 使用率、 記憶體使用量、 執行緒計數、 佇列長度和磁碟使用方式。 您可以使用哪些遙測資料的清單，請參閱[自動調整大小的常見指標](insights-autoscale-common-metrics.md)。

### <a name="time"></a>時間
根據 UTC 排程為基礎的規則。 設定您的規則時，您必須正確地設定您的時區。  

### <a name="rules"></a>規則
下圖顯示只有一個自動調整大小的規則，但您可以有許多都。 視您的情況，您可以建立複雜重疊的規則。  包含規則類型  

 - **公制型**-50%的 CPU 使用率時，例如，請執行此動作。
 - **以時間為基礎**的例如觸發程序 webhook 星期六在特定時區中每個 8 am。

公制為基礎的規則測量應用程式載入及新增或移除 Vm 根據該載入。 排程為基礎的規則，允許您，當您在負載查看時間模式，並且想要不按比例縮放前可能載入增加或減少縮放。  


### <a name="actions-and-automation"></a>動作和自動化

規則可以觸發一或多個類型的動作。

- **縮放**比例 Vm 或縮小
- **電子郵件**訂閱管理員、 共同管理員，及/或您指定的其他電子郵件地址傳送電子郵件
- **透過 webhooks 自動化**-通話 webhooks，可以觸發內部或外部 Azure 的多個複雜動作。 Azure 內，您可以開始 Azure 自動化 runbook、 Azure 函數或 Azure 邏輯應用程式。 範例第 3 Azure 外部的廠商 URL 包含可寬延時間和 Twilio 等服務。


## <a name="autoscale-settings"></a>自動調整大小的設定
自動調整大小會使用下列的術語與結構。

- **自動調整大小設定**為唯讀，自動調整大小引擎決定是否要不按比例縮放上或往下。 包含一個或多個設定檔，目標資源和通知設定的相關資訊。
    - **自動調整大小的設定檔**是容量設定，請一組規則來管理引動程序和縮放比例動作的設定檔及週期的組合。 您可以有多個設定檔，可讓您處理的不同重疊的需求。
        - **容量設定**表示最小值、 最大值] 及執行個體數目的預設值。 [使用 fig 1 適當的位置]
        - **規則**包含觸發程序，公制的觸發程序或時間的觸發程序，和縮放比例動作，指出向上或向下滿足該規則時，是否應該縮放自動調整大小。
        - **循環**表示自動調整大小何時應將這個設定檔放入效果。 您可以不同時間的日期或數天的週，例如有不同自動調整大小的設定檔。
- **通知設定**定義自動調整大小事件發生根據滿足準則的其中一個 [自動調整大小] 設定的設定檔時，應該就會發生何種通知。 自動調整大小可以通知一或多個電子郵件地址，或撥打一或多個 webhooks。

![Azure 自動縮放設定的設定檔，與規則結構](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

在 [[自動調整大小 REST API](https://msdn.microsoft.com/library/dn931928.aspx)使用哪個欄位和描述的完整清單。

程式碼範例，請參閱

* [VM 縮放比例設定為使用資源管理員範本的進階自動縮放設定](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [自動調整大小 REST API](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>水平與垂直縮放比例

自動調整大小水平增加中只比例的資源，會 （「 取出 」） 增加或減少 （「 中 」） 中 VM 執行個體數目。  水平縮放比例，這是更有彈性的雲端情況下它能讓您執行潛在數以千計的 Vm 處理載入。 垂直縮放比例與不同。 保留數相同的 Vm，但使 VM （「 設定 」） 更多或較少 （「 向下 」） 強大。 Power 是以記憶體、 CPU 速度、 磁碟空間等計算。 垂直縮放比例有更多的限制。 這是較大的硬體可能會依地區，並快速碰到和上限的可用性而定。 垂直縮放比例通常也需要 VM 停駐點並開始。 如需詳細資訊，請參閱[垂直調整 Azure 自動化 Azure 虛擬機器](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md)。


## <a name="methods-of-access"></a>Access 的方法
您可以設定透過自動調整大小

- [Azure 入口網站](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [跨平台的命令列介面 (CLI)](insights-cli-samples.md#autoscale )
- [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>自動調整大小的支援的服務


| 服務                              | 結構描述與文件                                       |
|--------------------------------------|-----------------------------------------------------|
| Web 應用程式                             | [擴充 Web 應用程式](insights-how-to-scale.md)              |
| 雲端服務                       | [自動調整大小雲端服務](../cloud-services/cloud-services-how-to-scale.md) |
| 虛擬機器︰ 傳統           | [縮放比例傳統的虛擬機器可用性集](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| 虛擬機器︰ Windows 縮放設定| [在 Windows 中縮放 VM 縮放設定](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| 虛擬機器︰ Linux 縮放設定  | [縮放比例 VM 縮放比例設定 Linux 中](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| 虛擬機器︰ Windows 範例   | [VM 縮放比例設定為使用資源管理員範本的進階自動縮放設定](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>後續步驟

若要進一步瞭解自動調整大小，請使用 [自動調整大小的逐步解說先前所列，或請參閱下列資源︰

- [Azure 監視器自動調整大小常見的指標](insights-autoscale-common-metrics.md)
- [Azure 監視器自動調整大小的最佳作法](insights-autoscale-best-practices.md)
- [使用自動調整大小動作來傳送電子郵件及 webhook 提醒通知](insights-autoscale-to-webhook-email.md)
- [自動調整大小 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
- [疑難排解虛擬機器縮放比例設定自動調整大小](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
