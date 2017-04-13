<properties 
   pageTitle="控制路由，並使用虛擬就在 [資源管理員] 中，使用 PowerShell |Microsoft Azure"
   description="瞭解如何控制路由，並使用虛擬就在 [資源管理員] 中，使用 PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-resource-manager-by-using-powershell"></a>使用 PowerShell 資源管理員] 中建立使用者定義的路由 (UDR)

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[建立 UDRs 傳統部署模型中](virtual-network-create-udr-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

範例 PowerShell 命令下方預期已經建立一個簡單環境根據上述情況。 如果您想要執行這份文件中所顯示的命令，第一次部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)建立測試環境**部署至 Azure**、 取代預設參數值，如有必要，，然後按一下依照入口網站中的指示進行。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>建立前端子網路 UDR
若要建立的路由表及傳送需要根據上述情況，前端子網路，請遵循下列步驟。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. 建立用來傳送給所有流量路由傳送至**FW1**虛擬應用裝置 (192.168.0.4) 的後端子網路 (192.168.2.0/24) 路由。

        $route = New-AzureRmRouteConfig -Name RouteToBackEnd `
            -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

4. 建立一個名為**UDR FrontEnd**包含先前所建立的路由**westus**區域中的路由資料表。

        $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
            -Name UDR-FrontEnd -Route $route

5. 建立包含子網路在哪裡 VNet 的變數。 在我們的案例中，VNet 稱為**TestVNet**。

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. 建立路由表格建立上方**FrontEnd**子網路之間的關聯。
        
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            -AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

>[AZURE.WARNING] 上述命令的輸出效果會顯示虛擬網路設定物件，只存在於執行 PowerShell 電腦的內容。 您需要執行**設定 AzureVirtualNetwork** cmdlet Azure 儲存這些設定。

7. Azure 中儲存新的子網路設定。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    預期的輸出︰

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
                            
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                                ...,
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              },
                                ...
                            ]   

## <a name="create-the-udr-for-the-back-end-subnet"></a>建立後端子網路 UDR
若要建立路由表和路由所需的後端子網路根據上述情況，請遵循下列步驟。

1. 建立用來傳送給所有流量路由傳送至**FW1**虛擬應用裝置 (192.168.0.4) 的前端子網路 (192.168.1.0/24) 路由。

        $route = New-AzureRmRouteConfig -Name RouteToFrontEnd `
            -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

4. 建立一個名為**UDR 後端**包含先前所建立的路由**uswest**區域中的路由資料表。

        $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
            -Name UDR-BackEnd -Route $route

5. 建立路由表格建立上方的**後端**子網路之間的關聯。

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
            -AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

7. Azure 中儲存新的子網路設定。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    預期的輸出︰

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
                            
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              ...,
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="enable-ip-forwarding-on-fw1"></a>啟用上 FW1 IP 轉送
若要啟用在 NIC **FW1**所使用的 IP，請遵循下列步驟。

1. 建立包含 NIC FW1 所使用的設定的變數。 在我們的案例中，而稱為**NICFW1**。

        $nicfw1 = Get-AzureRmNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. 啟用 IP 轉寄，並儲存 NIC 的設定。

        $nicfw1.EnableIPForwarding = 1
        Set-AzureRmNetworkInterface -NetworkInterface $nicfw1

    預期的輸出︰

        Name                 : NICFW1
        ResourceGroupName    : TestRG
        Location             : westus
        Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState    : Succeeded
        Tags                 : 
                               Name         Value                  
                               ===========  =======================
                               displayName  NetworkInterfaces - DMZ
                               
        VirtualMachine       : {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
                               }
        IpConfigurations     : [
                                 {
                                   "Name": "ipconfig1",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
                                   "PrivateIpAddress": "192.168.0.4",
                                   "PrivateIpAllocationMethod": "Static",
                                   "Subnet": {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
                                   },
                                   "PublicIpAddress": {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
                                   },
                                   "LoadBalancerBackendAddressPools": [],
                                   "LoadBalancerInboundNatRules": [],
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
        DnsSettings          : {
                                 "DnsServers": [],
                                 "AppliedDnsServers": [],
                                 "InternalDnsNameLabel": null,
                                 "InternalFqdn": null
                               }
        EnableIPForwarding   : True
        NetworkSecurityGroup : null
        Primary              : True

