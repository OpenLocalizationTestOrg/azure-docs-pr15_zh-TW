<properties
   pageTitle="如何設定的 ExpressRoute 電路路由 |Microsoft Azure"
   description="本文會引導您建立及佈建私人、 公開與 Microsoft 對等的 ExpressRoute 電路的步驟。 本文也會顯示如何檢查狀態更新或刪除您電路 peerings。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>建立及修改的 ExpressRoute 電路路由


> [AZURE.SELECTOR]
[Azure 入口網站的資源管理員](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell-資源管理員](expressroute-howto-routing-arm.md)
[PowerShell-傳統](expressroute-howto-routing-classic.md)



本文會引導您建立及管理使用 PowerShell 和 Azure 資源管理員部署模型 ExpressRoute 電路路由設定的步驟。  下列步驟，也會顯示您如何查看 [狀態]、 [更新] 或 [刪除 deprovision peerings ExpressRoute 電路。 


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>設定的先決條件

- 您必須 Azure PowerShell 模組的最新版本 1.0 或更新版本。 
- 請確定的檢閱[先決條件](expressroute-prerequisites.md)頁面、[路由需求](expressroute-routing.md)頁面和[工作流程](expressroute-workflows.md)] 頁面之前設定。
- 您必須使用中的 ExpressRoute 電路。 依照指示操作來[建立 ExpressRoute 電路](expressroute-howto-circuit-arm.md)，而且有啟用您的連線提供者，再繼續進行 「 電路。 ExpressRoute 電路必須能夠並啟用狀態，您就可以執行如下所述的指令程式。

電路與服務提供者提供第 2 層 connectivity 服務建立只適用於下列指示進行。 如果您使用的服務提供者提供受管理的圖層 3 服務 (通常是 IPVPN，例如 MPLS)，會設定您的連線提供者，並管理您的路由。

>[AZURE.IMPORTANT] 我們目前不通知 peerings 透過服務管理入口網站的服務提供者設定。 我們正在推出啟用此功能。 請檢查您的服務提供者設定 BGP peerings 之前。

您可以設定一個、 兩個或所有三個 peerings （Azure 私人、 Azure 公開與 Microsoft） ExpressRoute 電路。 您可以設定 peerings 以您選擇的順序。 不過，您必須確定在您完成設定的對等的各一次。 

## <a name="azure-private-peering"></a>Azure 私人對等

本節說明如何建立、 取得、 更新及刪除 ExpressRoute 電路 Azure 私人對等的設定。 

### <a name="to-create-azure-private-peering"></a>若要建立 Azure 私人對等

1. 匯入 PowerShell 模組的 ExpressRoute。
    
    您必須從[PowerShell 庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式，並 Azure 資源管理員模組匯 PowerShell 工作階段，才能開始使用的 ExpressRoute 指令程式。 您必須以系統管理員身分執行 PowerShell。

        Install-Module AzureRM

        Install-AzureRM

    匯入所有已知的語意版本範圍內的 AzureRM.* 模組

        Import-AzureRM

    您也可以匯入已知的語意版本範圍內選取模組 
        
        Import-Module AzureRM.Network 

    登入您的帳戶

        Login-AzureRmAccount

    選取您想要建立 ExpressRoute 電路的訂閱
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. 建立 ExpressRoute 電路。
    
    依照建立[ExpressRoute 電路](expressroute-howto-circuit-arm.md)，並讓該連線提供者所提供的指示。 

    如果您的連線提供者提供管理圖層 3 服務，您可以要求您的連線提供者，若要啟用您 Azure 私人對等。 在此情況下，您不需要遵循下一節中所列的指示。 不過，如果您的連線提供者不會管理路由，建立您的電路之後請遵循下列指示進行。 

3. 檢查以確保它已佈建的 ExpressRoute 電路。

    您必須先檢查 ExpressRoute 電路是否是佈建後，也會啟用。 請參閱下面的範例。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    回應會類似下面的範例中︰

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. 設定電路 Azure 私人對等。

    請確定您有下列項目，才能繼續進行下一個步驟︰

    - /30 子網路的主要的連結。 這不能保留虛擬網路的任何位址空間的一部分。
    - /30 子網路的次要的連結。 這不能保留虛擬網路的任何位址空間的一部分。
    - 若要建立外面上有效的虛擬區域網路 ID。 確定沒有其他對等電路中使用相同的虛擬區域網路識別碼。
    - 為的對等的數字。 您可以使用 2 位元組和數字的 4 位元組。 您可以使用私人的此對等的數字。 請確定您不使用 65515。
    - MD5 雜湊如果您選擇要使用其中一個。 **此為選用步驟**。
    
    您可以執行下列 cmdlet，以設定您的電路 Azure 私人對等。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    如果您選擇使用 MD5 雜湊，您可以使用下列指令程式。

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] 請確定您在對等 ASN，客戶 ASN 作為指定您另存新檔的電話號碼。

