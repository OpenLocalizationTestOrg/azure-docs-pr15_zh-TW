<properties
   pageTitle="執行多個 Vm |參照架構 |Microsoft Azure"
   description="如何在 Azure 上執行多個 VM 執行個體延展性、 恢復、 管理，以及安全性。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Azure 上執行多個 Vm 延展性與可用性 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文概述的執行多個虛擬機器 (VM) 執行個體來改善延展性、 可用性、 管理能力，與安全性證明作法一組。   

在這種架構，工作量分散到 VM 執行個體。 單一的公用 IP 位址，並網際網路流量散發給 Vm 負載平衡器。 此結構可用於單層應用程式，例如沒有狀態的 web 應用程式或儲存空間叢集。 也是 N 層應用程式的建置組塊。 

本文是根據[執行單一 VM 上 Azure][single vm]。 建議的文件中也會套用此架構。

## <a name="architecture-diagram"></a>架構圖表

Azure 中的 Vm 要求支援的網路和存放裝置的資源。

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是在 「 計算-多重 VM] 索引標籤。 」 

![[0]][0]

架構具有下列元件︰ 

- **可用性設定。** [可用性設定][availability set]包含 Vm 且為所需的支援[可用性 SLA Azure Vm][vm-sla]。

- **VNet**。 Azure 中的每個 VM 部署到更細分為**子網路**虛擬網路 (VNet)。

- **Azure 負載平衡器。** [負載平衡器]分配 VM 例項，在可用性設定內送網際網路要求。 負載平衡器包含一些相關的資源︰

  - **公用 IP 位址。** 公用 IP 位址所需的負載平衡器接收網際網路流量。

  - **前端設定。** 關聯負載平衡器中的公用 IP 位址。

  - **位址後端資料庫。** 包含將會收到的內送的流量 Vm 網路介面 (Nic)。

- **負載平衡器規則。** 用來發佈位址後端資料庫中的所有 Vm 間的網路流量。 

- **NAT 規則。** 用來路由流量至特定的 VM。 例如，若要啟用遠端桌面通訊協定 (RDP) Vm，建立每個 VM 的另一個網路地址轉譯 (NAT) 規則。 

- **網路介面 (Nic)**。 每個 VM 有 NIC 連線至網路。

- **儲存空間。** 儲存帳戶保留 VM 圖像和其他檔案相關資源，例如 VM 診斷由 Azure 擷取的資料。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供 Azure 資源管理員範本，以安裝參考架構遵循以下所列的建議。 如果您選擇，建立您自己參考架構，除非您有特定需求不支援的建議，請遵循這些建議。 

### <a name="availability-set-recommendations"></a>可用性設定建議

您必須設定為支援[可用性 SLA Azure Vm]可用性建立兩個以上的 Vm[vm-sla]。 請注意，Azure 負載平衡器也需要負載平衡 Vm 屬於相同的可用性設定。

### <a name="network-recommendations"></a>網路建議

前負載平衡器 Vm 應該所有放子網路相同。 不會揭露 Vm 直接至網際網路，但是改為每個 VM 私用的 IP 位址。 用戶端連線使用負載平衡器的公用 IP 位址。

### <a name="load-balancer-recommendations"></a>負載平衡器建議

新增所有 Vm 可用性設定為負載平衡器位址後端資料庫。

定義 Vm 直接網路流量負載平衡器規則。 例如，若要啟用 HTTP 流量，建立對應至連接埠 80 後端地址集區上的連接埠 80 從前端設定的規則。 當負載平衡器收到的公用 IP 位址的連接埠 80 要求時，它會將要求路由傳送至連接埠 80 在其中一個 Nic 位址後端資料庫中。

路由流量至特定的 VM 定義 NAT 規則。 例如，若要啟用 RDP 至 Vm 會建立的每個 VM 個別 NAT 規則。 每個規則應該對應的不同的連接埠號碼至連接埠 3389 RDP 的預設連接埠。 （例如，「 VM1，」 的 「 VM2，」 的連接埠 50002，使用連接埠 50001 等等。）指定 Vm 在 Nic NAT 規則。 

### <a name="storage-account-recommendations"></a>儲存帳戶建議

建立個別的 Azure 儲存體帳戶的每個 VM 保留虛擬硬碟 (Vhd)，以避免正中第二個[(IOPS) 限制]輸入輸出操作[vm-disk-limits]儲存帳戶。 

