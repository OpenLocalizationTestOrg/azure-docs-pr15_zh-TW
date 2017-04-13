
<properties
   pageTitle="Azure 虛擬網路對等 |Microsoft Azure"
   description="深入了解 VNet Azure 中對等。"
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>對等 VNet

VNet 對等是機制連接兩個虛擬網路 (VNets) 透過 Azure 骨幹網路相同的區域。 一旦 peered，兩個虛擬網路會有所有連線進行。 仍可管理為不同的資源，但這些虛擬網路中的虛擬機器可以彼此直接使用私用的 IP 位址。

虛擬機器中 peered 虛擬網路之間的流量經由 Azure 基礎結構，如同在相同的虛擬網路 Vm 之間路由流量。 使用 [VNet 對等的優點包括︰

- 低延遲，高頻寬之間的連線不同的虛擬網路中的資源。
- 使用資源，例如網路設備及 VPN 閘道器成 peered VNet 中的傳輸點的能力。
- 若要使用 Azure 資源管理員模型虛擬網路使用傳統的部署模型，並啟用這些虛擬網路中的資源之間的完整連線虛擬網路連線能力。

需求及 VNet 對等的重要︰

- 在相同的 Azure 地區應該 peered 兩個虛擬網路。
- Peered 虛擬網路應有非重疊的 IP 位址空間。
- 對等 VNet 介於兩個虛擬網路，而沒有衍生轉移關係。 例如，如果虛擬網路的 peered 與虛擬網路 B，而且如果虛擬網路 B peered 與虛擬網路 C，它不會翻譯至虛擬網路正在 peered 與虛擬網路 c。
- 對等可以建立兩個不同的訂閱中的虛擬網路之間獲授限的使用者的兩個訂閱授權對等的時間有多長，並訂閱會以相同的 Active Directory 租用戶相關聯。 
- 資源管理員模型中的虛擬網路與傳統部署模型之間對等需要 VNets 應該位於同一份訂閱。
- 使用資源管理員部署模型的虛擬網路可以 peered 與另一個使用此模型的虛擬網路或虛擬網路使用的是使用傳統的部署模型。 不過，您還無法使用傳統的部署模型的虛擬網路 peered 彼此。
- 虛擬機器中 peered 虛擬網路之間的通訊不有任何額外的頻寬限制，但仍適用於頻寬首字放大根據虛擬記憶體大小。


![對等的基本 VNet](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>連線
兩個虛擬網路 peered 之後，虛擬網路中的虛擬機器 （web/工作者角色） 可以直接使用 peered 虛擬網路中其他虛擬機器連線到。 下列兩個網路有完整 IP 層級連線。

Peered 虛擬網路中的兩個虛擬機器之間來回的網路延遲是在本機的虛擬網路的來回一樣。 網路生產力為基礎的頻寬，允許虛擬機器等比例大小。 目前沒有任何頻寬限制。

虛擬機器中 peered 虛擬網路之間的流量路由直接透過 Azure 的後端基礎結構，而非透過閘道器。

虛擬機器中虛擬網路可以存取內部負載平衡 (ILB) 中的端點 peered 虛擬網路。 如果想要封鎖其他虛擬網路或子網路的存取權有虛擬網路可以套用至網路安全性群組 (NSGs)。

當使用者設定外面時，他們可以開啟或關閉虛擬網路之間的 NSG 規則。 如果使用者選擇開啟完整 peered 虛擬網路 （這是預設的選項） 之間的連線，他們可以使用 NSGs 上特定的子網路或虛擬機器封鎖或拒絕特定的存取權。

Azure 提供內部 DNS 名稱解析虛擬機器無法透過 peered 虛擬網路。 虛擬機器有內部 DNS 名稱解析只在本機的虛擬網路中的。 不過，使用者可以設定為 DNS 伺服器的虛擬網路 peered 虛擬網路中執行的虛擬機器。

## <a name="service-chaining"></a>鏈結的服務
使用者可以設定指向虛擬機器中 peered 虛擬網路做為 「 躍 」 IP 位址的使用者定義的傳送資料表，如下圖所示本文稍後的圖表中。 這可讓的使用者獲得鏈結服務，透過此他們可以直接從一個虛擬網路流量到 peered 的虛擬網路，透過使用者定義的傳送資料表中執行的虛擬應用裝置。

使用者可以也有效地建立中樞可以主機基礎結構元素，例如網路虛擬應用裝置中心和-輻類型環境。 所有支點虛擬網路可以再對都等，以及執行中心虛擬網路中的應用裝置的流量的子集合。 簡言之，VNet 對等啟用 「 使用者定義路由表 」 的下一個躍點 IP 位址是虛擬機器 peered 虛擬網路中的 IP 位址。

## <a name="gateways-and-on-premises-connectivity"></a>閘道器與內部部署的連線
每個虛擬網路，無論是否與其他的虛擬網路，peered 它仍會有自己的閘道器及用來連線到內部部署。 即使 peered 虛擬網路使用者也可以使用 [閘道器，設定[VNet-VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

當虛擬網路互連的兩個選項的設定時，之間虛擬網路流量流向對等的設定 (也就是透過 Azure 骨幹)。

當虛擬網路 peered 時，使用者也可以設定閘道器 peered 虛擬網路中為內部部署傳輸點。 在此情況下，使用遠端閘道器的虛擬網路不能有自己的閘道器。 虛擬網路可能只有一個閘道器。 它可以是本機的閘道器或遠端閘道器 （peered 虛擬網路），如下圖所示。

不支援閘道器傳輸等虛擬網路使用的資源管理員模型，那些使用傳統的部署模型之間建立關聯。 在對等關係的兩個虛擬網路需要使用閘道器傳輸的資源管理員部署模型作業。

當共用單一的 Azure ExpressRoute 連線的虛擬網路 peered 時，它們之間流量透過對等的關聯性 (也就是透過 Azure 骨幹網路)。 使用者還是可以使用本機的閘道器中的每一個虛擬網路連線到內部部署電路。 或者，他們可以使用共用的閘道器，並設定的內部部署連線傳輸。

![VNet 對等傳輸](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>佈建
對等 VNet 是有權限的作業。 它是另一個函數下 VirtualNetworks 命名空間。 使用者可以指定授權對等的特定權限。 具有虛擬網路讀取及寫入權限的使用者會自動繼承這些權限。

系統管理員的使用者或獲授限的使用者的對等的能力可以啟動另一個 VNet 等操作。 如果有另一側，對等的相符要求，而且符合其他需求，就會建立對等。

請參閱若要進一步瞭解如何建立對等兩個虛擬網路之間的 VNet 」 下一個步驟 」 一節中的文章。

## <a name="limits"></a>限制
有限制 peerings 單一虛擬網路所允許的數目。 如需詳細資訊，請參閱[Azure 網路限制](../azure-subscription-service-limits.md#networking-limits)。

## <a name="pricing"></a>價格
對等 VNet 會在檢閱期間的免費。 放開之後，請輸入與輸出利用對等的流量會有 nominal 的費用。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/virtual-network)。


## <a name="next-steps"></a>後續步驟
- [設定對等虛擬網路之間](virtual-networks-create-vnetpeering-arm-portal.md)。
- 深入了解[NSGs](virtual-networks-nsg.md)。
- 進一步瞭解[使用者定義的路徑，IP 轉寄](virtual-networks-udr-overview.md)。
