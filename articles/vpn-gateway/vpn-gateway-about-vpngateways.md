<properties 
   pageTitle="關於 VPN 閘道 |Microsoft Azure"
   description="深入了解 VPN 閘道器的 Azure 虛擬網路的連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway"></a>關於 VPN 閘道器


虛擬網路閘道器用來傳送 Azure 虛擬網路與內部部署位置之間和 Azure (VNet-VNet) 內的虛擬網路之間的網路流量。 當您設定 VPN 閘道器時，您必須建立並設定虛擬網路閘道器與虛擬網路閘道器連線。

資源管理員部署模型中，當您建立虛擬網路閘道器資源，您指定數個設定。 需要設定的其中一個是 「-GatewayType 」。 有兩種虛擬網路閘道器︰ Vpn 及 ExpressRoute。 

當網路流量傳送專用的私人連線時，您會使用閘道器類型 'ExpressRoute'。 這也稱為 ExpressRoute 閘道器。 網路流量傳送時加密跨公用連線，您會使用閘道器類型 'Vpn 」。 這被指 VPN 閘道器。 為網站網站為網站點及 VNet-VNet 連線所有使用 VPN 閘道器。

每個虛擬網路可能只有一個虛擬網路閘道器，每個閘道器類型。 例如，您可以讓虛擬網路使用閘道器之-GatewayType ExpressRoute，並使用-GatewayType Vpn。 本文主要是解釋 VPN 閘道器。 如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## <a name="pricing"></a>價格

[AZURE.INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)] 


## <a name="gateway-skus"></a>閘道器 Sku

