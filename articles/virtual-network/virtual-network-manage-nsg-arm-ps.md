<properties 
   pageTitle="管理資源管理員] 中使用 PowerShell NSGs |Microsoft Azure"
   description="瞭解如何管理現存 NSGs 使用 PowerShell 中資源管理員"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>管理 NSGs 使用 PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>擷取資訊

您可以檢視您現有的 NSGs、 擷取規則的現有的 NSG，及找出哪些資源 NSG 相關聯。

### <a name="view-existing-nsgs"></a>檢視現有的 NSGs
若要檢視所有現有 NSGs 訂閱，請執行`Get-AzureRmNetworkSecurityGroup`cmdlet，如下所示。

    Get-AzureRmNetworkSecurityGroup

預期的結果︰

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


若要檢視的清單 NSGs 特定的資源群組中，執行`Get-AzureRmNetworkSecurityGroup`cmdlet，如下所示。 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

預期的輸出︰

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>NSG 的所有規則都清單

若要檢視的名稱為**NSG FrontEnd**NSG 規則，請執行`Get-AzureRmNetworkSecurityGroup`cmdlet，如下所示。 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

預期的輸出︰
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] 您也可以使用`Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules`清單**NSG FrontEnd** NSG 從預設的規則。

### <a name="view-nsgs-associations"></a>檢視 NSGs 關聯

若要檢視的資源是**NSG FrontEnd** NSG 的關聯，請執行`Get-AzureRmNetworkSecurityGroup`cmdlet，如下所示。

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

尋找**NetworkInterfaces**和**子網路**屬性如下所示︰

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

在上述範例中，NSG 相關任何網路介面 (Nic)，並不是名為**FrontEnd**子網路相關聯。

## <a name="manage-rules"></a>管理規則

您可以新增規則到現有的 NSG、 編輯現有的規則，並移除規則。

### <a name="add-a-rule"></a>新增規則

若要新增**NSG FrontEnd** NSG 任何電腦從連接埠**443**允許**輸入**傳輸規則，請遵循下列步驟。

1. 執行`Get-AzureRmNetworkSecurityGroup`cmdlet 來擷取現有 NSG，並將其儲存在變數中，如下所示。

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. 執行`Add-AzureRmNetworkSecurityRuleConfig`cmdlet，如下所示。

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. 若要儲存到 NSG 所做的變更，請執行`Set-AzureRmNetworkSecurityGroup`cmdlet，如下所示。

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    預期的輸出顯示安全性規則︰

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>變更規則

若要變更**網際網路**只允許輸入的流量上方建立的規則，請遵循下列步驟。

1. 執行`Get-AzureRmNetworkSecurityGroup`cmdlet 來擷取現有 NSG，並將其儲存在變數中，如下所示。

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. 執行`Set-AzureRmNetworkSecurityRuleConfig`cmdlet，如下所示。

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. 若要儲存到 NSG 所做的變更，請執行`Set-AzureRmNetworkSecurityGroup`cmdlet，如下所示。

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    預期的輸出顯示安全性規則︰

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>刪除規則

1. 執行`Get-AzureRmNetworkSecurityGroup`cmdlet 來擷取現有 NSG，並將其儲存在變數中，如下所示。

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. 執行`Remove-AzureRmNetworkSecurityRuleConfig`cmdlet，如下所示。

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. 若要儲存到 NSG 所做的變更，請執行`Set-AzureRmNetworkSecurityGroup`cmdlet，如下所示。

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    預期的輸出顯示僅限安全性規則**https 規則**已經不再列出的通知︰

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>管理關聯

您可以將子網路 NSG 和 Nic 關聯。 您也可以中斷 NSG 從任何它與相關聯的資源。

### <a name="associate-an-nsg-to-a-nic"></a>建立以 NIC NSG 之間的關聯

建立關聯**NSG FrontEnd** NSG 至**TestNICWeb1** NIC，請遵循下列步驟。

