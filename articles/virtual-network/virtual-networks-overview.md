<properties
   pageTitle="Azure 虛擬網路 (VNet) 概觀"
   description="深入了解 Azure 虛擬網路 (VNets)。"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="virtual-network-overview"></a>虛擬網路概觀

Azure 虛擬網路 (VNet) 是在雲端網路的表示。  這是專屬於您的訂閱 Azure 雲端邏輯隔離。 您也可以完全控制 IP 地址區塊、 DNS 設定、 安全性原則，以及傳送這個網路中的資料表。 您可以進一步也您 VNet 劃分子網路，並啟動 Azure IaaS 虛擬機器 (Vm) 及/或[雲端服務 （PaaS 角色執行個體）](../cloud-services/cloud-services-choose-me.md)。 此外，您也可以使用其中一個可用的[連線選項](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)Azure 中您內部部署網路連線虛擬網路。 實際上，您可以展開至 [Azure，您的網路完成上的控制項與企業規模 Azure 提供的好處 IP 地址區塊。

若要進一步瞭解 VNets，看看圖，其中顯示簡化內部部署網路。

![內部部署網路](./media/virtual-networks-overview/figure01.png)

上述圖透過路由器公用網際網路連線的內部部署網路。 您也可以查看路由器與 DMZ 主控 DNS 伺服器] 及 [web 伺服器陣列之間的防火牆。 網頁伺服器陣列是負載平衡硬體負載平衡器，公開至網際網路，並會耗費資源從內部子網路。 內部子網路是以其他的防火牆，與主機 Active Directory 網域控制站伺服器、 資料庫伺服器和應用程式伺服器分隔 DMZ。

下圖所示，可以 Azure 中裝載相同的網路。

![Azure 虛擬網路](./media/virtual-networks-overview/figure02.png)

請注意，如何 Azure 基礎結構所需的時間路由器，從您 VNet 允許存取，而不需要任何設定公用網際網路的角色。 防火牆可以取代網路安全性群組 (NSGs) 套用至每個個別的子網路。 與網際網路具和內部負載平衡器，Azure 中的替代實體負載平衡器。

>[AZURE.NOTE] Azure 中有兩種部署模式︰ 傳統 （也稱為服務管理） 和 Azure 資源管理員 (ARM)。 傳統 VNets 無法新增至相關性] 群組中，或建立地區的 VNet。 如果您有 VNet 相關性群組時，建議[移轉以地區的 VNet](virtual-networks-migrate-to-regional-vnet.md)。

## <a name="virtual-network-benefits"></a>虛擬網路優點

- **隔離**。 VNets 是從另一個完全隔離。 可讓您建立的開發、 測試和生產不相鄰使用相同的 CIDR 地址區塊的網路。

- **存取公用網際網路**。 根據預設，VNet 中的所有 IaaS Vm 和 PaaS 角色執行個體可以存取公用網際網路。 您可以使用網路安全性群組 (NSGs) 來控制存取權。

- **存取 Vm VNet 內**。 在相同的虛擬網路，則可以啟動 PaaS 角色執行個體及 IaaS Vm，他們可以連線至其他使用私用的 IP 位址，即使是在不同的子網路，而不需要設定閘道器，或使用的公用 IP 位址。

- **名稱解析**。 Azure 提供內部名稱解析 IaaS Vm 和部署在您 VNet PaaS 角色執行個體。 您也可以部署您自己的 DNS 伺服器，並設定 VNet 的使用方式。

- **安全性**。 使用網路安全性群組可以控制流量輸入及結束的虛擬機器及 PaaS VNet 中的角色執行個體。

