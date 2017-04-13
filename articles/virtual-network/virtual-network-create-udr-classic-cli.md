<properties 
   pageTitle="控制路由，並使用傳統的部署模型中使用 Azure CLI 虛擬就 |Microsoft Azure"
   description="瞭解如何控制中 VNets 傳統部署模型中使用 Azure CLI 路由"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>控制路由，並使用使用 Azure CLI 虛擬就 （傳統）

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[控制項路由，並在資源管理員部署模型中的虛擬設備](virtual-network-create-udr-arm-cli.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

下列範例 Azure CLI 命令預期已根據上述情況建立簡單環境。 如果您想要執行這份文件中所顯示的命令，，建立環境中[建立 （傳統） 使用 Azure CLI VNet](virtual-networks-create-vnet-classic-cli.md)顯示。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>建立前端子網路 UDR
若要建立的路由表及傳送需要根據上述情況，前端子網路，請遵循下列步驟。

1. 執行**`azure config mode`**可切換到傳統模式。

        azure config mode asm

    輸出︰

        info:    New mode is asm

3. 執行**`azure network route-table create`**命令，以建立前端子網路路由表格。

        azure network route-table create -n UDR-FrontEnd -l uswest

    輸出︰

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    參數︰
    - **-l （或--位置）**。 將會建立新的 NSG azure 的區域。 我們案例中， *westus*。
    - **-n （或--名稱）**。 新的 NSG 名稱。 我們案例中， *NSG FrontEnd*。

4. 執行**`azure network route-table route set`**建立路由傳送至**FW1** VM (192.168.0.4) 的後端子網路 (192.168.2.0/24) 所有流量建立上方路由表格] 命令。

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    輸出︰

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    參數︰
    - **-r （或--傳送資料表名稱）**。 將會新增路由傳送資料表的名稱。 我們案例中， *UDR FrontEnd*。
    - **-（或--地址前置字元）**。 地址封包的目的地位置的子網路的前置字元。 我們案例中， *192.168.2.0/24*。
    - **-t （或--下一步躍點等）**。 類型的物件流量便會傳送到。 可能的值是*VirtualAppliance*、 *VirtualNetworkGateway*、 *VNETLocal*、*網際網路*]，或*無*。
    - **-p (或--下一步躍點-ip 位址**)。 下一步的躍點的 IP 位址。 我們案例中， *192.168.0.4*。

5. 執行**`azure network vnet subnet route-table add`**命令，以建立路由表上面建立和**FrontEnd**子網路之間的關聯。

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    輸出︰

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    參數︰
    - **-t （或-vnet 名稱）**。 子網路所在 VNet 的名稱。 我們案例中， *TestVNet*。
    - **-n (或--子網路名稱**。 將表格便會新增至子網路的名稱。 我們案例中， *FrontEnd*。
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>建立後端子網路 UDR
若要建立路由表和路由所需的後端子網路根據上述情況，請遵循下列步驟。

3. 執行**`azure network route-table create`**命令，以建立後端子網路路由表格。

        azure network route-table create -n UDR-BackEnd -l uswest

4. 執行**`azure network route-table route set`**建立路由傳送至**FW1** VM (192.168.0.4) 的前端子網路 (192.168.1.0/24) 所有流量建立上方路由表格] 命令。

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. 執行**`azure network vnet subnet route-table add`**命令，以建立路由表先前所建立的**後端**子網路之間的關聯。

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

