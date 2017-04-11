### <a name="noconnection"></a>如何新增或移除前置詞-閘道器的連線

- **若要新增**額外的位址首碼至本機網路的閘道器之您建立了，但是，不會還沒有一個閘道器的連線，使用以下範例。 請務必針對您的變更的值。

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **若要移除**的區域網路的閘道器之沒有 VPN 連線，地址前置詞使用下面的範例。 離開您不再需要的前置詞出。 在此範例中，我們不再需要加上字首 20.0.0.0/24 （從先前的範例），因此我們將會更新本機網路閘道器及排除的前置字元。

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>如何新增或移除前置詞-現存的閘道器的連線

如果您已經建立閘道器的連線，並想要新增或移除的區域網路閘道中所包含的 IP 位址首碼，您必須執行下列步驟順序。 這會導致您的 VPN 連線的一些停機時間。 更新您的前置詞時，將第一次移除連線、 修改前置詞，然後建立新的連線。 在下面的範例中，請務必變更到您自己的值。

>[AZURE.IMPORTANT] 不要刪除 VPN 閘道器。 如果這麼做，您必須完成步驟，以重新建立，以及重新設定您的內部部署路由器以新的設定，請返回。
 
1. 移除連線。

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. 修改您的區域網路閘道器的地址首碼。

    設定 LocalNetworkGateway 變數。

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    修改前置字元。

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. 建立的連線。 在此範例中，我們要設定 IPsec 連線類型。 當您重新建立您的連線時，用於您的設定所指定連接類型。 其他連線類型，請參閱[PowerShell 指令程式](https://msdn.microsoft.com/library/mt603611.aspx)頁面。

    設定 VirtualNetworkGateway 變數。

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    建立的連線。 請注意，此範例會使用您在上述步驟中設定的變數 $local。


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
