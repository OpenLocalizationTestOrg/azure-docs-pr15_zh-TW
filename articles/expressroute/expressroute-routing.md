<properties
   pageTitle="路由需求 ExpressRoute |Microsoft Azure"
   description="本頁面提供設定和管理的 ExpressRoute 電路路由的詳細的需求。"
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>ExpressRoute 路由需求  

若要連線至 Microsoft 雲端服務使用 ExpressRoute，您需要設定及管理路由。 某些連線提供者提供設定及管理路由為受管理的服務。 請洽詢您的連線提供者，請參閱是否提供這項服務。 如果沒有，您必須遵循下列需求。 

請參閱[電路與路由網域](expressroute-circuit-peerings.md)中設定以利於進行連線需要路由工作階段的說明文章。

>[AZURE.NOTE] Microsoft 不支援的可用性設定任何路由器重複通訊協定 （例如 HSRP、 VRRP）。 我們依賴 BGP 工作階段，每個對等高可用性重複配對。

## <a name="ip-addresses-used-for-peerings"></a>Peerings 所用的 IP 位址

您需要保留幾個區塊的 IP 位址，以設定您的網路和 Microsoft 的企業邊緣 (MSEEs) 路由器之間的路徑。 本節提供需求的清單，並說明這些 IP 位址必須如何取得和使用規則。

### <a name="ip-addresses-used-for-azure-private-peering"></a>Azure 私人對等所用的 IP 位址

