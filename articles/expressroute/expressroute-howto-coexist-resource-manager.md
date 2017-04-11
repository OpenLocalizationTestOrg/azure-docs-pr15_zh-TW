<properties
   pageTitle="設定資源管理員部署模型可以並存的 Expressroute 和網站-VPN 連線 |Microsoft Azure"
   description="本文會引導您設定 ExpressRoute 及資源管理員模型可以並存網站-VPN 連線。"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charleywen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-resource-manager-deployment-model"></a>設定 ExpressRoute 和網站-共存資源管理員部署模型連線

> [AZURE.SELECTOR]
- [PowerShell-資源管理員](expressroute-howto-coexist-resource-manager.md)
- [PowerShell-傳統](expressroute-howto-coexist-classic.md)

設定網站-VPN 和 ExpressRoute 可以有幾項優點。 您可以設定為安全的容錯移轉路徑至網站 VPN ExressRoute，或使用網站-Vpn 連線至不透過 ExpressRoute 連線的網站。 我們會說明設定這兩種案例本文中的步驟。 本文適用於資源管理員部署模型。 無法 Azure 入口網站中使用此設定。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] 依照下列指示之前，就必須預先設定 ExpressRoute 電路。 請確定您已[建立 ExpressRoute 電路](expressroute-howto-circuit-arm.md)與[設定路由](expressroute-howto-routing-arm.md)至輔助線遵循之後，再執行下列步驟。

## <a name="limits-and-limitations"></a>限制和限制