### <a name="to-view-azure-private-peering-details"></a>若要檢視 Azure 私人的對等詳細資料

您可以取得設定使用下列 cmdlet 的詳細資訊

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>若要更新 Azure 私人的對等設定

您可以更新使用下列 cmdlet 設定的任何部分。 在下面的範例中，「 電路虛擬區域網路 ID 正在更新從 100 到 500。

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>若要刪除 Azure 私人對等

您可以藉由執行下列 cmdlet，移除您對等的設定。

>[AZURE.WARNING] 您必須確定所有的虛擬網路未連結的 ExpressRoute 電路，才能執行這個指令程式。 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Azure 公用對等

本節提供如何建立、 取得、 更新及刪除 ExpressRoute 電路 Azure 公用對等的設定指示。

### <a name="to-create-azure-public-peering"></a>若要建立 Azure 公用對等

1. 匯入 PowerShell 模組的 ExpressRoute。
    
    您必須從[PowerShell 庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式，並 Azure 資源管理員模組匯 PowerShell 工作階段，才能開始使用的 ExpressRoute 指令程式。 您必須以系統管理員身分執行 PowerShell。

        Install-Module AzureRM

        Install-AzureRM

    匯入所有已知的語意版本範圍內的 AzureRM.* 模組

        Import-AzureRM

    您也可以匯入已知的語意版本範圍內選取模組 
        
        Import-Module AzureRM.Network 

    登入您的帳戶

        Login-AzureRmAccount

    選取您想要建立 ExpressRoute 電路的訂閱
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. 建立 ExpressRoute 電路。
    
    依照建立[ExpressRoute 電路](expressroute-howto-circuit-arm.md)，並讓該連線提供者所提供的指示。 

    如果您的連線提供者提供管理圖層 3 服務，您可以要求您的連線提供者，若要啟用您 Azure 公用對等。 在此情況下，您不需要遵循下一節中所列的指示。 不過，如果您的連線提供者不會管理路由，建立您的電路之後請遵循下列指示進行。

3. 檢查以確保它已佈建 ExpressRoute 電路。

    您必須先檢查 ExpressRoute 電路是否是佈建後，也會啟用。 請參閱下面的範例。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    回應會類似下面的範例中︰

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. 設定電路 Azure 公用對等。

    請確定您有下列資訊，再繼續進行進一步。

    - /30 子網路的主要的連結。 這必須是有效的公用 IPv4 前置字元。
    - /30 子網路的次要的連結。 這必須是有效的公用 IPv4 前置字元。
    - 若要建立外面上有效的虛擬區域網路 ID。 確定沒有其他對等電路中使用相同的虛擬區域網路識別碼。
    - 為的對等的數字。 您可以使用 2 位元組和數字的 4 位元組。
    - MD5 雜湊如果您選擇要使用其中一個。 **此為選用步驟**。
    
    您可以執行下列 cmdlet，以設定您的電路 Azure 公用對等

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    如果您選擇使用 MD5 雜湊，您可以使用下列 cmdlet

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] 請確定您在對等 ASN 與客戶 ASN 為指定您另存新檔的電話號碼。

### <a name="to-view-azure-public-peering-details"></a>若要檢視 Azure 公用等詳細資料

您可以取得設定使用下列 cmdlet 的詳細資訊

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>若要更新 Azure 公用等設定

