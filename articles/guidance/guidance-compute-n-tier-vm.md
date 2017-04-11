<properties
   pageTitle="執行 Windows Vm N 層架構 |參照架構 |Microsoft Azure"
   description="如何在 Azure 付費特定來加以強調可用性、 安全性、 延展性及管理能力安全性實作多層架構。"
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
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>執行 Windows Vm N 層架構 Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Azure Linux Vm 執行 N 層架構](guidance-compute-n-tier-vm-linux.md)
- [執行 Windows Vm N 層架構 Azure](guidance-compute-n-tier-vm.md)

本文概述經過驗證的作法來執行 Windows 虛擬機器 (Vm) 一組 N 層架構與應用程式。 在[執行多個 Vm Azure 上]的文件建置[multi-vm]。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 本文所用的資源管理員，Microsoft 建議新的部署。

## <a name="architecture-diagram"></a>架構圖表

有 N 層架構的變化。 大多數的情況下，差異不應這些建議的影響。 本文假設一般 3 層 web 應用程式︰

- **網頁層。** 處理傳入 HTTP 要求。 回覆會傳回透過此層。

- **業務層級。** 實作商業程序和系統的其他功能邏輯。

- **資料庫層。** 提供常設資料的儲存空間，使用[SQL Server 永遠在可用性群組][sql-alwayson]高可用性。

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是在 「 計算-多重層 (Windows) 頁面。

![[0]][0]

- **可用的設定。** 建立[可用性設定][azure-availability-sets]每個層，以及佈建至少兩個 Vm 每一層。 這種方法，才能達到可用性[SLA] [vm-sla]的 Vm。

- **子網路。** 建立不同的子網路的每一層。 指定[CIDR]標記法使用地址範圍及子網路遮罩。 

- **負載平衡器。** 使用[網際網路的負載平衡器][load-balancer-external]至發佈網頁層] 及 [[內部負載平衡器]的連入網際網路流量[load-balancer-internal]散佈商務層從 web 層網路流量。

- **Jumpbox**。 _Jumpbox_，也稱為[堡壘主機]，為系統管理員使用連線至其他 Vm 網路上的 VM。 Jumpbox 有 NSG，可讓遠端流量僅從 whitelisted 的公用 IP 位址。 NSG 應該允許遠端桌面 (RDP) 傳輸。

- **監控**。 監視軟體，例如[Nagios]、 [Zabbix]或[Icinga]可讓您深入了解回應時間 VM 存留時間、 與整體系統的狀況。 安裝監控軟體 VM 位於個別管理子網路上。

- **NSGs**。 使用[網路安全性群組][ nsg] (NSGs) 來限制 VNet 內的網路流量。 例如，在這裡所顯示的 3 層架構，資料庫層不接受從 web 前端，僅從商務層和管理子網路流量。

- **一律在可用性] 群組中的 SQL Server。** 提供資料層上，在可用性複寫和容錯移轉。

- **Active Directory 網域服務 (AD DS) 伺服器**。 Active Directory 網域服務 (AD DS) 會儲存目錄資料，及管理使用者和網域，包括使用者登入程序、 驗證及目錄搜尋之間的通訊。 Active Directory 網域控制站是執行 AD DS 的伺服器。 在 Windows Server 2016 之前, 一律上顯示狀態群組必須加入網域。 這是因為可用性群組取決於 Windows Server 容錯移轉叢集 (WSFC) 技術。 在 Windows Server 2016 介紹建立容錯移轉叢集不 Active Directory 的能力。 如需詳細資訊，請參閱[什麼是容錯 Windows Server 2016 中的新增功能][wsfc-whats-new]

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇要建立您自己的參考架構請遵循這些建議，除非您有建議不符合特定需求。

### <a name="vnet--subnets"></a>VNet / 子網路

當您建立 VNet 時，配置您需要的子網路空間不足的地址。 指定 VNet 地址範圍及子網路遮罩[CIDR]標記法使用。 使用地址空間落在標準的[私用的 IP 位址區塊]內[private-ip-space]，哪些是 10.0.0.0/8/8、 172.16.0.0/12，以及 192.168.0.0/16。

您需要更新版本設定 VNet 與內部部署網路之間的閘道器，請選擇 [不會與您的內部部署網路重疊位址範圍]。 一旦您建立 VNet 時，您無法變更的位址範圍。

設計子網路記住的功能及安全性需求。 在相同層或角色中的所有 Vm 應該都移到相同的子網路，它可以是安全性邊界。 更多關於設計 VNets 和子網路的詳細資訊，請參閱[規劃及設計 Azure 虛擬網路][plan-network]。

針對每一個子網路，指定子位址空間 CIDR 標記法。 例如，「 10.0.0.0/24 」 會建立 256 個 IP 位址範圍。 （Vm 可以使用以下 251; 五個會保留。 請參閱[虛擬網路常見問題集][vnet faq]。)請確定位址範圍不致重疊子網路之間。

### <a name="network-security-groups"></a>網路安全性群組

