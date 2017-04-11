<properties
   pageTitle="實作安全的混合式網路架構 Azure 中 |Microsoft Azure"
   description="實作安全的混合式網路架構 Azure 中的方式。"
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-your-on-premises-datacenter"></a>實作 DMZ 之間 Azure 及內部部署資料中心

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明執行延伸 Azure 內部部署網路安全的混合式網路的最佳作法。 此參考架構實作 DMZ 內部網路和使用使用者定義路由 (UDRs) Azure 虛擬網路之間。 DMZ 包含高度可用網路虛擬應用裝置 (NVAs) 的實作封包檢查等防火牆的安全性功能。 所有外寄流量 VNet 是內部部署網路的網際網路強制通道可稽核。 

此結構必須連接到內部部署資料中心實作使用任一[VPN 閘道][ra-vpn]，或[ExpressRoute] [ra-expressroute]連線。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和傳統。 此參考架構會使用資源管理員，Microsoft 建議新的部署。

此結構的一般的使用案例包括︰

- 混合式應用程式位置負載執行只能內部部署和只能在 Azure。

- Azure 基礎結構路由從內部部署和網際網路的內送流量。

- 稽核外寄流量所需的應用程式。 這通常是管理許多商業系統的需求，並有助於防止公開揭露私人資訊。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示在這種架構的重要元件︰

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是 「 DMZ-私人 」 的頁面上。

[![0]][0]

- **內部部署網路。** 這是一個網路的電腦，連線到私人實作組織中的區域網路的裝置。

- **Azure 虛擬網路 (VNet)。** VNet 主控應用程式，並在雲端中執行的其他資源。

- **閘道器。** 閘道器提供在內部部署網路路由器與 VNet 之間的連線。

- **網路虛擬應用裝置 (NVA)。** NVA 是描述 VM 執行工作，例如允許或拒絕存取為防火牆、 最佳化 WAN 作業 （包括網路壓縮）、 自訂路由]，或其他網路功能的一般術語。 

- **Web 層、 商務層和資料層子網路。** 這些是子網路裝載 Vm 和實作範例 3 層應用程式在雲端中執行的服務。 請參閱[Azure 上 N 層架構執行 Windows Vm] [ra-n-tier]如需詳細資訊。

- **使用者定義的路徑 (UDR)。** [使用者定義的路由][udr-overview]定義內 Azure VNets IP 流量傳送。 

> [AZURE.NOTE] 根據您的 VPN 連線的需求，您可以用其他方法來使用 UDRs 實作將流量導向轉寄規則往 [內部部署網路設定框線閘道器通訊協定 (BGP) 路由。

- **管理子網路。** 子網路包含 Vm 實作管理和監視 VNet 中執行的元件。 

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇，建立您自己參考架構，您應該遵循上述建議，除非您有建議不符合特定需求。

### <a name="rbac-recommendations"></a>RBAC 建議

建立多個 RBAC 角色來管理您的應用程式中的資源。 請考慮建立 DevOps[自訂角色][rbac-custom-roles]具備權限管理應用程式的基礎結構。 請考慮建立集中的 IT 系統管理員[角色自訂][rbac-custom-roles]管理網路資源及個別的安全性 IT 系統管理員[角色自訂][rbac-custom-roles]管理安全的網路資源，例如 NVAs。 

DevOps 角色應該包含部署應用程式元件，以及監視器，並重新啟動 Vm 的權限。 集中式的 IT 系統管理員角色應該包含監控網路資源的權限。 這些都不角色應有存取 NVA 資源，為此應 IT 系統管理員角色的安全性限制。

### <a name="resource-group-recommendations"></a>資源群組的建議

Azure 的資源，例如 Vm VNets，與負載平衡器可以輕鬆地管理群組成一起資源群組。 然後您可以指定上方的角色限制存取的每個資源群組。

我們建議您建立的動作︰

- 資源群組 （不含 Vm） 的子網路、 NSGs，和內部部署網路連線的閘道器資源。 將集中的 IT 系統管理員角色指派給此資源群組。

- 資源群組之 NVAs （包括負載平衡器）、 跳轉] 方塊及其他管理 Vm，以及強制所有流量 NVAs 閘道器子網路 UDR Vm。 將安全性 IT 系統管理員角色指派給此資源群組。

- 分隔每個應用程式層包含負載平衡器和 Vm 的資源群組。 請注意，此資源群組不應包含的每一層的子網路。 DevOps 角色指派給此資源群組。