- **連線**。 使用網路閘道器或 VNet 對等彼此 VNets 可連線。 VNets 可以連接到內部部署資料中心網站-VPN 網路，或是 Azure ExpressRoute。 若要進一步瞭解網站-VPN 連線，請造訪[相關 VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)。 若要進一步瞭解 ExpressRoute，請造訪[ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。 若要進一步瞭解 VNet 對等，請造訪[VNet 對等](virtual-network-peering-overview.md)。

    >[AZURE.NOTE] 請確定您部署任何 IaaS Vm 或 PaaS 角色執行個體 Azure 環境之前，先建立 VNet。 ARM Vm 需要 VNet，，如果您沒有指定現有的 VNet，Azure 建立預設 VNet 可能會有與內部部署網路 CIDR 地址區塊衝突。 讓您無法將您 VNet 連線至您的內部部署網路。

## <a name="subnets"></a>子網路

子網路的 IP 位址範圍中 VNet，您可以將 VNet 分割成多個子網路，組織及安全性。 Vm 和 PaaS 角色執行個體部署至內子網路 （相同或不同） VNet 可以彼此通訊，而不需要任何額外的設定。 您也可以設定路由資料表和 NSGs 子網路。

## <a name="ip-addresses"></a>IP 位址


有兩種類型的 Azure 中分派給資源的 IP 位址︰*公用*與*私人*。 公用 IP 位址允許 Azure 資源進行通訊的網際網路和[Azure Redis 快取](https://azure.microsoft.com/services/cache/)， [Azure 事件集線器](https://azure.microsoft.com/documentation/services/event-hubs/)等其他 Azure-在公開服務。 私人 IP 位址可讓虛擬網路，以及透過 VPN 連線中的資源之間的通訊，而不使用網際網路舉例來說的 IP 位址。

若要進一步瞭解 Azure 中的 IP 位址，請造訪[虛擬網路中的 IP 位址](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Azure 負載平衡器

可以使用 Azure 負載平衡器的網際網路公開虛擬機器和虛擬網路中的雲端服務。 僅限商務應用程式的內部公開可以負載平衡使用內部負載平衡器。

- **外部負載平衡器**。 您可以使用外部負載平衡器，提供高可用性 IaaS Vm 和 PaaS 角色執行個體，從公用網際網路存取。

- **內部負載平衡器**。 您可以使用內部負載平衡器，提供高可用性 IaaS Vm 及其他服務中您 VNet 存取 PaaS 角色執行個體。

若要進一步瞭解負載平衡 Azure 中，請造訪[負載平衡器概觀](../load-balancer/load-balancer-overview.md)。

## <a name="network-security-group-nsg"></a>網路安全性群組 (NSG)

您可以建立控制傳入和傳出網路介面 (Nic)，存取 NSGs Vm 和子網路。 每個 NSG 包含一或多個規則，以指定已核准或拒絕傳輸根據來源 IP 位址、 來源連接埠、 目的地 IP 位址，與目的地連接埠。 若要進一步瞭解 NSGs 」 的資訊，請造訪[什麼是網路安全性群組](virtual-networks-nsg.md)。

## <a name="virtual-appliances"></a>虛擬裝置

虛擬應用裝置是在執行軟體基礎應用裝置函數，例如防火牆、 WAN 最佳化或侵入偵測您 VNet 只是另一個 VM。 您可以將您 VNet 流量路由若要使用其功能的虛擬應用裝置透過 Azure 中建立路由。

例如，NSGs 可以用於提供您 VNet 安全性。 不過，NSGs 提供層 4 存取控制清單 (ACL) 傳入與傳出的封包。 如果您想要使用的圖層 7 安全性模型，您需要使用防火牆應用裝置。

虛擬就[使用者定義的路徑及 IP 轉接](virtual-networks-udr-overview.md)而定。

## <a name="limits"></a>限制
在訂閱中允許的虛擬網路數目的限制，如需詳細資訊請參閱[Azure 網路限制](../azure-subscription-service-limits.md#networking-limits)。

## <a name="pricing"></a>價格
那里是不額外成本 Azure 中使用虛擬網路。 計算執行個體中 Vnet 啟動需要付費的標準工資率[Azure VM 價格](https://azure.microsoft.com/pricing/details/virtual-machines/)所述。 [VPN 閘道器](https://azure.microsoft.com/pricing/details/vpn-gateway/)及 [公用 IP 位址] (https://azure.microsoft.com/pricing/details/ip-addresses/) 用於 VNet 也會充電的標準工資率。

## <a name="next-steps"></a>後續步驟

- [建立 VNet](virtual-networks-create-vnet-arm-pportal.md)和子網路。
- [建立在 VNet VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。
- 深入了解[NSGs](virtual-networks-nsg.md)。
- 深入瞭解[使用者定義的路徑及 IP 轉寄](virtual-networks-udr-overview.md)。
