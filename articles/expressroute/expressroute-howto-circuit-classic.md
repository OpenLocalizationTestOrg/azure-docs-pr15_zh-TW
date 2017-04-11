<properties
   pageTitle="建立及修改 ExpressRoute 電路使用傳統的部署模型和 PowerShell |Microsoft Azure"
   description="本文會引導您建立與佈建 ExpressRoute 電路的步驟。 本文也說明如何檢查狀態、 更新或刪除與 deprovision 您電路。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>建立及修改 ExpressRoute 電路

> [AZURE.SELECTOR]
[Azure 入口網站的資源管理員](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell-資源管理員](expressroute-howto-circuit-arm.md)
[PowerShell-傳統](expressroute-howto-circuit-classic.md)

本文會引導您建立的 Azure ExpressRoute 電路使用 PowerShell cmdlet 和傳統部署模型的步驟。 本文也會顯示您如何查看 [狀態]、 [更新] 或 [刪除 deprovision ExpressRoute 電路。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>開始之前

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1.檢閱的先決條件與工作流程文件

請確定的檢閱的[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)之前設定。  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2.安裝 Azure PowerShell 模組的最新版本 

如何設定您的電腦，使用 PowerShell 的 Azure 模組依照逐步指引[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中的指示進行。

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3.登入您的 Azure 帳戶，並選取訂閱

1. 執行下列 cmdlet 使用較高的 Windows PowerShell 提示︰

        Add-AzureAccount
2. 在登入畫面出現，登入您的帳戶。

3. 取得您的訂閱的清單。

        Get-AzureSubscription
4. 選取您想要使用的訂閱。
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>建立與佈建 ExpressRoute 電路

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1.匯入 PowerShell 模組的 ExpressRoute

 如果您有不這麼做，您必須將匯入 Azure 和 ExpressRoute 模組 PowerShell 工作階段才能開始使用的 ExpressRoute 指令程式。 您匯入模組對方已安裝在您的本機電腦上的位置。 根據您用來安裝模組的方法，位置可能會不同於下列範例所示。 如有必要，請修改範例。  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2.取得支援的提供者、 位置及頻寬清單

建立 ExpressRoute 電路之前，您會需要支援的連線提供者、 位置及頻寬選項的清單。

PowerShell cmdlet`Get-AzureDedicatedCircuitServiceProvider`傳回這項資訊，您會使用更新的步驟︰

    Get-AzureDedicatedCircuitServiceProvider

檢查是否那里列出您的連線提供者。 因為您需要將其稍後當您建立電路時，請記下下列資訊︰

- 名稱

- PeeringLocations

- BandwidthsOffered

現在，您準備好要建立 ExpressRoute 電路。         

### <a name="3-create-an-expressroute-circuit"></a>3.建立 ExpressRoute 電路

下列範例會示範如何建立 200 Mbps 透過 Equinix ExpressRoute 電路矽谷。 如果您使用不同的提供者和其他設定，以取代該資訊，當您進行您的要求。

>[AZURE.IMPORTANT] 您的 ExpressRoute 電路從發出的服務金鑰方式付款。 請確定您準備好佈建電路連線提供者時執行此作業。


以下是新的服務索引鍵的範例要求︰

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

或者，如果要建立 ExpressRoute 電路與進階版附加元件，請使用下列範例。 請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)，如需詳細資訊的進階版附加元件。

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


回應包含的服務鍵。 您可以取得所有參數的詳細的說明，藉由執行下列動作︰

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4。 列出所有 ExpressRoute 電路

您可以執行`Get-AzureDedicatedCircuit`命令，以取得您所建立的所有 ExpressRoute 電路的清單︰


    Get-AzureDedicatedCircuit

回應會類似下面的範例︰

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

您可以使用來擷取隨時這項資訊`Get-AzureDedicatedCircuit`指令程式。 撥號不具任何參數列出所有電路。 您的服務金鑰會列在 [ *ServiceKey* ] 欄位。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

您可以取得所有參數的詳細的說明，藉由執行下列動作︰

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5。 傳送服務金鑰給您的連線提供者提供


*ServiceProviderProvisioningState*提供的服務提供者一邊佈建的目前狀態的相關資訊。 *狀態*提供 Microsoft 這邊的狀態。 如需有關電路佈建狀態的詳細資訊，請參閱[工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states)。

當您建立新的 ExpressRoute 電路時，電路會在下列狀態︰

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


當您啟用正在連線提供者電路就會前往下列狀態︰

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

在下列狀態，您就可以使用它必須 ExpressRoute 電路︰

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6.定期檢查狀態及電路鍵的狀態

這個選項可讓您知道當您的提供者已啟用您電路。 在設定電路之後， *ServiceProviderProvisioningState*會顯示為*Provisioned* ，如下列範例所示︰

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7。 建立您路由設定

請參閱[ExpressRoute 電路路由設定 （建立與修改電路 peerings）](expressroute-howto-routing-classic.md)文章︰ 如需逐步指示。

>[AZURE.IMPORTANT] 電路與提供圖層 2 connectivity 服務的服務提供者建立的只適用於下列指示進行。 如果您使用的提供受管理的服務提供者的 [圖層 3 服務 (通常是 IP VPN，例如 MPLS)，您的連線提供者會設定並管理您的路由。

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8.連結 ExpressRoute 電路虛擬網路

接下來，連結至您的 ExpressRoute 電路的虛擬網路。 如需逐步指示，請參閱[連結 ExpressRoute 電路虛擬網路](expressroute-howto-linkvnet-classic.md)。 如果您需要使用傳統的部署模型的 ExpressRoute 建立虛擬網路，請參閱[建立虛擬網路的 ExpressRoute](expressroute-howto-vnet-portal-classic.md) ，如需相關指示。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>快速 ExpressRoute 電路的狀態

您可以使用來擷取隨時這項資訊`Get-AzureCircuit`指令程式。 撥號不具任何參數列出所有電路。

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

您可以藉由將服務金鑰做為參數傳遞給呼叫取得特定的 ExpressRoute 電路上的資訊︰

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


您可以取得所有參數的詳細的說明，藉由執行下列動作︰

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>修改 ExpressRoute 電路

您可以修改 ExpressRoute 電路的特定屬性，而不會影響連線。

您可以執行下列動作不中斷的情況︰

- 啟用或停用您的 ExpressRoute 電路 ExpressRoute 進階版附加元件。
- 增加您 ExpressRoute 電路的頻寬。 請注意，不支援降級的電路頻寬。
- 變更測量計劃計量付費的資料不受限制的資料。 請注意，不支援測量的方案不受限制的資料變更計量付費的資料。
- 您可以啟用及停用*允許傳統的作業*。

請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)的限制和限制的詳細資訊。

### <a name="to-enable-the-expressroute-premium-add-on"></a>若要啟用 ExpressRoute 進階版附加元件

您可以使用下列 PowerShell 指令程式，您現有的電路啟用 ExpressRoute 進階版附加元件︰

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

您電路現在必須啟用 ExpressRoute 進階的附加功能。 請注意，我們就會開始計費您的進階版附加元件功能一經已經順利執行命令。

### <a name="to-disable-the-expressroute-premium-add-on"></a>若要停用 ExpressRoute 進階版附加元件

>[AZURE.IMPORTANT] 如果您使用大於什麼標準的電路所允許的資源，這項作業可能會失敗。

請注意下列事項︰

- 您必須確定連結至電路虛擬網路的數目少於 10 之前您從進階降級為標準。 如果您不要這麼做，您的更新要求將會失敗，您可以向收費的進階版工資率。

- 您必須取消所有的虛擬網路，在淉的其他區域。 如果您不要這麼做，您的更新要求將會失敗，您可以向收費的進階版工資率。

- 您傳送的資料表必須是私人對等的小於 4000 路由。 如果您傳送的表格大小大於 4000 路由，BGP 工作階段會卸除，並不會重新啟動，直到通知前置詞數目低於 4000。

您可以使用下列 PowerShell 指令程式來關閉您現有的電路 ExpressRoute 進階版附加元件︰

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>若要更新的 ExpressRoute 電路頻寬

檢查您的提供者支援的頻寬選項[ExpressRoute 常見問題集](expressroute-faqs.md)。 您可以選擇大於您現有的電路的大小，只要實體的連接埠 （在您電路建立） 可讓任何大小。

>[AZURE.IMPORTANT] 您無法以減少 ExpressRoute 電路，而不中斷的頻寬。 降級頻寬會要求您 deprovision ExpressRoute 電路，然後人金鑰新的 ExpressRoute 電路。

決定您需要何種大小之後，您可以使用下列命令以調整大小您電路︰

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

您電路將有已調整大小 Microsoft 方。 您必須連絡您的連線提供者，若要更新設定以符合這項變更其位於。 請注意，我們就會開始計費您從這裡開始，更新的頻寬選項上。

如果增加電路頻寬時，您會看到下列錯誤，，表示那里沒有足夠的頻寬留在您現有的電路建立所在位置的實體連接埠。 您需要刪除此電路，建立新的電路，您需要的大小。 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>取消及刪除 ExpressRoute 電路

請注意下列事項︰

- 您必須取消所有的虛擬網路，從 ExpressRoute 電路順利完成這項作業。 請檢查您是否這項作業失敗時，會連結至電路任何虛擬網路。

- 如果 ExpressRoute 電路服務提供者佈建狀態**提供**或**Provisioned**您必須使用您的服務提供者 deprovision 他們電路。 我們會繼續保留資源，並向您收取費用，直到完成解除電路服務提供者，並通知我們。

- 如果服務提供者已取消 （服務提供者佈建狀態設定為**不佈建後**） 的電路提供您可以刪除線電路。 這會停止電路帳單。

您可以刪除您 ExpressRoute 電路，執行下列命令︰

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>後續步驟

建立您的電路之後，請確定您執行下列動作︰

- [建立及修改您的 ExpressRoute 電路路由](expressroute-howto-routing-classic.md)
- [連結您 ExpressRoute 電路虛擬網路](expressroute-howto-linkvnet-classic.md)
