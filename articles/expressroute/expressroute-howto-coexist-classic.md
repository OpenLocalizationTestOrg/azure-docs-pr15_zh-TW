<properties
   pageTitle="設定 Expressroute 和網站-VPN 連線可以並存 |Microsoft Azure"
   description="本文會引導您設定 ExpressRoute 及可以並存傳統部署模型網站-VPN 連線。"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-classic-deployment-model"></a>設定 ExpressRoute 和網站-共存傳統部署模型連線


> [AZURE.SELECTOR]
- [PowerShell-資源管理員](expressroute-howto-coexist-resource-manager.md)
- [PowerShell-傳統](expressroute-howto-coexist-classic.md)

設定網站-VPN 和 ExpressRoute 可以有幾項優點。 您可以設定為安全的容錯移轉路徑至網站 VPN ExressRoute，或使用網站-Vpn 連線至不透過 ExpressRoute 連線的網站。 我們會說明設定這兩種案例本文中的步驟。 本文適用於傳統部署模型。 無法在入口網站中使用此設定。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] 依照下列指示之前，就必須預先設定 ExpressRoute 電路。 請確定您已[建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)與[設定路由](expressroute-howto-routing-classic.md)至輔助線遵循之後，再執行下列步驟。

## <a name="limits-and-limitations"></a>限制和限制

