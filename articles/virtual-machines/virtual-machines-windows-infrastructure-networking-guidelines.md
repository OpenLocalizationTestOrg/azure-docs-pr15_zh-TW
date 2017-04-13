<properties
    pageTitle="網路基礎結構的指導方針 |Microsoft Azure"
    description="深入了解部署虛擬網路 Azure 基礎結構服務中的重要的設計及實作方針。"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="networking-infrastructure-guidelines"></a>網路基礎結構的指導方針

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文著重於瞭解必要的規劃步驟中 Azure 虛擬網路與連線的現有的內部部署環境之間。


## <a name="implementation-guidelines-for-virtual-networks"></a>實作虛擬網路的方針

決策︰

- 您需要裝載您的 IT 工作量或基礎結構何種類型的虛擬網路 (僅限雲端或跨內部部署)？
- 跨內部部署虛擬網路，地址空間量，您需要裝載的子網路和 Vm 現在和未來合理擴充？
- 您要建立集中的虛擬網路或建立個別的虛擬網路，每個資源群組？

工作︰

- 定義要建立虛擬網路位址空間。
- 定義每組子網路和位址空間。
- 跨內部部署虛擬網路，定義一組虛擬網路中的 Vm 需要達到內部部署位置的區域網路位址空間。
- 工作時建立交互內部部署的虛擬網路，以確保適當的路由網路小組設定與內部部署。
- 建立虛擬網路使用您的命名慣例。


## <a name="virtual-networks"></a>虛擬網路

虛擬網路的來支援虛擬機器 (Vm) 之間的通訊。 您可以定義子網路、 自訂的 IP 位址、 DNS 設定安全性篩選，並負載平衡，與實際的網路。 藉由使用[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[Express 路由電路](../expressroute/expressroute-introduction.md)，您可以連線到您的內部網路的 Azure 虛擬網路。 您可以閱讀更多關於[虛擬網路和與其元件](../virtual-network/virtual-networks-overview.md)。

使用資源群組，必須將虛擬的網路元件的設計的彈性。 Vm 可以連線至自己的資源群組以外的虛擬網路。 常見的設計方法就是建立包含可由一般的小組，然後 Vm 並應用程式部署至不同的資源群組您核心網路基礎結構的集中式的資源群組。 這種方法可讓應用程式的擁有者存取權而不需開啟的存取權的寬度的虛擬網路資源設定包含其 Vm 的資源群組。

## <a name="site-connectivity"></a>網站的連線

### <a name="cloud-only-virtual-networks"></a>雲端專用的虛擬網路
內部部署使用者和電腦不需要進行中的連線至 Vm Azure 虛擬網路中，如果您的虛擬網路設計是直接向︰

![基本雲端專用虛擬網狀圖](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

此方法通常是針對網際網路的工作量，例如以網際網路為基礎的網頁伺服器。 您可以管理這些 Vm 使用 RDP 或點為網站 VPN 連線。

因為他們無法連線到內部部署網路，僅供 Azure 虛擬網路可以使用 [私用的 IP 位址空間的任何部分，即使相同的私人空間中使用內部部署。


### <a name="cross-premises-virtual-networks"></a>跨內部部署虛擬網路
如果內部部署使用者和電腦需要進行中的連線至 Vm Azure 虛擬網路中，建立交互內部部署的虛擬網路。  將其連線至您的內部部署網路與 ExpressRoute 或網站-VPN 連線。

![跨內部部署虛擬網狀圖](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

在此組態中，Azure 虛擬網路是基本上雲端副檔名為內部網路。

因為其連線至您的內部部署網路，跨內部部署的虛擬網路必須使用使用您的組織都是唯一的地址空間的一部分。 以相同的方式不同的公司位置指派特定 IP 子網路，Azure 會變成另一個位置，如您擴充出您的網路。

若要允許您跨內部部署的虛擬網路前往您的內部網路的封包，您必須設定內部相關位址首碼設定虛擬網路的區域網路定義的一部分。 根據虛擬網路的位址空間，組相關的內部部署位置可以有多個地址前置詞本機網路中。

雲端專用的虛擬網路轉換跨內部部署虛擬網路，但是您最有可能需要重新 ip 您虛擬網路位址空間和 Azure 資源。 因此，請仔細考慮是否虛擬網路需要連線到內部部署網路，當您指派 IP 子網路。

## <a name="subnets"></a>子網路
子網路讓您組織相關，資源的 [以邏輯方式 （例如，一個 Vm 相同的應用程式與相關聯的子網路），或實際 （例如，資源群組每一個子網路）。 您也可以採用子網路隔離技巧，以提高安全性。

跨內部部署虛擬網路，您應該設計子網路與您用於內部部署資源的相同慣例。 **Azure 一律使用位址空間，每一個子網路的前三個 IP 位址**。 若要判斷子網路所需的地址的數目，開始計算 Vm 您現在需要的數目。 估計未來的成長，以及然後使用下表來判定子網路的大小。

Vm 所需的數字 | Host （主機） 所需的位元數 | 子網路的大小
--- | --- | ---
1-3 | 3 | / 29
4-11     | 4 | / 28
12-27 | 5 | / 27
28 – 59 | 6 | / 26
60 – 123 | 7 | / 25

> [AZURE.NOTE] 標準內部部署子網路主機地址前面的 n 個主機位元與子網路數目上限是 2<sup>n</sup> -2。 Azure 的子網路，n 主機位元與子網路主機地址數目上限是 2<sup>n</sup> -5 （2 加上 3 Azure 使用每一個子網路上的地址）。

如果您選擇太小子網路大小，您必須重新 IP，並重新部署 Vm 子網路中。


## <a name="network-security-groups"></a>網路安全性群組
您可以篩選將規則套用至流量流向透過虛擬網路使用網路安全性群組。 您可以建立細微篩選的規則，以保護您的虛擬網路環境控制傳入和傳出流量、 來源和目的 IP 範圍，允許的連接埠、 等等。在虛擬網路的子網路或直接指定虛擬網路介面，可以套用網路安全性群組。 建議您具有某種程度的網路安全性群組篩選虛擬網路流量。 您可以閱讀更多關於[網路安全性群組](../virtual-network/virtual-networks-nsg.md)。


## <a name="additional-network-components"></a>額外的網路元件
與內部部署實體網路基礎結構，Azure 虛擬網路最多可以包含多個網路和 IP 位址。 設計您的應用程式基礎結構時，您可能會想要併入某些其他元件︰

- [VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)-Azure 虛擬網路連線至其他 Azure 虛擬網路，或將連線到內部部署網路透過網站-VPN 連線。 實作 Express 路由專用、 安全連線的連線。 您也可以提供使用者直接存取權，以點為網站 VPN 連線。
- [負載平衡器](../load-balancer/load-balancer-overview.md)-提供負載平衡流量的內部和外部視流量。
- [應用程式閘道](../application-gateway/application-gateway-introduction.md)-HTTP 應用程式層的負載平衡、 web 應用程式提供一些額外的福利，而非部署 Azure 負載平衡器。
- [流量管理員](../traffic-manager/traffic-manager-overview.md)-DNS 傳輸通訊群組，將使用者至最接近的可用的應用程式端點，可讓您裝載在不同區域中的 Azure 資料中心登出應用程式。


## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 