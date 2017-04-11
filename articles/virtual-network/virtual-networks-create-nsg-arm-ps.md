<properties
   pageTitle="如何建立 NSGs Azure 資源管理員] 中，使用 PowerShell |Microsoft Azure"
   description="瞭解如何建立並部署 NSGs Azure 資源管理員] 中，使用 PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-create-nsgs-in-resource-manager-by-using-powershell"></a>如何建立 NSGs 資源管理員] 中，使用 PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[建立 NSGs 傳統部署模型中](virtual-networks-create-nsg-classic-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

範例 PowerShell 命令下方預期已經建立一個簡單環境根據上述情況。 如果您想要執行這份文件中所顯示的命令，第一次部署[此範本](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd)建立測試環境**部署至 Azure**、 取代預設參數值，如有必要，，然後按一下依照入口網站中的指示進行。

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>如何建立前端子網路 NSG
若要建立 NSG 名為*NSG FrontEnd*根據上述情況，請遵循下列步驟︰

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) ，並遵循指示到登入 Azure，然後選取您的訂閱結束。

2. 建立允許從網際網路存取其連接埠 3389 安全性規則。

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100
            -SourceAddressPrefix Internet -SourcePortRange *
            -DestinationAddressPrefix * -DestinationPortRange 3389

3. 建立允許從網際網路存取其連接埠 80 安全性規則。

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 101
            -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix *
            -DestinationPortRange 80

4. 新增至新的 NSG 名為**NSG FrontEnd**先前所建立的規則。

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus
        -Name "NSG-FrontEnd" -SecurityRules $rule1,$rule2

5. 核取 NSG 中建立的規則。

        $nsg

    輸出顯示安全性規則︰

        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
                                   "Description": "Allow RDP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "3389",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 100,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "web-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
                                   "Description": "Allow HTTP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "80",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 101,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

6. 建立 NSG 建立上方*FrontEnd*子網路之間的關聯。

                    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
                    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
                        -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

                Output showing only the *FrontEnd* subnet settings, notice the value for the **NetworkSecurityGroup** property:

                    Subnets           : [
                                          {
                                            "Name": "FrontEnd",
                                            "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                            "AddressPrefix": "192.168.1.0/24",
                                            "IpConfigurations": [
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                              },
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                              }
                                            ],
                                            "NetworkSecurityGroup": {
                                              "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                            },
                                            "RouteTable": null,
                                            "ProvisioningState": "Succeeded"
                                          }

    >[AZURE.WARNING] 上述命令的輸出效果會顯示虛擬網路設定物件，只存在於執行 PowerShell 電腦的內容。 您需要執行`Set-AzureRmVirtualNetwork`Azure 儲存這些設定的指令程式。

7. 將新的 VNet 設定儲存至 Azure 中。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    輸出顯示 NSG 部分︰

        "NetworkSecurityGroup": {
          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
        }

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>如何建立後端子網路 NSG
若要建立名為*NSG 後端*為基礎上述 NSG，請遵循下列步驟︰

1. 建立規則，允許連接埠 1433 （SQL Server 使用預設連接埠） 前端子網路存取安全性。

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100
            -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange *
            -DestinationAddressPrefix * -DestinationPortRange 1433

2. 建立安全性規則封鎖存取網際網路。

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet"
            -Access Deny -Protocol * -Direction Outbound -Priority 200
            -SourceAddressPrefix * -SourcePortRange *
            -DestinationAddressPrefix Internet -DestinationPortRange *

3. 新增至新的 NSG 名為**NSG 後端**先前所建立的規則。

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-BackEnd"
            -SecurityRules $rule1,$rule2

4. 建立 NSG 建立上方的*後端*子網路之間的關聯。

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd
            -AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

    輸出顯示只*後端*子網路的設定，請注意**NetworkSecurityGroup**屬性的值︰

        Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

5. 將新的 VNet 設定儲存至 Azure 中。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet


## <a name="how-to-remove-an-nsg"></a>如何移除 NSG

若要刪除現有的 NSG，稱為的*NSG Frontend*在此情況下，請遵循下列步驟︰

執行**移除 AzureRmNetworkSecurityGroup**以下所示，並請務必包含 NSG 位於資源群組。

            Remove-AzureRmNetworkSecurityGroup -Name "NSG-FrontEnd" -ResourceGroupName "TestRG"
