<properties
   pageTitle="使用資源管理員部署模型和 Azure 入口網站連結 ExpressRoute 電路虛擬網路 |Microsoft Azure"
   description="這份文件提供概略瞭解如何連結 ExpressRoute 電路虛擬網路 (VNets)。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
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
   ms.author="cherylmc" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>連結 ExpressRoute 電路虛擬網路

> [AZURE.SELECTOR]
- [Azure 入口網站的資源管理員](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell-資源管理員](expressroute-howto-linkvnet-arm.md)
- [PowerShell-傳統](expressroute-howto-linkvnet-classic.md)



本文可協助您使用的資源管理員部署模型和 Azure 入口網站，連結至 Azure ExpressRoute 電路的虛擬網路 (VNets)。 虛擬網路數可以是在同一份訂閱，或可以是另一個訂閱的一部分。


**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>設定的先決條件

- 請確定的檢閱[先決條件](expressroute-prerequisites.md)、[路由需求](expressroute-routing.md)及[工作流程](expressroute-workflows.md)之前設定。
- 您必須使用中的 ExpressRoute 電路。
    - 依照指示[建立 ExpressRoute 電路](expressroute-howto-circuit-arm.md)，而且有啟用您的連線提供者的電路。

    - 請確定您有 Azure 私人對等設定您的電路。 請參閱[設定路由](expressroute-howto-routing-portal-resource-manager.md)路由的指示。

    - 確定 Azure 私人對等設定您的網路和 Microsoft 之間 BGP 對等已啟動，好讓您可以啟用端對端連線。

    - 請確定您擁有虛擬網路和建立並完全佈建後，虛擬網路閘道器。 請依照下列指示建立[VPN 閘道器](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)（追蹤只步驟 1-5）。

您可以連結最多 10 個虛擬網路至標準的 ExpressRoute 電路。 使用標準的 ExpressRoute 電路時，所有的虛擬網路必須是相同淉區域中。 您可以連結的 ExpressRoute 線電路淉區域外的虛擬網路或連線到您的 ExpressRoute 電路的較大的數字的虛擬網路，如果您已啟用 ExpressRoute 進階版附加元件。 核取 [[常見問題集](expressroute-faqs.md)，如需詳細資訊的進階版附加元件]。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>在同一份訂閱虛擬網路連線到電路


### <a name="to-create-a-connection"></a>若要建立的連線

1. 請確定您的 ExpressRoute 電路和 Azure 私人對等有已設定成功。 請依照[建立 ExpressRoute 電路](expressroute-howto-circuit-arm.md)] 和 [[路由] 設定](expressroute-howto-routing-arm.md)中的指示進行。 您的 ExpressRoute 電路看起來應該像下列圖像。

    ![ExpressRoute 電路螢幕擷取畫面](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] BGP 設定資訊不會顯示的階層 3 提供者是否設定您的 peerings。 如果您電路處於能夠狀態時，應該能夠建立連線。

2. 您現在可以開始佈建與連結至您 ExpressRoute 電路您虛擬網路閘道器的連線。 按一下 [**連線** > **新增**]，開啟 [**新增連線**刀中，然後設定值。 請參閱下列參考的範例。


    ![新增連線的螢幕擷取畫面](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. 成功設定您的連線之後，您連線的物件就會顯示連線資訊。

    ![連線物件的螢幕擷取畫面](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>若要刪除連線

您可以刪除連線，選取您的連線刀上的 [**刪除**] 圖示。

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>在不同的訂閱將虛擬網路連線到電路

此時，您無法使用 Azure 入口網站虛擬網路連線訂閱。 不過，您可以使用 PowerShell 執行此動作。 請參閱[PowerShell](expressroute-howto-linkvnet-arm.md)如需詳細資訊。

## <a name="next-steps"></a>後續步驟

如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
