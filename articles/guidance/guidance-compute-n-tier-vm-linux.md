<properties
   pageTitle="執行 Azure N 層架構 Linux Vm |Microsoft Azure"
   description="如何執行 Linux Vm 針對 Microsoft Azure 中 N 層架構。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-for-an-n-tier-architecture-on-azure"></a>Azure Linux Vm 執行 N 層架構

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]


> [AZURE.SELECTOR]
- [Azure Linux Vm 執行 N 層架構](guidance-compute-n-tier-vm-linux.md)
- [執行 Windows Vm N 層架構 Azure](guidance-compute-n-tier-vm.md)

本文概述經過驗證的作法來執行 Linux 虛擬機器 (Vm) 一組 N 層架構與應用程式。 在[執行多個 Vm Azure 上]的文件建置[multi-vm]。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 本文所用的資源管理員，Microsoft 建議新的部署。

## <a name="architecture-diagram"></a>架構圖表

有 N 層架構的變化。 大多數的情況下，差異不應這些建議的影響。 本文假設一般 3 層 web 應用程式︰

- **網頁層。** 處理傳入 HTTP 要求。 回覆會傳回透過此層。

- **業務層級。** 實作商業程序和系統的其他功能邏輯。

- **資料庫層。** 提供常設資料的儲存空間，使用 Apache Cassandra 高可用性。

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是在 「 計算-多重層 (Linux) 頁面。

![[0]][0]

- **可用的設定。** 建立[可用性設定][azure-availability-sets]每個層，以及佈建至少兩個 Vm 每一層。 這種方法，才能達到可用性[SLA] [vm-sla]的 Vm。

- **子網路。** 建立不同的子網路的每一層。 指定[CIDR]標記法使用地址範圍及子網路遮罩。 

- **負載平衡器。** 使用[網際網路的負載平衡器][load-balancer-external]至發佈網頁層] 及 [[內部負載平衡器]的連入網際網路流量[load-balancer-internal]散佈商務層從 web 層網路流量。

- **Jumpbox**。 _Jumpbox_，也稱為[堡壘主機]，為系統管理員使用連線至其他 Vm 網路上的 VM。 Jumpbox 有 NSG，可讓遠端流量僅從 whitelisted 的公用 IP 位址。 NSG 應該允許安全殼層 (SSH) 傳輸。

- **監控**。 監視軟體，例如[Nagios]、 [Zabbix]或[Icinga]可讓您深入了解回應時間 VM 存留時間、 與整體系統的狀況。 安裝監控軟體 VM 位於個別管理子網路上。

- **NSGs**。 使用[網路安全性群組][ nsg] (NSGs) 來限制 VNet 內的網路流量。 例如，在這裡所顯示的 3 層架構，資料庫層不接受從 web 前端，僅從商務層和管理子網路流量。

- **Apache Cassandra 資料庫**。 提供資料層上，在可用性複寫和容錯移轉。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇要建立您自己的參考架構請遵循這些建議，除非您有建議不符合特定需求。

### <a name="vnet--subnets"></a>VNet / 子網路

當您建立 VNet 時，配置您需要的子網路空間不足的地址。 指定 VNet 地址範圍及子網路遮罩[CIDR]標記法使用。 使用地址空間落在標準的[私用的 IP 位址區塊]內[private-ip-space]，哪些是 10.0.0.0/8/8、 172.16.0.0/12，以及 192.168.0.0/16。

您需要更新版本設定 VNet 與內部部署網路之間的閘道器，請選擇 [不會與您的內部部署網路重疊位址範圍]。 一旦您建立 VNet 時，您無法變更的位址範圍。

設計子網路記住的功能及安全性需求。 在相同層或角色中的所有 Vm 應該都移到相同的子網路，它可以是安全性邊界。 更多關於設計 VNets 和子網路的詳細資訊，請參閱[規劃及設計 Azure 虛擬網路][plan-network]。

針對每一個子網路，指定子位址空間 CIDR 標記法。 例如，「 10.0.0.0/24 」 會建立 256 個 IP 位址範圍。 （Vm 可以使用以下 251; 五個會保留。 請參閱[虛擬網路常見問題集][vnet faq]。)請確定位址範圍不致重疊子網路之間。

### <a name="load-balancers"></a>負載平衡器

外部負載平衡器分散到網頁層的網際網路流量。 建立此負載平衡器的公用 IP 位址。 請參閱[建立具網際網路負載平衡器][lb-external-create]。

內部負載平衡器分配商務層從 web 層網路流量。 若要讓此負載平衡器私用的 IP 位址，建立一個主選單 IP 設定，關聯業務層級的子網路。 請參閱[開始建立內部負載平衡器][lb-internal-create]。

### <a name="cassandra"></a>Cassandra

我們建議您[DataStax 企業][datastax]生產使用，但這些建議套用至任何 Cassandra 版本。 如需有關如何執行 DataStax Azure 中的詳細資訊，請參閱[Azure DataStax 企業部署指南][cassandra-in-azure]。 

將 Cassandra 叢集 Vm 放在可用性設定，以確保 Cassandra 複本分散到多個錯誤網域，並升級網域。 如需有關故障網域和升級的網域的詳細資訊，請參閱[管理的虛擬機器可用性][availability-sets-manage]。 

設定可用性設定每 3 的故障網域 （最大值）。 

設定每可用性設定 18 升級的網域。 這可讓您升級的網域的最大數目超過仍由平均故障網域。   

設定機架注意模式中的節點。 對應故障網域中的機架`cassandra-rackdc.properties`檔案。

您不需要負載平衡器叢集前面。 用戶端直接連線到叢集節點。

### <a name="jumpbox"></a>Jumpbox

