<properties 
   pageTitle="使用 PowerShell ExpressRoute 電路連結虛擬網路 |Microsoft Azure"
   description="這份文件提供使用 PowerShell 「 資源管理員部署模型至 ExpressRoute 電路概略瞭解如何連結虛擬網路 (VNets)。"
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>連結 ExpressRoute 電路虛擬網路

> [AZURE.SELECTOR]
- [Azure 入口網站的資源管理員](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell-資源管理員](expressroute-howto-linkvnet-arm.md)
- [PowerShell-傳統](expressroute-howto-linkvnet-classic.md)


本文可協助您連結至 Azure ExpressRoute 電路的虛擬網路 (VNets)，使用 PowerShell 「 資源管理員部署模型。 虛擬網路可以是在同一份訂閱或另一個訂閱的一部分。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>設定的先決條件

- 您需要 Azure PowerShell 模組的最新版本 (至少 1.0 版)。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。
- 您需要設定之前檢閱[先決條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)，與[工作流程](expressroute-workflows.md)。
- 您必須使用中的 ExpressRoute 電路。 
    - 依照指示[建立 ExpressRoute 電路](expressroute-howto-circuit-arm.md)，而且有啟用您的連線提供者的電路。 
    - 請確定您有 Azure 私人對等設定您的電路。 請參閱[設定路由](expressroute-howto-routing-arm.md)路由的指示。 
    - 確定 Azure 私人對等設定您的網路和 Microsoft 之間 BGP 對等已啟動，好讓您可以啟用端對端連線。
    - 請確定您擁有虛擬網路和建立並完全佈建後，虛擬網路閘道器。 依照指示建立[VPN 閘道器](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)，但請務必使用`-GatewayType ExpressRoute`。

您可以連結標準 ExpressRoute 電路最多 10 個虛擬網路。 所有虛擬網路，必須在同一個淉區域中使用標準 ExpressRoute 電路。 

您可以連結 ExpressRoute 線電路淉區域外的虛擬網路或連線到您 ExpressRoute 電路的較大的數字的虛擬網路，如果您已啟用 ExpressRoute 進階版附加元件。 核取 [[常見問題集](expressroute-faqs.md)，如需詳細資訊的進階版附加元件]。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>在同一份訂閱虛擬網路連線到電路

您可以使用下列指令程式連線至 ExpressRoute 電路虛擬網路閘道器。 請確定虛擬網路閘道器會建立並準備好迎接連結之前執行指令程式︰

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>在不同的訂閱將虛擬網路連線到電路

您可以共用 ExpressRoute 電路跨多個訂閱。 下圖顯示一個簡單的方式共用的作業方式 ExpressRoute 電路圖解跨多個訂閱。

每個較小的雲朵大型雲端中用來代表所屬組織內的不同部門的訂閱。 每個組織中的部門可以使用自己的訂閱部署其服務，但它們可以共用單一 ExpressRoute 電路回到您的內部部署網路連線。 單一部門 (在此範例中︰ IT) 可以擁有 ExpressRoute 電路。 在組織內其他訂閱，可以使用 ExpressRoute 電路。

>[AZURE.NOTE] 連線與頻寬專用電路費用會套用到 ExpressRoute 電路擁有者。 所有的虛擬網路共用相同的頻寬。

![跨訂閱連線](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>管理

*電路擁有者*是 ExpressRoute 電路資源的授權的 power 使用者。 電路擁有者可以建立可折*電路*使用者的授權。 *電路使用者*已擁有者的虛擬網路閘道器 （的不是在同一份訂閱為 ExpressRoute 電路）。 *電路使用者*可以兌換 （虛擬網路每一個授權） 的授權。

*電路擁有者*有修改，和撤銷隨時的授權。 撤銷所有連結連線已撤銷其存取權的訂閱刪除授權結果。

### <a name="circuit-owner-operations"></a>電路擁有者作業 

#### <a name="creating-an-authorization"></a>建立驗證
    
電路擁有人所建立的授權。 這會建立的授權金鑰電路使用者可以使用連線至 ExpressRoute 電路其虛擬網路閘道器。 授權是有效的只有一個連線。

下列 cmdlet 程式碼片段顯示如何建立授權︰

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

授權索引鍵和狀態，會包含此回應︰

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>檢閱的授權

電路擁有者可以檢閱所有授權所發行特定電路上，執行下列 cmdlet:

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>新增授權

電路擁有者可以使用下列 cmdlet，以新增授權︰

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>刪除的授權

電路擁有者可以撤銷/刪除授權給使用者執行下列 cmdlet:

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>電路使用者作業

電路使用者需要對等識別碼和授權金鑰電路擁有者。 [授權] 鍵是 GUID。

對等識別碼，可以從下列命令核取。

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>兌換連線的授權

電路使用者可以執行下列 cmdlet 兌換連結授權︰

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>釋放連線的授權

您可以連結至虛擬網路的 ExpressRoute 電路刪除連線以釋放授權。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
