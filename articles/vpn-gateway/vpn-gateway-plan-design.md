<properties 
   pageTitle="VPN 閘道規劃及設計 |Microsoft Azure"
   description="深入了解 VPN 閘道規劃以及用於跨內部部署與混合式部署，VNet-VNet 連線的設計"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc"/>

# <a name="planning-and-design-for-vpn-gateway"></a>規劃及設計 VPN 閘道器

規劃及設計跨內部部署和 VNet-VNet 設定可以簡單或複雜，視您的網路需求而定。 本文會引導您基本的規劃及設計考量。

## <a name="planning"></a>規劃


### <a name="compare"></a>跨內部部署連線選項

如果您想要安全地虛擬網路連線您內部部署的網站，您有三種不同的方式，若要這麼做︰ 為網站網站為網站點和 ExpressRoute。 比較不同的跨內部部署連線，可在。 您選擇的選項可以取決於各種因素，例如︰


- 您的方案需要哪一類的處理量？
- 您要通訊安全 VPN，透過網際網路的公用或私人連線嗎？
- 您有可用的公用 IP 位址嗎？
- 您打算使用 VPN 裝置？ 如果是這樣，是否相容？
- 您連線在幾部的電腦，或您想要持續連接您的網站？
- 何種類型的 VPN 閘道器的您想要建立的解決方案的必要條件為何？
- 您應該使用哪一個閘道器 SKU？


下表可以協助您決定您的方案的最佳連線選項。


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>根據 VPN 類型和 SKU 的閘道器需求

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

