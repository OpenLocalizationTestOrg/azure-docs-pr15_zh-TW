<properties
    pageTitle="效能測試和小數位數結果內部部署的內部部署 HYPER-V 複寫網站復原 |Microsoft Azure"
    description="本文提供內部部署的內部部署複寫使用 Azure 網站修復測試效能相關資訊。"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>效能測試並不按比例縮放的內部部署網站復原的內部部署 HYPER-V 複寫的結果

您可以使用 Microsoft Azure 網站復原協調和管理的虛擬機器和 Azure，或第二個資料中心的實體伺服器複寫。 本文提供效能測試我們當兩個之間複製 HYPER-V 虛擬機器內部部署資料中心的結果。



## <a name="overview"></a>概觀

測試的目標是要檢查 Azure 網站復原穩定狀態複寫期間的執行方式。 虛擬機器已完成初始複寫並同步處理 delta 變更時，就會發生穩定狀態複寫。 請務必測量使用穩定的狀態，因為它是大部分的虛擬機器維持除非發生無法預期的中斷狀態的效能。


測試部署包含兩個內部部署網站與 VMM 伺服器中每個網站。 這項測試部署是一般的標頭的 office 分支 office 部署中，使用標頭做為主要的網站和次要或復原網站分公司的 office。

### <a name="what-we-did"></a>我們所做的一樣

以下是什麼我們已在傳遞︰

1. 建立使用 VMM 範本的虛擬機器。

1. 啟動虛擬機器和擷取基線效能超過 12 小時。

1. 建立的主要和修復 VMM 伺服器上雲朵。

1. Azure 網站修復，包括對應的來源及修復雲朵設定的雲端保護。

1. 啟用的虛擬機器保護，讓他們可以完成初始複寫。

1. 等待幾個系統穩定的時數。

1. 擷取效能指標超過 12 小時，確保所有的虛擬機器留在預期的複寫狀態這些 12 小時。

1. 測量基線效能與複寫效能指標之間的差異。

## <a name="test-deployment-results"></a>測試部署結果

### <a name="primary-server-performance"></a>主要伺服器的效能

- HYPER-V 複本非同步追蹤變更的記錄檔以最小值的儲存空間開銷主要伺服器上。

- HYPER-V 複本利用自我維護的記憶體快取，以將最小化 IOPS 負荷的追蹤。 它會儲存在記憶體中 VHDX 撰寫及清除這些記錄檔將記錄傳送給復原網站前。 如果寫入叫用的預先定義的限制，也會發生清除磁碟。

- 下列圖表顯示複寫穩定狀態 IOPS 負荷。 我們可以看到負荷因為複寫 IOPS 大約是 5%，即為很低。

