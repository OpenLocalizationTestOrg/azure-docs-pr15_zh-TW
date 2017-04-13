<properties
   pageTitle="概觀與 Azure VPN 閘道 BGP |Microsoft Azure"
   description="本文提供 BGP 與 Azure VPN 閘道的概觀。"
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
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>與 Azure VPN 閘道 BGP 的概觀

本文提供 BGP （框線閘道器通訊協定） 支援 Azure VPN 閘道器的概觀。

## <a name="about-bgp"></a>關於 BGP

BGP 是通常用於在網際網路中路由及性之間交換資訊兩個或多個網路的標準路由通訊協定。 BGP Azure 虛擬網路的內容中使用時，啟用 Azure VPN 閘道器，您的內部部署 VPN 裝置，稱為 BGP 同儕或其他例項，exchange 「 路由 」 會通知可用性及連線至 [閘道器] 或 [路由器瀏覽這些前置詞的兩個閘道器的步驟。 BGP 也可以啟用傳輸路由多個網路之間散佈的路由 BGP 閘道器學習從一個 BGP 對所有其他 BGP 對等。
 
### <a name="why-use-bgp"></a>為什麼要使用 BGP？

BGP 是一種選擇性功能，您可以使用 Azure 路由型 VPN 閘道器。 您也應該要確定您的內部部署 VPN 裝置可支援 BGP，才能啟用此功能。 您可以繼續使用 Azure VPN 閘道和內部部署 VPN 裝置不 BGP。 使用靜態路由 （不含 BGP)*與*使用您的網路和 Azure 之間使用 BGP 動態路由，相當。

有數種優點和與 BGP 的新功能︰

#### <a name="support-automatic-and-flexible-prefix-updates"></a>支援自動和彈性的前置詞更新

使用 BGP，您只需要透過 IPsec S2S VPN 通道宣告到特定的 BGP 對等的最小值的前置字元。 為主機前置詞很 （/ 32） 的內部部署 VPN 裝置的 BGP 等 IP 位址。 您可以控制的內部部署網路首碼您想要以允許 Azure 虛擬網路存取 Azure 通知。
    
您也可以通知可能包括您 VNet 地址前置字元，例如大型私人 IP 位址空間 (例如 10.0.0.0/8) 的一些較大前置字元。 請注意雖然您不能使用任何一種您 VNet 前置詞相同。 將拒絕您 VNet 前置詞與相同路由。

>[AZURE.IMPORTANT] 目前，通知 Azure VPN 閘道器的預設路由 (0.0.0.0 / 0) 會遭到封鎖。 一旦啟用此功能，將會提供更進一步的更新。

#### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>使用自動根據 BGP 的容錯移轉支援 VNet 與內部部署網站之間的多個通道

您可以建立多個連線，您 Azure VNet 與您內部部署 VPN 裝置之間的同一個位置。 此功能的作用中作用中設定兩個網路之間提供多個通道 （路徑）。 如果其中一個通道中斷，對應路由會撤回透過 BGP 和流量會自動轉換到剩餘通道。
    
下圖顯示此高度可用的安裝程式的簡單範例︰
    
![多個作用中的路徑](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>支援您的內部部署網路與多個 Azure VNets 之間的傳輸路由

BGP 是否直接或間接連線，可讓多個閘道學習和傳播前置詞，從不同的網路。 這可以讓傳輸路由與 Azure VPN 閘道或跨多個 Azure 虛擬網路之間您內部部署的網站。
    
下圖顯示可以傳輸流量兩個內部部署網路透過 Azure VPN 閘道內 Microsoft 網路之間的多個路徑的多個躍點拓撲範例︰

![多個躍點傳輸](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faqs"></a>BGP 常見問題集


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## <a name="next-steps"></a>後續步驟

請參閱[快速入門 BGP 上 Azure VPN 閘道器](./vpn-gateway-bgp-resource-manager-ps.md)的步驟以設定 BGP 跨內部部署和 VNet-VNet 連線。