建立一個儲存帳戶的 [診斷記錄。 所有 Vm 可以都共用此儲存帳戶。

## <a name="scalability-considerations"></a>延展性考量

有延展 Vm Azure 中的兩個選項︰ 

- 使用負載平衡器分散 Vm 一組網路流量。 若要查看的縮放，佈建其他 Vm 並將它們放前負載平衡器。 

- 使用[虛擬機器縮放比例設定][vmss]。 縮放比例設定包含指定數字的相同 Vm 前負載平衡器。 VM 縮放比例設定為支援自動縮放根據效能指標。 增加 Vm 負載時，其他 Vm 會自動新增至負載平衡器。 

下一節比較這兩個選項。

### <a name="load-balancer-without-vm-scale-sets"></a>不 VM 縮放比例設定負載平衡器

負載平衡器會接受傳入的網路要求，並將之散發在 Nic 位址後端資料庫中。 若要縮放水平，新增更多 VM 執行個體至可用性集 （或解除縮小 Vm）。 

例如，假設您執行的 web 伺服器。 您可以新增連接埠 80 及/或連接埠 443 的負載平衡器規則 （適用於 SSL)。 當用戶端傳送 HTTP 要求時，負載平衡器選擇使用[雜湊演算法]的後端 IP 位址[load balancer hashing]包含來源 IP 位址。 在這種方式，用戶端要求分佈所有 Vm。 

> [AZURE.TIP] 當您新增新的 VM 可用性設定、 確認 NIC 建立 VM，和在 NIC 新增至負載平衡器上位址後端資料庫。 否則，就不會路由傳送新 VM 網際網路流量。

每個 Azure 的訂閱有的位置，包括 Vm 每個地區的最大數目的預設限制。 您可以增加限制歸檔支援要求。 如需詳細資訊，請參閱[Azure 訂閱和服務限制，配額和限制式][subscription-limits]。  

### <a name="vm-scale-sets"></a>VM 縮放設定 

VM 縮放比例設定可協助您部署及管理相同 Vm 一組。 所有的 Vm 相同的設定、 VM 縮放比例設定支援，則為 true 的自動調整大小，不會預先佈建 Vm，讓您容易建置大型目標顯示較大的計算、 顯示較大的資料及編工作負載的服務。 

如需有關 VM 縮放比例設定的詳細資訊，請參閱[虛擬機器縮放比例設定概觀][vmss]。

使用 VM 縮放比例設定的考量︰

- 若要快速縮放出 Vm，或要自動調整大小，請考慮縮放比例設定。 

- 目前，縮放比例設定不支援資料磁碟。 將資料儲存選項都 Azure 檔案儲存空間、 OS 磁碟機、 暫時或外部存放區，例如 Azure 儲存體。 

- 自動設定的比例內的所有 VM 執行個體都屬於相同的可用性設定，5 故障網域與 5 更新的網域。

- 根據預設，縮放比例設定使用 「 overprovisioning 」，這表示縮放比例設定最初佈建新的更多 Vm 超過您要求，然後刪除額外 Vm。 佈建 Vm 時，這可改善整體成功工資率。 

- 沒有其他建議，然後儲存每 20 Vm 比帳戶與 overprovisioning overprovisioning 已啟用]、 或不超過 40 Vm 停用]。  

- 您可以找到資源管理員範本的部署縮放比例設定為在[Azure 快速入門範本][vmss-quickstart]。

- 有兩種基本方式來設定 Vm 部署縮放比例設定︰ 建立自訂的圖像，或者使用副檔名加以佈建後，設定 VM。

    - 內建的自訂圖像上的縮放比例設定，必須建立一個儲存帳戶內的所有 OS 磁碟 Vhd。 

    - 自訂圖像，您需要保持在最新的圖像。

    - 使用副檔名，可能需要更長的時間，新能夠 vm 向上微調。

其他考量，請參閱[設計 VM 縮放比例設定為小數位數][vmss-design]。

> [AZURE.TIP]  使用時自動調整大小的任何方案，以測試生產層級的工作負載事前。 

## <a name="availability-considerations"></a>可用性考量

設定讓您的應用程式更具彈性計劃和尚未計劃進行的維修作業事件。

- 當 Microsoft 更新基礎的平台，有時候會造成 Vm 重新啟動時，就會發生_預定進行的維修作業_。 Azure 會讓確定可用性集中 Vm 不是一次重新啟動所有，至少一個保留其他人會重新啟動時執行。

- 硬體失敗時，會發生_未計劃進行的維修作業_。 Azure 可確保跨多個伺服器機架佈建 Vm，在可用性設定。 如此有助於減少硬體失敗的影響，網路中斷、 電源中斷，依此類推。