### <a name="virtual-network-gateway-recommendations"></a>虛擬網路閘道器的建議

內部部署經過 VNet 至虛擬網路閘道器。 我們建議您為[Azure VPN 閘道][guidance-vpn-gateway]或[Azure ExpressRoute 閘道器][guidance-expressroute]。 

### <a name="nva-recommendations"></a>NVA 建議

NVAs 提供不同的服務管理及監視網路流量。 Azure Marketplace 提供數種協力廠商 NVAs，包括︰

- [Barracuda Web 應用程式防火牆][barracuda-waf]和[Barracuda NextGen 防火牆][barracuda-nf]

- [彼此連結之網路 VNS3 防火牆或路由器/VPN][vns3]

- [Fortinet FortiGate VM][fortinet]

- [SecureSphere Web 應用程式防火牆][securesphere]

- [DenyAll Web 應用程式防火牆][denyall]

- [檢查點 vSEC][checkpoint]

如果這些協力廠商 NVAs 都不符合您需求，您可以建立自訂的 NVA 使用 Vm。 建立自訂 NVAs 的範例，請參閱 DMZ 在這種參照架構實作下列功能︰

- 使用[IP 轉接]路由流量[ip-forwarding]NVA Nic 上。

- 允許流量通過 NVA，才可執行此作業。 參考架構中每個 NVA VM 是簡單的 Linux 路由器與送達網路介面*eth0*，和外寄流量相符的規則，透過網路介面*eth1*分派的自訂指令碼所定義的輸入流量。 

- 流量路由傳送至管理子網路並不會通過 NVAs 和 NVAs 只可以管理子網路設定。 管理子網路流量導向，才能透過 NVAs 傳送，如果沒有路由至管理子網路修正 NVAs 如果他們應該會失敗。  

- 為 NVA Vm 會包含在可用性前負載平衡器設定。 在閘道器子網路 UDR 指示負載平衡器 NVA 要求。

其他考量建議連線與執行工作的特定的安全性每個 NVA 數列中的多個 NVAs。 這個選項可讓每個安全性函數，用於每個 NVA 來管理。 例如，實作防火牆 NVA 可將其置於數列中與執行身分識別服務 NVA。 以便於管理缺點是加入的額外的躍可能會增加延遲，因此請確定此不會影響應用程式的效能。

### <a name="nsg-recommendations"></a>NSG 建議

VPN 閘道公開內部部署網路連線的公用 IP 位址。 我們建議您建立網路安全性群組 (NSG) 輸入 NVA 子網路實作規則封鎖所有流量不來自內部部署網路。

我們也建議您執行的每一個子網路 NSGs 提供保護略過設定不正確，或停用 NVA 輸入流量的第二個層級。 例如，參考架構中的網頁層子網路實作略過所有要求以外收到來自內部部署網路 (192.168.0.0/16) 或 VNet，以及另一個規則會忽略不連接埠 80 上所做的所有要求 NSG 的規則。 

### <a name="internet-access-recommendations"></a>網際網路存取建議

[強制通道][azure-forced-tunneling]透過您的內部部署網路使用的網站-VPN 通道及傳送到網際網路時使用的所有輸出網際網路流量網路地址 tranlation (NAT)。 這樣會防止意外洩露任何儲存在您的資料層中的機密資訊，也讓檢查和所有外寄流量的稽核。

> [AZURE.NOTE] 不完全封鎖從網站與商務應用程式層的網際網路流量。 如果這些層使用它們依賴 VM 診斷記錄的公用 IP 位址的 Azure PaaS 服務，請下載的 VM 擴充功能，以及其他功能。 Azure 診斷也需要元件可以讀取及撰寫網際網路相關 Azure 儲存體帳戶。

進一步建議您確認輸出網際網路流量是強制通道正確。 如果您使用的 VPN 連線[路由及遠端存取服務][routing-and-remote-access-service]於內部部署伺服器上使用的工具，例如[WireShark] [wireshark]或[Microsoft 訊息分析](https://www.microsoft.com/en-us/download/details.aspx?id=44226)。

### <a name="management-subnet-recommendations"></a>管理子網路建議

管理子網路包含跳轉] 方塊中，執行管理與監控功能。 實作跳轉] 方塊中的下列建議︰
- 無法建立 [跳轉] 方塊中的公用 IP 位址。 
- 建立一個路由透過內送的閘道器存取跳轉] 方塊，並只允許路由回應要求管理子網路中實作 NSG。
- 限制 [跳轉] 方塊中執行所有安全管理工作。