您可以使用下列 cmdlet 設定的任何部分，更新

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

虛擬區域網路的識別碼電路正在從 200 更新為 600 上面範例中。

### <a name="to-delete-azure-public-peering"></a>若要刪除 Azure 公用對等

您可以執行下列 cmdlet，以移除您對等的設定

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft 對等

本節提供如何建立、 取得、 更新及刪除 ExpressRoute 電路 Microsoft 對等的設定指示。 

### <a name="to-create-microsoft-peering"></a>若要建立 Microsoft 對等

1. 匯入 PowerShell 模組的 ExpressRoute。
    
    您必須從[PowerShell 庫](http://www.powershellgallery.com/)安裝最新的 PowerShell 安裝程式，並 Azure 資源管理員模組匯 PowerShell 工作階段，才能開始使用的 ExpressRoute 指令程式。 您必須以系統管理員身分執行 PowerShell。

        Install-Module AzureRM

        Install-AzureRM

    匯入所有已知的語意版本範圍內的 AzureRM.* 模組

        Import-AzureRM

    您也可以匯入已知的語意版本範圍內選取模組 
        
        Import-Module AzureRM.Network 

    登入您的帳戶

        Login-AzureRmAccount

    選取您想要建立 ExpressRoute 電路的訂閱
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. 建立 ExpressRoute 電路。
    
    依照建立[ExpressRoute 電路](expressroute-howto-circuit-arm.md)，並讓該連線提供者所提供的指示。 

    如果您的連線提供者提供管理圖層 3 服務，您可以要求您的連線提供者，若要啟用您 Azure 私人對等。 在此情況下，您不需要遵循下一節中所列的指示。 不過，如果您的連線提供者不會管理路由，建立您的電路之後請遵循下列指示進行。

3. 檢查以確保它已佈建 ExpressRoute 電路。

    您必須先檢查 ExpressRoute 電路是否是佈建後，也會啟用。 請參閱下面的範例。

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    回應會類似下面的範例中︰

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. 設定 Microsoft 對等電路。

    請確定您有下列資訊，再繼續進行。

    - /30 子網路的主要的連結。 這必須是有效公用 IPv4 首碼您所擁有與登錄中 RIR / IRR。
    - /30 子網路的次要的連結。 這必須是有效公用 IPv4 首碼您所擁有與登錄中 RIR / IRR。
    - 若要建立外面上有效的虛擬區域網路 ID。 確定沒有其他對等電路中使用相同的虛擬區域網路識別碼。
    - 為的對等的數字。 您可以使用 2 位元組和數字的 4 位元組。
    - 通知首碼︰ 您必須提供所有您計劃通知透過 BGP 工作階段的前置字元的清單。 僅限公用 IP 位址首碼會接受。 如果您要傳送一組前置詞，您可以傳送以逗號分隔的清單。 這些前置詞必須在 RIR 中註冊您 / IRR。
    - 客戶 ASN︰ 如果您是以數字對等未註冊的廣告前置詞，您可以指定其所註冊的另存新檔數字。 **此為選用步驟**。
    - 路由登錄名稱︰ 您可以指定 RIR / IRR 的數字和前置詞是註冊。
    - MD5 雜湊上，如果您選擇要使用其中一個。 **此為選用步驟。**
    
    您可以執行下列 cmdlet，以設定您的電路對等的 Microsoft

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>若要取得 Microsoft 對等的詳細資料

您可以取得設定使用下列 cmdlet 的詳細資訊。

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>更新 Microsoft 對等的設定

您可以更新使用下列 cmdlet 設定的任何部分。

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>若要刪除 Microsoft 對等

您可以藉由執行下列 cmdlet，移除您對等的設定。

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>後續步驟

下一個步驟，[連結到 ExpressRoute 電路 VNet](expressroute-howto-linkvnet-arm.md)。

-  如需有關 ExpressRoute 工作流程的詳細資訊，請參閱[ExpressRoute 工作流程](expressroute-workflows.md)。

-  如需有關電路對等的詳細資訊，請參閱[ExpressRoute 電路與路由的網域](expressroute-circuit-peerings.md)。

-  如需有關如何使用虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