如需詳細資訊，請參閱[管理的虛擬機器可用性][availability set]。 以下影片也有可用性組完整概述了︰[如何設定顯示狀態設定為 [縮放比例 Vm][availability set ch9]。 

> [AZURE.WARNING]  請確定設定可用時您佈建 VM 設定。 目前，有任何資源管理員 VM 新增到可用性 VM 已佈建後，設定。

負載平衡器會使用[狀況探查]監控 VM 執行個體的可用性。 如果檢查無法連接逾時期間內的執行個體，負載平衡器停駐點的 vm 傳送流量。 不過，負載平衡器會繼續探查，並再次有空 VM，如果負載平衡器會繼續傳送流量到該 VM。

以下是一些建議，負載平衡器狀況探查︰

- 探查可以測試 HTTP 或 TCP。 如果您 Vm 執行 HTTP 伺服器 （IIS、 nginx、 Node.js 應用程式，以及等），建立 HTTP 探查。 否則，請建立 TCP 探查。

- HTTP 探查指定 HTTP 端點的路徑。 此路徑 HTTP 200 回應探查檢查有。 這可能是根路徑 （「 / 」） 或健康監視的端點實作某些自訂邏輯檢查應用程式的狀況。 端點必須允許匿名 HTTP 要求。

- 從[已知]傳送探查[health-probe-ip]168.63.129.16 IP 位址。 請確定您不會封鎖流量或從這個 IP 任何防火牆原則或網路安全性群組 (NSG) 規則。

- 使用[狀況探查記錄][health probe log]若要檢視的狀況檢查狀態。 啟用記錄功能為每個負載平衡器 Azure 入口網站。 Azure Blob 儲存體寫入記錄檔。 記錄顯示多少 Vm 後端未收到失敗的探查回應因為網路流量。

## <a name="manageability-considerations"></a>管理能力考量

使用多個 Vm，變成自動化程序，使其可靠且可重複。 您可以使用[Azure 自動化][azure-automation]自動化部署、 OS 修補，以及其他工作。 Azure 自動化是 Azure 上執行，並為基礎的 Windows PowerShell 自動化服務。 範例自動化指令碼，可從 TechNet 上的[Runbook 圖庫]。

## <a name="security-considerations"></a>安全性考量

虛擬網路是 Azure 中的流量隔離邊界。 在一個 VNet Vm 無法直接對 Vm 中不同的 VNet 通訊。 在相同的 Vm VNet 可以進行通訊，除非您建立[網路安全性群組][ nsg] (NSGs) 來限制流量。 如需詳細資訊，請參閱[Microsoft 雲端服務和網路安全性][network-security]。

內送的網際網路流量的負載平衡器規則定義的流量連絡後端]。 不過，負載平衡器規則不支援 IP 家，因此，如果您想要 whitelist 特定的公用 IP 位址新增到子網路的 NSG。

## <a name="solution-deployment"></a>解決方案部署

實作這些建議參照結構的部署會提供 GitHub。 此參考架構包括虛擬網路 (VNet)、 網路安全性群組 (NSG)、 負載平衡器，以及兩個虛擬機器 (Vm)。

參考架構可以使用 Windows 或 Linux Vm 部署按照下列指示︰ 

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. 一旦連結已開啟 Azure 入口網站中，您必須輸入值的部分設定︰ 
    - **資源群組**名稱已定義參數檔案中，因此，選取 [**使用現有**，輸入`ra-multi-vm-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 選取的下拉式方塊、 **windows**或**linux**的**Os 類型**。 
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

4. 參數檔案包含的硬式編碼管理員的使用者名稱和密碼，然後強烈建議您立即變更兩者。 按一下名為 VM `ra-multi-vm1` Azure 入口網站中。 然後按一下上的 [**重設密碼****支援 + 疑難排解**刀中。 在 [**模式**] 下拉式清單方塊中，選取 [**重設密碼**，然後選取新的**使用者名稱**和**密碼**。 按一下 [**更新**] 按鈕，若要儲存新的使用者名稱和密碼。 名為 vm 重複`ra-multi-vm2`。

部署此參照架構的其他方式的資訊，請參閱在[指南-單一-vm]的讀我檔案[github-folder]GitHub 資料夾。 

## <a name="next-steps"></a>後續步驟

將多個 Vm 前負載平衡器是建立多層架構的建置組塊。 如需詳細資訊，請參閱[Azure 上 N 層架構執行 Windows Vm] [n-tier-windows]及[執行 Linux Vm 上 Azure N 層架構][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[狀況探查]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[負載平衡器]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Runbook 圖庫]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "在包含設定兩個 Vm 負載平衡與可用性 Azure 多重 VM 解決方案的架構"
