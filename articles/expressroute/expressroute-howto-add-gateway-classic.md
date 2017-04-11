<properties
   pageTitle="針對使用 PowerShell ExpressRoute 設定 VNet 閘道器 |Microsoft Azure"
   description="設定 VNet 閘道傳統部署模型 VNet 使用 PowerShell ExpressRoute 設定。"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>設定為使用傳統的部署模型和 PowerShell ExpressRoute 的虛擬網路閘道器

> [AZURE.SELECTOR]
- [PowerShell-資源管理員](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell-傳統](expressroute-howto-add-gateway-classic.md)

本文會引導您新增、 調整大小，以及移除現有的 VNet 虛擬網路 (VNet) 閘道器的步驟執行。 步驟的此設定專為 VNets 所建立的使用**傳統部署模型**，將會用於 ExpressRoute 設定。 

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>在開始之前

請確認您已安裝此設定所需 PowerShell 的 Azure cmdlet (1.0.2 或更新版本)。 如果您還沒有安裝 cmdlet，必須先開頭的設定步驟。 如需有關如何安裝 PowerShell 的 Azure 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

    
## <a name="next-steps"></a>後續步驟

建立 VNet 閘道器後，您可以連結您 VNet ExpressRoute 電路。 請參閱[連結 ExpressRoute 電路虛擬網路](expressroute-howto-linkvnet-classic.md)。
