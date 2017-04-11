
<properties
   pageTitle="網際網路對負載平衡器概觀 |Microsoft Azure "
   description="網際網路對負載平衡器和其功能的概觀。 負載平衡器的運作方式的 Azure 虛擬機器和雲端服務使用。"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internet-facing-load-balancer-overview"></a>網際網路具負載平衡器概觀

Azure 負載平衡器會對應到私人 IP 位址和連接埠號碼的虛擬機器，反之亦然回應流量的公用 IP 位址和連接埠號碼的內送的流量，從虛擬機器。 負載平衡規則可讓您發佈特定類型的多個虛擬機器或服務之間的流量。 例如，您可以過多的網頁伺服器或 web 角色分配 web 要求流量的載入。

包含網頁角色或工作者角色的執行個體的雲端服務，您可以定義公用結束點服務定義 (.csdef) 檔案中。

_Servicedefinition.csdef_檔案包含端點組態和負載平衡器時您有多個網頁或背景工作角色部署的角色執行個體，就會設定為它。 新增至您的雲端部署的執行個體，方法變更執行個體計數服務設定檔案 (.csfg)。

下圖顯示的公開及私密金鑰 TCP 連接埠 443 的三個虛擬機器之間共用加密的網頁流量的負載平衡結束點。 下列三個虛擬機器位於負載平衡的設定。

![公用負載平衡器範例](./media/load-balancer-internet-overview/IC727496.png))

圖 1-負載平衡加密的網頁流量的端點

當網際網路用戶端傳送 TCP 連接埠 443 的雲端服務的公用 IP 位址網頁要求時，請 Azure 負載平衡器會將三個虛擬機器中的負載平衡設定之間的要求。 如需有關負載平衡器演算法的詳細資訊，請參閱[負載平衡器概觀] 頁面](load-balancer-overview.md#load-balancer-features)。

根據預設，Azure 負載平衡器會將多個虛擬機器執行個體之間平均分佈的網路流量。 您也可以設定工作階段的相關性，如需詳細資訊，請參閱[負載平衡器分配模式](load-balancer-distribution-mode.md)。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解哪些負載平衡器較適合您的雲端部署，深入了解[內部負載平衡器](load-balancer-internal-overview.md)。

您也可以[開始建立網際網路圖示的負載平衡器](load-balancer-get-started-internet-arm-ps.md)及設定何種類型的特定負載平衡器網路流量行為的[通訊群組模式](load-balancer-distribution-mode.md)。

如果您的應用程式需要保留作用之後負載平衡器伺服器的連線，您可以瞭解更多有關[閒置的 TCP 逾時設定的負載平衡器](load-balancer-tcp-idle-timeout.md)。 它會協助瞭解閒置連線行為，當您使用的 Azure 負載平衡器。
