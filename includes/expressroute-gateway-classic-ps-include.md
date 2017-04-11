您必須建立 VNet 及閘道器子網路首先，請使用下列任務之前。 請參閱[使用 [傳統] 入口網站的虛擬網路設定](../articles/expressroute/expressroute-howto-vnet-portal-classic.md)的詳細資訊。   

## <a name="add-a-gateway"></a>新增閘道器

您可以使用下列命令來建立閘道器。 請務必替代您自己的任何值。

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>確認閘道器已建立

使用下列命令確認已經建立的閘道器。 這個命令也會擷取閘道器 ID，您需要的其他操作。

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>調整大小的閘道器

[閘道器 Sku](../articles/expressroute/expressroute-about-virtual-network-gateways.md)的幾種。 若要隨時變更閘道器 SKU，您可以使用下列命令。

>[AZURE.IMPORTANT] 這個命令無法運作 UltraPerformance 閘道器。 若要變更您的閘道器至 UltraPerformance 閘道器，第一次移除現有 ExpressRoute 閘道器，然後建立新的 UltraPerformance 閘道器。 若要將降級從 UltraPerformance 閘道閘道，第一次移除 UltraPerformance 閘道器，然後建立新的閘道器。 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>移除閘道器

使用下列命令移除閘道器

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>