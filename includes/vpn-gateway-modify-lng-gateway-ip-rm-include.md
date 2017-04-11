若要修改的閘道器 IP 位址，請使用`New-AzureRmVirtualNetworkGatewayConnection`指令程式。 只要您現有的名稱完全相同保留的區域網路閘道器名稱，將會覆寫設定。 此時，「 設定 」 cmdlet 不支援修改閘道器 IP 位址。

### <a name="gwipnoconnection"></a>如何修改閘道器的 IP 位址-閘道器的連線

若要更新您還沒有連線的區域網路閘道的閘道器 IP 位址，請使用下面的範例。 您也可以一次更新地址前置字元。 您的設定將會覆寫現有的設定。 請務必使用您的區域網路閘道器的現有的名稱。 如果沒有的話，您將會建立一個新的區域網路閘道，不會覆寫現有的項目。

使用下列範例中，為您自己的值被取代。

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>如何修改閘道器的 IP 位址-現有的閘道器的連線

如果 [閘道器連線已經存在，首先必須以移除連線。 然後，您可以修改的閘道器 IP 位址，並重新建立新的連線。 這會導致您的 VPN 連線的一些停機時間。


>[AZURE.IMPORTANT] 不要刪除 VPN 閘道器。 如果這麼做，您必須完成步驟，以重新建立，以及重新指派到的新建立的閘道器 IP 位址的內部部署路由器設定，請返回。
 

1. 移除連線。 您可以使用找到您的連線名稱`Get-AzureRmVirtualNetworkGatewayConnection`指令程式。

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. 修改 GatewayIpAddress 的值。 您也可以修改您的地址前置詞次，如有必要。 請注意，這會覆寫現有的區域網路閘道器設定。 修改，好讓設定將會覆寫時，請使用您的區域網路閘道器的現有的名稱。 如果沒有的話，您將會建立一個新的區域網路閘道，無法修改現有的項目。

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. 建立的連線。 在此範例中，我們要設定 IPsec 連線類型。 當您重新建立您的連線時，用於您的設定所指定連接類型。 其他連線類型，請參閱[PowerShell 指令程式](https://msdn.microsoft.com/library/mt603611.aspx)頁面。  若要取得 VirtualNetworkGateway 名稱，您可以執行`Get-AzureRmVirtualNetworkGateway`指令程式。

    設定變數︰

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    建立連線︰
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

