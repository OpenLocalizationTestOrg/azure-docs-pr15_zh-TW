<properties
   pageTitle="將 ExpressRoute 電路傳統到資源管理員 |Microsoft Azure"
   description="本頁面說明如何將傳統電路移至 [資源管理員部署模型。"
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>將傳統 ExpressRoute 電路移到資源管理員部署模型

## <a name="configuration-prerequisites"></a>設定的先決條件

- 您需要 Azure PowerShell 模組的最新版本 (至少 1.0 版)。
- 請確定的檢閱[先決條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)及[工作流程](expressroute-workflows.md)之前設定。
- 前進一步前面，檢閱下[移動從傳統到資源管理員 ExpressRoute 電路](expressroute-move.md)提供的資訊。 請確定您有完全瞭解的限制和限制可以執行的作業。
- 如果您想要從傳統部署模型的 Azure ExpressRoute 電路移至 Azure 資源管理員部署模型，您必須電路完整設定，並在傳統的部署模型的操作。
- 請確定您已在資源管理員部署模型中建立的資源群組。

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>將 ExpressRoute 電路移到資源管理員部署模型

您必須移動 ExpressRoute 電路資源管理員部署模型，讓您可以在 [傳統] 與 [資源管理員部署模型使用它。 您可以執行下列 PowerShell 命令。

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>步驟 1︰ 從傳統部署模型收集電路詳細資料

您需要先收集您 ExpressRoute 電路的相關資訊。

登入 Azure 傳統的環境，然後收集服務金鑰。 您可以使用下列 PowerShell 程式碼片段收集的資訊︰

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

複製您想要移至資源管理員部署模型電路的**服務鍵**。

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>步驟 2︰ 登入的資源管理員環境，然後建立新的資源群組

您可以使用下列程式碼片段，以建立新的資源群組︰

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

如果您已經有，您也可以使用現有的資源群組。

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>步驟 3︰ 將 ExpressRoute 電路移到資源管理員部署模型

您已準備好在您的 ExpressRoute 電路從傳統移到資源管理員部署模型。 檢閱下[移動至資源管理員部署模型傳統的 ExpressRoute 電路](expressroute-move.md)提供之前進行進一步的資訊。

您可以執行下列程式碼片段︰

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] 移動完成後，會列在先前的指令程式的新名稱會用於解決資源。 電路基本上會被重新命名。

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>啟用兩種部署模型 ExpressRoute 電路

您必須之前控制存取權的部署模型，將您 ExpressRoute 電路移至資源管理員部署模型中。

執行下列 cmdlet，以存取兩種部署模型︰

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

這項作業成功完成後，您可以檢視電路傳統部署模型中。

執行下列動作以取得 ExpressRoute 電路的詳細資料︰

    get-azurededicatedcircuit

您必須能夠查看所列的服務鍵。 您現在可以管理 ARM VNETs 使用傳統 VNets 您標準傳統部署模型命令和標準 ARM 命令的 ExpressRoute 電路的連結。 下列文章會引導您完成如何管理 ExpressRoute 電路的連結︰

- [資源管理員部署模型中您 ExpressRoute 電路連結虛擬網路](expressroute-howto-linkvnet-arm.md)
- [在傳統的部署模型您 ExpressRoute 電路連結虛擬網路](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>停用 ExpressRoute 電路傳統部署模型

執行下列 cmdlet，若要停用的存取權的傳統部署模型︰

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

這項作業成功完成後，您將無法檢視電路傳統部署模型中。

## <a name="next-steps"></a>後續步驟

建立您的電路之後，請確定您執行下列動作︰

- [建立及修改您 ExpressRoute 電路路由](expressroute-howto-routing-arm.md)
- [連結您 ExpressRoute 電路虛擬網路](expressroute-howto-linkvnet-arm.md)
