<properties
   pageTitle="連線 VPN 閘道器與 PowerShell 的 Azure VNets |Microsoft Azure"
   description="本文會引導您在一起使用 Azure 資源管理員與 PowerShell 連線虛擬網路。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-vnet-to-vnet-connection-for-resource-manager-using-powershell"></a>資源管理員 」 的使用 PowerShell 設定 VNet-VNet 連線

> [AZURE.SELECTOR]
- [資源管理員-Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [資源管理員-PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [傳統-傳統入口網站](virtual-networks-configure-vnet-to-vnet-connection.md)

本文會引導您將建立在資源管理員部署模型中使用 VPN 閘道 VNets 之間的連線的步驟。 在相同或不同的區域，並從相同或不同的訂閱，可以是虛擬網路。


![v2v 圖表](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>部署模型和 VNet-VNet 連線的方法

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示目前可用的部署模型和 VNet-VNet 設定的方法。 當有包含設定步驟的文章時，我們連結直接從這個資料表。

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>對等 VNet

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="about-vnet-to-vnet-connections"></a>關於 VNet-VNet 連線

虛擬網路連線到另一個虛擬網路 (VNet-VNet) 很類似 VNet 連線至內部部署網站位置。 這兩種連線類型使用 Azure VPN 閘道器提供使用 IPsec/IKE 的安全通道。 您連線 VNets 可以在不同區域。 或在不同的訂閱。 您甚至可以結合多網站組態 VNet-VNet 通訊。 此可讓，您建立合併的網路拓撲跨內部部署與虛擬間的網路連線的連線，如下圖所示︰


![關於連線](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### <a name="why-connect-virtual-networks"></a>為什麼連線虛擬網路？

若要將虛擬網路連線，原因如下︰

- **跨地區地理重複性和地理目前狀態**
    - 您可以設定您自己的地理複寫或同步處理使用安全連線不透過網際網路的結束點。
    - Azure 流量管理員及負載平衡器，您可以設定跨多個 Azure 區域高度可用地理重複使用的工作量。 其中一個重要的範例是設定 SQL 永遠分配跨多個 Azure 區域的可用群組。

- **隔離或管理邊界的地區多層應用程式**
    - 在同一個區域中，您可以設定多層應用程式使用多個連接在一起，因為隔離或管理需求的虛擬網路。


### <a name="vnet-to-vnet-faq"></a>VNet-VNet 常見問題集

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


## <a name="which-set-of-steps-should-i-use"></a>哪些設定步驟的我應該使用？

本文中，您會看到兩組不同的步驟。 一組步驟[VNets 的位於同一份訂閱](#samesub)，與其他的[VNets 的位於不同的訂閱](#difsub)。 集之間的主要差異是是否您可以建立及設定在相同的 PowerShell 工作階段中的所有虛擬網路和閘道器資源。

本文中的步驟，使用變數宣告在每一章節的開頭。 如果您已使用現有的 VNets，修改以反映您自己的環境中設定的變數。 

![兩個連線](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


## <a name="samesub"></a>如何連線 VNets 的同一份訂閱

![v2v 圖表](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>開始之前
    
在開始之前，必須先安裝 Azure 資源管理員 PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

### <a name="Step1"></a>步驟 1-規劃您的 IP 位址範圍


在下列步驟，我們可以建立兩個虛擬網路，以及其個別的閘道器子網路和設定。 然後，我們會建立兩個 VNets 之間的連線。 請務必規劃您的網路設定的 IP 位址範圍。 請記住，您必須確定不 VNet 範圍或區域網路範圍的重疊以任何方式。

我們的範例中使用下列的值︰

**TestVNet1 值︰**

- VNet 名稱︰ TestVNet1
- 資源群組︰ TestRG1
- 位置︰ 東亞美國
- TestVNet1: 10.11.0.0/16 與 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- 後端︰ 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- DNS 伺服器︰ 8.8.8.8
- GatewayName: VNet1GW
- 公用 IP: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- 連線類型︰ VNet2VNet


**TestVNet4 值︰**

- VNet 名稱︰ TestVNet4
- TestVNet2: 10.41.0.0/16 與 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- 後端︰ 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- 資源群組︰ TestRG4
- 位置︰ 西美國
- DNS 伺服器︰ 8.8.8.8
- GatewayName: VNet4GW
- 公用 IP: VNet4GWIP
- VPNType: RouteBased
- 連線︰ VNet4toVNet1
- 連線類型︰ VNet2VNet



### <a name="Step2"></a>步驟 2-建立及設定 TestVNet1

1. 宣告變數

    先宣告變數。 此範例會宣告變數這個練習中使用的值。 在大部分情況下，您應該使用您自己取代的值。 不過，您可以使用這些變數，如果您熟悉此類型的設定步驟執行。 修改變數，如有需要然後複製並貼到您的 PowerShell 主控台。

        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"

2. 連線至您的訂閱

    切換至 [使用資源管理員 cmdlet PowerShell 模式。 開啟您 PowerShell 主控台，連線到您的帳戶。 您可以使用下列範例可協助您連線︰

        Login-AzureRmAccount

    核取訂閱的帳戶。

        Get-AzureRmSubscription 

    指定您想要使用的訂閱。

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. 建立新的資源群組

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. 建立 TestVNet1 的子網路設定

    此範例會建立名為 TestVNet1 與三個子網路、 一個稱為的 GatewaySubnet、 一個稱為的 FrontEnd 和一個稱為後端虛擬網路。 當替代值，是很重要您永遠命名閘道器子網路特別 GatewaySubnet。 如果您將其命名其他項目，您閘道器將無法建立。 

    下列範例會使用您先前設定的變數。 在此範例中，使用 /27 閘道器子網路。 雖然可以建立閘道器子網路 /29 小，我們建議您建立較大的子網路選取至少 /28 或 /27 包含多個地址。 這可讓足夠的地址，以容納，您可以在未來可能額外設定。 

        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. 建立 TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. 要求的公用 IP 位址

    要求要配置給您，為您 VNet 會建立閘道器的公用 IP 位址。 請注意，AllocationMethod 動態。 您無法指定您想要使用的 IP 位址。 動態配置至您的閘道器。 

        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic

7. 建立閘道器設定

    閘道器組態定義子網路，以及要使用的公用 IP 位址。 使用範例，建立您的閘道器設定。 

        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1

8. 建立閘道器的 TestVNet1

    在此步驟中，您會為您 TestVNet1 建立虛擬網路閘道器。 VNet-VNet 設定需要 RouteBased VpnType。 建立閘道器時可能需要 （45 分鐘或更多] 以完成）。

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>步驟 3-建立及設定 TestVNet4

一旦您設定 TestVNet1，建立 TestVNet4。 請依照下，使用您自己的需要時取代的值。 可以在相同的 PowerShell 工作階段中完成這個步驟，因為它是在同一份訂閱。

1. 宣告變數

    請務必以您想要使用您設定的項目取代的值。

        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"

    在繼續之前，請確定您仍然連線訂閱 1。

2. 建立新的資源群組

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. 建立 TestVNet4 的子網路設定

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. 建立 TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. 要求的公用 IP 位址

        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic

6. 建立閘道器設定

        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. 建立 TestVNet4 閘道器

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>步驟 4-連線閘道器

1. 取得兩個虛擬網路閘道器

    在此範例中，因為這兩個閘道器在同一份訂閱，此步驟即可完成相同的 PowerShell 工作階段。

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. 建立到 TestVNet4 連線 TestVNet1

    在此步驟中，您建立連線 TestVNet1 至 TestVNet4。 您會看到共用的金鑰參照的範例。 您可以使用您自己的值的 [共用] 鍵。 重要的是共用的金鑰必須符合的兩個連線。 若要完成簡短時可能需要建立的連線。

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. 建立到 TestVNet1 連線 TestVNet4

    這是步驟類似上方，除了您建立的連線從 TestVNet4 TestVNet1。 請確定符合共用索引鍵。

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    請稍候幾分鐘，應該建立連線。

4. 請確認您的連線。 請參閱[如何驗證您的連線](#verify)。


## <a name="difsub"></a>如何連線 VNets 中不同的訂閱


![v2v 圖表](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

在此案例中，我們連接 TestVNet1 和 TestVNet5。 TestVNet1 和 TestVNet5 位於不同的訂閱。 此設定的步驟才能將 TestVNet1 連線至 TestVNet5 新增額外的 VNet-VNet 連線。 

此處的差別一些步驟，設定要在第二個訂閱的內容中的個別 PowerShell 工作階段中執行。 尤其是當兩個訂閱屬於不同的組織。 

從先前的步驟，列於上方，繼續指示進行。 您必須完成[步驟 1](#Step1)和[步驟 2](#Step2) ，以建立及設定 TestVNet1 TestVNet1 及 VPN 閘道器。 當您完成步驟 1 到步驟 2 時，繼續執行步驟 5，建立 TestVNet5。

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>步驟 5-驗證額外的 IP 位址範圍

請務必確認新虛擬網路 TestVNet5 的 IP 位址空間不會與任何 VNet 範圍或區域網路閘道器範圍的重疊。 

在此範例中，虛擬網路可能屬於不同的組織。 此練習中，您可以使用下列的值為 TestVNet5:

**TestVNet5 值︰**

- VNet 名稱︰ TestVNet5
- 資源群組︰ TestRG5
- 位置︰ 日本東亞
- TestVNet5: 10.51.0.0/16 與 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- 後端︰ 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- DNS 伺服器︰ 8.8.8.8
- GatewayName: VNet5GW
- 公用 IP: VNet5GWIP
- VPNType: RouteBased
- 連線︰ VNet5toVNet1
- 連線類型︰ VNet2VNet

**其他 TestVNet1 的值︰**

- 連線︰ VNet1toVNet5


### <a name="step-6---create-and-configure-testvnet5"></a>步驟 6-建立及設定 TestVNet5

新的訂閱的內容中，必須完成這個步驟。 管理員可以在不同的組織擁有的訂閱，可以執行此組件。

1. 宣告變數

    請務必以您想要使用您設定的項目取代的值。

        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"

2. 連線至訂閱 5

    開啟您 PowerShell 主控台，連線到您的帳戶。 您可以使用下列範例可協助您連線︰

        Login-AzureRmAccount

    核取訂閱的帳戶。

        Get-AzureRmSubscription 

    指定您想要使用的訂閱。

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. 建立新的資源群組

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. 建立 TestVNet4 的子網路設定
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. 建立 TestVNet5

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. 要求的公用 IP 位址

        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic


7. 建立閘道器設定

        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. 建立 TestVNet5 閘道器

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>步驟 7-連線閘道器

在此範例中，因為閘道器在不同的訂閱，我們已分割此步驟中將兩個 PowerShell 工作階段標示為 [訂閱 1] 和 [訂閱 5]。

1. **[訂閱 1]**虛擬網路閘道器取得訂閱 1

    請確認您登入，並連線到訂閱 1。

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    複製下列元素的輸出並傳送這些訂閱 5 的系統管理員透過電子郵件或另一種方法。

        $vnet1gw.Name
        $vnet1gw.Id

    下列兩個項目會有類似下列範例輸出值︰

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[訂閱 5]**取得訂閱 5 的虛擬網路閘道器

    請確認您登入並連線到訂閱 5。

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    複製下列元素的輸出並傳送這些訂閱 1 的系統管理員透過電子郵件或另一種方法。

        $vnet5gw.Name
        $vnet5gw.Id

    下列兩個項目會有類似下列範例輸出值︰

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[訂閱 1]**建立到 TestVNet5 連線 TestVNet1

    在此步驟中，您建立連線 TestVNet1 至 TestVNet5。 以下的差異，是因為它是在不同的訂閱中的 $vnet5gw 也無法直接取得。 您必須使用傳達訂閱 1 上述步驟中的值建立新的 PowerShell 物件。 使用下列的範例。 您自己的值取代名稱、 Id 和共用索引鍵。 重要的是共用的金鑰必須符合的兩個連線。 若要完成簡短時可能需要建立的連線。

    請確定您連線至訂閱 1。 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[訂閱 5]**建立到 TestVNet1 連線 TestVNet5

    這是步驟類似上方，除了您建立的連線從 TestVNet5 TestVNet1。 建立從訂閱 1 取得數值為根據的 PowerShell 物件的相同程序也適用於以下方式。 在此步驟中，請務必將共用的金鑰比對。

    請確定您連線至訂閱 5。

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>如何驗證連線


[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="next-steps"></a>後續步驟

- 完成您的連線之後，您可以新增到您的虛擬網路的虛擬機器。 如需的步驟，請參閱[建立虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。
- BGP 的相關資訊，請參閱[BGP 概觀](vpn-gateway-bgp-overview.md)以及[如何設定 BGP](vpn-gateway-bgp-resource-manager-ps.md)。 

