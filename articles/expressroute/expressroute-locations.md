<properties
   pageTitle="ExpressRoute 位置 |Microsoft Azure"
   description="本文將概述詳細的位置會提供服務的位置，以及如何連線至 Azure 的區域。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴及對等的位置

本文中的資料表提供 ExpressRoute 連線提供者，ExpressRoute 地理涵蓋範圍、 ExpressRoute 及 ExpressRoute 系統整合服務 (SIs) 支援的 Microsoft 雲端服務的資訊。

## <a name="partners"></a>ExpressRoute 連線提供者

ExpressRoute 支援過所有 Azure 區域和位置。 下列對應提供 Azure 地區 ExpressRoute 位置的清單。 ExpressRoute 位置，請參閱為 Microsoft 對等的多個服務提供者的位置。

![位置地圖][0]

如果您連線到淉區域內至少有一個 ExpressRoute 位置，您會透過淉區域內的所有區域有權存取 Azure 服務。 下表提供地圖的 Azure ExpressRoute 位置淉區域內的區域。

|**淉區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|
|**北美地區**|設定適用於美國西美國、 東亞美國 2、 美國中部、 南美洲美國中部、 北美國中部、 加拿大中央，加拿大東亞|亞特蘭大芝加哥、 某地、 「 拉斯維加斯 」、 洛杉磯、 紐約、 西雅圖、 DC 矽谷，州、 蒙特婁 +、 魁北克縣/市 +、 多倫多|
|**南美洲**|巴西南部|聖保羅|
|**在 Europe**|北美歐洲西歐，uk （英國） 西部南部 uk （英國）|Amsterdam 都柏林、 倫敦、 Newport(Wales) +、 巴黎|
|**亞洲**|中式地址東南亞|香港，新加坡|
|**日本**|日本西東部日本|大阪東京|
|**澳大利亞**|澳大利亞 Southeast，澳洲東亞|墨爾本雪梨|
|**印度**|印度 （印度） Central 南部印度西部|Chennai Mumbai|



下表提供資訊區域及淉邊界的國際雲朵。

|**淉區域**|**Azure 區域**|**ExpressRoute 位置**|
|---|---|---|---|
|**美國政府雲端**|美國 Gov 稍後，美國 Gov 維吉尼亞州|芝加哥，達拉斯，DC 紐約，州|
|**China （中國)**|北，china （中國） 東亞中國|北京上海|
|**德國**|德國中央，德國東亞|柏林 Frankfurt|


在標準的 ExpressRoute SKU 上不支援跨淉區域的連線。 您必須啟用 ExpressRoute 進階版附加元件，可支援全域的連線。 連線至雲端國際環境不受支援。 如果發生這種需要，您可以使用您的連線提供者。


## <a name="connectivity-provider-locations"></a>連線提供者的位置

