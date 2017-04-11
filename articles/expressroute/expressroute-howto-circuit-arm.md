<properties
   pageTitle="建立及修改 ExpressRoute 電路使用資源管理員與 PowerShell |Microsoft Azure"
   description="本文將說明如何建立、 佈建、 驗證、 更新、 刪除及 deprovision ExpressRoute 電路。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>建立及修改 ExpressRoute 電路

> [AZURE.SELECTOR]
[Azure 入口網站的資源管理員](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell-資源管理員](expressroute-howto-circuit-arm.md)
[PowerShell-傳統](expressroute-howto-circuit-classic.md)


本文將說明如何使用 Windows PowerShell cmdlet 和 Azure 資源管理員部署模型建立 Azure ExpressRoute 電路。 本文也會顯示您如何查看的電路狀態、 更新，或刪除 deprovision 它。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>開始之前


- 取得 Azure PowerShell 模組的最新版本 (至少 1.0 版)。 針對如何設定您的電腦，使用 PowerShell 模組的逐步指引，依照[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中的指示進行。

- 設定之前，請檢閱的[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。

## <a name="create-and-provision-an-expressroute-circuit"></a>建立與佈建 ExpressRoute 電路

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1.登入您的 Azure 帳戶，然後選取您的訂閱

若要開始您的設定，請登入您 Azure 帳戶。 如需 PowerShell 的詳細資訊，請參閱[使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。 您可以使用下列範例可協助您連線︰

    Login-AzureRmAccount

檢查帳戶的訂閱︰

    Get-AzureRmSubscription

選取您想要建立的 ExpressRoute 電路訂閱︰

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2.取得支援的提供者、 位置及頻寬清單

建立 ExpressRoute 電路之前，您會需要支援的連線提供者、 位置及頻寬選項的清單。

PowerShell cmdlet`Get-AzureRmExpressRouteServiceProvider`傳回這項資訊，您會使用更新的步驟︰

    Get-AzureRmExpressRouteServiceProvider

檢查是否那里列出您的連線提供者。 因為您需要將其稍後當您建立電路時，請記下下列資訊︰

- 名稱

- PeeringLocations

- BandwidthsOffered

現在，您準備好要建立 ExpressRoute 電路。   

### <a name="3-create-an-expressroute-circuit"></a>3.建立 ExpressRoute 電路

如果您還沒有資源群組，您必須建立一個，建立您的 ExpressRoute 電路之前。 您可以執行下列命令以執行︰


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


下列範例會示範如何建立 200 Mbps 透過 Equinix ExpressRoute 電路矽谷。 如果您使用不同的提供者和其他設定，以取代該資訊，當您進行您的要求。 以下是新的服務索引鍵的範例要求︰

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

請確定您指定正確的 SKU 層和 SKU 系列︰

- SKU 層會決定是否已啟用 ExpressRoute 標準或 ExpressRoute 進階版附加元件。 您可以指定*標準*標準 SKU 或*進階版*取得進階版附加元件。

- SKU 系列決定付款類型。 您可以指定*Metereddata*計量付費的資料計劃與*Unlimiteddata*無限制的資料計劃。 請注意，您可以從*Metereddata*變更付款類型，以*Unlimiteddata*，但您無法變更類型從*Unlimiteddata* *Metereddata*。


>[AZURE.IMPORTANT] 您的 ExpressRoute 電路從發出的服務金鑰方式付款。 請確定您準備好佈建電路連線提供者時執行此作業。

回應包含的服務鍵。 您可以取得所有參數的詳細的說明，藉由執行下列動作︰


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4。 列出所有 ExpressRoute 電路

若要取得您所建立的所有 ExpressRoute 電路的清單，請執行`Get-AzureRmExpressRouteCircuit`命令︰


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

回應看起來類似下列範例︰


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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

您可以使用來擷取隨時這項資訊`Get-AzureRmExpressRouteCircuit`指令程式。 不含任何參數撥號列出所有電路。 您的服務金鑰會列在*ServiceKey*欄位︰


    Get-AzureRmExpressRouteCircuit


回應看起來類似下列範例︰


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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


您可以取得所有參數的詳細的說明，藉由執行下列動作︰


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5。 傳送服務金鑰給您的連線提供者提供

*ServiceProviderProvisioningState*提供的服務提供者一邊佈建的目前狀態的相關資訊。 狀態提供 Microsoft 方的狀態。 如需有關電路佈建狀態的詳細資訊，請參閱[工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states)。

當您建立新的 ExpressRoute 電路時，電路會在下列狀態︰


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



當您啟用正在連線提供者的下列狀態會變更電路︰

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

您可以使用 ExpressRoute 電路，它必須在下列狀態︰

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6.定期檢查狀態及電路鍵的狀態

檢查狀態及電路鍵的狀態，可讓您知道當您的提供者已啟用您電路。 電路之後已設定， *ServiceProviderProvisioningState*會顯示為*Provisioned*，如下列範例所示︰


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


回應看起來類似下列範例︰


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

### <a name="7-create-your-routing-configuration"></a>7。 建立您路由設定

如需逐步指示，請參閱建立及修改電路 peerings [ExpressRoute 電路路由設定](expressroute-howto-routing-arm.md)文件。


>[AZURE.IMPORTANT] 電路與提供圖層 2 connectivity 服務的服務提供者建立的只適用於下列指示進行。 如果您使用的提供受管理的服務提供者的 [圖層 3 服務 (通常是 IP VPN，例如 MPLS)，您的連線提供者會設定並管理您的路由。

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8.連結 ExpressRoute 電路虛擬網路

接下來，連結至您的 ExpressRoute 電路的虛擬網路。 使用資源管理員部署模型時，請使用 [[連結至 ExpressRoute 電路的虛擬網路](expressroute-howto-linkvnet-arm.md)文章。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>快速 ExpressRoute 電路的狀態

您可以使用來擷取隨時這項資訊`Get-AzureRmExpressRouteCircuit`指令程式。 不含任何參數撥號列出所有電路。

    Get-AzureRmExpressRouteCircuit


回應會類似下面的範例︰


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


您可以藉由將資源群組名稱與電路名稱做為參數傳遞至通話取得特定的 ExpressRoute 電路上的資訊︰


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


回應看起來類似下列範例︰


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


您可以取得所有參數的詳細的說明，藉由執行下列動作︰

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>修改 ExpressRoute 電路

您可以修改 ExpressRoute 電路的特定屬性，而不會影響連線。

您可以執行下列動作不中斷的情況︰

- 啟用或停用您的 ExpressRoute 電路 ExpressRoute 進階版附加元件。
- 增加您 ExpressRoute 電路的頻寬。 請注意，不支援降級的電路頻寬。
- 變更測量計劃計量付費的資料不受限制的資料。 請注意，不支援測量的方案不受限制的資料變更計量付費的資料。
-  您可以啟用及停用*允許傳統的作業*。

如需有關限制和限制的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。

### <a name="to-enable-the-expressroute-premium-add-on"></a>若要啟用 ExpressRoute 進階版附加元件

您可以使用下列 PowerShell 程式碼片段，您現有的電路啟用 ExpressRoute 進階版附加元件︰

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


電路現在必須啟用 ExpressRoute 進階的附加功能。 請注意，我們會開始計費您的進階版附加元件功能一經已經順利執行命令。

### <a name="to-disable-the-expressroute-premium-add-on"></a>若要停用 ExpressRoute 進階版附加元件

>[AZURE.IMPORTANT] 如果您使用大於什麼標準的電路所允許的資源，這項作業可能會失敗。

請注意下列事項︰

- 您從進階降級為標準之前，您必須確定虛擬網路電路所連結的數字是小於 10。 如果您不要這麼做，您的更新要求失敗，請與我們會向您收取費用進階版率。

- 您必須取消所有的虛擬網路，在淉的其他區域。 如果您不要這麼做，您的更新要求將會失敗，，我們會向您收取費用進階版率。

- 您傳送的資料表必須是私人對等的小於 4000 路由。 如果您傳送的表格大小大於 4000 路由，放 BGP 工作階段，並不會重新啟動，直到通知前置詞數目低於 4000。

您可以使用下列 PowerShell 指令程式來停用現有電路 ExpressRoute 進階版附加元件︰


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>若要更新的 ExpressRoute 電路頻寬

支援的頻寬選項設定於您的提供者，請[ExpressRoute 常見問題集](expressroute-faqs.md)。 您可以選取任何大小大於您現有的電路的大小。

>[AZURE.IMPORTANT] 您無法以減少 ExpressRoute 電路，而不中斷的頻寬。 降級頻寬要求您 deprovision ExpressRoute 電路與然後人金鑰新的 ExpressRoute 電路。

決定您需要何種大小之後，請使用下列命令以調整大小您電路︰


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


您電路將調整大小 Microsoft 方。 然後，您必須連絡您的連線提供者，若要更新設定以符合這項變更其位於。 進行此通知之後，我們就會開始計費您更新的頻寬] 選項。


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>若要移動的 SKU，從計量付費限制

您可以變更的 ExpressRoute 電路的 SKU，使用下列 PowerShell 程式碼片段︰

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>若要控制傳統和資源管理員環境的存取權  

檢閱[移動 ExpressRoute 電路從傳統資源管理員部署模型](expressroute-howto-move-arm.md)中的指示進行。  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>取消及刪除 ExpressRoute 電路

請注意下列事項︰

- 您必須取消所有的虛擬網路，從 ExpressRoute 電路。 如果這項作業失敗，請檢查任何虛擬網路如果連結至電路。

- 如果 ExpressRoute 電路服務提供者佈建狀態**提供**或**Provisioned**您必須使用您的服務提供者 deprovision 他們電路。 我們會繼續保留資源，並向您收取費用，直到完成解除電路服務提供者，並通知我們。

- 如果服務提供者已取消 （服務提供者佈建狀態設定為**不佈建後**） 的電路提供您可以刪除線電路。 這會停止電路帳單

您可以刪除您 ExpressRoute 電路，執行下列命令︰

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>後續步驟

建立您的電路之後，請確定您執行下列動作︰

- [建立及修改您的 ExpressRoute 電路路由](expressroute-howto-routing-arm.md)
- [連結您 ExpressRoute 電路虛擬網路](expressroute-howto-linkvnet-arm.md)
