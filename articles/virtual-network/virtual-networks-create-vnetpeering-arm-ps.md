<properties
   pageTitle="建立 VNet 對等使用 Powershell cmdlet |Microsoft Azure"
   description="瞭解如何建立一個虛擬網路 Azure 入口網站中資源管理員。"
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>建立 VNet 對等使用 Powershell cmdlet

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要建立對等使用 PowerShell VNet，請遵循下列步驟︰

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) ，並遵循指示到登入 Azure，然後選取您的訂閱結束。

> [AZURE.NOTE] 適用於管理 VNet 對等的 PowerShell cmdlet 隨附於[Azure PowerShell 1.6。](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. 讀取虛擬網路物件︰

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. 若要建立對等的 VNet，您需要建立另一個用於每個方向的兩個連結。 下列步驟將會建立 VNet 對等的連結至 VNet2 VNet1 的第一次︰

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    輸出顯示︰

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. 此步驟會建立以 VNet1 VNet2 VNet 對等的連結︰

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    輸出顯示︰

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. VNet 對等連結建立後，您可以查看下列連結狀態︰

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    輸出顯示︰

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    有幾個可設定 VNet 對等的內容︰

  	|選項|描述|預設值|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|是否地址等 VNet 要 Virtual_network 標籤的一部分的空間|[是]|
  	|AllowForwardedTraffic|是否接受或卸除不源自 peered VNet 流量|無|
  	|AllowGatewayTransit|允許對使用 VNet 閘道 VNet|無|
  	|UseRemoteGateways|使用您對 VNet 閘道器。 對 VNet 必須將閘道器設定和 AllowGatewayTransit 選取。 如果您有閘道器設定，您便無法使用此選項|無|

    在 VNet 對等的每個連結有一組以上的屬性。 例如，您可以將 AllowVirtualNetworkAccess 設定為 True 的 VNet 對等連結 VNet1 VNet2，並將它設定為在其他方向 VNet 對等的連結，則為 False。

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    您可以執行取得 AzureRmVirtualNetworkPeering 以雙核取 [屬性] 的值變更後。 從 [輸出] 清單中，您可以看到 AllowForwardedTraffic 執行上述 cmdlet 之後將設定變更為 True。

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    對等在這個案例中建立之後，您應該可以起始連線從任何虛擬機器任何虛擬機器的兩個 VNets。 根據預設，AllowVirtualNetworkAccess 為 True，而且 VNet 對等會佈建適當 Acl 允許 VNets 之間的通訊。 您仍然可以套用網路安全性群組 (NSG) 規則封鎖特定子網路或取得的兩個虛擬網路之間的存取權的細部控制權的虛擬機器之間的連線。  如需有關如何建立 NSG 規則的詳細資訊，請參閱這[篇文章](virtual-networks-create-nsg-arm-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

若要建立 VNet 外面使用 PowerShell 的訂閱，請遵循下列步驟︰

1. 登入 Azure 權限的使用者 A 的帳戶訂閱 A 並執行下列 cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    這不是需求，可以建立對等，即使使用者個別提高對等的要求的其個別 VNets，只要符合要求。 在本機 VNet 中的使用者身分新增獲授限的使用者的其他 VNet 讓您更容易執行安裝程式。

2. 登入 Azure 權限-B 使用者的帳戶訂閱 B，然後執行下列 cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. 在 [使用者 A 的登入工作階段，執行下列 cmdlet:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. B 使用者的登入工作階段，執行下列 cmdlet:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. 對等建立之後，任何的虛擬機器中 VNet3 應會與任何的虛擬機器中 VNet5 通訊。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 在此案例中，您可以執行下列建立 VNet 對等的 PowerShell 指令程式。  您需要 AllowForwardedTraffic 屬性設為 True，並連結 VNET1 HubVNet，可從輸入的流量以外的對等的 VNet 位址空間。

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. 對等建立之後，您可以參照此[文件](virtual-network-create-udr-arm-ps.md)，並定義使用者定義的路徑 (UDR) VNet1 流量重新導向透過虛擬的應用裝置，若要使用其功能。 當您指定的下一個躍點地址傳送時，可以設定該中對 VNet HubVNet 虛擬應用裝置的 IP 位址。 以下是範例︰

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

若要建立對等傳統的虛擬網路與 PowerShell 的 Azure 資源管理員虛擬網路之間 VNet，請遵循下列步驟︰

1. 閱讀虛擬網路物件的**VNET1**，Azure 資源管理員虛擬網路，如下所示︰

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. 若要建立 VNet 對等在這個案例中，需要只有一個連結，特別是連結從**VNET1** **VNET2**。 此步驟可讓您必須知道傳統 VNet 資源識別碼。 資源群組識別碼格式看起來像︰

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    請務必 SubscriptionID ResourceGroupName，與 VirtualNetworkName 換成適當的名稱。

    作法如下︰

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. 一次的 VNet，會建立對等的連結，您可以查看連結狀態輸出以下所示︰

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>移除 VNet 對等

1.  若要移除對等 VNet，您需要執行下列 cmdlet:

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. 一旦您移除中 VNET 外面的一個連結，請對連結狀態就會前往中斷連線。 在這個狀態，您無法重新建立連結直到等連結狀態變更為起始。 我們建議您移除再重新建立 VNet 對等的兩個連結。
