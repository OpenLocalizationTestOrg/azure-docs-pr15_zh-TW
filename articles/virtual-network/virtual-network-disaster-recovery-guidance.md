<properties
    pageTitle="如何影響 Azure 虛擬網路 Azure 服務中斷事件 |Microsoft Azure"
    description="瞭解如何影響 Azure 虛擬網路 Azure 服務中斷事件。"
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>虛擬網路 – 業務連續性

##<a name="overview"></a>概觀

虛擬網路 (VNet) 是網路的您雲端中的邏輯表示。 其可讓您定義自己私用的 IP 位址空間，並將子網路區段的網路。 VNets 做為信任界限裝載您的計算資源 Azure 虛擬機器等雲端服務 （web/工作者角色）。 VNet 可讓資源裝載於其私人直接 IP 通訊。 虛擬網路也可以透過其中一個混合式選項，例如 VPN 閘道器或 ExpressRoute 內部部署網路連結。
 
VNet 會建立範圍內的區域。 您可以在兩個不同的區域內的相同位址空間建立 VNets (也就是美國東部和美國西部但不能將它們連接至另一個直接)。 

##<a name="business-continuity"></a>業務連續性

可能有幾種不同的方式，您的應用程式可能會中斷。 指定的區域可能會完全裁切自然損毀或部分的損毀，因為的多個裝置/服務失敗。 VNet 服務影響是不同的各種情況下。

**問︰ 您進行到整個區域在中斷時？例如若區域是完全截止因為自然損毀？虛擬網路裝載於區域會發生什麼情況？**

答︰ 虛擬網路和受影響的區域中的資源仍無法存取服務中斷的期間。

![簡單的虛擬網狀圖](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**問︰ 什麼是否可重新建立相同的虛擬網路中的不同部分？**

答︰ 虛擬網路 (VNet) 是相當精簡的資源。 您可以叫用 Azure Api 建立 VNet 與不同區域的相同位址空間。 若要重新建立相同的環境，是受影響的區域中，您必須撥打 API 重新部署您的雲端服務 （web/工作者角色） 和您的虛擬機器。 您也必須微調 VPN 閘道器設定，並連線到您的內部部署網路如果您有內部部署連線 （例如混合式部署）。

找不到建立 VNet 的指示進行[以下](./virtual-networks-create-vnet-arm-pportal.md)。 

**問︰ 複本的指定區域內 VNet 可以重新建立另一個區域提前嗎？**

A: [是]，您可以建立兩個 VNets 提前的兩個不同區域中使用的相同私用的 IP 位址空間和資源。 如果客戶所裝載網際網路對 VNet 的服務，它們可能地理路由流量至作用中的地區設定有流量 Manager。 不過，客戶無法連接兩個 VNets 相同的位址空間，他們的內部部署網路因為會造成路由問題。 損毀的時間，在某個區域 VNet 的影響，客戶可以搭配得宜的位址空間，他們的內部部署網路連線其他 VNet 中可用的區域。

找不到建立 VNet 的指示進行[以下](./virtual-networks-create-vnet-arm-pportal.md)。
