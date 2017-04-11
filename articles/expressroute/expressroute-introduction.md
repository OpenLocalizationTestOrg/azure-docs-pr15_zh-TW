<properties 
   pageTitle="簡介 ExpressRoute |Microsoft Azure"
   description="本頁面提供 ExpressRoute 服務，包括 ExpressRoute 連線的運作方式的概觀。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-technical-overview"></a>ExpressRoute 技術概觀

Microsoft Azure ExpressRoute 可讓您透過一個連線提供者所提供的專用私人連線時，將您的內部部署網路延伸至 Microsoft 雲端。 有 ExpressRoute，您可以建立連線至 Microsoft 雲端服務，例如 Microsoft Azure、 Office 365 和 CRM Online。 連線可以從任何-任何 (IP VPN) 網路、 點對點乙太網路或虛擬連線提供者所提供的代管設備在跨-連線。 ExpressRoute 連線，請勿繼續進行公用網際網路上。 這個選項可讓 ExpressRoute 連線至網際網路上提供更多的可靠性、 更快的速度，較低延遲，以及較高的安全性比一般的連線。

![](./media/expressroute-introduction/expressroute-basic.png)

**主要優點包括︰**

- 圖層 3 之間內部部署網路與 Microsoft 雲端連線提供者所提供的連線。 連線可從任何-任何的 (IPVPN) 網路，點對點乙太網路連線，或透過虛擬透過乙太網路 exchange 跨-連線。
- 連線至 Microsoft 雲端服務，透過淉區域中的所有區域。
- 全域的連線至 Microsoft 服務，在所有區域的 ExpressRoute 進階版附加元件。
- 動態路由網路和 Microsoft 之間工業標準通訊協定 (BGP)。
- 增加可靠性的對等的位置中的內建重複。
- 連線的執行時間[SLA](https://azure.microsoft.com/support/legal/sla/)。
- QoS 和支援服務的特殊的應用程式，例如商務用 Skype 的多個類別。

請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)，如需詳細資訊。

## <a name="howtoconnect"></a>如何將我的網路連線至 Microsoft 使用 ExpressRoute

您可以建立您的內部部署網路與 Microsoft 雲端之間的連線，三個不同的方式︰

### <a name="co-located-at-a-cloud-exchange"></a>共同位於雲端 exchange

如果您共同位於雲端 exchange 設備，您就可以排序虛擬代管提供者的乙太網路 exchange 透過 Microsoft 雲端的跨連線。 第 2 層跨連線或受管理圖層 3 跨-之間的連線內共同位置基礎結構與 Microsoft 雲端，可提供代管提供者。

### <a name="point-to-point-ethernet-connections"></a>點對點乙太網路連線 

您可以透過點對點乙太網路連結 Microsoft 雲端連線您內部部署資料中心/辦公室。 點對點乙太網路提供者可以提供第 2 層連線，或管理您的網站與 Microsoft 雲端之間的圖層 3 連線。

### <a name="any-to-any-ipvpn-networks"></a>任何-任何 (IPVPN) 網路

您可以整合您 WAN 與 Microsoft 雲端。 IPVPN 提供者 (通常是 MPLS VPN) 提供任何-任何分公司和資料中心之間的連線。 Microsoft 雲端可以相互連接，以您 WAN，使其看起來就像其他任何分公司。 WAN 提供者通常會提供受管理的圖層 3 連線。 ExpressRoute 功能與功能是全部相同的所有上述 connectivity 模型。 

連線提供者可以提供一或多個連線的模型。 您可以使用您的連線提供者，若要選擇最適合您的模型。

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## <a name="expressroute-features"></a>ExpressRoute 功能

ExpressRoute 支援下列的功能︰ 

### <a name="layer-3-connectivity"></a>第 3 層連線

Microsoft 會使用業界標準動態路由通訊協定 (BGP) exchange 路由內部網路，您的執行個體中 Azure 與 Microsoft 之間公用的地址。  我們建立多個 BGP 工作階段與您的網路流量不同的設定檔。 可以[ExpressRoute 電路與路由網域](expressroute-circuit-peerings.md)文件中找到更多詳細資料。

### <a name="redundancy"></a>重複