> [AZURE.SELECTOR]
[提供者的位置](expressroute-locations.md#connectivity-provider-locations)
[位置的提供者](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>生產 Azure

| **服務提供者**  |**Microsoft Azure** | **Office 365 和 CRM Online** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | 支援 | 支援 | 墨爾本雪梨 |
| **[Aryaka 網路]( http://www.aryaka.com/)** | 支援 | 支援 | Amsterdam，矽谷、 新加坡東京，州 DC |
| **[在與 T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支援 | 支援 | Amsterdam 芝加哥、 達拉斯、 倫敦、 矽谷，新加坡、 DC 雪梨，州 |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 支援 | 支援 | Amsterdam 香港、 倫敦、 矽谷，新加坡、 雪梨、 DC 東京，州 |
|**CenturyLink** | 即將推出 | 即將推出| 矽谷 |
|**中國電信全域管理員** | 支援 | 不支援 | 香港 |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | 支援 | 即將推出 | 達拉斯蒙特婁 + 多倫多 |
| **[柯特]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  支援 | 支援 | Amsterdam，都柏林，倫敦東京 |
| **Comcast** | 支援 | 支援 | 芝加哥，DC 矽谷，州 |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | 支援 | 支援 | 洛杉磯 | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支援 | 支援 | Amsterdam 亞特蘭大、 芝加哥、 達拉斯、 香港、 倫敦、 洛杉磯、 Melbourne、 紐約、 大阪、 巴黎 +、 聖保羅、 西雅圖、 矽谷，新加坡、 雪梨、 東京、 DC 多倫多，州 |
| **euNetworks** |  支援 | 支援 | Amsterdam |
| **GÉANT** | 支援 | 支援 | Amsterdam |
| **[網際網路計劃日本 Inc.IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  支援 | 支援 | 大阪東京 |
| **[InterCloud]( https://www.intercloud.com/)** | 支援 | 支援 | Amsterdam，倫敦的公司，新加坡，州 DC |
| **網際網路解決方案-雲端連線** | 支援 | 支援 | Amsterdam 倫敦 |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | 支援 | 支援 | Amsterdam，倫敦巴黎 |
| **Jisc** | 支援 | 支援 | 倫敦的公司 | 
| **[階層 3 通訊]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支援 | 支援 | Amsterdam 芝加哥、 達拉斯、 「 拉斯維加斯 」 +、 倫敦、 西雅圖、 DC 矽谷，州 |
| **Megaport** | 支援 | 支援 | 達拉斯，香港、 Las （拉斯維加斯） 以外，或洛杉磯 Melbourne、 紐約西雅圖、 新加坡雪梨，州 DC |
| **MTN** | 支援 | 支援 | 倫敦的公司 |
| **下一步產生的資料** | 即將推出 | 即將推出 | Newport(Wales) + |
| **NEXTDC** | 支援 | 支援 | 墨爾本雪梨 |
| **NTT 通訊** | 支援 | 支援 | （倫敦） 以外，或洛杉磯大阪東京 |
| **[橘色]( http://www.orange-business.com/en/products/business-vpn-galerie)** | 支援 | 支援 | Amsterdam，香港特別行政區倫敦矽谷、 新加坡雪梨，州 DC |
| **全域 PCCW 有限** | 支援 | 支援 | 香港 |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  支援 | 支援 | 新加坡 |
| **Softbank** | 支援 | 支援 | 大阪東京 | 
| **[Tata 通訊](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 支援 | 支援 | Amsterdam，Chennai，香港特別行政區，倫敦 Mumbai，矽谷，新加坡，州 DC |
| **[TeleCity 群組]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 支援 | 支援 | Amsterdam，都柏林，倫敦 |
| **Telefonica** | 支援 | 支援 | 聖保羅 |
| **Telenor** | 支援 | 支援 | Amsterdam 倫敦 |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 支援 | 支援 | 墨爾本雪梨 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | 支援 | 支援 | Amsterdam 香港、 倫敦、 矽谷，新加坡、 雪梨、 DC 東京，州 |
| **Vodafone** | 支援 | 不支援 | 倫敦的公司 | 
| **[Zayo 群組]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | 支援 | 支援 | 芝加哥，洛杉磯、 紐約矽谷，多倫多，州 DC |

 **+**表示即將推出

### <a name="national-cloud-environments"></a>美國國家雲端環境

#### <a name="us-government-cloud"></a>美國政府雲端

| **服務提供者**  |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[在與 T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支援 | 支援 | DC 芝加哥州 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支援 | 支援 | 芝加哥，達拉斯，DC 紐約，州 |
| **[階層 3 通訊]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支援 | 支援 | 芝加哥，紐約 +，州 DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 支援 | 支援 | 芝加哥，達拉斯 + DC 紐約，州 |

#### <a name="china"></a>China （中國)

| **服務提供者**  |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **中國電信規劃** | 支援 | 不支援 | 北京上海|
若要深入瞭解，請參閱[中國的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)。

#### <a name="germany"></a>德國

| **服務提供者**  |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[柯特]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 支援 | 不支援 | 柏林 +、 Frankfurt|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支援 | 不支援 | Frankfurt|
| **e 屋** | 支援 | 不支援 | 柏林|
| **Interxion** | 支援 | 不支援 | Frankfurt|

## <a name="nonpartners"></a>透過服務提供者未列出的連線

如果您的連線提供者並未列在前一節，您仍然可以建立的連線。

- 請洽詢您的連線提供者，請參閱如果連線到任何以上表格中的往來。 您可以檢查下列收集 exchange 提供者所提供的服務的相關詳細資訊連結。 已連接至乙太網路交換數個連線提供者。

    - [Equinix 雲端 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- 有延伸您選擇的對等的位置的網路連線提供者。
    - 請確定，您的連線提供者延伸連線高度可用的方式，讓沒有單一點失敗。
- 訂購的 ExpressRoute 電路與 exchange，為您的連線提供者連線至 Microsoft。
    - [建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)設定連線的步驟。

|**連線提供者**|**Exchange**|**位置**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|新加坡|
|**阿拉斯加通訊**|Equinix|西雅圖|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|DC 紐約州|
|**[XO 通訊](http://www.xo.com/)**|Equinix|矽谷|


## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合

啟用符合您需求的私人連線可以容易，根據您的網路的小數位數。 您可以使用任何系統整合來協助您登入至 ExpressRoute 如下表所示。

|**系統整合者**|**洲**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| 亞洲、 歐洲、 美制]。 |
|**[Dotnet 解決方案](http://www.dotnetsolutions.co.uk/)**| 在 Europe |
|**[Equinix 專業服務](http://www.equinix.com/services/consulting/)**|我們|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | 亞洲 |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 我們 |
|**[專案領導](http://www.projectleadership.net/azure)** | 我們 |

## <a name="next-steps"></a>後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
- 請確定的符合所有的先決條件。 請參閱[ExpressRoute 的先決條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地圖"