在相同的 VNet 為其他 Vm，但在個別管理子網路，請將 jumpbox。

建立 jumpbox[的公用 IP 位址]。

使用 jumpbox，例如 [標準 A1 小型的虛擬記憶體大小。

設定為允許透過傳遞管理子網路的系統管理 (SSH) 流量 NSGs web 層、 商務層和資料庫層子網路。

若要保護 jumpbox，建立 NSG 並將其套用至 jumpbox 子網路。 新增 NSG 規則，允許 SSH 連線，只從 whitelisted 的公用 IP 位址。

子網路或 jumpbox NIC 可附加 NSG 在此案例中，我們建議您附加至 NIC，因此 SSH 流量可以僅 jumpbox，即使您將其他 Vm 新增至相同的子網路。

不允許 SSH 存取公用網際網路 Vm 執行應用程式工作負載的。 不過，所有 SSH 存取這些 Vm 必須都經過 jumpbox。 系統管理員 jumpbox，將記錄，然後記錄其他 VM 從 jumpbox 到。 從網際網路]，但僅從已知，whitelisted IP 位址，jumpbox 可讓 SSH 流量。

## <a name="availability-considerations"></a>可用性考量

將每個層或 VM 角色成不同的顯示狀態。 不放入 Vm 來自不同層相同的可用性設定。 

在資料庫層，有多個 Vm 不會自動翻譯至高度可用的資料庫。 關聯式資料庫，您通常必須使用複寫和容錯移轉來達成高的可用性。  

如果您需要更高的可用性比[Vm Azure SLA] [vm-sla]提供，跨兩個區域複寫應用程式，並使用容錯移轉 Azure 流量管理員。 如需詳細資訊，請參閱[執行 Linux Vm 在多個區域內的可用性][multi-dc]。  

## <a name="security-considerations"></a>安全性考量

您可以使用 NSG 規則，限制層之間的流量。 例如，3 層架構如上所示，在網頁層不會直接與通訊資料庫層。 若要強制執行這種情況，資料庫層應該封鎖連入網頁層子網路流量。  

  1. 建立 NSG，並將其至資料庫層子網路相關聯。

  2. 新增從 VNet 拒絕所有輸入的流量的規則。 (使用`VIRTUAL_NETWORK`規則中的標記。) 

  3. 新增具有較高的優先順序，讓來自商務層子網路輸入的傳輸規則。 此規則覆寫先前的規則，並可讓資料庫層和溝通商業層。

  4. 新增規則，允許從資料庫層子網路本身中輸入的流量。 此規則可讓您在資料庫層，所需的資料庫複寫和容錯移轉 Vm 之間的通訊。

  5. 新增一個規則，允許 SSH jumpbox 子網路流量。 此規則可讓系統管理員從 jumpbox 連線至資料庫層。

  > [AZURE.NOTE] NSG 具有[預設規則][ nsg-rules] ，讓 VNet 內任何輸入的流量。 您無法刪除這些規則，但您可以藉由建立較高優先順序規則覆寫這些。

請考慮加入網路虛擬應用裝置 (NVA) 若要建立公用網際網路和 Azure 虛擬網路之間 DMZ。 NVA 是一般虛擬應用裝置可以執行網路相關工作，例如防火牆、 封包檢查、 稽核、 自訂路由]，或其他作業的各種不同的字詞。 如需詳細資訊，請參閱[實作 Azure 與網際網路之間 DMZ][dmz]。

## <a name="scalability-considerations"></a>延展性考量

負載平衡器發佈至網頁和商務層網路流量。 不按比例縮放水平藉由新增新的 VM 執行個體。 請注意，可以調整網頁和商務層獨立，根據載入。 若要降低可能需要維護用戶端關聯性所造成的複雜，在網頁層 Vm 應無狀態。 裝載的商務邏輯 Vm 也應該無狀態。

## <a name="manageability-considerations"></a>管理能力考量

使用集中式的管理工具，例如[Azure 自動化]簡化管理整個系統的[azure-administration]， [Microsoft 作業管理套件][operations-management-suite]，[主廚][chef]，或[傀儡][puppet]。 這些工具，可以合併彙算診斷和健康狀況資訊擷取從多個 Vm 提供整體系統的檢視。

## <a name="solution-deployment"></a>解決方案部署

實作這些建議參照結構的部署才會提供[Github][github-folder]。 此參考架構包含網頁層、 商務層和資料層。

1. 按一下 [下] 按鈕。  
[![」 部署至 Azure 」][1]][2]

2. 連結已開啟之後 Azure 入口網站中，輸入下列值︰ 
  - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-ntier-sql-network-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

3. 核取 Azure 入口網站的通知訊息部署已完成。

4. 參數檔案包含硬式編碼的系統管理員使用者名稱和密碼、 以及它強烈建議您立即變更同時在所有 Vm。 每個 VM Azure 入口網站中按一下，然後按一下**重設密碼****支援 + 疑難排解**刀中。 在 [**模式**] 下拉式清單方塊中，選取 [**重設密碼**，然後選取新的**使用者名稱**和**密碼**。 按一下 [**更新**] 按鈕，將新的使用者名稱和密碼。

## <a name="next-steps"></a>後續步驟

若要達到此參照架構的可用性，建議您[部署到多個區域][multi-dc]。

<!-- links -->

[azure-administration]: ../automation/automation-intro.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[availability-sets-manage]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[堡壘主機]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-consistency-usage]: http://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters-linux.md
[multi-vm]: guidance-compute-multi-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公用 IP 位址]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./media/blueprints/compute-n-tier-linux.png "使用 Microsoft Azure N 層架構"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier%2Fazuredeploy.json


