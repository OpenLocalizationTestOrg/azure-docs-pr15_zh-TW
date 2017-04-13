<properties 
   pageTitle="關於 VPN 閘道設定的虛擬網路閘道器 |Microsoft Azure"
   description="深入了解 Azure 虛擬網路 VPN 閘道器設定。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>關於 VPN 閘道器設定

VPN 閘道器連線解決方案依賴的多個資源設定，才能傳送虛擬網路之間的網路流量和內部部署的位置。 每個資源包含可設定的設定。 資源與設定的組合決定連線結果。

本文中的各節討論的資源和關聯到 VPN 閘道器在**資源管理員**部署模型中的設定。 您可能會發現使用連線拓撲圖表，檢視可用的設定，還是有幫助。 您可以找到的描述與拓撲圖表的[VPN 閘道器的相關](vpn-gateway-about-vpngateways.md)文件中每個連線的方案。 

## <a name="gwtype"></a>閘道器類型

每個虛擬網路只能有一個的每一種類型的虛擬網路閘道。 當您建立一個虛擬網路閘道時，您必須確定閘道器類型是正確的設定。

可用的-GatewayType 值是︰ 

- Vpn
- ExpressRoute

閘道需要`-GatewayType` *Vpn*。  

範例︰

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>閘道器 Sku


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>SKU 的閘道器設定

**Azure 入口網站中指定的閘道器 SKU**

如果您使用 Azure 入口網站建立資源管理員虛擬網路閘道器時，您可以使用下拉式清單中選取 SKU 的閘道器。 您會看到的選項會對應到的閘道器類型和您所選取的 VPN 類型。

例如，如果您選取的閘道器類型 'VPN 」，而且 VPN ' 原則型 」，您看到 '基本 」 的 SKU 的不只適用於 PolicyBased Vpn 的 SKU，因此。 如果您選取 「 傳送為基礎 」，您可以選取從基本、 標準和 HighPerformance sku 皆可。 


**指定的閘道器 SKU 使用 PowerShell**


下列 PowerShell 範例指定`-GatewaySku`為*標準*。

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**變更閘道器 SKU**

如果您想要將閘道 SKU 升級為功能更強大的 SKU，（從基本/標準以 HighPerformance） 您可以使用`Resize-AzureRmVirtualNetworkGateway`PowerShell 指令程式。 您也可以降級閘道器 SKU 大小使用這個指令程式。

下列 PowerShell 範例 SKU 至 HighPerformance 正在調整大小的閘道器。

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>估計閘道器 SKU 的彙總處理量]，然後輸入

下表顯示的閘道器類型及估計的彙總處理量。 此表格適用於資源管理員] 及 [傳統部署模型。

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>連接類型

資源管理員部署模型中每個設定會需要特定的虛擬網路閘道器連線類型。 使用資源管理員 PowerShell 值`-ConnectionType`是︰

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

在下列 PowerShell 範例中，我們會建立所需的連線類型*IPsec*S2S 連線。

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>VPN 類型

當您建立 VPN 閘道設定的虛擬網路閘道時，您必須指定 VPN 類型。 您選擇要有 vpn 才能類型取決於您想要建立連線拓撲。 例如，P2S 連線需要 RouteBased VPN 類型。 VPN 類型也取決於您要使用的硬體。 S2S 設定需要 VPN 裝置。 某些 VPN 裝置僅支援特定 VPN 類型。

選取的 VPN 類型必須符合所有連線解決方案的需求您想要建立。 比方說，如果您想要建立 S2S VPN 閘道器連線和相同的虛擬網路 P2S VPN 閘道器連線，您會使用 VPN 類型*RouteBased*因為 P2S 需要 RouteBased VPN 類型。 您也必須驗證 VPN 裝置支援 RouteBased VPN 連線。 

只要建立一個虛擬網路閘道之後，您無法變更 VPN 類型。 您需要刪除虛擬網路閘道器，並建立一個新。 有兩種 VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


下列 PowerShell 範例指定`-VpnType`為*RouteBased*。 當您建立閘道器時，您必須確定-VpnType 是正確的設定。 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>閘道器需求

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>閘道器子網路

若要設定虛擬網路閘道器，您必須為您 VNet 建立閘道器子網路。 閘道器子網路必須命名*GatewaySubnet*才能正常運作。 此名稱可讓 Azure 知道子網路適用於閘道器。

閘道器子網路的最小完全取決於您想要建立的設定。 雖然您可以建立閘道器子網路 /29 小，我們建議您建立閘道器子 /28 或更大 (/ 28 /27、 /26，等等。)。 

建立較大的閘道器可讓您執行閘道器的大小限制。 例如，您可能會閘道器子網路大小 /29 S2S 連線建立虛擬網路閘道器。 您現在想来設定 S2S/ExpressRoute 共存設定。 該設定需要閘道器子網路最小的大小 /28。 若要建立您的設定，您必須修改閘道器子網路，以納入其間的連線，也就是 /28 的最低需求。

下列資源管理員 PowerShell 範例名為 GatewaySubnet 閘道器子網路。 您可以看到 CIDR 標記法指定 /27，可以針對大部分的設定現有足夠 IP 位址。

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>區域網路閘道器

在建立 VPN 閘道器設定時，區域網路閘道器通常表示您內部部署的位置。 傳統的部署模型中的區域網路閘道器已稱為本機的網站。 

您提供的區域網路閘道器名稱，內部部署 VPN 裝置的公用 IP 位址，並指定內部部署的位置上的地址前置字元。 Azure 網路流量的目的地位址首碼、 查閱的設定，您已指定的區域網路閘道，並根據這一點來路由傳送封包。 您也可以指定 VNet-VNet 設定使用 VPN 閘道器連線的區域網路閘道器。

在下列 PowerShell 中，建立新的區域網路閘道器︰

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

有時候，您需要修改區域網路閘道器設定。 例如，當您新增或修改位址範圍，或如果要變更的 VPN 裝置的 IP 位址。 為傳統的 VNet，您可以變更這些設定在 [傳統] 入口網站在本機的網路] 頁面。 為資源管理員，請參閱[使用 PowerShell 修改區域網路閘道器設定](vpn-gateway-modify-local-network-gateway.md)。

## <a name="resources"></a>REST Api 和 PowerShell cmdlet

如需其他技術資源與特定的語法需求使用 REST Api 及 PowerShell cmdlet VPN 閘道器設定時，請參閱下列頁面︰

|**傳統** | **資源管理員**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>後續步驟

可用的連線設定的相關資訊，請參閱[關於 VPN 閘道器](vpn-gateway-about-vpngateways.md)。 







 
