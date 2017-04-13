<properties
   pageTitle="概觀與 Azure VPN 閘道可用性設定 |Microsoft Azure"
   description="本文提供使用 Azure VPN 閘道器的高度可用的設定選項的概觀。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/24/2016"
   ms.author="yushwang"/>

# <a name="highly-available-cross-premises-and-vnet-to-vnet-connectivity"></a>高度可用的跨內部部署及 VNet-VNet 連線

本文提供您跨內部部署與使用 Azure VPN 閘道器的 VNet-VNet 連線的高度可用的設定選項的概觀。

## <a name = "activestandby"></a>關於 Azure VPN 閘道器重複

每個 Azure VPN 閘道組成的作用中待命設定的兩個執行個體。 預定進行的維護或條件的變化的作用中的執行個體的計畫的中斷，備用的執行個體會自動接手 （容錯移轉），然後繼續 S2S VPN 或 VNet-VNet 連線。 在切換會導致中斷。 預定進行的維護的連線應該會還原 10 到 15 秒內。 非預期的問題，連接復原就會更長的時間，約 1 分鐘 1 和半分鐘表現最差的大小寫。 閘道器 P2S VPN 用戶端連線，P2S 連線將會中斷，使用者必須從用戶端電腦重新連線。

![作用中待命](./media/vpn-gateway-highlyavailable/active-standby.png)

## <a name="highly-available-cross-premises-connectivity"></a>高度可用的跨內部部署連線

若要提供更佳的可用性交叉部署連線，有幾個選項可用︰

- 在多個內部部署 VPN 裝置
- 主動-主動 Azure VPN 閘道器
- 兩者的組合

### <a name = "activeactiveonprem"></a>在多個內部部署 VPN 裝置

您可以使用多個要有 vpn 才能裝置從您的內部部署網路連線至 Azure VPN 閘道，如下圖所示︰

![多個內部部署 VPN](./media/vpn-gateway-highlyavailable/multiple-onprem-vpns.png)

此設定會提供多個作用中的通道，從相同的 Azure VPN 閘道器至您的內部部署裝置的同一個位置。 有一些需求及限制︰

1. 您需要建立 Azure VPN 裝置的多個 S2S VPN 連線。 當您從相同的內部部署網路連線多個要有 vpn 才能裝置至 Azure 時，您需要從 Azure VPN 閘道區域網路閘道器建立的每一個要有 vpn 才能裝置的一個區域網路閘道和一個連線。

2. 對應到您要有 vpn 才能裝置的區域網路閘道器都必須擁有 「 GatewayIpAddress 」 屬性中的唯一的公用 IP 位址。

3. BGP 都需要此設定。 代表 VPN 裝置每個區域網路閘道器必須具有唯一 BGP 對等的 IP 位址 」 BgpPeerIpAddress] 屬性中指定。

4. 每個區域網路閘道器的 AddressPrefix 屬性欄位不能重疊。 您應該指定 「 BgpPeerIpAddress 」 中/32 的 AddressPrefix 欄位中，例如 10.200.200.254/32 CIDR 格式。

5. 您應該使用 BGP 廣告相同的前置詞相同的內部部署網路 Azure VPN 閘道，前置詞與流量將轉寄到這些通道同時。

6. 針對您 Azure VPN 閘道器，10 的基本版和標準的 Sku，通道數目上限計算每一種連線到 HighPerformance SKU 30。 