- **不支援傳輸路由。** 您無法連線至網站 VPN 透過您的區域網路與您連線透過 ExpressRoute 的區域網路之間的路由傳送 （透過 Azure)。
- **不支援基本 SKU 閘道器。** 您必須使用非基本 SKU 閘道器的[ExpressRoute 閘道器](expressroute-about-virtual-network-gateways.md)，並[要有 vpn 才能閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
- **只傳送型 VPN 閘道功能受支援。** 您必須使用路由型[VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
- **靜態路由應該要有 vpn 才能閘道設定。** 如果您的區域網路 ExpressRoute 和網站-VPN 連線，您必須在您將網站-VPN 連線路由至公用網際網路的區域網路設定的靜態路由。
- **必須先設定 ExpressRoute 閘道器。** 您新增至網站 VPN 閘道器前，您必須先建立 ExpressRoute 閘道器。


## <a name="configuration-designs"></a>設定設計

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>設定網站-VPN 作為容錯移轉路徑 ExpressRoute

您可以在 ExpressRoute 當成備份設定網站-VPN 連線。 僅適用於虛擬網路連結至 Azure 私人對等的路徑。 沒有存取透過 Azure 公用和 Microsoft peerings vpn 容錯移轉解決方案的服務。 ExpressRoute 電路永遠是主要的連結。 ExpressRoute 電路失敗時，才時，資料會透過網站-VPN 路徑。
>[AZURE.NOTE] 雖然這兩個路由時相同，ExpressRoute 電路的是慣用網站-VPN，Azure 會使用 longuest 前置詞符合的項目選擇根據封包的目的地路由。

![同時存在](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>設定網站-VPN 連線至網站未連線到 ExpressRoute

您可以設定您的網路部分網站直接連接到 Azure 透過網站-VPN 及部分網站透過 ExpressRoute 位置。 

![同時存在](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] 您無法為傳輸路由器的虛擬網路設定。

## <a name="selecting-the-steps-to-use"></a>選取要使用的步驟

有兩組不同的選擇來設定連線，可以並存的程序。 您選取的設定程序取決於您是否具有您要連線，或您想要建立新的虛擬網路的現有虛擬網路。


- 我不是 VNet，，需要建立一個。
    
    如果您還沒有虛擬網路，此程序會逐步引導您完成建立新的虛擬網路使用資源管理員部署模型，並建立新的 ExpressRoute 和網站-VPN 連線。 若要設定，請遵循在[建立新的虛擬網路和共存連線](#new)的文件] 區段中的步驟。

- 我已經有資源管理員部署模型 VNet。

    使用現有的網站-VPN 連線或 ExpressRoute 位置，您可能已經有虛擬網路。 [若要設定的現有的 VNet coexsiting 連線](#add)] 區段會引導您刪除閘道器，並建立新的 ExpressRoute 和網站-VPN 連線。 請注意，在建立新的連線時，必須完成步驟，以特定順序。 不使用中的指示其他文件來建立您的閘道器和連線。

    此程序中建立可同時存在的連線會要求您刪除閘道，，然後設定新的閘道器。 這表示您刪除並重新建立您的閘道器和連線，但您不需要的任何 Vm 或服務移轉至新的虛擬網路時，您就必須停機時間的跨內部部署連線。 您的 Vm 和服務會仍然可以透過進行通訊出負載平衡器時，如果設定要這麼做，設定您的閘道器。


## <a name="new"></a>若要建立新的虛擬網路和共存的連線

此程序會逐步引導您完成建立 VNet，並建立網站的網站和會同時存在的 ExpressRoute 連線。
    
1. 您需要安裝最新版 Azure PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請注意，您會使用此設定的 cmdlet 可能稍有不同於您可能的熟悉。 請務必使用這些指示中指定的指令程式。

2. 登入您的帳戶及設定環境。
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. 建立虛擬網路包括閘道器子網路。 如需有關虛擬網路設定的詳細資訊，請參閱[Azure 虛擬網路設定](../virtual-network/virtual-networks-create-vnet-arm-ps.md)。

    >[AZURE.IMPORTANT] 閘道器子網路必須 /27 或短的前置字元 （例如 /26 或 /25）。
    
    建立新的 VNet。

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    新增子網路。

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    儲存 VNet 設定。

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>建立 ExpressRoute 閘道器。 如需有關 ExpressRoute 閘道設定的詳細資訊，請參閱[ExpressRoute 閘道器設定](expressroute-howto-add-gateway-resource-manager.md)。 GatewaySKU 必須*標準*、 *HighPerformance*或*UltraPerformance*。

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. 連結至 ExpressRoute 電路 ExpressRoute 閘道器。 完成這個步驟之後，建立您的內部部署網路和 Azure ExpressRoute，透過之間的連線。 如需有關連結作業的詳細資訊，請參閱[連結 VNets 至 ExpressRoute](expressroute-howto-linkvnet-arm.md)。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>接下來，建立您的網站-VPN 閘道器。 如需有關 VPN 閘道設定的詳細資訊，請參閱[設定 VNet 網站的連線](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。 GatewaySKU 必須*標準*、 *HighPerformance*或*UltraPerformance*。 VpnType 必須*RouteBased*。

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

    Azure VPN 閘道支援 BGP。 在以下命令，您可以指定-EnableBgp。

        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true

    您可以找到 BGP 對等 IP 和 Azure 使用 $azureVpn.BgpSettings.BgpPeeringAddress 和 $azureVpn.BgpSettings.Asn VPN 閘道器的另存新檔號碼。 如需詳細資訊，請參閱 Azure VPN 閘道器[設定 BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) 。

7. 建立本機網站 VPN 閘道器的實體。 這個命令無法設定您的內部部署 VPN 閘道器。 而其可讓您提供的本機的閘道器設定，例如公用 IP 和內部部署位址空間，以便 Azure VPN 閘道器可以連線至該。

    如果您的本機 VPN 裝置只支援靜態路由，您可以設定的靜態路由以下列方式。

        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress

    如果您的本機 VPN 裝置支援 BGP，而且您想要啟用動態路由，您必須知道對等 IP 和另存新檔數字的本機 VPN 裝置使用 BGP。

        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN

8. 設定您的本機 VPN 裝置連線到新的 Azure VPN 閘道器。 如需有關 VPN 裝置設定的詳細資訊，請參閱[VPN 裝置設定](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。

9. 連結至網站 VPN 閘道器在 Azure 本機的閘道器。

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>若要設定的現有的 VNet coexsiting 連線

如果您有現有的虛擬網路，檢查閘道器子網路的大小。 如果 /28 或 /29 閘道器子網路，您必須先刪除虛擬網路閘道器，並放大閘道器子網路。 本節中的步驟會顯示該怎麼做。

如果閘道器子網路 /27 或更大虛擬網路連線透過 ExpressRoute，您可以略過下列步驟，並繼續進行[「 步驟 6-建立網站-VPN 閘道器 」](#vpngw)前一節。 

>[AZURE.NOTE] 當您刪除現有的閘道器時，您的本機部署將會遺失虛擬網路的連線，而您可使用此設定。 

1. 您需要安裝最新版 Azure PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請注意，您會使用此設定的 cmdlet 可能稍有不同於您可能的熟悉。 請務必使用這些指示中指定的指令程式。 

2. 刪除現有的 ExpressRoute 或網站-VPN 閘道器。 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. 刪除閘道器子網路。
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. 新增閘道器子網路的 /27 或更大。
    >[AZURE.NOTE] 如果您沒有足夠的虛擬網路的子網路放大閘道器的 IP 位址，您需要新增更多的 IP 位址空間。

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    儲存 VNet 設定。

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. 此時，您必須使用任何閘道 VNet。 若要建立新的閘道器，並完成您的連線，您可以繼續進行[步驟 4-建立 ExpressRoute 閘道器](#gw)，在先前一組步驟中找到。

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>若要新增點為網站設定 VPN 閘道
您可以遵循下列步驟以點為網站組態加入共存設定您要有 vpn 才能閘道器。

1. 新增 VPN 用戶端位址集區。 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. 上傳至 Azure VPN 閘道的 VPN 根憑證。 在此範例中，則假設根憑證，會儲存在本機電腦執行下列 PowerShell 指令程式的位置。 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

如需有關點為網站 VPN 的詳細資訊，請參閱[設定點為網站連線](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