每個 ExpressRoute 電路包含兩個連接到兩個 Microsoft Enterprise 邊緣路由器 (MSEEs) 連線提供者 / 您網路邊緣。 Microsoft 會要求連線提供者所提供的雙 BGP 連線 / 側 – 一到每個 MSEE。 您可以選擇不部署多餘的裝置 / 乙太網路及電路您結尾。 不過，連線提供者會使用多餘的裝置，以確保的連線都會傳送給 Microsoft 多餘的方式。 重複的圖層 3 連線設定是我們[SLA](https://azure.microsoft.com/support/legal/sla/)有效的需求。 

### <a name="connectivity-to-microsoft-cloud-services"></a>連線至 Microsoft 雲端服務

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

ExpressRoute 連線啟用存取下列服務︰

- Microsoft Azure 服務
- Microsoft Office 365 服務
- Microsoft CRM 線上服務 
 
您可以造訪 ExpressRoute 支援服務的詳細清單的[ExpressRoute 常見問題集](expressroute-faqs.md)頁面。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>連線至淉區域內的所有區域

您可以連線至 Microsoft 我們[對等的位置](expressroute-locations.md)，並有權存取所有淉區域內的區域。 

例如，如果您連線至 Microsoft 透過 ExpressRoute Amsterdam，您會有存取裝載於北歐洲及西歐所有 Microsoft 雲端服務。 請參閱[ExpressRoute 合作夥伴及對等的位置](expressroute-locations.md)文章概略淉區域、 相關聯的 Microsoft cloud 地區和對應的 ExpressRoute 對等的位置。

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>全域連線具備 ExpressRoute 進階版增益集

您可以啟用 ExpressRoute 進階版附加元件功能延伸淉邊界的連線。 例如，如果您連線至 Microsoft 透過 ExpressRoute Amsterdam，您能夠存取世界裝載在所有區域內的所有 Microsoft 雲端服務 （排除國際雲朵）。 您可以存取服務部署南美洲或澳大利亞中相同的方式存取北和西歐區域。

### <a name="rich-connectivity-partner-ecosystem"></a>Rtf 連線合作夥伴生態

ExpressRoute 有連線提供者及 SI 合作夥伴持續成長生態。 您可以參考[ExpressRoute 提供者及位置](expressroute-locations.md)的文件的最新的資訊。

### <a name="connectivity-to-national-clouds"></a>連線至國際雲朵

Microsoft 的運作方式特殊的淉區域及客戶區段隔離的雲端的環境。 請參閱[ExpressRoute 提供者及位置](expressroute-locations.md)] 頁面，國際雲朵和提供者的清單。

### <a name="supported-bandwidth-options"></a>支援的頻寬選項

您可以購買各種頻寬 ExpressRoute 電路。 以下是支援的頻寬的清單。 請務必檢查您的連線提供者，以判斷其所提供的支援頻寬的清單。

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 Gbps
- 2 Gbps
- 5 Gbps
- 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>頻寬動態縮放比例

您可以增加 ExpressRoute 電路頻寬 （最佳的投入比基礎） 而不需要清除您的連線。 

### <a name="flexible-billing-models"></a>彈性的帳單模型

您可以選擇最適合您的帳單模型。 選擇 [帳單下方所列的模型之間。 請參閱更多詳細資料的[ExpressRoute 常見問題集](expressroute-faqs.md)頁面。 

- **無限制的資料**。 ExpressRoute 電路負責根據每月的費用，並包含免費付費的所有傳入和傳出的資料傳輸。 
- **計量付費資料**。 ExpressRoute 電路負責根據每月費用。 所有的連入的資料傳輸是免費的項目。 輸出資料傳輸負責每 GB 的資料傳輸。 資料傳輸速度因區域。
- **ExpressRoute 進階版附加元件**。 ExpressRoute 進階版的 ExpressRoute 電路是附加元件。 ExpressRoute 進階版附加元件提供下列功能︰ 
    - 提高的傳送限制 Azure 公用和 Azure 私人對等從 4000 路由到 10000 路由。
    - 全域服務的連線。 （不含國際雲朵） 的任何區域中建立 ExpressRoute 電路都可以存取資源跨世界中的任何其他區域。 例如，可以透過矽谷佈建 ExpressRoute 電路存取西歐中所建立的虛擬網路。
    - 更的多每 10 的較大的限制，根據的電路頻寬的 ExpressRoute 電路 VNet 連結。

## <a name="next-steps"></a>後續步驟

- 深入了解 ExpressRoute 連線和路由的網域。 請參閱[ExpressRoute 電路與路由的網域](expressroute-circuit-peerings.md)。
- 尋找服務提供者。 請參閱[ExpressRoute 合作夥伴與對等的位置](expressroute-locations.md)。
- 請確定的符合所有的先決條件。 請參閱[ExpressRoute 的先決條件](expressroute-prerequisites.md)。
- 請參閱[路由](expressroute-routing.md)、 [NAT](expressroute-nat.md)及[QoS](expressroute-qos.md)需求。
- 設定您的 ExpressRoute 連線。
    - [建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)
    - [設定路由](expressroute-howto-routing-classic.md)
    - [連結 VNet ExpressRoute 電路](expressroute-howto-linkvnet-classic.md)
