<properties
   pageTitle="連結至 ExpressRoute 電路的虛擬網路，使用傳統的部署模型和 PowerShell |Microsoft Azure"
   description="這份文件使用 PowerShell 與傳統部署模型，以 ExpressRoute 電路提供概略瞭解如何連結虛擬網路 (VNets)。"
   services="expressroute"
   documentationCenter="na"
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
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>連結 ExpressRoute 電路虛擬網路

> [AZURE.SELECTOR]
- [Azure 入口網站的資源管理員](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell-資源管理員](expressroute-howto-linkvnet-arm.md)
- [PowerShell-傳統](expressroute-howto-linkvnet-classic.md)



本文可協助您連結至 Azure ExpressRoute 電路的虛擬網路 (VNets)，使用傳統的部署模型和 PowerShell。 虛擬網路可以是在同一份訂閱，或者可以是另一個訂閱的一部分。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>設定的先決條件

1. 您需要 Azure PowerShell 模組的最新版本。 您可以從[Azure 下載 」 頁面](https://azure.microsoft.com/downloads/)的 PowerShell 區段下載最新的 PowerShell 模組。 如何設定您的電腦，使用 PowerShell 的 Azure 模組依照逐步指引[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中的指示進行。
2. 您需要設定之前檢閱[先決條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)，與[工作流程](expressroute-workflows.md)。
3. 您必須使用中的 ExpressRoute 電路。
    - 依照指示[建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)，而且有啟用電路您連線提供者。
    - 請確定您有 Azure 私人對等設定您的電路。 請參閱[設定路由](expressroute-howto-routing-classic.md)路由的指示。
    - 確定 Azure 私人對等設定您的網路和 Microsoft 之間 BGP 對等已啟動，好讓您可以啟用端對端連線。
    - 您必須虛擬網路和建立並完全佈建後，虛擬網路閘道器。 依照指示[設定的 ExpressRoute 虛擬網路](expressroute-howto-vnet-portal-classic.md)。

您可以連結 ExpressRoute 電路最多 10 個虛擬網路。 所有虛擬網路必須在同一個淉地區。 您可以連結至您的 ExpressRoute 電路，虛擬網路或連結時，在其他淉區域內啟用 ExpressRoute 進階版附加元件的虛擬網路大的數字。 核取 [[常見問題集](expressroute-faqs.md)，如需詳細資訊的進階版附加元件]。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>在同一份訂閱虛擬網路連線到電路

您可以連結至 ExpressRoute 電路的虛擬網路，使用下列指令程式。 請確定虛擬網路閘道器會建立並準備好迎接連結之前執行指令程式。

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>在不同的訂閱將虛擬網路連線到電路

您可以共用 ExpressRoute 電路跨多個訂閱。 下圖顯示一個簡單的方式共用的作業方式 ExpressRoute 電路圖解跨多個訂閱。

每個較小的雲朵大型雲端中用來代表所屬組織內的不同部門的訂閱。 每個組織中的部門可以使用自己的訂閱，部署其服務，但部門可以共用單一 ExpressRoute 電路回到您的內部部署網路連線的。 單一部門 (在此範例中︰ IT) 可以擁有 ExpressRoute 電路。 在組織內其他訂閱，可以使用 ExpressRoute 電路。

>[AZURE.NOTE] 連線與頻寬專用電路費用會套用到 ExpressRoute 電路擁有者。 所有的虛擬網路共用相同的頻寬。

![跨訂閱連線](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>管理

*電路擁有者*是系統管理員/coadministrator ExpressRoute 電路建立所在的訂閱。 電路擁有者可以授權管理員/coadministrators 的其他訂閱，稱為*電路使用者*，請使用自己的專用的電路。 電路使用者只要獲授權使用組織的 ExpressRoute 電路可以將其訂閱中的虛擬網路連結至 ExpressRoute 電路之後這些授權。

電路擁有修改，和撤銷授權隨時 power。 撤銷授權會遭刪除的存取權已撤銷的訂閱中的所有連結。

### <a name="circuit-owner-operations"></a>電路擁有者作業

#### <a name="creating-an-authorization"></a>建立驗證

電路擁有授權使用指定的電路其他訂閱的管理員。 在下列範例中，電路 (Contoso IT) 的系統管理員啟用另一個訂閱 （開發-測試） 電路連結兩個虛擬網路系統的管理員。 Contoso IT 系統管理員啟用指定的裝置測試 Microsoft id。 Cmdlet 不會將電子郵件傳送至指定的 Microsoft id。 電路擁有者必須明確通知其他訂閱擁有者授權已完成。

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

#### <a name="reviewing-authorizations"></a>檢閱的授權

電路擁有者可以檢閱所有授權所發行特定電路上，執行下列 cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


#### <a name="updating-authorizations"></a>更新的授權

電路擁有者可以使用下列 cmdlet，以修改授權︰

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


#### <a name="deleting-authorizations"></a>刪除的授權

電路擁有者可以撤銷/刪除授權給使用者執行下列 cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>電路使用者作業

#### <a name="reviewing-authorizations"></a>檢閱的授權

電路使用者可以使用下列 cmdlet，以檢閱授權︰

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

#### <a name="redeeming-link-authorizations"></a>兌換連結的授權

電路使用者可以執行下列 cmdlet 兌換連結授權︰

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