若要設定 peerings，您可以使用 [私用的 IP 位址或公用 IP 位址。 用於設定路由的地址範圍不能用來建立虛擬網路 Azure 中的地址範圍重疊。 

 - 您必須保留 /29 子網路或兩個 /30 子網路路由介面。
 - 私用的 IP 位址或公用 IP 位址，可用於路由的子網路。
 - 在子網路必須衝突保留用於在 Microsoft 雲端客戶的範圍。
 - 如果 /29 子網路，則會分成兩個 /30 子網路。 
     - 第一個 /30 子網路會用的主要連結和第二個/30 子網路將會用於次要的連結。
     - 每個 /30 子網路，您必須使用 /30 的第一個 IP 位址子網路上路由器。 Microsoft 會使用第二個 IP 位址的 /30 子網路設定的 BGP 工作階段。
     - 您必須設定為有效我們[可用性 SLA](https://azure.microsoft.com/support/legal/sla/)的兩個 BGP 工作階段。  

#### <a name="example-for-private-peering"></a>私人對等的範例

如果您選擇使用 a.b.c.d/29 設定的對等，它會分成兩個 /30 子網路。 在下面的範例中，我們會查看 a.b.c.d/29 子網路的使用方式。 

將分割 a.b.c.d/30 和 a.b.c.d+4/30 a.b.c.d/29，並將其向下傳送至 Microsoft 透過佈建 Api。 您將會用於 a.b.c.d+1 VRF ip 主要 PE 與 Microsoft 會使用 a.b.c.d+2 的主要 MSEE 的 VRF ip。 您將會用於 a.b.c.d+5 VRF ip 次要 PE 和 Microsoft 會使用 VRF ip a.b.c.d+6 的次要 MSEE。

請考慮您選取 [192.168.100.128/29 設定私人對等的大小寫。 192.168.100.128/29 包含來自 192.168.100.128 至 192.168.100.135，間的地址︰

- 使用 192.168.100.129 和使用 192.168.100.130 Microsoft 提供者時，會指定 192.168.100.128/30 link1。
- 使用 192.168.100.133 和使用 192.168.100.134 Microsoft 提供者時，會指定 192.168.100.132/30 link2。

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Azure 公用和 Microsoft 對等的 IP 位址

如需設定 BGP 工作階段，您必須使用您自己的公用 IP 位址。 Microsoft 必須驗證的 IP 位址透過路由網際網路登錄和網際網路路由登錄擁有權。 

- 您必須使用唯一/29 子網路或兩個 /30 子網路設定的每個對等 ExpressRoute 每對等 BGP 電路 （如果您有超過一個）。 
- 如果 /29 子網路，則會分成兩個 /30 子網路。 
    - 第一個 /30 子網路會用的主要連結和第二個/30 子網路將會用於次要的連結。
    - 每個 /30 子網路，您必須使用 /30 的第一個 IP 位址子網路上路由器。 Microsoft 會使用第二個 IP 位址的 /30 子網路設定的 BGP 工作階段。
    - 您必須設定為有效我們[可用性 SLA](https://azure.microsoft.com/support/legal/sla/)的兩個 BGP 工作階段。

## <a name="public-ip-address-requirement"></a>公用 IP 位址需求 

### <a name="private-peering"></a>私人對等 

您可以選擇要用於公用或私用 IPv4 位址私人對等。 因此重疊的地址與其他客戶無法若私人對等，我們會提供您的資料傳輸的端對端隔離。 這些地址不會通知網際網路。 

### <a name="public-peering"></a>公用對等

Azure 公用等路徑可讓您連線至自己的公用 IP 位址透過裝載於 Azure 中的所有服務。 包括[ExpessRoute 常見問題集](expressroute-faqs.md)中所列的服務以及由 Microsoft Azure 上 Isv 裝載任何服務。 將 Microsoft 網路連線至 Microsoft Azure 服務公用對等永遠啟動時從您的網路。 您必須使用 Microsoft 網路流量的公用 IP 位址。

### <a name="microsoft-peering"></a>Microsoft 對等

Microsoft 對等路徑可讓您連線至 Microsoft 雲端服務不支援透過 Azure 公用對等的路徑。 服務清單，包括 Office 365 服務，例如 Exchange Online、 SharePoint Online、 商務用 Skype，並 CRM Online。 Microsoft 支援雙向連線 Microsoft 對等。 Microsoft 雲端服務流量進入 Microsoft 網路之前，必須使用有效公用 IPv4 位址。

請確定您的 IP 位址和為數字註冊您的登錄下列其中一種。

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] 透過 ExpressRoute 通知給 Microsoft 的公用 IP 位址無法必須通知至網際網路。 這可能會中斷連線至其他 Microsoft 服務。 不過，在您的網路與 Microsoft 內的 O365 端點通訊的伺服器所使用的公用 IP 位址可能會透過 ExpressRoute 已公告。 

## <a name="dynamic-route-exchange"></a>動態傳送 exchange

路由 exchange 會透過 eBGP 通訊協定。 MSEEs 與路由器之間，會建立 EBGP 工作階段。 BGP 工作階段的驗證並非必要。 如有需要，您可以設定 MD5 雜湊。 如需設定 BGP 工作階段的資訊，請參閱[設定路由](expressroute-howto-routing-classic.md)及[電路佈建工作流程與電路狀態](expressroute-workflows.md)。

## <a name="autonomous-system-numbers"></a>獨立的系統數字

Microsoft 會使用另存新檔 12076 來 Azure 公開，Azure 私人和 Microsoft 對等。 我們有 65520 內部使用從 65515 保留 ASNs。 16，32 位元為支援數字。

沒有必要的資料傳輸對稱周圍。 往前然後返回路徑可能往返不同路由器配對。 相同路由必須通知從任一側邊，跨多個電路配對屬於您。 將指標不需要相同。

## <a name="route-aggregation-and-prefix-limits"></a>將彙總和前置字元的限制

我們支援最 4000 通知給我們透過 Azure 的私人對等的前置字元。 這可以增加最多 10000 前置詞，如果啟用 ExpressRoute 進階版附加元件。 我們接受最多 200 前置詞，每個 BGP 工作階段的 Azure 公用和 Microsoft 對等。 

如果前置詞數目超過限制，就會刪除 BGP 工作階段。 我們就會接受私人對等連結只上的預設路由。 提供者必須篩選出預設路由公用 azure 私用的 IP 位址 (RFC 1918) 和 Microsoft 對等的路徑。 

## <a name="transit-routing-and-cross-region-routing"></a>傳輸路由，以及跨地區路由

ExpressRoute 無法設定為傳輸路由器。 您必須使用您的傳輸路由服務的連線提供者。

## <a name="advertising-default-routes"></a>廣告預設路由

僅在 Azure 私人的對等工作階段允許預設路由。 在這種情況下，我們會傳送到您的網路所有來自相關聯的虛擬網路流量。 從 Azure 被封鎖的網際網路路徑會造成廣告預設路由至私人對等。 您必須依賴您公司的邊緣，即可將從，並裝載於 Azure 服務的網際網路流量路由傳送。 

 若要啟用連線至其他 Azure 服務和基礎結構服務，您必須確定下列項目位於位置︰

 - 路由流量至公用端點 azure 公用對等已啟用
 - 您使用使用者定義路由，讓每一個子網路需要網際網路連線的網際網路連線。
 
>[AZURE.NOTE] Windows 和其他啟動 VM 授權，就會中斷廣告預設路由。 依照指示進行[以下](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx)若要解決此問題。

## <a name="support-for-bgp-communities-preview"></a>支援 BGP 社群 （預覽版本）


本節提供 BGP 社群的 ExpressRoute 的使用方式的概觀。 Microsoft 會通知路線標記有適當的社群值中的公用及 Microsoft 對等路徑路由。 這樣的解釋和社群值的詳細資料如下所述。 Microsoft，不過，將不受影響到路由通知給 Microsoft 標記任何社群值。

如果您連線到任何位置等淉區域內的 ExpressRoute 透過 Microsoft，您必須存取所有的 Microsoft 雲端服務跨淉邊界中的所有區域。 

例如，如果您連線至 Microsoft 透過 ExpressRoute Amsterdam，您會有存取裝載於北美歐洲及西歐所有 Microsoft 雲端服務。 

請參閱淉區域相關聯的 Azure 區域，與相對應的 ExpressRoute 對等位置的詳細清單的[ExpressRoute 合作夥伴及對等的位置](expressroute-locations.md)頁面。

您可以購買一個以上的 ExpressRoute 電路，每個淉地區。 將多個連線，讓您有效的優點，高因為地理重複性的可用性。 在您有多個 ExpressRoute 電路的情況下，您會收到通知上公用的對等的 Microsoft 和 Microsoft 對等路徑的前置詞的同一組。 這表示您必須從您的網路到 Microsoft 多重路徑。 這可能會導致未最佳化路由的決策，可在您的網路。 如此一來，您可能會遭遇未最佳化的連線體驗，以不同的服務。 

Microsoft 會標記通知透過公用對等的前置詞和前置詞裝載於適當的 BGP 社群值，指出區域與對等的 Microsoft。 您可以依賴決定適當路由提供[最佳路由至客戶](expressroute-optimize-routing.md)的社群值。

| **淉區域** | **Microsoft Azure 區域** | **BGP 社群值** |
|---|---|---|
| **北美地區** |    |  |
|    | 設定適用於美國 | 12076:51004 |
|    | 設定適用於美國 2 | 12076:51005 |
|    | 西美制]。 | 12076:51006 |
|    | 西美國 2 | 12076:51026 |
|    | 西部美國中部 | 12076:51027 |
|    | 北美美國中部 | 12076:51007 |
|    | 美國中部南美洲 | 12076:51008 |
|    | 美國中部 | 12076:51009 |
|    | 加拿大中部 | 12076:51020 |
|    | 加拿大東亞 | 12076:51021 |
| **南美洲** |  |  |
|    | 巴西南部 | 12076:51014 |
| **在 Europe** |    |  |
|    | 北美歐洲 | 12076:51003 |
|    | 西歐 | 12076:51002 |
| **亞太地區** |    |   |
|    | 中式地址 | 12076:51010 |
|    | 東南亞 | 12076:51011 |
| **日本** |     |   |
|    | 日本東亞 | 12076:51012 |
|    | 日本西部 | 12076:51013 |
| **澳大利亞** |    |   | 
|    | 澳大利亞東亞 | 12076:51015 |
|    | 澳大利亞 Southeast | 12076:51016 |
| **印度** |    |   |
|    | 印度南部 | 12076:51019 |
|    | 印度西部 | 12076:51018 |
|    | 印度 Central | 12076:51017 |

將標記所有路由 Microsoft 的通知，以適當的社群的值。 

>[AZURE.IMPORTANT] 全域前置字元會以適當的社群值標記，並會通知，只有時啟用 ExpressRoute 進階版附加元件。


除了以上，Microsoft 也會標記前置詞根據其所屬的服務。 這只適用於 Microsoft 對等。 下表提供服務 BGP 社群值的對應。

| **服務** | **BGP 社群值** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **商務用 Skype** | 12076:5030 |
| **線上 CRM** | 12076:5040 |
| **其他 Office 365 服務** | 12076:5100 |

>[AZURE.NOTE] Microsoft 不會接受您設定通知給 Microsoft 路由任何 BGP 社群值。

## <a name="next-steps"></a>後續步驟

- 設定您的 ExpressRoute 連線。

    - [建立傳統的部署模型 ExpressRoute 電路](expressroute-howto-circuit-classic.md)或[建立及修改使用 Azure 資源管理員 ExpressRoute 電路](expressroute-howto-circuit-arm.md)
    - [設定路由傳統部署模型](expressroute-howto-routing-classic.md)] 或 [[資源管理員部署模型路由的設定](expressroute-howto-routing-arm.md)
    - [連結至 ExpressRoute 電路傳統 VNet](expressroute-howto-linkvnet-classic.md)或[連結至 ExpressRoute 電路資源管理員 VNet](expressroute-howto-linkvnet-arm.md)