### <a name="nva-recommendations"></a>NVA 建議

包含 [圖層 7 NVA 終止 NVA 層級的應用程式連線及維護作業以後端層的相關性。 這可以確保對稱的後端層回應流量傳回透過 NVA 的連線。

## <a name="scalability-considerations"></a>延展性考量

參考架構實作負載平衡器導向集區 NVA 裝置的內部部署網路流量。 如前述，NVA 裝置執行網路流量路由規則 Vm 而部署到[可用性設定][availability-set]。 此設計可讓您監控 NVAs 的一段時間，然後新增 NVA 裝置來增加載入回應。

標準的 SKU VPN 閘道支援最多 100 Mbps 持續處理的量。 高的效能 SKU 提供最多 200 Mbps。 更高的頻寬，請考慮升級至 ExpressRoute 閘道器。 ExpressRoute 提供最多 10 個 Gbps 頻寬較低比 VPN 連線的延遲。

> [AZURE.NOTE] [實作與 Azure 及內部部署 VPN 混合式網路架構]的文章[guidance-vpn-gateway]和[實作與 Azure ExpressRoute 的混合式網路架構][guidance-expressroute]說明周圍的 Azure 閘道器的擴充的問題。

## <a name="availability-considerations"></a>可用性考量

參考架構實作負載平衡器發送 NVA 裝置 Azure 中的集區來自內部部署的要求。 NVA 裝置執行網路流量路由規則 Vm 作業，而將[可用性設定]部署[availability-set]。 負載平衡器定期查詢狀況檢查每個 NVA 上執行，並將會移除任何短時間無反應 NVAs 資料庫中。 

如果您使用的 Azure ExpressRoute 提供 VNet 和內部部署網路[設定 VPN 閘道器提供容錯移轉]之間的連線[guidance-vpn-failover]如果 ExpressRoute 連線無法使用。

特定維護 VPN 和 ExpressRoute 連線的可用性的詳細資訊，請參閱[實作與 Azure 及內部部署 VPN 混合式網路架構]的文章[guidance-vpn-gateway]和[實作與 Azure ExpressRoute 的混合式網路架構][guidance-expressroute]。

## <a name="manageability-considerations"></a>管理能力考量

所有應用程式與資源監視應該可執行管理子網路中的 [跳轉] 方塊。 根據您的應用程式的需求，您可能需要新增其他監控資源管理子網路中，但一次存取任何這些額外的資源透過 [跳轉] 方塊。

如果從您的內部部署網路 Azure 的閘道器連線中斷，您仍部署 PIP，從網際網路新增跳轉] 方塊中，並在遠端連絡跳轉] 方塊。

參考架構中的每一層子網路受到 NSG 規則]，並可能需要建立規則，以開啟 Windows Vm RDP 存取的連接埠 3389 或 Linux Vm SSH 存取的連接埠 22。 其他管理及監視工具可能需要開啟其他連接埠的規則。

如果您使用 ExpressRoute 可提供您的內部部署資料中心和 Azure 之間的連線，請使用[Azure 連線工具組 (AzureCT)] [azurect]監控和疑難排解連線問題。

> [AZURE.NOTE] 您可以找到其他資訊，特別是針對監控和管理文章[實作與 Azure 及內部部署 VPN 混合式網路架構]中的 VPN 及 ExpressRoute 連線[guidance-vpn-gateway]和[實作與 Azure ExpressRoute 的混合式網路架構][guidance-expressroute]。

## <a name="security-considerations"></a>安全性考量

此參考架構實作多個安全性層級︰ 

### <a name="routing-all-on-premises-user-requests-through-the-nva"></a>路由 NVA 到所有的內部部署使用者要求

在閘道器子網路 UDR 封鎖以外收到來自內部部署的所有使用者要求。 UDR 階段允許 NVAs 要求在私人 DMZ 子網路，且這些要求傳遞至應用程式如果 NVA 規則允許。 其他路由可以新增至 UDR，但確保他們不 NVAs 或區塊管理流量給管理子網路不慎略過。