![主要的結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

HYPER-V 複本利用磁碟效能最佳化主要伺服器上的記憶體。 下圖所示，在主要叢集所有伺服器上的費用記憶體是臨界。 負荷顯示的記憶體是記憶體使用複寫比較 HYPER-V 伺服器上已安裝記憶體總計的百分比。

![主要的結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

HYPER-V 複本具有最小的 CPU 負荷。 顯示圖表] 中，複製成本是 2-3%的範圍中。

![主要的結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>次要 （復原） 伺服器的效能

HYPER-V 複本使用小的記憶體復原伺服器最佳化儲存運算的數目。 圖形摘要列出復原伺服器上的記憶體使用率。 負荷顯示的記憶體是記憶體使用複寫比較 HYPER-V 伺服器上已安裝記憶體總計的百分比。

![第二個結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

復原網站上的 I/O 作業的是主要網站寫入操作的數字的函數。 讓我們來看看情況總 I/O 作業復原網站上的總 I/O 作業及寫入主要的網站上的操作。 圖形顯示總 IOPS 復原網站上是

- 周圍 1.5 倍寫入 IOPS 主要。

- 周圍的主要的網站上的總 IOPS 37%。

![第二個結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![第二個結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>複寫網路使用的效果

針對現有的頻寬 5 GB 的秒主要及復原節點 （含啟用壓縮） 之間使用每秒的網路頻寬 275 MB 的平均值。

![結果網路使用率](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>複寫的虛擬機器效能的影響

重要的考量是複寫虛擬機器上執行的實際執行工作負載的影響。 如果主站台累積複寫佈建後，在負載不應會有任何影響。 追蹤機制 HYPER-V 複本輕量型可確保虛擬機器中執行的工作負載均不受影響穩定複寫期間。 下圖所示。

此圖顯示執行虛擬機器執行不同的工作負載之前及之後已啟用複寫 IOPS。 您可以看到是沒有兩者之間的差異。

![複本效果結果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

下圖顯示的虛擬機器執行不同的工作負載之前和之後已啟用複寫處理量。 您可以看到複寫具有沒有嚴重影響。

![結果複本效果](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>結束時

結果清楚地顯示 Azure 網站修復，搭配 HYPER-V 複本，請調整與大型叢集負荷的最小值。  Azure 網站復原提供簡單的部署、 複製、 管理及監視。 HYPER-V 複本提供必要的基礎結構成功複寫縮放比例。 規劃的最佳部署建議您下載[HYPER-V 複本容量規劃](https://www.microsoft.com/download/details.aspx?id=39057)。

## <a name="test-environment-details"></a>測試環境的詳細資料

### <a name="primary-site"></a>主要的網站

- 主要網站有叢集，其中包含執行 470 虛擬機器的五個 HYPER-V 伺服器。

- 虛擬機器執行不同的工作負載，所有已啟用 Azure 網站復原保護。

- 由 iSCSI 舊提供叢集節點的儲存空間。 模型 Hitachi HUS130。

- 每個叢集伺服器有四個網路卡 (Nic) 的一個 Gbps。

- 兩個網路卡連線至 iSCSI 私人網路和兩個連線至企業外部網路。 外部網路的其中一個保留為叢集通訊。

![主要的硬體需求](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|伺服器|大的 RAM|模型|處理器|處理器數目|NIC|軟體|
|---|---|---|---|---|---|---|
|在 [叢集 HYPER-V 伺服器︰ <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25|128ESTLAB HOST25 具有 256|Dell™ PowerEdge™ R820|Intel(R) Xeon(R) CPU E5-4620 0 @ 2.20 GHz|4|我 Gbps x 4|Windows Server 的資料中心 2012 R2 (x64) + HYPER-V 角色|
|VMM 伺服器|2|||2|1 Gbps|Windows Server 資料庫 2012 R2 (x64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>次要 （復原） 網站

- 次要網站有六個節點容錯移轉叢集。

- 由 iSCSI 舊提供叢集節點的儲存空間。 模型 Hitachi HUS130。

![主要的硬體規格](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|伺服器|大的 RAM|模型|處理器|處理器數目|NIC|軟體|
|---|---|---|---|---|---|---|
|在 [叢集 HYPER-V 伺服器︰ <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell™ PowerEdge™ R720|Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30 GHz|2|我 Gbps x 4|Windows Server 的資料中心 2012 R2 (x64) + HYPER-V 角色|
|ESTLAB HOST17|128|Dell™ PowerEdge™ R820|Intel(R) Xeon(R) CPU E5-4620 0 @ 2.20 GHz|4||Windows Server 的資料中心 2012 R2 (x64) + HYPER-V 角色|
|ESTLAB HOST24|256|Dell™ PowerEdge™ R820|Intel(R) Xeon(R) CPU E5-4620 0 @ 2.20 GHz|2||Windows Server 的資料中心 2012 R2 (x64) + HYPER-V 角色|
|VMM 伺服器|2|||2|1 Gbps|Windows Server 資料庫 2012 R2 (x64) + VMM 2012 R2|

### <a name="server-workloads"></a>伺服器負載

- 進行測試我們挑選常用的企業的客戶案例的工作量。

- 我們會將[IOMeter](http://www.iometer.org)使用彙總資料表中的模擬的工作量特性。

- 所有 IOMeter 設定檔都設定為撰寫模擬最糟的隨機位元組撰寫工作負載的模式。

|工作負載|I/O (kb)|%存取|%已讀取|未處理 I/o|I/O 圖樣|
|---|---|---|---|---|---|
|檔案伺服器|48163264|60%20%5%5%10%|80%80%80%80%80%|88888|隨機的所有 100%|
|SQL Server （大量 1） SQL Server （大量 2）|864|100%100%|70%0%|88|連續的 100%random100%|
|Exchange|32|100%|67%|8|隨機 100%|
|在 VDI 工作站 /|464|快了 66%34%|70%95%|11|隨機兩個 100%|
|Web 檔案伺服器|4864|33%34%33%|95%95%95%|888|隨機的所有 75%|

### <a name="virtual-machine-configuration"></a>虛擬機器設定

- 在 [主要叢集 470 虛擬機器。

- 所有的虛擬機器 VHDX 磁碟。

- 執行彙總資料表中的工作負載的虛擬機器。 所有 VMM 範本以建立。

|工作負載|# Vm|最大的 RAM (GB)|最大的 RAM (GB)|每個 VM 邏輯磁碟大小 (GB)|最大 IOPS|
|---|---|---|---|---|---|
|SQL Server|51|1|4|167|10|
|Exchange Server|71|1|4|552|10|
|檔案伺服器|50|1|2|552|22|
|在 VDI|149|.5|1|80|6|
|網頁伺服器|149|.5|1|80|6|
|總計|470|||96.83 TB|4108|

### <a name="azure-site-recovery-settings"></a>Azure 網站復原設定

- Azure 網站復原已設定好內部部署的內部部署的保護

- VMM 伺服器有四個雲朵設定，包含 HYPER-V 叢集伺服器及它們的虛擬機器。

|主要 VMM 雲端|在雲端受保護的虛擬機器|複寫的頻率|其他復原點|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 分鐘|無|
|PrimaryCloudRpo30s|47|30 秒|無|
|PrimaryCloudRpo30sArp1|47|30 秒|1|
|PrimaryCloudRpo5m|235|5 分鐘|無|

### <a name="performance-metrics"></a>效能指標

表摘要列出的效能標準及測量了部署內的計數器。

|公制|[計數器|
|---|---|
|CPU|\Processor(_Total)\%處理器時間|
|可用的記憶體|\Memory\Available Mb|
|IOPS|\PhysicalDisk (_Total) \Disk 傳輸秒|
|VM 讀取 (IOPS) 作業秒|\Hyper-V 虛擬存放裝置 (<VHD>) \Read 作業秒|
|VM 寫入 (IOPS) 作業秒|\Hyper-V 虛擬存放裝置 (<VHD>) \Write 作業/S|
|VM 閱讀處理量|\Hyper-V 虛擬存放裝置 (<VHD>) \Read 位元組數秒|
|VM 寫入處理能力|\Hyper-V 虛擬存放裝置 (<VHD>) \Write 位元組數秒|


## <a name="next-steps"></a>後續步驟

- [設定兩個內部部署 VMM 網站之間的保護](site-recovery-vmm-to-vmm.md)
