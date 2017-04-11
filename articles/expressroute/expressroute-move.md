<properties
   pageTitle="從傳統資源管理員來移動 ExpressRoute 電路 |Microsoft Azure"
   description="本頁面提供必須知道橋傳統和資源管理員部署模型的概觀。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>從 [傳統的 ExpressRoute 電路移到 [資源管理員部署模型

本文提供意義，將 Azure ExpressRoute 電路傳統從 Azure 資源管理員部署模型的概觀。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-classic-rm-include.md)]

您可以使用單一 ExpressRoute 電路連線至虛擬傳統和資源管理員部署模型已部署的網路。 跨兩種部署模型，可以立即 ExpressRoute 電路，無論它的建立方式，連結至虛擬網路。

![連結至虛擬網路跨兩種部署模型 ExpressRoute 電路](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>在傳統的部署模型中建立的 ExpressRoute 電路

在傳統的部署模型中建立的 ExpressRoute 電路需要先啟用傳統和資源管理員部署模型連線移到資源管理員部署模型。 有時，無法連線遺失或中斷連線已被移動。 在傳統的部署模型中 （在相同的訂閱和交互訂閱） 的所有電路至虛擬網路連結會都保留。

移動已順利完成後，ExpressRoute 電路看起來會執行，與完全類似 ExpressRoute 電路資源管理員部署模型中所建立。 您現在可以在資源管理員部署模型中建立虛擬網路的連線。

之後 ExpressRoute 電路已移至資源管理員部署模型，您可以管理生命週期的 ExpressRoute 電路只使用資源管理員部署模型。 這表示您可以執行作業，例如新增/更新/刪除 peerings，更新電路內容 （例如頻寬、 SKU，及付款類型） 和刪除電路只在資源管理員部署模型中。 請參閱下方的章節上電路資源管理員部署模型，如需有關如何管理存取權兩種部署模型中建立的。

您沒有包含您的連線提供者，若要執行移動。

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>資源管理員部署模型中建立的 ExpressRoute 電路

您可以啟用資源管理員部署模型，可以從這兩種部署模型中建立的 ExpressRoute 電路。 在您的訂閱中的任何 ExpressRoute 電路可以啟用從兩種部署模型存取。

- 資源管理員部署模型中所建立的 ExpressRoute 電路預設沒有傳統部署模型的存取權。
- 已從傳統部署模型移至 [資源管理員部署模型的 ExpressRoute 電路從來存取預設兩種部署模型。
- ExpressRoute 電路具有資源管理員部署模型，不論是否建立在 [資源管理員或傳統部署模型的存取權。 這表示您可以建立下列指示[連結的虛擬網路](expressroute-howto-linkvnet-arm.md)資源管理員部署模型中建立的虛擬網路連線。
- 由**allowClassicOperations**中參數的 ExpressRoute 電路控制傳統部署模型的存取權。

>[AZURE.IMPORTANT] 套用會記錄在[服務限制](../azure-subscription-service-limits.md)] 頁面的所有配額。 作為範例，標準電路可以有最多 10 個虛擬網路連結/連線跨傳統和資源管理員部署模型。


## <a name="controlling-access-to-the-classic-deployment-model"></a>控制存取權的傳統部署模型

您可以啟用單一 ExpressRoute 電路連結至這兩種部署模型中的虛擬網路設定的 ExpressRoute 電路**allowClassicOperations**參數。

**AllowClassicOperations**設為 TRUE，可讓您連結虛擬網路的兩種部署模型，以 ExpressRoute 電路。 您可以連結至傳統部署模型中的虛擬網路[如何連結在傳統的部署模型中的虛擬網路](expressroute-howto-linkvnet-classic.md)追蹤指引。 您可以連結至資源管理員部署模型中的虛擬網路[如何連結資源管理員部署模型中的虛擬網路](expressroute-howto-linkvnet-arm.md)追蹤指引。

設定從傳統部署模型電路 FALSE 區塊存取**allowClassicOperations** 。 不過，會保留在傳統的部署模型中的所有虛擬網路連結。 在此情況下，ExpressRoute 電路看不到傳統的部署模型中。

## <a name="supported-operations-in-the-classic-deployment-model"></a>在傳統的部署模型中支援的作業

下列傳統作業支援 ExpressRoute 電路上**allowClassicOperations**設為 TRUE 時︰

 - 取得 ExpressRoute 電路資訊
 - 建立/更新/取得/刪除虛擬網路連結到傳統的虛擬網路
 - 建立/更新/取得/刪除虛擬網路連結授權跨訂閱連線

您無法在**allowClassicOperations**設為 TRUE 時，執行下列傳統作業︰

 - 建立/更新/取得/刪除框線閘道器通訊協定 (BGP) peerings 的 Azure 的私人、 Azure 公用和 Microsoft peerings
 - 刪除 ExpressRoute 電路

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>傳統和資源管理員部署模型之間的通訊

ExpressRoute 電路就像是傳統與資源管理員部署模型之間的橋樑。 在傳統的部署模型中的虛擬網路 virtual machines 和資源管理員部署模型流，如果這兩個虛擬網路連結到相同的 ExpressRoute 電路 ExpressRoute 透過虛擬網路中的流量。

彙總處理量受限於處理量容量虛擬網路閘道器。 流量不會輸入連線提供者的網路或您的網路在這種情況下。 虛擬網路之間的流量完全包含在 Microsoft 網路。

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure 公用和 Microsoft 對等資源的存取權

您可以繼續存取透過 Azure 公用對等和 Microsoft 對等沒有任何中斷通常可以存取的資源。  

## <a name="whats-supported"></a>支援的項目

本節將描述 ExpressRoute 電路支援的功能︰

 - 您可以使用單一 ExpressRoute 電路存取虛擬部署傳統和資源管理員部署模型中的網路。
 - 資源管理員部署模型，您可以從傳統移動 ExpressRoute 電路。 移動時之後，ExpressRoute 電路的外觀、 覺得，並執行像其他任何資源管理員部署模型中建立的 ExpressRoute 電路。
 - 您可以移動 ExpressRoute 電路。 電路連結、 虛擬網路和 VPN 閘道器無法移動透過此作業。
 - 之後 ExpressRoute 電路已移至資源管理員部署模型，您可以管理生命週期的 ExpressRoute 電路只使用資源管理員部署模型。 這表示您可以執行作業，例如新增/更新/刪除 peerings，更新電路內容 （例如頻寬、 SKU，及付款類型） 和刪除電路只在資源管理員部署模型中。
 - ExpressRoute 電路就像是傳統與資源管理員部署模型之間的橋樑。 在傳統的部署模型中的虛擬網路 virtual machines 和資源管理員部署模型流，如果這兩個虛擬網路連結到相同的 ExpressRoute 電路 ExpressRoute 透過虛擬網路中的流量。
 - 跨訂閱連線支援的傳統和資源管理員部署模型。

## <a name="whats-not-supported"></a>不支援的項目

本節說明內容不支援 ExpressRoute 電路︰

 - 從傳統將電路連結、 閘道和虛擬網路移至 [資源管理員部署模型中。
 - 管理生命週期的 ExpressRoute 電路從傳統部署模型。
 - 傳統的部署模型角色型存取控制 (RBAC) 支援。 您無法在傳統的部署模型中執行電路 RBAC 控制項。 訂閱任何管理員/coadministrator 可以連結，或取消虛擬網路以電路。

## <a name="configuration"></a>設定

依照指示中[移動至資源管理員部署模型傳統的 ExpressRoute 電路](expressroute-howto-move-arm.md)所述。

## <a name="next-steps"></a>後續步驟

- 工作流程的詳細資訊，請參閱[ExpressRoute 電路佈建工作流程與電路狀態](expressroute-workflows.md)。
- 若要設定您的 ExpressRoute 連線︰

    - [建立 ExpressRoute 電路](expressroute-howto-circuit-arm.md)
    - [設定路由](expressroute-howto-routing-arm.md)
    - [連結 ExpressRoute 電路虛擬網路](expressroute-howto-linkvnet-arm.md)
