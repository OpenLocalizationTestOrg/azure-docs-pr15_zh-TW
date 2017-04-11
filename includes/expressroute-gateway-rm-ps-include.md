這項工作的步驟會使用 VNet，根據以下的值。 其他設定，名稱也會列於此清單中。 雖然我們執行新增變數根據此清單中的值，我們不直接在任何一個步驟，使用此清單。 您可以複製清單作為參照，以使用您自己取代的值。

設定參考清單列出︰
    
- 虛擬網路名稱 = 「 TestVNet 」
- 虛擬網路位址空間 = 192.168.0.0/16
- 資源群組 = 「 TestRG 」
- Subnet1 名稱 = 「 FrontEnd 」 
- Subnet1 位址空間 = 「 192.168.0.0/16 」
- 閘道器子網路名稱︰ 您必須永遠命名閘道器子網路*GatewaySubnet*「 GatewaySubnet 」。
- 閘道器子網路位址空間 = 「 192.168.200.0/26 」
- 地區 = 」 東亞美國 」
- 閘道器名稱 = 「 GW 」
- 閘道器 IP 名稱 = 「 GWIP 」
- 閘道器 IP 設定名稱 = 「 gwipconf 」
-  類型 ="ExpressRoute 」 此類型是 ExpressRoute 設定的必要。
- 閘道器的公用 IP 名稱 = 「 gwpip 」


## <a name="add-a-gateway"></a>新增閘道器

1. 連線至 Azure 訂閱。 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. 您在本練習變數宣告。 此範例會使用使用變數下列範例中。 請務必進行編輯，以反映您想要使用的設定。 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. 儲存一個變數的虛擬網路物件。

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. 加入虛擬網路閘道器子網路。 閘道器子網路必須名為 「 GatewaySubnet 」。 您會想要建立閘道器的 /27 或更大 (/ 26 日/25，等等。)。
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. 設定。

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. 儲存一個變數的閘道器子網路。

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. 要求的公用 IP 位址。 建立閘道器之前所需要的 IP 位址。 您無法指定您想要使用; IP 位址動態配置。 [下一步組態] 區段中，您會使用這個 IP 位址。 AllocationMethod 必須是動態。

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. 建立您的閘道器設定。 閘道器組態定義子網路，以及要使用的公用 IP 位址。 在此步驟中，指定當您建立閘道器會使用的設定。 此步驟實際上並未建立閘道器物件。 您可以使用下列範例來建立您的閘道器設定。 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. 建立閘道器。 在此步驟中， **-GatewayType**是特別重要。 您必須使用**ExpressRoute**的值。 請注意，執行下列 cmdlet 之後，花費 20 分鐘或更多] 以建立閘道器。

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>確認閘道器已建立

使用下列命令確認已經建立的閘道器。

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>調整大小的閘道器

[閘道器 Sku](../articles/expressroute/expressroute-about-virtual-network-gateways.md)的幾種。 若要隨時變更閘道器 SKU，您可以使用下列命令。

>[AZURE.IMPORTANT] 這個命令無法運作 UltraPerformance 閘道器。 若要變更您的閘道器至 UltraPerformance 閘道器，第一次移除現有的 ExpressRoute 閘道]，然後建立新的 UltraPerformance 閘道器。 若要將降級從 UltraPerformance 閘道閘道，第一次移除 UltraPerformance 閘道器，然後建立新的閘道器。

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>移除閘道器

使用下列命令移除閘道器

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