您可以使用 NSG 規則，限制層之間的流量。 例如，3 層架構如上所示，在網頁層不會直接與通訊資料庫層。 若要強制執行這種情況，資料庫層應該封鎖連入網頁層子網路流量。  

  1. 建立 NSG，並將其至資料庫層子網路相關聯。

  2. 新增從 VNet 拒絕所有輸入的流量的規則。 (使用`VIRTUAL_NETWORK`規則中的標記。) 

  3. 新增具有較高的優先順序，讓來自商務層子網路輸入的傳輸規則。 此規則覆寫先前的規則，並可讓資料庫層和溝通商業層。

  4. 新增規則，允許從資料庫層子網路本身中輸入的流量。 此規則可讓您在資料庫層，所需的資料庫複寫和容錯移轉 Vm 之間的通訊。

  5. 新增一個規則，允許 RDP jumpbox 子網路流量。 此規則可讓系統管理員從 jumpbox 連線至資料庫層。

  > [AZURE.NOTE] NSG 具有[預設規則][ nsg-rules] ，讓 VNet 內任何輸入的流量。 您無法刪除這些規則，但您可以藉由建立較高優先順序規則覆寫這些。

### <a name="load-balancers"></a>負載平衡器

外部負載平衡器分散到網頁層的網際網路流量。 建立此負載平衡器的公用 IP 位址。 請參閱[建立具網際網路負載平衡器][lb-external-create]。

內部負載平衡器散佈商務層從 web 層網路流量。 若要讓此負載平衡器私用的 IP 位址，建立一個主選單 IP 設定，關聯業務層級的子網路。 請參閱[開始建立內部負載平衡器][lb-internal-create]。

### <a name="sql-server-always-on-availability-groups"></a>一律在可用性群組的 SQL Server

我們建議您[永遠在可用性群組][ sql-alwayson] SQL Server 高可用性。 一律在可用性群組需要網域控制站。 [可用性] 群組中的所有節點都必須在同一個 AD 網域。

其他層連線至資料庫透過[可用性群組接聽][sql-alwayson-listeners]。 接聽程式可讓 SQL 用戶端連線，而不知道實體 SQL Server 執行個體的名稱。 Access 資料庫必須加入網域 Vm。 用戶端 （在此例中另一層） 使用 DNS 接聽程式的虛擬網路名稱解析成 IP 位址。

SQL Server 永遠上設定，如下所示︰

- 建立 Windows Server 容錯移轉叢集 (WSFC) 叢集和 SQL Server 永遠在可用性群組。 如需詳細資訊，請參閱[快速入門永遠在可用性群組][sql-alwayson-getting-started]。

- 內部負載平衡器建立私人的靜態 IP 位址。

- 建立可用性群組接聽程式，並將對應接聽 DNS 名稱為內部負載平衡器 IP 位址。 

- 建立負載平衡器規則的 SQL Server 聆聽連接埠 （TCP 連接埠 1433年預設）。 負載平衡器規則必須啟用_浮動 IP_，也稱為 [直接伺服器傳回。 這會使 VM，直接回覆的用戶端，這可讓直接連線至主要的複本。

    > [AZURE.NOTE] 啟用浮動 IP 時，必須是前端連接埠號碼的後端連接埠號碼負載平衡器規則中相同。

當 SQL 用戶端嘗試連線時，負載平衡器會將連線要求路由至目前的主要的複本。 如果有容錯移轉至另一個複本，請負載平衡器會自動傳送的後續要求至新的主要複本。 如需詳細資訊，請參閱[設定一直在載入平衡器 sql][sql-alwayson-ilb]。

故障時, 關閉現有的用戶端連線。 容錯移轉完成後，會路由新連線到新的主要複本。

如果您的應用程式可讓您更多的讀取與寫入，您可以下載的一些唯讀查詢次要複本。 請參閱[使用接聽程式連線至唯讀次要複本 （唯讀路由）][sql-alwayson-read-only-routing]。

測試您的部署強制執行[手動容錯移轉][sql-alwayson-force-failover]。

### <a name="jumpbox"></a>Jumpbox

不允許 RDP 存取公用網際網路 Vm 執行應用程式工作負載的。 不過，所有 RDP/SSH 存取這些 Vm 必須都經過 jumpbox。 系統管理員 jumpbox，將記錄，然後記錄其他 VM 從 jumpbox 到。 從網際網路]，但僅從已知，whitelisted IP 位址，jumpbox 可讓 RDP 流量。

在相同的 VNet 為其他 Vm，但在個別管理子網路，請將 jumpbox。

建立 jumpbox[的公用 IP 位址]。