負載平衡器前面 NVAs 也做為安全性裝置，並忽略未開啟負載平衡規則中的連接埠的流量。 參考架構中的負載平衡器只接聽 HTTP 要求連接埠 80 和連接埠 443 的 HTTPS 要求。 必須記錄新增至負載平衡器任何其他規則，而且應該監視流量，以確保沒有任何安全性問題。

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>封鎖/行程流量應用程式層之間使用 NSGs

每個網頁、 商業及資料層限制它們之間的資料傳輸使用 NSGs。 也就是商務層使用 NSG 封鎖所有流量的不是在網頁層，且資料層使用 NSG 封鎖所有不是在商務層的流量。 如果您有以展開 NSG 規則，允許更廣泛存取這些層需求，權衡這些需求，針對安全性風險。 每個新的連入的路徑代表意外或故意資料外洩或應用程式損毀的商機。

### <a name="devops-access"></a>DevOps 存取

限制 DevOps 可使用[RBAC]每一個層級執行的作業[rbac]可用來管理權限。 授與權限時, 使用的[最低權限原則][security-principle-of-least-privilege]。 登入的所有管理操作，並執行定期稽核，以確保已規劃在變更任何設定。

> [AZURE.NOTE] 如果更詳細的資訊、 範例和案例管理網路證券 Azure，請參閱[Microsoft 雲端服務和網路安全性][cloud-services-network-security]。 如需詳細瞭解如何保護雲端中的資源的詳細資訊，請參閱[快速入門 Microsoft Azure 安全性][getting-started-with-azure-security]。 如透過 Azure 閘道器連線，處理安全性問題的其他詳細資料，請參閱[執行混合式網路架構與 Azure 及內部部署 VPN] [guidance-vpn-gateway]和[實作與 Azure ExpressRoute 的混合式網路架構][guidance-expressroute]。

## <a name="solution-deployment"></a>解決方案部署

實作這些建議參照結構的部署會提供 Github。 此參考架構包括虛擬網路 (VNet)、 網路安全性群組 (NSG)、 負載平衡器，以及兩個虛擬機器 (Vm)。

參考架構可以使用 Windows 或 Linux Vm 部署按照下列指示︰ 

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet%2Fazuredeploy.json)

2. 一旦連結已開啟 Azure 入口網站中，您必須輸入值的部分設定︰ 
    - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-private-dmz-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

4. 參數檔案包含的所有 Vm 的硬式編碼的系統管理員使用者名稱和密碼，然後強烈建議您立即變更兩者]。 針對每個 VM 部署中，在 Azure 入口網站中加以選取，然後按一下**重設密碼****支援 + 疑難排解**刀中。 在 [**模式**] 下拉式清單方塊中，選取 [**重設密碼**，然後選取新的**使用者名稱**和**密碼**。 按一下 [**更新**] 按鈕，若要保存。

## <a name="next-steps"></a>後續步驟

- 瞭解如何實作[DMZ Azure 與網際網路之間](./guidance-iaas-ra-secure-vnet-dmz.md)。
- 瞭解如何實作[高度可用的混合式網路架構](./guidance-hybrid-network-expressroute-vpn-failover.md)。

<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-create-availability-set.md
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[azure-forced-tunneling]: https://azure.microsoft.com/en-gb/documentation/articles/vpn-gateway-forced-tunneling-rm/
[barracuda-nf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/barracuda-ng-firewall/
[barracuda-waf]: https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/
[checkpoint]: https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/
[cloud-services-network-security]: https://azure.microsoft.com/documentation/articles/best-practices-network-security/
[denyall]: https://azure.microsoft.com/marketplace/partners/denyall/denyall-web-application-firewall/
[fortinet]: https://azure.microsoft.com/marketplace/partners/fortinet/fortinet-fortigate-singlevmfortigate-singlevm/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[ip-forwarding]: ../virtual-network/virtual-networks-udr-overview.md#IP-forwarding
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[ra-n-tier]: ./guidance-compute-n-tier-vm.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[rbac-custom-roles]: ../active-directory/role-based-access-control-custom-roles.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[routing-and-remote-access-service]: https://technet.microsoft.com/library/dd469790(v=ws.11).aspx
[securesphere]: https://azure.microsoft.com/marketplace/partners/imperva/securesphere-waf-for-azr/
[security-principle-of-least-privilege]: https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vns3]: https://azure.microsoft.com/marketplace/partners/cohesive/cohesiveft-vns3-for-azure/
[wireshark]: https://www.wireshark.org/
[0]: ./media/blueprints/hybrid-network-secure-vnet.png "安全的混合式網路架構"