- **不支援傳輸路由。** 您無法連線至網站 VPN 透過您的區域網路與您連線透過 ExpressRoute 的區域網路之間的路由傳送 （透過 Azure)。
- **點-網站不支援。** 您無法啟用點為網站 VPN 連線到已連線至 ExpressRoute 相同 VNet。 點-網站 VPN 及 ExpressRoute 無法同時存在的相同 VNet。
- **強制通道無法啟用網站-VPN 閘道器上。** 您可以只 」 強制 」 所有的網際網路繫結流量至您的內部部署網路透過 ExpressRoute。
- **不支援基本 SKU 閘道器。** 您必須使用非基本 SKU 閘道器的[ExpressRoute 閘道器](expressroute-about-virtual-network-gateways.md)，並[要有 vpn 才能閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
- **只傳送型 VPN 閘道功能受支援。** 您必須使用路由型[VPN 閘道器](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
- **靜態路由應該要有 vpn 才能閘道設定。** 如果您的區域網路 ExpressRoute 和網站-VPN 連線，您必須在您將網站-VPN 連線路由至公用網際網路的區域網路設定的靜態路由。
- **必須先設定 ExpressRoute 閘道器。** 您新增至網站 VPN 閘道器前，您必須先建立 ExpressRoute 閘道器。

## <a name="configuration-designs"></a>設定設計

### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>設定網站-VPN 作為容錯移轉路徑 ExpressRoute

您可以在 ExpressRoute 當成備份設定網站-VPN 連線。 僅適用於虛擬網路連結至 Azure 私人對等的路徑。 沒有存取透過 Azure 公用和 Microsoft peerings vpn 容錯移轉解決方案的服務。 ExpressRoute 電路永遠是主要的連結。 ExpressRoute 電路失敗時，才時，資料會透過網站-VPN 路徑。 

![同時存在](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>設定網站-VPN 連線至網站未連線到 ExpressRoute

您可以設定您的網路部分網站直接連接到 Azure 透過網站-VPN 及部分網站透過 ExpressRoute 位置。 

![同時存在](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] 您無法為傳輸路由器的虛擬網路設定。

## <a name="selecting-the-steps-to-use"></a>選取要使用的步驟

有兩組不同的選擇來設定連線，可以並存的程序。 您選取的設定程序取決於您是否具有您要連線，或您想要建立新的虛擬網路的現有虛擬網路。


- 我不是 VNet，，需要建立一個。
    
    如果您還沒有虛擬網路，此程序會逐步引導您完成建立新的虛擬網路使用傳統的部署模型，並建立新的 ExpressRoute 和網站-VPN 連線。 若要設定，請遵循在[建立新的虛擬網路和共存連線](#new)的文件] 區段中的步驟。

- 我已經有傳統部署模型 VNet。

    使用現有的網站-VPN 連線或 ExpressRoute 位置，您可能已經有虛擬網路。 [若要設定的現有的 VNet coexsiting 連線](#add)的 [文件] 區段會引導您刪除閘道器，並建立新的 ExpressRoute 和網站-VPN 連線。 請注意，在建立新的連線時，必須完成步驟，以特定順序。 不使用中的指示其他文件來建立您的閘道器和連線。

    此程序中建立可同時存在的連線會要求您刪除閘道，，然後設定新的閘道器。 這表示您刪除並重新建立您的閘道器和連線，但您不需要的任何 Vm 或服務移轉至新的虛擬網路時，您就必須停機時間的跨內部部署連線。 您的 Vm 和服務會仍然可以透過進行通訊出負載平衡器時，如果設定要這麼做，設定您的閘道器。


## <a name="new"></a>若要建立新的虛擬網路和共存的連線

此程序會逐步引導您完成建立 VNet，並建立網站的網站和會同時存在的 ExpressRoute 連線。

1. 您需要安裝最新版 Azure PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請注意，您會使用此設定的 cmdlet 可能稍有不同於您可能的熟悉。 請務必使用這些指示中指定的指令程式。 

2. 建立虛擬網路結構描述。 如需設定結構描述的詳細資訊，請參閱[Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

    當您建立您的結構描述時，請確定您使用下列的值︰

    - 閘道器網路的子虛擬必須 /27 或短的前置字元 （例如 /26 或 /25）。
    - 閘道器連線類型 」 致力於 」。

              <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
                <AddressSpace>
                  <AddressPrefix>10.17.159.192/26</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Subnet-1">
                    <AddressPrefix>10.17.159.192/27</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.17.159.224/27</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>

3. 之後，建立及設定您的 xml 結構描述檔案上傳檔案。 這會建立虛擬網路。

    您可以使用下列 cmdlet 上, 傳您的檔案，取代值以自己。

        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. <a name="gw"></a>建立 ExpressRoute 閘道器。 請務必為*標準*、 *HighPerformance*，或*UltraPerformance*以及為*DynamicRouting*GatewayType 指定 GatewaySKU。

    使用下列範例中，以取代您自己的值。

        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. 連結至 ExpressRoute 電路 ExpressRoute 閘道器。 完成這個步驟之後，建立您的內部部署網路和 Azure ExpressRoute，透過之間的連線。

        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. <a name="vpngw"></a>接下來，建立您的網站-VPN 閘道器。 GatewaySKU 必須*標準*、 *HighPerformance*，或*UltraPerformance*和 GatewayType 必須*DynamicRouting*。

        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

    若要擷取的虛擬網路閘道器設定，包括閘道器識別碼] 和 [的公用 IP，使用`Get-AzureVirtualNetworkGateway`指令程式。

        Get-AzureVirtualNetworkGateway

        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded

7. 建立本機網站 VPN 閘道器的實體。 這個命令無法設定您的內部部署 VPN 閘道器。 而其可讓您提供的本機的閘道器設定，例如公用 IP 和內部部署位址空間，以便 Azure VPN 閘道器可以連線至該。

    >[AZURE.IMPORTANT] Netcfg 中未定義網站-VPN 本機網站。 不過，您必須使用這個 cmdlet 來指定本機網站參數。 您無法定義並使用入口網站或 netcfg 檔案。

    使用下列範例中，使用您自己取代的值。

        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>

    > [AZURE.NOTE] 如果您的區域網路有多個路由，您可以將傳遞做為陣列中所有地方。  $MyLocalNetworkAddress =@("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  


    若要擷取的虛擬網路閘道器設定，包括閘道器識別碼] 和 [的公用 IP，使用`Get-AzureVirtualNetworkGateway`指令程式。 請參閱下列範例。

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


8. 設定您的本機 VPN 裝置連線到新的閘道器。 使用您在步驟 6 中設定您的 VPN 裝置時擷取的資訊。 如需有關 VPN 裝置設定的詳細資訊，請參閱[VPN 裝置設定](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。

9. 連結至網站 VPN 閘道器在 Azure 本機的閘道器。

    在此範例中，connectedEntityId 是本機的閘道器識別碼，您可以藉由執行找到`Get-AzureLocalNetworkGateway`。 您可以使用找到 virtualNetworkGatewayId`Get-AzureVirtualNetworkGateway`指令程式。 在此步驟中，建立網站-VPN 連線透過您的區域網路和 Azure 之間的連線。


        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>若要設定的現有的 VNet coexsiting 連線

如果您有現有的虛擬網路，檢查閘道器子網路的大小。 如果 /28 或 /29 閘道器子網路，您必須先刪除虛擬網路閘道器，並放大閘道器子網路。 本節中的步驟會顯示該怎麼做。

如果閘道器子網路 /27 或更大虛擬網路連線透過 ExpressRoute，您可以略過下列步驟，並繼續進行[「 步驟 6-建立網站-VPN 閘道器 」](#vpngw)前一節。

>[AZURE.NOTE] 當您刪除現有的閘道器時，您的本機部署將會遺失虛擬網路的連線，而您可使用此設定。

1. 您需要安裝最新版 Azure 資源管理員 PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。 請注意，您會使用此設定的 cmdlet 可能稍有不同於您可能的熟悉。 請務必使用這些指示中指定的指令程式。 

2. 刪除現有的 ExpressRoute 或網站-VPN 閘道器。 使用下列 cmdlet，使用您自己取代的值。

        Remove-AzureVNetGateway –VnetName MyAzureVNET

3. 匯出虛擬網路結構描述。 使用下列 PowerShell 指令程式，使用您自己取代的值。

        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”

4. 編輯網路設定檔案結構描述，以便閘道器子網路是 /27 或短的前置字元 （例如 /26 或 /25）。 請參閱下列範例。 
>[AZURE.NOTE] 如果您沒有足夠的虛擬網路的子網路放大閘道器的 IP 位址，您需要新增更多的 IP 位址空間。 如需設定結構描述的詳細資訊，請參閱[Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)。

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

5. 如果您先前的閘道器至網站 VPN，那麼您也必須**Dedicated**來變更連線類型。

                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>

6. 此時，您必須使用任何閘道 VNet。 若要建立新的閘道器，並完成您的連線，您可以繼續進行[步驟 4-建立 ExpressRoute 閘道器](#gw)，在先前一組步驟中找到。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)
