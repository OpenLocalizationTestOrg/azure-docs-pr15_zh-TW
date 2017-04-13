<properties 
   pageTitle="控制路由，並使用虛擬就資源管理員 」 中使用 Azure CLI |Microsoft Azure"
   description="瞭解如何控制路由，並使用虛擬就使用 Azure CLI"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>建立使用者定義路由 (UDR) Azure CLI 中

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[建立 UDRs 傳統部署模型中](virtual-network-create-udr-classic-cli.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

下列範例 Azure CLI 命令預期已根據上述情況建立簡單環境。 如果您想要執行這份文件中所顯示的命令，第一次部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)建立測試環境**部署至 Azure**、 取代預設參數值，如有必要，，然後按一下依照入口網站中的指示進行。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>建立前端子網路 UDR
若要建立的路由表及傳送需要根據上述情況，前端子網路，請遵循下列步驟。

3. 執行**`azure network route-table create`**命令，以建立前端子網路路由表格。

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    輸出︰

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    參數︰
    - **-g （或--資源群組）**。 將會建立 UDR 資源群組的名稱。 我們案例中， *TestRG*。
    - **-l （或--位置）**。 將會建立新的 UDR azure 的區域。 我們案例中， *westus*。
    - **-n （或--名稱）**。 新的 UDR 名稱。 我們案例中， *UDR FrontEnd*。

4. 執行**`azure network route-table route create`**建立路由傳送至**FW1** VM (192.168.0.4) 的後端子網路 (192.168.2.0/24) 所有流量建立上方路由表格] 命令。

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    輸出︰

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    參數︰
    - **-r （或--傳送資料表名稱）**。 將會新增路由傳送資料表的名稱。 我們案例中， *UDR FrontEnd*。
    - **-（或--地址前置字元）**。 地址封包的目的地位置的子網路的前置字元。 我們案例中， *192.168.2.0/24*。
    - **-y （或--下一步躍點等）**。 類型的物件流量便會傳送到。 可能的值是*VirtualAppliance*、 *VirtualNetworkGateway*、 *VNETLocal*、*網際網路*]，或*無*。
    - **-p (或--下一步躍點-ip 位址**)。 下一步的躍點的 IP 位址。 我們案例中， *192.168.0.4*。

5. 執行**`azure network vnet subnet set`**命令，以建立路由表上面建立和**FrontEnd**子網路之間的關聯。

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    輸出︰

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    參數︰
    - **-e （或-vnet 名稱）**。 子網路所在 VNet 的名稱。 我們案例中， *TestVNet*。
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>建立後端子網路 UDR
若要建立路由表和路由所需的後端子網路根據上述情況，請遵循下列步驟。

1. 執行**`azure network route-table create`**命令，以建立後端子網路路由表格。

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. 執行**`azure network route-table route create`**建立路由傳送至**FW1** VM (192.168.0.4) 的前端子網路 (192.168.1.0/24) 所有流量建立上方路由表格] 命令。

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. 執行**`azure network vnet subnet set`**命令，以建立路由表先前所建立的**後端**子網路之間的關聯。

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>啟用上 FW1 IP 轉送
若要啟用在 NIC **FW1**所使用的 IP，請遵循下列步驟。

1. 執行**`azure network nic show`**命令，以及**啟用 IP 轉接**注意事項值。 應該將它設定為*false*。

        azure network nic show -g TestRG -n NICFW1

    輸出︰
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. 執行**`azure network nic set`**命令，以啟用 IP 轉寄。

        azure network nic set -g TestRG -n NICFW1 -f true

    輸出︰

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    參數︰

    - **-f （或--啟用 ip 轉送）**。 *true*或*false*。