[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

如需有關閘道器 Sku 的詳細資訊，請參閱[閘道器 sku 皆可](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

### <a name="estimated-aggregate-throughput-by-sku"></a>估計的 SKU 的彙總處理量

下表顯示的閘道器類型及估計的彙總處理量。 此表格適用於資源管理員] 及 [傳統部署模型。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="configuring-a-vpn-gateway"></a>VPN 閘道器設定

當您設定 VPN 閘道器時，您使用的指示，取決於您用來建立虛擬網路部署模型。 比方說，如果您建立使用傳統的部署模型您 VNet，您使用準則及傳統部署模型的指示建立及設定您要有 vpn 才能的閘道器設定。 如需有關部署模型的詳細資訊，請參閱[瞭解資源管理員與傳統部署模型](../resource-manager-deployment-model.md)。

VPN 閘道器連線依賴的設定特定值的多個資源。 雖然必須設定特定順序在某些情況下，您可以分別設定大部分的資源。 您可以開始建立及設定使用一個設定工具，例如 Azure 入口網站的資源。 您可以再稍後決定要切換至另一個工具，例如 PowerShell，若要設定其他的資源，或修改現有的資源時適用。 目前您無法在 Azure 入口網站設定每個資源與資源設定。 針對每個連線拓撲文章中的指示指定時，需要特定組態工具。 個別的資源和 VPN 閘道設定的相關資訊，請參閱[關於 VPN 閘道器設定](vpn-gateway-about-vpn-gateway-settings.md)。

下列各節包含] 清單中的表格︰

- 可用的部署模型
- 可用的設定工具
- 讓您直接文章，如果使用的連結

使用圖表和描述協助選取連線拓撲，以符合您的需求。 圖表顯示主要的比較基準拓撲，但可以建立更複雜的設定為指導方針，使用圖表。

## <a name="site-to-site-and-multi-site"></a>網站為網站與多個網站

### <a name="site-to-site"></a>若要網站

網站的網站 」 (S2S) VPN 閘道器連線是透過 IPsec/IKE （IKEv1 或 IKEv2） VPN 通道的連線。 此連線類型需要 VPN 裝置位於內部部署的已指派的公用 IP 位址，並不會位於 nat S2S 連線可跨內部部署與混合式設定。   

![S2S 連線](./media/vpn-gateway-about-vpngateways/demos2s.png "若要網站")


### <a name="multi-site"></a>多網站

您可以建立並設定您的 VNet 與多個內部部署網路之間的 VPN 閘道器連線。 使用多個連線工作時，您必須使用 RouteBased VPN 類型 （傳統 VNets 動態閘道）。 因為 VNet 只能有一個 VPN 閘道，透過閘道器的所有連線都共用可用的頻寬。 這通常稱為 「 多的網站 」 連線。
 

![多個網站的連線](./media/vpn-gateway-about-vpngateways/demomulti.png "多網站")

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>部署模型和網站的網站] 及 [多網站的方法

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## <a name="vnet-to-vnet"></a>VNet-VNet

虛擬網路連線到另一個虛擬網路 (VNet-VNet) 很類似 VNet 連線至內部部署網站位置。 這兩種連線類型使用 VPN 閘道器提供使用 IPsec/IKE 的安全通道。 您甚至可以結合 VNet-VNet 通訊的多網站的連線設定。 這個選項可讓您建立的網路拓撲結合跨內部部署連線虛擬間的網路連線。

您連線 VNets 可以是︰

- 在相同或不同區域內
- 在相同或不同的訂閱 
- 在相同的不同的部署模型


![VNet 到 VNet 連線](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-vnet")

#### <a name="connections-between-deployment-models"></a>部署模型之間的連線

Azure 目前有兩種部署模型︰ 傳統和資源管理員。 如果您使用 Azure 段時間，您可能有 Azure Vm 以及在傳統的 VNet 中執行的執行個體角色。 資源管理員中建立 VNet 可能會執行您的較新的 Vm 和角色執行個體。 您可以建立一個 VNet 直接與另一個資源通訊中允許資源 VNets 之間的連線。

#### <a name="vnet-peering"></a>對等 VNet

您可以使用 VNet 對等來建立您的連線，只要虛擬網路符合特定需求。 對等 VNet 並不會使用虛擬網路閘道器。 如需詳細資訊，請參閱[VNet 對等](../virtual-network/virtual-network-peering-overview.md)。


### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>部署模型和 VNet-VNet 的方法

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## <a name="point-to-site"></a>點-網站

點網站 (P2S) VPN 閘道器連線，可讓您建立虛擬網路個別的用戶端電腦從安全的連線。 P2S SSTP （安全通訊端通道通訊協定） 是 VPN 連線。 P2S 連線不需要 VPN 裝置或-在公開的 IP 位址搭配使用。 您可以從用戶端電腦，以建立 VPN 連線。 當您要連線到您 VNet 從遠端位置，例如從家用版或會議，或您只需要幾個需要連線到 VNet 的用戶端，此方案相當實用。 P2S 連線可搭配 S2S 連線到相同的 VPN 閘道器，但的兩個連線的設定需求都相容。


![點-網站連線](./media/vpn-gateway-about-vpngateways/demop2s.png "點-網站")

### <a name="deployment-models-and-methods-for-point-to-site"></a>部署模型和點-網站的方法

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## <a name="expressroute"></a>ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

在 ExpressRoute 連接虛擬網路閘道器設定是 「 ExpressRoute 」，而不是 「 Vpn 」 的閘道器類型。 如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。


## <a name="site-to-site-and-expressroute-coexisting-connections"></a>網站的網站和 ExpressRoute 共存的連線

ExpressRoute 是 Microsoft 服務，包括 Azure 從您 WAN （不是透過公用網際網路） 直接的專用連線。 加密網際網路的公用網站-VPN 流量達成。 也可以設定網站-VPN 及 ExpressRoute 相同的虛擬網路連線有幾項優點。

您可以設定為安全的容錯移轉路徑至網站 VPN ExpressRoute，或使用網站-Vpn 連線至不是您的網路的部分，但透過 ExpressRoute 連線的網站。 請注意，這需要兩個虛擬網路閘道器的相同的虛擬網路，-GatewayType Vpn，並使用-GatewayType ExpressRoute 另一個使用。


![Coexist 連線](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute site2site")


### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>部署模型和 S2S 和 ExpressRoute 的方法

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## <a name="next-steps"></a>後續步驟

規劃您 VPN 閘道器設定。 請參閱[VPN 閘道器規劃及設計](vpn-gateway-plan-design.md)和[Azure 您內部網路的連線](../guidance/guidance-connecting-your-on-premises-network-to-azure.md)。








 
