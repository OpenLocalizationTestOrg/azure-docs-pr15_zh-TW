<properties
   pageTitle="如何設定主動-主動 S2S VPN 連線與使用 Azure 資源管理員與 PowerShell 的 Azure VPN 閘道 |Microsoft Azure"
   description="本文會引導您以使用 Azure 資源管理員與 PowerShell 的 Azure VPN 閘道器設定主動-主動連線。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>使用 Azure 資源管理員與 PowerShell 的 Azure VPN 閘道器設定主動-主動 S2S VPN 連線

本文會引導您建立主動-主動跨內部部署與使用資源管理員部署模型和 PowerShell VNet-VNet 連線的步驟。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>關於高度可用的跨內部部署連線

若要達成可用性跨內部部署與 VNet-VNet 連線，您應該部署多個要有 vpn 才能閘道器，並建立您的網路和 Azure 之間的多個平行連線。 請參閱[高度可用的跨部署及 VNet-VNet 連線](./vpn-gateway-highlyavailable.md)的連線選項和拓撲的概觀。

本文所提供的指示，設定主動-主動跨內部部署 VPN 連線，以及主動-主動兩個虛擬網路之間的連線︰

- [第 1 部份-在建立及設定 Azure VPN 閘道主動-主動模式](#aagateway)

- [第 2 部分-建立主動-主動跨內部部署連線](#aacrossprem)

- [第 3 部分-建立主動-主動 VNet-VNet 連線](#aav2v)

- [第 4 部分-主動-主動與作用中待命之間更新現有的閘道器](#aaupdate)

您可以結合在一起，以建立符合您需求的更複雜的高度可用的網路拓撲這些。

>[AZURE.IMPORTANT] 請注意，主動-主動模式僅適用於 HighPerformance SKU


## <a name ="aagateway"></a>第 1 部份-建立及設定主動-主動 VPN 閘道器

下列步驟將主動-主動模式中設定您 Azure VPN 閘道器。 作用中主動與作用中待命閘道器之間的主要差異︰

- 您需要建立兩個閘道器 IP 設定兩個公用 IP 位址
- 您需要設定 EnableActiveActiveFeature 旗標
- 必須 HighPerformance SKU 的閘道器。

其他屬性是非作用中的作用中閘道器相同。 

### <a name="before-you-begin"></a>開始之前

- 請確認您擁有 Azure 訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。
    
- 您需要安裝的 Azure 資源管理員 PowerShell 指令程式。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

### <a name="step-1---create-and-configure-vnet1"></a>步驟 1-建立及設定 VNet1

#### <a name="1-declare-your-variables"></a>1.宣告變數

此練習中，我們會先宣告我們的變數。 下列範例宣告變數這個練習中使用的值。 請務必生產環境設定時，使用您自己取代值。 如果您熟悉此類型的設定步驟執行，您可以使用這些變數。 修改變數，然後複製並貼到您的 PowerShell 主控台。

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.連線至您的訂閱，並建立新的資源群組

請確定您切換到 PowerShell 模式，來使用資源管理員 cmdlet。 如需詳細資訊，請參閱[使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

開啟您 PowerShell 主控台，連線到您的帳戶。 您可以使用下列範例可協助您連線︰

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3.建立 TestVNet1

下列範例會建立名為 TestVNet1 與三個子網路、 一個稱為的 GatewaySubnet、 一個稱為的 FrontEnd 和一個稱為後端虛擬網路。 當替代值，是很重要您永遠命名閘道器子網路特別 GatewaySubnet。 如果您將其命名其他項目，您閘道器將無法建立。 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>步驟 2-主動-主動模式的 TestVNet1 建立 VPN 閘道器

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1.建立公用 IP 位址和閘道器 IP 設定

要求要配置給您，為您 VNet 會建立閘道器的兩個公用 IP 位址。 您也會定義子網路和所需的 IP 設定。 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2.主動-主動設定建立 VPN 閘道器

建立 TestVNet1 虛擬網路閘道器。 請注意，有兩個 GatewayIpConfig 項目，EnableActiveActiveFeature 旗標設定。 主動-主動模式需要 HighPerformance SKU 的路由型 VPN 閘道器。 建立閘道器時可能需要 （30 分鐘或更多] 以完成）。

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3.取得閘道器的公用 IP 位址和 BGP 等 IP 位址

閘道器建立後，您必須取得 Azure VPN 閘道 BGP 等 IP 位址。 為您的內部部署 VPN 裝置 BGP 等設定 Azure VPN 閘道器時需要此位址。

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

使用下列 cmdlet，以顯示兩個公用配置 VPN 閘道及他們對應 BGP 等 IP 位址的每個閘道執行個體的 IP 位址︰

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

閘道執行個體的公用 IP 順序地址和對應 BGP 對等的地址都相同。 在此範例中，閘道器的 40.112.190.5 的公用 ip VM 使用便會 10.12.255.4 其 BGP 對等地址，且 138.91.156.129 閘道會使用 10.12.255.5。 設定上的部署 VPN 裝置連線至作用中作用中閘道器時，被必要資訊。 閘道器是由所有地址下圖所示︰

![作用中作用中閘道器](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

閘道器建立後，您可以使用此閘道器建立主動-主動跨內部部署或 VNet-VNet 連線。 下列各節將逐步完成練習的步驟。


## <a name ="aacrossprem"></a>第 2 部分-建立主動-主動跨內部部署連線

若要建立跨內部部署連線，您需要建立本機網路閘道器，代表您內部部署的 VPN 裝置，並連線 Azure VPN 閘道器的區域網路閘道器的連線。 在此範例中，Azure VPN 閘道器為作用中作用中的模式。 如此一來，即使沒有只有一個內部部署 VPN 裝置 （本機網路閘道） 和一個連線資源，兩個 Azure VPN 閘道執行個體建立 S2S VPN 通道與內部部署裝置。

在進行之前，請確定您已完成此練習的[第 1 部分](#aagateway)。

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>步驟 1-建立及設定的區域網路閘道器

#### <a name="1-declare-your-variables"></a>1.宣告變數

這個練習會繼續建立圖所示的設定。 請務必以您想要使用您設定的項目取代的值。

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

幾個區域網路閘道器參數有關注意的事項︰

- 區域網路閘道器可以在為 VPN 閘道器中的相同或不同的位置和資源群組。 此範例顯示其在不同的資源群組，但在相同的 Azure 位置。

- 如果有一個內部部署 VPN 裝置如上所示，包含或不含 BGP 通訊協定，就可以使用主動-主動連線。 此範例使用 BGP 跨內部部署連線。

- 如果啟用 BGP，您需要的區域網路閘道器的宣告的前置詞是您 BGP 對等的 IP 位址 VPN 裝置上的主機地址。 在此情況下，則 / 32 的 「 10.52.255.253/32 」 的前置字元。

- 提醒，您必須使用您的內部網路和 Azure VNet 之間的不同 BGP ASNs。 如果他們是相同，您需要變更您 VNet ASN，如果您的內部部署 VPN 裝置已使用 ASN 對等其他 BGP 其他例項。

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2.建立 Site5 區域網路閘道器
    
在繼續之前，請確定您仍然連線訂閱 1。 如果還沒有建立，請建立資源群組。

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>步驟 2-連接 VNet 閘道器與本機網路閘道器

#### <a name="1-get-the-two-gateways"></a>1.取得兩個閘道器

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2。 建立到 Site5 連線 TestVNet1

在此步驟中，您將建立從 TestVNet1 連線以使用 「 EnableBGP 」 設定為 [$True Site5_1。

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3.VPN 及 BGP 您內部部署 VPN 裝置參數

下列範例會列出會將 BGP 組態] 區段中輸入此練習您內部部署的 VPN 裝置的參數︰

    - Site5 ASN: 65050
    - Site5 BGP IP: 10.52.255.253
    - 要宣告首碼: （例如） 10.51.0.0/16 和 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 至 40.112.190.5 通道
    - Azure VNet BGP IP 2: 10.12.255.5 至 138.91.156.129 通道
    - 靜態路由︰ 目的地 10.12.255.4/32、 個躍點 VPN 通道介面 40.112.190.5 至目的地 10.12.255.5/32，VPN 通道介面 138.91.156.129 個躍點
    - eBGP Multihop: eBGP 已啟用您的裝置上如有需要請確定 「 multihop 」 選項

應該建立連線，請稍候幾分鐘，然後建立 IPsec 連線之後，就會開始 BGP 對等工作階段。 此範例中到目前為止已設定只有一個內部部署 VPN 裝置，結果在圖表中所示︰

![作用中的使用中-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>步驟 3-主動-主動 VPN 閘道連線的兩個內部部署 VPN 裝置

如果您在相同的內部網路有兩個 VPN 裝置，您可以連線至第二個 VPN 裝置 Azure VPN 閘道器來達成雙重重複。

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1.建立第二個區域網路閘道器的 Site5

請注意的閘道器 IP 位址、 地址前置詞及第二個區域網路閘道器的 BGP 對等的地址不能重疊的相同的內部網路上一個區域網路閘道器。 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2.連接 VNet 閘道器與第二個區域網路閘道器

建立與 「 EnableBGP 」 設定為 [$True Site5_2 TestVNet1 的連線

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3.VPN 及 BGP 第二個內部部署 VPN 裝置的參數

同樣地，清單下方的參數會輸入第二個 VPN 裝置︰

    - Site5 ASN: 65050
    - Site5 BGP IP: 10.52.255.254
    - 要宣告首碼: （例如） 10.51.0.0/16 和 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 至 40.112.190.5 通道
    - Azure VNet BGP IP 2: 10.12.255.5 至 138.91.156.129 通道
    - 靜態路由︰ 目的地 10.12.255.4/32、 個躍點 VPN 通道介面 40.112.190.5 至目的地 10.12.255.5/32，VPN 通道介面 138.91.156.129 個躍點
    - eBGP Multihop: eBGP 已啟用您的裝置上如有需要請確定 「 multihop 」 選項

連線 （通道） 會建立之後，您會有雙多餘的 VPN 裝置及通道連接您的內部部署網路和 Azure:

![雙重-重複-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>第 3 部分-建立主動-主動 VNet-VNet 連線

本節會以 BGP 建立 VNet-VNet 連線至作用中作用。 

從先前的步驟，列於上方，繼續下列指示進行。 您必須完成[第 1 部分](#aagateway)進行建立與設定 BGP TestVNet1 和 VPN 閘道器。 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>步驟 1-建立 TestVNet2 和 VPN 閘道器

請務必確認新虛擬網路 TestVNet2 的 IP 位址空間不會與任何 VNet 範圍的重疊。

在此範例中，虛擬網路屬於同一份訂閱。 您可以將 VNet-VNet 不同的訂閱方案; 之間的連線設定請參閱[設定 VNet-VNet 連線](./vpn-gateway-vnet-vnet-rm-ps.md)]，以了解更多詳細資料。 請確定您新增 「-EnableBgp $True 」 建立啟用 BGP 連線時。

#### <a name="1-declare-your-variables"></a>1.宣告變數

請務必以您想要使用您設定的項目取代的值。

    $RG2           = "TestAARG2"
    $Location2     = "East US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2.TestVNet2 建立新的 [資源] 群組中

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3.建立 TestVNet2 主動-主動 VPN 閘道器

要求要配置給您，為您 VNet 會建立閘道器的兩個公用 IP 位址。 您也會定義子網路和所需的 IP 設定。 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

另存新檔數字與 「 EnableActiveActiveFeature 」 標幟建立 VPN 閘道器。 請注意，您必須會覆寫預設 ASN 上您 Azure VPN 閘道器。 必須啟用 BGP 和傳輸路由至不同的連線 VNets ASNs。

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>步驟 2-連線 TestVNet1 與 TestVNet2 閘道器

在此範例中，這兩個閘道器會位於同一份訂閱。 您可以在相同的 PowerShell 工作階段中完成這個步驟。

#### <a name="1-get-both-gateways"></a>1.取得兩個閘道器

請確認您登入，並連線到訂閱 1。

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2.建立兩個連線

在此步驟中，您會建立 TestVNet2，從 TestVNet1 連線和連線從 TestVNet2 TestVNet1。

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] 請務必針對這兩個連線啟用 BGP。

完成這些步驟，連線將建立在幾分鐘和 BGP 之後對等工作階段就會設定，一旦完成含雙重重複 VNet-VNet 連線︰

![作用中的使用中-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>第 4 部分-主動-主動與作用中待命之間更新現有的閘道器

[姓氏] 區段會說明如何設定從作用待命現有 Azure VPN 閘道器，主動-主動模式，或反向操作。

>[AZURE.IMPORTANT] 請注意，主動-主動模式僅適用於 HighPerformance SKU

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>設定主動-主動閘道作用待命閘道器

#### <a name="1-gateway-parameters"></a>1.閘道器的參數

下列範例會將作用中待命閘道轉換成作用中作用中閘道器。 您需要建立另一個的公用 IP 位址]，然後新增第二個閘道器 IP 設定。 下方顯示所用的參數︰

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2.建立公用的 IP 位址]，然後新增第二個閘道器 IP 設定

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3.啟用主動-主動模式及更新閘道器

您必須設定閘道器物件在 PowerShell 來觸發實際的更新。 也必須將閘道器物件的 SKU 變更為 HighPerformance，因為它為標準先前建立。

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

此更新可能需要 30 到 45 分鐘。

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>設定使用中待命閘道主動-主動閘道器

#### <a name="1-gateway-parameters"></a>1.閘道器的參數

使用相同的參數上方，取得 IP 設定您想要移除的名稱。

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2.移除閘道器 IP 設定，並停用主動-主動模式

同樣地，您必須設定閘道器物件在 PowerShell 來觸發實際的更新。

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

此更新可能需要最 30 到 45 分鐘。


## <a name="next-steps"></a>後續步驟

完成您的連線之後，您可以新增到您的虛擬網路的虛擬機器。 如需的步驟，請參閱[建立虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。