如需有關閘道器 Sku 的詳細資訊，請參閱[VPN 閘道器設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。

#### <a name="aggregate-throughput-by-sku-and-vpn-type"></a>依 SKU 及 VPN 類型的彙總處理量

下表顯示的閘道器類型及估計的彙總處理量。 估計的彙總處理量可能是您的設計的決策因素。
價格會閘道器 Sku 之間的不同。 價格的相關資訊，請參閱[VPN 閘道器價格](https://azure.microsoft.com/pricing/details/vpn-gateway/)。 此表格適用於資源管理員] 及 [傳統部署模型。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

#### <a name="supported-configurations-by-sku-and-vpn-type"></a>依 SKU 及 VPN 類型支援的設定

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 

### <a name="wf"></a>工作流程

下列清單列出雲端連線的常見工作流程︰

1.  設計和規劃您的連線拓撲並] 清單中的所有您要連線的網路位址空間。
2.  建立 Azure 虛擬網路。 
3.  建立 VPN 閘道器的虛擬網路。
4.  建立及設定內部網路或其他虛擬網路的連線 （如有需要）。
5.  建立及設定點為網站連線 Azure VPN 閘道 （如有需要）。
 

## <a name="design"></a>設計

### <a name="topologies"></a>連線拓撲

首先請查看[VPN 閘道器的相關](vpn-gateway-about-vpngateways.md)文件中的圖表。 本文包含基本圖表，每個拓撲 （資源管理員或傳統） 的部署模型和哪些部署工具，您可以用來部署您的設定。   

### <a name="designbasics"></a>設計的基本概念

下列各節討論 VPN 閘道器的基本概念。 此外，請考慮[網路服務限制](../articles/azure-subscription-service-limits.md#networking-limits)。


#### <a name="subnets"></a>關於子網路

當您建立的連線時，您必須考慮您子網路範圍。 您不能有重疊的子網路位址範圍。 重疊的子網路時，一個虛擬網路或內部部署的位置包含的其他位置含有相同地址空間。 這表示您需要針對您的本機內部部署網路您網路工程師，設想供您使用的您 Azure 的 IP 範圍處理空間/子網路。 您需要本機內部部署網路未使用的位址空間。 

避免重疊子網路也很重要您正在使用 VNet-VNet 連線時。 如果您子網路重疊，並在傳送和目的地 VNets 存在的 IP 位址，VNet-VNet 連線失敗。 Azure 無法將資料傳送至其他 VNet，因為目的地地址傳送 VNet 的一部分。 

VPN 閘道需要特定的子網路，稱為 [閘道器子網路。 閘道器的所有子網路必須命名 GatewaySubnet 才能正常運作。 請確定未命名閘道器子網路不同的名稱，並不部署 Vm 或其他項目至閘道器子網路。 請參閱[閘道器子網路](vpn-gateway-about-vpn-gateway-settings.md#gwsub)。

#### <a name="local"></a>關於區域網路閘道器

區域網路閘道器通常是指您內部部署的位置。 傳統的部署模型中的區域網路閘道器被指在本機的網路網站。 當您設定的區域網路的閘道器時，您為其命名、 指定公用 IP 位址的內部部署 VPN 裝置，並指定地址前置詞的內部部署的位置。 Azure 網路流量的目的地位址首碼、 查閱設定所指定的區域網路閘道器，並根據這一點來路由傳送封包。 如有需要您可以修改這些地址前置詞。 如需詳細資訊，請參閱[區域網路閘道器](vpn-gateway-about-vpn-gateway-settings.md#lng)。


#### <a name="gwtype"></a>閘道器類型

選取您的拓撲的正確的閘道器類型很重要。 如果您選取類型錯誤，您的閘道器無法正常運作。 閘道器類型指定如何本身的閘道器連線資源管理員部署模型的必要的組態設定。

閘道器類型如下︰

- Vpn
- ExpressRoute

#### <a name="connectiontype"></a>有關連線類型

每個設定需要為特定的連接類型。 連線類型如下︰

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>關於 VPN 類型

每個設定需要特定 VPN 類型。 如果您結合兩個設定，例如建立網站-連線和點為網站連線至相同的 VNet，您必須使用 VPN 類型滿足兩個連線的需求。

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)] 

下表顯示 VPN 類型，為它對應至每個連線的設定。 請確定您的閘道器的 VPN 類型符合您想要建立的設定。 


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)] 

### <a name="devices"></a>VPN 裝置網站的連線

若要設定網站的連線，不論部署模型，您需要下列項目︰

- 相容於 Azure VPN 閘道 VPN 裝置
- 不 nat-在公開 IPv4 IP 位址

您需要有體驗設定您要有 vpn 才能的裝置，或擁有人可以為您設定裝置。 如要有 vpn 才能和裝置有關的詳細資訊，請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 VPN 裝置本文包含已驗證的裝置、 需求尚未驗證的裝置和裝置設定文件如果提供的連結資訊。

### <a name="forcedtunnel"></a>請考慮強制的通道路由

大部分的設定，您可以設定強制通道。 強制通道可讓您重新導向或 「 強制 」 的所有網際網路繫結流量透過檢查及稽核網站-VPN 通道都返回您內部部署的位置。 這是大部分的企業 IT 重要的安全性需求原則。 

不強制通道，從您的 Vm Azure 中的網際網路繫結流量會永遠從 Azure 網路基礎結構直接出往返至網際網路，而不允許您檢查或稽核流量的選項。 未經授權的存取網際網路可能會導致資訊公開或其他類型的安全性漏洞。

**強制通道的圖表**

![強制通道連線](./media/vpn-gateway-plan-design/forced-tunnel.png "強制通道")

在兩種部署模型以及使用不同的工具，您可以設定強制通道連線。 請參閱下表中的詳細資訊。 為新的文章，新的部署模型和其他工具會變成適用於此設定，我們就會更新此表格。 使用文件時，我們直接從連結資料表。

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 



## <a name="next-steps"></a>後續步驟

請參閱[VPN 閘道器常見問題集](vpn-gateway-vpn-faq.md)與[VPN 閘道器的相關](vpn-gateway-about-vpngateways.md)文件的詳細資訊，以協助您與您的設計。

如需有關特定閘道設定的詳細資訊，請參閱[關於 VPN 閘道器設定](vpn-gateway-about-vpn-gateway-settings.md)。




