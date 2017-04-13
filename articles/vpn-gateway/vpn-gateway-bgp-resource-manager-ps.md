<properties
   pageTitle="如何使用 Azure 資源管理員與 PowerShell 的 Azure VPN 閘道器設定 BGP |Microsoft Azure"
   description="本文會引導您設定 BGP 與使用 Azure 資源管理員與 PowerShell 的 Azure VPN 閘道。"
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
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>如何設定 BGP 上使用 Azure 資源管理員與 PowerShell 的 Azure VPN 閘道器

本文會引導您的步驟來啟用 BGP 跨內部部署網站的網站 」 (S2S) VPN 連線，以及使用 PowerShell 與資源管理員部署模型 VNet-VNet 連線。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>關於 BGP

BGP 是通常用於在網際網路中路由及性之間交換資訊兩個或多個網路的標準路由通訊協定。 BGP 啟用 Azure VPN 閘道器與您的內部部署 VPN 裝置，稱為 BGP 同儕或其他例項，exchange 「 路由 」，會通知可用性及連線至 [閘道器] 或 [路由器與有關瀏覽這些前置詞的兩個閘道器。 BGP 也可以啟用傳輸路由多個網路之間散佈的路由 BGP 閘道器學習從一個 BGP 對所有其他 BGP 對等。

請如需有關討論優點 BGP 和瞭解技術需求，以及使用 BGP 的考量，參閱[概觀的 BGP 與 Azure VPN 閘道](./vpn-gateway-bgp-overview.md)。

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>快速入門 BGP 上 Azure VPN 閘道器

本文會引導您執行的步驟，請執行下列工作︰

- [第 1 部份-啟用 BGP 上 Azure VPN 閘道](#enablebgp)

- [第 2 部分-建立與 BGP 跨內部部署連線](#crossprembgp)

- [第 3 部分-建立與 BGP VNet-VNet 連線](#v2vbgp)

指示的每一個部分表單的在您的網路連線啟用 BGP 基本建置組塊。 如果您完成所有三個部分，您會建立拓撲，如下圖所示︰

![BGP 拓撲](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

您可以結合在一起，以建立更複雜、 多希望，傳輸網路符合您需求的這些。

## <a name ="enablebgp"></a>第 1 部分-Azure VPN 閘道器設定 BGP

下列設定步驟會設定 BGP 參數 Azure VPN 閘道器，如下圖所示︰

![BGP 閘道器](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>開始之前

- 請確認您擁有 Azure 訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。
    
- 您需要安裝的 Azure 資源管理員 PowerShell 指令程式。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

### <a name="step-1---create-and-configure-vnet1"></a>步驟 1-建立及設定 VNet1 

#### <a name="1-declare-your-variables"></a>1.宣告變數

此練習中，我們會先宣告我們的變數。 下列範例宣告變數這個練習中使用的值。 請務必生產環境設定時，使用您自己取代值。 如果您熟悉此類型的設定步驟執行，您可以使用這些變數。 修改變數，然後複製並貼到您的 PowerShell 主控台。

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
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
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>步驟 2-BGP 參數的 TestVNet1 建立 VPN 閘道器

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1.建立 IP 與子網路的設定

要求要配置給您，為您 VNet 會建立閘道器的公用 IP 位址。 您也會定義子網路和所需的 IP 設定。 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2.建立 VPN 閘道器另存新檔數字

建立 TestVNet1 虛擬網路閘道器。 請注意 BGP 需要路由型 VPN 閘道器，以及加入參數，-Asn，若要設定 TestVNet1 ASN （另存新檔數字）。 建立閘道器時可能需要 （30 分鐘或更多] 以完成）。

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3.取得的 Azure BGP 等 IP 位址

閘道器建立後，您必須取得 Azure VPN 閘道 BGP 等 IP 位址。 為您的內部部署 VPN 裝置 BGP 等設定 Azure VPN 閘道器時需要此位址。

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

上一個命令會顯示對應的 BGP 設定 Azure VPN 閘道器; 上例如︰

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

閘道器建立後，您可以使用此閘道器建立交互內部部署連線或 BGP VNet-VNet 連線。 下列各節將逐步完成練習的步驟。

## <a name ="crossprembbgp"></a>第 2 部分-建立與 BGP 跨內部部署連線

若要建立跨內部部署連線，您需要建立本機網路閘道器，代表您內部部署的 VPN 裝置，並連線 Azure VPN 閘道器的區域網路閘道器的連線。 本文中的指示進行之間的差異在於指定 BGP 設定參數所需的其他屬性。

![針對跨內部部署的 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

在進行之前，請確定您已完成此練習的[第 1 部分](#enablebgp)。

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>步驟 1-建立及設定的區域網路閘道器

#### <a name="1-declare-your-variables"></a>1.宣告變數

這個練習會繼續建立圖所示的設定。 請務必以您想要使用您設定的項目取代的值。

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

幾個區域網路閘道器參數有關注意的事項︰

- 區域網路閘道器可以在為 VPN 閘道器中的相同或不同的位置和資源群組。 此範例會顯示其以不同的資源群組在不同的位置。

- 您需要宣告區域網路閘道器的最小值的前置詞是您 BGP 對等的 IP 位址 VPN 裝置上的主機地址。 在此情況下，則 / 32 的 「 10.52.255.254/32 」 的前置字元。

- 提醒，您必須使用您的內部網路和 Azure VNet 之間的不同 BGP ASNs。 如果他們是相同，您需要變更您 VNet ASN，如果您的內部部署 VPN 裝置已使用 ASN 對等其他 BGP 其他例項。
    
在繼續之前，請確定您仍然連線訂閱 1。

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2.建立 Site5 區域網路閘道器

請務必如果它不會建立，您建立的區域網路閘道器之前，先建立資源群組。 請注意 [區域網路閘道器的兩個額外的參數︰ Asn 與 BgpPeerAddress。

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>步驟 2-連接 VNet 閘道器與本機網路閘道器

#### <a name="1-get-the-two-gateways"></a>1.取得兩個閘道器

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2。 建立到 Site5 連線 TestVNet1

在此步驟中，您將建立從 TestVNet1 連線至 Site5。 您必須指定 「-EnableBGP $True 」 BGP 啟用此連線。 如前述，則可能有 BGP 和非 BGP 都相同 Azure VPN 閘道器的連線。 除非 BGP 已啟用 [連線] 屬性中，Azure 不會啟動 BGP 此連線即使 BGP 參數已在兩個閘道器設定。

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


下列範例會列出會將 BGP 組態] 區段中輸入此練習您內部部署的 VPN 裝置的參數︰

    - Site5 ASN: 65050
    - Site5 BGP IP: 10.52.255.254
    - 要宣告首碼: （例如） 10.51.0.0/16 和 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP: 10.12.255.30
    - 靜態路由︰ 為新增一個路由 10.12.255.30/32，個躍點正在 VPN 通道介面，您的裝置上使用
    - eBGP Multihop: eBGP 已啟用您的裝置上如有需要請確定 「 multihop 」 選項

應該建立連線，請稍候幾分鐘，然後建立 IPsec 連線之後，就會開始 BGP 對等工作階段。
 
## <a name ="v2vbgp"></a>第 3 部分-建立與 BGP VNet-VNet 連線

本節新增 VNet-VNet 連線與 BGP，如下圖所示。 

![針對 VNet-VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

從先前的步驟，列於上方，繼續下列指示進行。 您必須完成[部分](#enablebgp)進行建立與設定 BGP TestVNet1 和 VPN 閘道器。 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>步驟 1-建立 TestVNet2 和 VPN 閘道器

請務必確認新虛擬網路 TestVNet2 的 IP 位址空間不會與任何 VNet 範圍的重疊。

在此範例中，虛擬網路屬於同一份訂閱。 您可以設定 VNet-VNet 不同的訂閱方案; 之間的連線請參閱[設定 VNet-VNet 連線](./vpn-gateway-vnet-vnet-rm-ps.md)]，以了解更多詳細資料。 請確定您新增 「-EnableBgp $True 」 建立啟用 BGP 連線時。

#### <a name="1-declare-your-variables"></a>1.宣告變數

請務必以您想要使用您設定的項目取代的值。

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
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
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2.TestVNet2 建立新的 [資源] 群組中

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3.建立 VPN 閘道器的 TestVNet2 BGP 參數

要求要配置給您，為您 VNet 會建立閘道器的公用 IP 位址。 您也會定義子網路和所需的 IP 設定。 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

另存新檔數字建立 VPN 閘道器。 請注意，您必須會覆寫預設 ASN 上您 Azure VPN 閘道器。 必須啟用 BGP 和傳輸路由至不同的連線 VNets ASNs。

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>步驟 2-連線 TestVNet1 與 TestVNet2 閘道器

在此範例中，這兩個閘道器會位於同一份訂閱。 您可以在相同的 PowerShell 工作階段中完成這個步驟。

#### <a name="1-get-both-gateways"></a>1.取得兩個閘道器

請確認您登入並連線到訂閱 1。

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2.建立兩個連線

在此步驟中，您會建立 TestVNet2，從 TestVNet1 連線和連線從 TestVNet2 TestVNet1。

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] 請務必針對這兩個連線啟用 BGP。

完成這些步驟，連線將會建立在幾分鐘，BGP 對等工作階段會一次設定後會完成 VNet-VNet 連線。

如果您已經完成這個練習的所有三個部分，您將建立的網路拓撲如下所示︰

![針對 VNet-VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>後續步驟

完成您的連線之後，您可以新增到您的虛擬網路的虛擬機器。 如需的步驟，請參閱[建立虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。