1. 執行`Get-AzureRmNetworkSecurityGroup`cmdlet 來擷取現有 NSG，並將其儲存在變數中，如下所示。

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. 執行`Get-AzureRmNetworkInterface`cmdlet 來擷取現有 NIC，並將其儲存在變數中，如下所示。

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. 設定**NetworkSecurityGroup**的屬性**NIC**變數**NSG**變數值，如下所示。

        $nic.NetworkSecurityGroup = $nsg

4. 若要儲存在 NIC 所做的變更，請執行`Set-AzureRmNetworkInterface`cmdlet，如下所示。

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    預期的輸出顯示**NetworkSecurityGroup**屬性︰

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>就從 NIC NSG

若要中斷**NSG FrontEnd** NSG 從**TestNICWeb1** NIC，請遵循下列步驟。

1. 執行`Get-AzureRmNetworkSecurityGroup`cmdlet 來擷取現有 NSG，並將其儲存在變數中，如下所示。

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. 執行`Get-AzureRmNetworkInterface`cmdlet 來擷取現有 NIC，並將其儲存在變數中，如下所示。

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. 設定**NetworkSecurityGroup**的屬性**NIC**變數**$null**，如下所示。

        $nic.NetworkSecurityGroup = $null

4. 若要儲存在 NIC 所做的變更，請執行`Set-AzureRmNetworkInterface`cmdlet，如下所示。

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    預期的輸出顯示**NetworkSecurityGroup**屬性︰

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>中斷子網路 NSG

若要中斷**NSG FrontEnd** NSG **FrontEnd**子網路，請遵循下列步驟。

1. 執行`Get-AzureRmVirtualNetwork`cmdlet 來擷取現有 VNet，並將其儲存在變數中，如下所示。

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. 執行`Get-AzureRmVirtualNetworkSubnetConfig`cmdlet 來擷取**FrontEnd**子網路，並將其儲存在變數中，如下所示。

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. 設定**NetworkSecurityGroup**的屬性的**子網路**變數**$null**，如下所示。

        $subnet.NetworkSecurityGroup = $null

4. 若要儲存到子網路所做的變更，請執行`Set-AzureRmVirtualNetwork`cmdlet，如下所示。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    預期的成果，顯示**FrontEnd**子網路的屬性。 請注意沒有**NetworkSecurityGroup**的屬性︰

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>建立子網路 NSG 之間的關聯

若要再次關聯**NSG FrontEnd** NSG **FronEnd**子網路，請遵循下列步驟。

1. 執行`Get-AzureRmVirtualNetwork`cmdlet 來擷取現有 VNet，並將其儲存在變數中，如下所示。

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. 執行`Get-AzureRmVirtualNetworkSubnetConfig`cmdlet 來擷取**FrontEnd**子網路，並將其儲存在變數中，如下所示。

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. 執行`Get-AzureRmNetworkSecurityGroup`cmdlet 來擷取現有 NSG，並將其儲存在變數中，如下所示。

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. 設定**NetworkSecurityGroup**的屬性的**子網路**變數**$null**，如下所示。

        $subnet.NetworkSecurityGroup = $nsg

4. 若要儲存到子網路所做的變更，請執行`Set-AzureRmVirtualNetwork`cmdlet，如下所示。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    預期的輸出顯示只**FrontEnd**子網路**NetworkSecurityGroup**屬性︰

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>刪除 NSG

您只可以刪除任何資源不相關聯 NSG。 若要刪除 NSG，請遵循下列步驟。

1. 若要檢查與 NSG 相關聯的資源，請執行`azure network nsg show`[檢視 NSGs 關聯](#View-NSGs-associations)所示。
2. 如果與任何 Nic NSG，請執行`azure network nic set`如下所示的每個 NIC [Dissociate 從 NIC NSG](#Dissociate-an-NSG-from-a-NIC) 
3. 如果 NSG 至任何子網路相關聯，請執行`azure network vnet subnet set`如下所示的每一個子網路[Dissociate NSG 從子網路](#Dissociate-an-NSG-from-a-subnet)。
4. 若要刪除 NSG，請執行`Remove-AzureRmNetworkSecurityGroup`cmdlet，如下所示。

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] **-強制**參數，可確保您不需要確認刪除。

## <a name="next-steps"></a>後續步驟

- [啟用記錄](virtual-network-nsg-manage-log.md)NSGs。