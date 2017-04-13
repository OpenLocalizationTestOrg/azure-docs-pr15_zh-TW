<properties 
   pageTitle="控制路由，並使用傳統的部署模型中使用 PowerShell 虛擬就 |Microsoft Azure"
   description="瞭解如何控制 VNets 傳統部署模型中使用 PowerShell 中路由"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>控制路由，並使用虛擬就 （傳統） 使用 PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

下列命令預期已經建立一個簡單環境 PowerShell 的 Azure 根據上述情況的範例。 如果您想要執行這份文件中所顯示的命令，，建立環境中[建立 （傳統） 使用 PowerShell VNet](virtual-networks-create-vnet-classic-netcfg-ps.md)顯示。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>建立前端子網路 UDR
若要建立的路由表及傳送需要根據上述情況，前端子網路，請遵循下列步驟。

3. 執行**`New-AzureRouteTable`**cmdlet 來建立前端子網路路由表格。

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    輸出︰

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. 執行**`Set-AzureRoute`**cmdlet 來建立路由傳送資料表中建立上方，若要傳送所有流量來**FW1** VM (192.168.0.4) 的後端子網路 (192.168.2.0/24)。
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    輸出︰

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. 執行**`Set-AzureSubnetRouteTable`** **FrontEnd**子網路以建立上方的 cmdlet 來建立 [傳送] 資料表的關聯。

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>建立後端子網路 UDR
若要建立路由表和路由所需的後端子網路根據上述情況，請遵循下列步驟。

3. 執行**`New-AzureRouteTable`**cmdlet 來建立後端子網路路由表格。

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. 執行**`Set-AzureRoute`**cmdlet 來建立路由傳送資料表中建立上方，若要傳送所有流量來**FW1** VM (192.168.0.4) 的前端子網路 (192.168.1.0/24)。

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. 執行**`Set-AzureSubnetRouteTable`****後端**子網路以建立上方的 cmdlet 來建立 [傳送] 資料表的關聯。

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>啟用 FW1 VM 上的 IP 轉送
若要啟用 IP FW1 VM 中轉接，請遵循下列步驟。

1. 執行**`Get-AzureIPForwarding`**cmdlet 來 chec IP 轉寄的狀態

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    輸出︰

        Disabled

2. 執行**`Set-AzureIPForwarding`**命令，以啟用*FW1* VM IP 轉寄功能。

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
