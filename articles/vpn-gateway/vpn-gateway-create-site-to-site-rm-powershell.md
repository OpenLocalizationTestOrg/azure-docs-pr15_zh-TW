<properties
   pageTitle="建立與使用 Azure 資源管理員] 及 [PowerShell 網站-VPN 連線的虛擬網路 |Microsoft Azure"
   description="本文將引導您建立 VNet 使用資源管理員部署模型，並連接至本機內部部署網路使用 S2S VPN 閘道器連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>使用 PowerShell 網站-連線建立 VNet

> [AZURE.SELECTOR]
- [資源管理員-Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [資源管理員-PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [傳統-傳統入口網站](vpn-gateway-site-to-site-create.md)

本文會引導您建立虛擬網路和網站-VPN 閘道器連線到內部部署網路使用 Azure 資源管理員部署模型。 跨內部部署與混合式用於網站的連線設定。

![網站為網站圖表](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "若要網站") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>部署模型和網站的連線的方法

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

下表顯示目前可用的部署模型和網站的網站設定的方法。 當有包含設定步驟的文章時，我們連結直接從這個資料表。 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>其他設定

如果您要放在一起，連線 VNets，但不是建立連線到內部部署的位置，請參閱[設定 VNet-VNet 連線](vpn-gateway-vnet-vnet-rm-ps.md)。 如果您想要新增至網站連線至 VNet 已有連線，請參閱[新增現有的 VPN 閘道器連線 VNet S2S 連線](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。

## <a name="before-you-begin"></a>開始之前

確認您有下列項目，才能開始設定。

- 相容的 VPN 裝置，可以設定讓它的人。 請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果您不熟悉設定您要有 vpn 才能的裝置，或不熟悉 IP 位址範圍位於您的內部部署網路設定，您需要進行共同作業的人可以為您提供這些詳細資料。

- 對外公開 IP 位址 VPN 裝置。 Nat 找不到這個 IP 位址
    
- Azure 的訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。
    
- Azure 資源管理員 PowerShell cmdlet 最新版本。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。


## <a name="Login"></a>1.連線至您的訂閱 

請確定您切換到 PowerShell 模式，來使用資源管理員 cmdlet。 如需詳細資訊，請參閱[使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

開啟您 PowerShell 主控台，連線到您的帳戶。 您可以使用下列範例可協助您連線︰

    Login-AzureRmAccount

核取訂閱的帳戶。

    Get-AzureRmSubscription 

指定您想要使用的訂閱。

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2.建立虛擬網路和閘道器子網路

範例使用 /28 的閘道器子網路。 雖然可以建立閘道器子網路 /29 小，我們建議您建立較大的子網路選取至少 /28 或 /27 包含多個地址。 這可讓足夠的地址，以容納，您可以在未來可能額外設定。

如果您已經有的閘道器子網路的虛擬網路/29 或更大，您可以直接新增[您的區域網路閘道器](#localnet)。


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>若要建立虛擬網路和閘道器子網路

若要建立虛擬網路和閘道器子網路中使用下列範例。 取代您自己的值。 

首先，建立資源群組︰
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

接下來，建立虛擬網路。 請確認您所指定的地址空格不致重疊任何您在內部部署網路有位址空間。

下列範例會建立名為*testvnet*和兩個子網路，一個稱為*GatewaySubnet*虛擬網路，另一個名為*Subnet1*。 請務必建立一個名為特別*GatewaySubnet*的子網路。 如果您將其命名其他項目，您的連線設定將會失敗。 

設定變數。

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

建立 VNet。

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>若要新增閘道器子網路，您已經建立虛擬網路

只有當您需要將閘道器子網路新增到您先前建立 VNet 需要此步驟。

您可以使用下列範例，以建立閘道器子網路。 請務必命名閘道器子網路 'GatewaySubnet'。 如果您將其命名其他項目，建立子網路，但是 Azure 不會將它視為閘道器子網路。

設定變數。

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

建立閘道器子網路。

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

設定。 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3.<a name="localnet"></a>新增您的區域網路閘道器

在虛擬網路中，區域網路閘道器通常是指您內部部署的位置。 為網站所用 Azure 參考，及也指定 [區域網路閘道器的地址空間前置詞的名稱。 

Azure 所用的 IP 位址首碼您指定識別哪些流量傳送給您內部部署的位置。 這表示您已指定每個您想要與您的區域網路閘道器相關聯的地址前置字元。 如果您的內部部署網路變更，您可以輕鬆更新這些前置詞。 

使用 PowerShell 範例，請注意下列動作︰
    
- *GatewayIPAddress*是您的內部部署 VPN 裝置的 IP 位址。 Nat 找不到您要有 vpn 才能的裝置 
- *AddressPrefix*是您的內部部署位址空間。

若要新增的區域網路閘道器的單一的地址前置詞︰

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

若要新增多個地址前置詞與本機網路閘道器︰

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>若要修改您的區域網路閘道器的 IP 位址首碼

有時候您的區域網路閘道器前置詞變更。 若要修改您的 IP 位址首碼時採取的步驟取決於您是否有建立 VPN 閘道器連線。 請參閱本文的[區域網路閘道器的修改 IP 位址首碼](#modify)一節。


## <a name="PublicIP"></a>4.要求要有 vpn 才能閘道器的公用 IP 位址

接下來，要求要配置給您 Azure VNet VPN 閘道器的公用 IP 位址。 這不是相同的 IP 位址指派給您要有 vpn 才能的裝置。而是將其指派給 Azure VPN 閘道器本身。 您無法指定您想要使用的 IP 位址。 動態配置至您的閘道器。 您設定您的內部部署 VPN 裝置時使用這個 IP 位址，以連線至閘道器。

資源管理員部署模型目前的 Azure VPN 閘道只支援使用動態配置方法的公用 IP 位址。 不過，這不表示的 IP 位址會變更。 只有 Azure VPN 閘道器 IP 位址變更時，刪除並重新建立閘道器時。 閘道器的公用 IP 位址無法在調整大小時、 重設，或其他內部維護/升級您的 Azure VPN 閘道器的變更。

使用下列 PowerShell 範例︰

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5。 建立閘道器 IP 位址設定

閘道器組態定義子網路，以及要使用的公用 IP 位址。 您可以使用下列範例來建立您的閘道器設定。

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6。 建立虛擬網路閘道器

在此步驟中，您可以建立虛擬網路閘道器。 建立閘道器花費很長的時間才能完成。 通常 45 分鐘或更多。 

使用下列的值︰

- 網站-設定*-GatewayType*是*Vpn*。 閘道器類型一律是設定的實作特定的。 例如，其他閘道器組態可能會要求-GatewayType ExpressRoute。 

- *-VpnType*可*RouteBased* （稱為部分文件的動態閘道器） 或*PolicyBased* （又稱為靜態閘道器部分文件）。 如需有關 VPN 閘道器類型的詳細資訊，請參閱[關於 VPN 閘道器](vpn-gateway-about-vpngateways.md#vpntype)。
- *-GatewaySku*可以是*基本*]、 [*標準*] 或 [ *HighPerformance*。   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7。 設定 VPN 裝置

此時，您需要設定您的內部部署 VPN 裝置虛擬網路閘道器的公用 IP 位址。 使用您裝置的製造商的特定的設定資訊。 您可以參考[VPN 裝置](vpn-gateway-about-vpn-devices.md)，如需詳細資訊。

若要尋找您的虛擬網路閘道器的公用 IP 位址，請使用下列範例︰

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8。 建立 VPN 連線

接下來，建立網站為網站之間的連線虛擬網路閘道和 VPN 裝置。 請務必的值取代為您自己。 共用的金鑰必須符合您所使用 VPN 裝置設定的值。 請注意，`-ConnectionType`站台站台是*IPsec*。 

設定變數。

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

建立的連線。

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

連線時簡短之後將會建立。 

## <a name="toverify"></a>若要確認 VPN 連線

有一些不同的方式，以驗證您的 VPN 連線。

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>若要修改的區域網路閘道器的 IP 位址首碼

如果您需要變更您的區域網路閘道器的前置詞，請使用下列指示。 提供兩組的指示進行。 您已經建立閘道器的連線是否取決於您選擇的指示進行。 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>若要修改的區域網路的閘道器的閘道器 IP 位址

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>後續步驟

- 您可以新增您的虛擬網路虛擬機器。 如需的步驟，請參閱[建立虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。

- BGP 的相關資訊，請參閱[BGP 概觀](vpn-gateway-bgp-overview.md)以及[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。