在此組態中，Azure VPN 閘道器時仍使用中-，因此相同的容錯移轉行為與簡短中斷仍會發生所述[上述](#activestandby)。 不過此設定會避免發生失敗或在您的內部部署網路和 VPN 裝置上的干擾。
 
### <a name="active-active-azure-vpn-gateway"></a>主動-主動 Azure VPN 閘道器

您現在可以建立 Azure VPN 閘道器在主動-主動組態中，位置的閘道器 Vm 兩個執行個體會建立 S2S VPN 通道至內部部署 VPN 裝置，如下圖如下圖所示︰

![作用中作用中](./media/vpn-gateway-highlyavailable/active-active.png)

在此組態中，每個 Azure 閘道執行個體會唯一的公用 IP 位址，且每個會建立 IPsec/IKE S2S VPN 通道至您指定的區域網路閘道器與連線的內部部署 VPN 裝置。 請注意，這兩個 VPN 通道實際的部份相同的連線。 您仍會需要設定您的內部部署 VPN 裝置，如果要接受或建立兩個 S2S VPN 通道，這些兩個 Azure VPN 閘道器公用 IP 位址。

Azure 閘道執行個體是在 [作用中作用中的設定，因為從 Azure 虛擬網路流量至您的內部部署網路會同時路由傳送到兩個通道，即使您的內部部署 VPN 裝置可能會優先於一個通道其他。 請注意︰ 雖然相同通道或路徑，一律會往返相同的 TCP 或 UDP 流程，除非進行的維修作業事件發生在其中一個執行個體。

當預定進行的維護或非預期的事件發生問題一個閘道執行個體時，將會中斷 IPsec 通道從該執行個體至您的內部部署 VPN 裝置。 要有 vpn 才能在裝置上的對應路由應該移除，或讓流量將會切換到其他作用中 IPsec 通道自動撤回。 Azure 一邊，移到切換將會自動從受影響的執行個體的作用中的執行個體。

### <a name="dual-redundancy-active-active-vpn-gateways-for-both-azure-and-on-premises-networks"></a>同時 Azure 及內部網路的雙重複︰ 主動-主動 VPN 閘道器

最可靠的方式是結合主動-主動閘道器的網路和 Azure，如下圖所示。

![雙重重複](./media/vpn-gateway-highlyavailable/dual-redundancy.png)

以下您建立及設定 Azure VPN 閘道器在主動-主動組態中，建立兩個區域網路閘道器和兩個連接您的兩個內部部署 VPN 裝置上所述。 結果是 4 IPsec 通道 Azure 虛擬網路與內部部署網路之間的完整網狀連線。

所有閘道和通道都是作用中 Azure 端，因此流量會被散佈到所有的 4 通道同時，雖然各項的 TCP 或 UDP 流量會再次追蹤相同通道或路徑 Azure 端。 即使所用的流量，您可能會看到稍微提升透過 IPsec 通道，此設定的主要目的是高可用性。 而且分配的統計特性，因為很難提供不同的應用程式流量條件測量會影響彙總處理量。

此拓撲需要兩個區域網路閘道器及兩個連線到支援的內部部署 VPN 裝置，組，BGP 需要以允許的兩個連線到相同的內部部署網路。 這些需求是相同的[上方](#activeactiveonprem)。 

## <a name="highly-available-vnet-to-vnet-connectivity-through-azure-vpn-gateways"></a>透過 Azure VPN 閘道可用性 VNet-VNet 連線

若要 VNet Azure VNet-連線也可以套用相同的作用中作用中設定。 您可以建立兩個虛擬網路，主動-主動 VPN 閘道器，並連接圖形放在一起的兩個 VNets，4 通道表單相同的完整網狀連線下圖所示︰

![VNet-VNet](./media/vpn-gateway-highlyavailable/vnet-to-vnet.png)

如此一來，可確保一律是一組的兩個虛擬網路所有預定進行的維修作業事件，提供更好的可用性通道。 即使連線需要兩個連接的相同拓撲跨部署，如上所示的 VNet-VNet 拓撲需要只有一個連線每一個閘道器。 此外，BGP 是選擇性的傳輸路由透過 VNet-VNet 連線不需要。


## <a name="next-steps"></a>後續步驟

請參閱[設定主動-主動 VPN 閘道器的跨內部部署和 VNet-VNet 連線](vpn-gateway-activeactive-rm-powershell.md)的步驟設定主動-主動跨內部部署和 VNet-VNet 連線。
