<properties
   pageTitle="NAT 需求 ExpressRoute 電路 |Microsoft Azure"
   description="本頁面提供設定和管理 NAT ExpressRoute 電路詳細的需求。"
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

# <a name="expressroute-nat-requirements"></a>ExpressRoute NAT 需求

若要連線至 Microsoft 雲端服務使用 ExpressRoute，您需要設定及管理 Nat。 某些連線提供者提供設定及管理 NAT，為受管理的服務。 請洽詢您的連線提供者，請參閱是否提供這類服務。 如果沒有出現，您必須遵守如下所述的需求。 

檢閱[ExpressRoute 電路與路由的網域](expressroute-circuit-peerings.md)頁面各種路由網域的概觀。 若要符合 Azure 公用和 Microsoft 對等的公用 IP 位址需求，我們建議您將 NAT 設定您的網路和 Microsoft 之間。 本節提供您要設定的 NAT 基礎結構的詳細的描述。

## <a name="nat-requirements-for-azure-public-peering"></a>Azure 公用對等的 NAT 需求

Azure 公用等路徑可讓您連線至自己的公用 IP 位址透過裝載於 Azure 中的所有服務。 包括[ExpessRoute 常見問題集](expressroute-faqs.md)中所列的服務以及由 Microsoft Azure 上 Isv 裝載任何服務。 將 Microsoft 網路連線至 Microsoft Azure 服務公用對等一律啟動時從您的網路。 Microsoft Azure 上公用對等流量在進入 Microsoft 網路之前，必須是 SNATed 到有效公用 IPv4 位址。 下圖中提供的 NAT 無法設定的方式最高層級圖片符合上述的需求。

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP 資料庫並傳送通知

您必須確定流量輸入有效的公用 IPv4 位址 Azure 公用對等的路徑。 Microsoft 必須驗證地區路由網際網路登錄 (RIR) 或網際網路路由登錄 (IRR) 的 NAT IPv4 位址集區的擁有權。 核取將會執行根據正在使用 peered 另存新檔號碼]，然後 NAT 所用的 IP 位址 請參閱路由登錄的詳細資訊的[ExpressRoute 路由需求](expressroute-routing.md)頁面。
 
沒有任何限制通知透過此對等的 NAT IP 前置字元的長度。 您必須監控 NAT 區，並確保您不渴望的 NAT 工作階段。

>[AZURE.IMPORTANT] 通知給 Microsoft 的 NAT IP 集區不必須通知至網際網路。 這會中斷連線至其他 Microsoft 服務。

## <a name="nat-requirements-for-microsoft-peering"></a>Microsoft 對等的 NAT 需求

Microsoft 對等路徑可讓您連線至 Microsoft 雲端服務不支援透過 Azure 公用對等的路徑。 服務清單，包括 Office 365 服務，例如 Exchange Online、 SharePoint Online、 商務用 Skype，並 CRM Online。 Microsoft 需要 Microsoft 對等支援雙向連線。 Microsoft 雲端服務流量必須先 SNATed 有效公用 IPv4 位址，他們會進入 Microsoft 網路。 您的網路 Microsoft 雲端服務的流量必須先 SNATed，他們會進入您的網路。 下圖會提供高層級的如何 NAT 應設定 Microsoft 對等的圖片。
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png) 


#### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>針對您傳送至 Microsoft 的網路流量

- 您必須確定流量輸入有效的公用 IPv4 位址 Microsoft 對等的路徑。 Microsoft 必須驗證 IPv4 NAT 位址區地區路由網際網路登錄機碼 (RIR) 或網際網路路由登錄 (IRR) 的擁有者。 核取將會執行根據正在使用 peered 另存新檔數] 及 [NAT 所用的 IP 位址 請參閱[ExpressRoute 路由需求](expressroute-routing.md)的頁面路由登錄的詳細資訊。

- Azure 公用等設定和其他 ExpressRoute 電路所用的 IP 位址必須不會通知給 Microsoft 透過 BGP 工作階段。 沒有任何限制通知透過此對等的 NAT IP 前置字元的長度。

    >[AZURE.IMPORTANT] 通知給 Microsoft NAT IP 集區不必須通知至網際網路。 這會中斷連線至其他 Microsoft 服務。

#### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>來自 Microsoft 指向您的網路流量

- 某些情況下需要 Microsoft 進行服務端點裝載在您的網路連線能力。 常見的案例的範例會在 ADFS 伺服器裝載於您從 Office 365 的網路的連線。 在這種情況下，您必須將 Microsoft 對等適當的前置詞遺漏從您的網路。 

- 您必須 SNAT 流量至您的網路中的 IP 位址 microsoft。 

## <a name="next-steps"></a>後續步驟

- 請參閱[路由](expressroute-routing.md)及[QoS](expressroute-qos.md)需求。
- 工作流程的詳細資訊，請參閱[ExpressRoute 電路佈建工作流程與電路狀態](expressroute-workflows.md)。
- 設定 ExpressRoute 連線。

    - [建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)
    - [設定路由](expressroute-howto-routing-classic.md)
    - [連結 VNet ExpressRoute 電路](expressroute-howto-linkvnet-classic.md)

