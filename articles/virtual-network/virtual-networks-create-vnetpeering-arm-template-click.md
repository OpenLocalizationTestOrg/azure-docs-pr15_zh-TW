<properties
   pageTitle="建立 VNet 對等使用資源管理員範本 |Microsoft Azure"
   description="瞭解如何建立虛擬網路，外面資源管理員] 中使用的範本。"
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
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
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>建立 VNet 對等使用資源管理員範本

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要建立對等使用資源管理員範本 VNet，請遵循下列步驟︰

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) ，並遵循指示到登入 Azure，然後選取您的訂閱結束。

    > [AZURE.NOTE] 適用於管理 VNet 對等的 PowerShell cmdlet 隨附於[Azure PowerShell 1.6。](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. 下列的文字會顯示 VNet1 VNet 對等的連結定義到 VNet2，根據上述情況。 複製下方內容，並將其儲存為檔案命名 VNetPeeringVNet1.json。

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. 以下的章節會顯示 VNet2 VNet 對等的連結定義到 VNet1，根據上述情況。  複製下方內容，並將其儲存為檔案命名 VNetPeeringVNet2.json。

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    上述範本所示，有幾個可設定 VNet 對等的內容︰

  	|選項|描述|預設值|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|對等 VNet 的位址空間是否 virtual_network 標籤的一部分。|[是]|
  	|AllowForwardedTraffic|不源自 peered VNet 流量是否接受或卸除。|無|
  	|AllowGatewayTransit|可讓等 VNet 使用 VNet 閘道。|無|
  	|UseRemoteGateways|使用您對 VNet 閘道器。 對 VNet 必須將閘道器設定和 AllowGatewayTransit 選取。 如果您有閘道器設定，您無法使用此選項。|無|

    在 VNet 對等的每個連結有一組以上的屬性。 例如，您可以將 AllowVirtualNetworkAccess 設定為 True 的 VNet 對等連結 VNet1 VNet2，並將它設定為在其他方向 VNet 對等的連結，則為 False。


4. 若要部署範本檔案，您可以執行新增 AzureRmResourceGroupDeployment cmdlet，以建立或更新部署。 如需有關如何使用資源管理員範本的詳細資訊，請參閱這[篇文章](../resource-group-template-deploy.md)。

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] 請取代資源群組名稱和範本檔案視。

    以下是範例根據上述情況︰

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    輸出顯示︰

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    輸出顯示︰

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. 部署完成之後，您可以執行下列檢視的對等的狀態指令程式︰

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    輸出顯示︰

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    對等在這個案例中建立之後，您應該能啟動任何虛擬機器中兩個 VNets 任何虛擬機器連線。 根據預設，AllowVirtualNetworkAccess 為 True，而且 VNet 對等會佈建適當 Acl 允許 VNets 之間的通訊。 您仍然可以套用網路安全性群組 (NSG) 規則封鎖特定子網路或細微控制的兩個虛擬網路之間的存取權的虛擬機器之間的連線。  如需建立 NSG 規則的詳細資訊，請參閱這[篇文章](virtual-networks-create-nsg-arm-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

若要建立對等訂閱 VNet，請遵循下列步驟︰

1. 登入 Azure 權限的使用者 A 的訂閱的帳戶，請執行下列 cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    這不是需求，可以建立對等，即使使用者個別提高對等的要求的其個別 Vnets，只要符合要求。 為使用者本機 VNet 新增獲授限的使用者的其他 VNet 讓您更容易執行安裝程式。

2. 登入 Azure 權限-B 使用者的帳戶訂閱 B，然後執行下列 cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. 在 [使用者 A 的登入工作階段，執行此 cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    以下是如何定義 JSON 檔案。  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. 使用者 B 的登入工作階段，執行下列 cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    以下是如何定義 JSON 檔案︰

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    對等在這個案例中建立之後，您應該可以起始連線從任何虛擬機器任何虛擬機器的兩個 VNets 不同訂閱。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 在此案例中，您可以部署建立 VNet 對等以下範例範本。  您需要設定 AllowForwardedTraffic 屬性設為 True，讓網路虛擬應用裝置中傳送及接收流量 peered VNet。

    以下是建立對等的 HubVNet 至 VNet1 VNet 的範本。 請注意，AllowForwardedTraffic 設為 false。

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. 以下是建立對等的 VNet1 至 HubVnet VNet 的範本。 注意 AllowForwardedTraffic 設為 true。

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. 對等建立之後，您可以參考定義以便 VNet1 流量重新導向透過虛擬的應用裝置，若要使用其功能的使用者定義 routes(UDR) 此[文件](virtual-network-create-udr-arm-ps.md)。 當您指定的下一個躍點地址傳送時，可以將它中對 VNet HubVNet 虛擬應用裝置的 IP 位址。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

若要建立對等，從不同的部署模型的虛擬網路之間，請遵循下列步驟︰
1. 以下文字顯示在這個案例中 VNET2 VNET1 的定義的 VNet 對等的連結。 只有一個連結，才能對等 Azure 資源管理員虛擬網路傳統虛擬網路。

    請務必將放在您的 [傳統的虛擬網路] 或 [VNET2 所在的訂閱識別碼，並將 MyResouceGroup 變更適當的資源群組名稱。

    {「 $schema 」: 「 https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json# 」、 「 contentVersion 」: 「 1.0.0.0 「，」 參數 」: {}、 「 變數 」: {}、 「 資源 」: [{「 apiVersion 」: 「 2016年-06-01 」、 「 類型 」: 「 Microsoft.Network/virtualNetworks/virtualNetworkPeerings 」，「 名稱 」: 「 VNET1/LinkToVNET2 」、 「 位置 」: 」 [resourceGroup （).location] 」，[內容]: {「 allowVirtualNetworkAccess 」: 為 true，則 「 allowForwardedTraffic 」: 為 false，「 allowGatewayTransit 」: 為 false，「 useRemoteGateways 」: 為 false，「 remoteVirtualNetwork 」: {「 識別碼 」: 」 [預設 (' Microsoft.ClassicNetwork/virtualNetworks 的 「 VNET2 」)] 」}}}]}

2. 若要部署範本檔案，執行下列 cmdlet 建立或更新部署。

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. 部署成功之後，您可以執行下列 cmdlet，若要檢視的對等的狀態︰

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

對等傳統的 VNet 與資源管理員 VNet 之間建立之後，您應該能啟動任何虛擬機器中 VNET2，反之亦然從任何的虛擬機器中 VNET1 連線。