使用 jumpbox，例如 [標準 A1 小型的虛擬記憶體大小。

設定為允許透過傳遞管理子網路的系統管理 (RDP) 流量 NSGs web 層、 商務層和資料庫層子網路。

若要保護 jumpbox，建立 NSG 並將其套用至 jumpbox 子網路。 新增 NSG 規則，允許 RDP 連線，只從 whitelisted 的公用 IP 位址。

子網路或 jumpbox NIC 可附加 NSG 在此案例中，我們建議您附加至 NIC，因此 RDP 流量可以僅 jumpbox，即使您將其他 Vm 新增至相同的子網路。

## <a name="availability-considerations"></a>可用性考量

將每個層或 VM 角色成不同的顯示狀態。 不放入 Vm 來自不同層相同的可用性設定。 

在資料庫層，有多個 Vm 不會自動翻譯至高度可用的資料庫。 關聯式資料庫，您通常必須使用複寫和容錯移轉來達成高的可用性。 SQL Server，建議您使用[永遠在可用性群組][sql-alwayson]。 

如果您需要更高的可用性比[Vm Azure SLA] [vm-sla]提供，跨兩個區域複寫應用程式，並使用容錯移轉 Azure 流量管理員。 如需詳細資訊，請參閱[執行 Windows Vm 在多個區域內的可用性][multi-dc]。   

## <a name="security-considerations"></a>安全性考量

加密其餘資料。 使用[Azure 金鑰保存庫][azure-key-vault]管理資料庫加密金鑰。 索引鍵保存庫可以儲存加密金鑰硬體安全性模組 (Hsm)。 如需詳細資訊，請參閱[適用於 SQL Server Azure Vm 上設定 Azure 金鑰保存庫整合][sql-keyvault]金鑰保存庫儲存應用程式的密碼，資料庫的連接字串，例如也建議使用。

請考慮加入網路虛擬應用裝置 (NVA) 若要建立公用網際網路和 Azure 虛擬網路之間 DMZ。 NVA 是一般虛擬應用裝置可以執行網路相關工作，例如防火牆、 封包檢查、 稽核、 自訂路由]，或其他作業的各種不同的字詞。 如需詳細資訊，請參閱[實作 Azure 與網際網路之間 DMZ][dmz]。

## <a name="scalability-considerations"></a>延展性考量

負載平衡器發佈至網頁和商務層網路流量。 不按比例縮放水平藉由新增新的 VM 執行個體。 請注意，可以調整網頁和商務層獨立，根據載入。 若要降低可能需要維護用戶端關聯性所造成的複雜，在網頁層 Vm 應無狀態。 裝載的商務邏輯 Vm 也應該無狀態。

## <a name="manageability-considerations"></a>管理能力考量

使用集中式的管理工具，例如[Azure 自動化]簡化管理整個系統的[azure-administration]， [Microsoft 作業管理套件][operations-management-suite]，[主廚][chef]，或[傀儡][puppet]。 這些工具，可以合併彙算診斷和健康狀況資訊擷取從多個 Vm 提供整體系統的檢視。

## <a name="solution-deployment"></a>解決方案部署

實作這些建議參照結構的部署才會提供[Github][github-folder]。 此參考架構包含 web 層、 商務層、 資料層，以及 jumpbox VM 與 Active Directory 網域。

參考架構，可以依照下列指示部署︰ 

1. 按一下 [下] 按鈕。  
[![」 部署至 Azure 」][1]][2]

2. 連結已開啟之後 Azure 入口網站中，輸入下列值︰ 
  - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-ntier-sql-network-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

3. 核取 Azure 入口網站的通知訊息部署已完成。

4. 按一下 [下] 按鈕。  
[![」 部署至 Azure 」][1]][3]

5. 連結已開啟之後 Azure 入口網站中，輸入下列值︰ 
  - **資源群組**名稱已經定義參數檔案中，選取 [**使用現有**，然後輸入`ra-ntier-sql-workload-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

6. 核取 Azure 入口網站的通知訊息部署已完成。

7. 按一下 [下] 按鈕。  
[![」 部署至 Azure 」][1]][4]

8. 連結已開啟之後 Azure 入口網站中，輸入下列值︰ 
  - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-ntier-sql-network-rg`在文字方塊中。
  - 從 [**位置**] 下拉式方塊中選取的地區。
  - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
  - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
  - 按一下 [**購買**] 按鈕。

9. 核取 Azure 入口網站的通知訊息部署已完成。

10. 參數檔案包含硬式編碼的系統管理員使用者名稱和密碼、 以及它強烈建議您立即變更同時在所有 Vm。 每個 VM Azure 入口網站中按一下，然後按一下**重設密碼****支援 + 疑難排解**刀中。 在 [**模式**] 下拉式清單方塊中，選取 [**重設密碼**，然後選取新的**使用者名稱**和**密碼**。 按一下 [**更新**] 按鈕，將新的使用者名稱和密碼。 

部署此參照架構的其他方式的資訊，請參閱在[指南-單一-vm]的讀我檔案[github-folder]Github 資料夾。 

## <a name="next-steps"></a>後續步驟

若要達到此參照架構的可用性，建議您[部署到多個區域][multi-dc]。

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[堡壘主機]: https://en.wikipedia.org/wiki/Bastion_host
[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公用 IP 位址]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "使用 Microsoft Azure N 層架構"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json