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
| **位置**  | **服務提供者** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka 網路中，於 & T NetBond British Telecom、 柯特、 Equinix、 euNetworks，GÉANT InterCloud、 網際網路解決方案-雲端連線，Interxion，階層 3 通訊，橘色，Tata 通訊，TeleCity] 群組中，Telenor，Verizon |
| **亞特蘭大** | Equinix |
| **Chennai** | Tata 通訊 |
| **芝加哥** | 在與 T NetBond、 Comcast、 Equinix、 階層 3 通訊，Zayo] 群組 |
| **達拉斯** | 在與 T NetBond Cologix、 Equinix，層級 3 通訊，Megaport |
| **都柏林** | 柯特、 Telecity 群組 |
| **香港** | 全域 British Telecom （中國） 電信 Equinix、 Megaport、 橘色、 PCCW 全域限制，Tata 通訊 Verizon |
| **倫敦的公司** | 在與 T NetBond British Telecom、 柯特、 Equinix、 InterCloud、 網際網路解決方案-雲端連線，Interxion，Jisc，層級 3 通訊，MTN，NTT 通訊，橘色，Tata 通訊，Telecity] 群組中，Telenor，Verizon，Vodafone |
| **「 拉斯維加斯 」** | 階層 3 通訊 +、 Megaport
| **洛杉磯** | CoreSite、 Equinix，Megaport，NTT，Zayo 群組 |
| **在 Melbourne** | AARNet，Equinix，Megaport，NEXTDC，Telstra Corporation |
| **紐約** | Equinix、 Megaport，Zayo 群組 |
| **Newport(Wales) +** | 下一步產生資料 + |
| **蒙特婁** | Cologix + |
| **Mumbai** | Tata 通訊 |
| **大阪** | Equinix 網際網路計劃日本 Inc.-IIJ，NTT 通訊 Softbank |
| **輸入辦公室電話** | Interxion Equinix + |
| **聖保羅** | Equinix Telefonica |
| **西雅圖** | Equinix，層級 3 通訊 Megaport |
| **矽谷** | Aryaka 網路 AT & T NetBond、 British Telecom、 CenturyLink +、 Comcast、 Equinix、 階層 3 通訊，橘色，Tata 通訊，Verizon，Zayo 群組 |
| **新加坡** | Aryaka 網路 AT & T NetBond British Telecom、 Equinix、 InterCloud、 Megaport、 橘色、 SingTel、 Tata 通訊、 Verizon |
| **雪梨** | AARNet，於 & T NetBond，英國電信，Equinix，Megaport，NEXTDC，橘色，Telstra 公司、 Verizon |
| **東京** | Aryaka 網路，英國電信，柯特，Equinix，網際網路計劃日本 Inc.-IIJ，NTT 通訊，Softbank，Verizon |
| **多倫多** | Cologix、 Equinix，Zayo 群組 |
| **華盛頓州 DC** | Aryaka 網路 AT & T NetBond、 British Telecom、 Comcast、 Equinix、 InterCloud、 階層 3 通訊，Megaport，橘色，Tata 通訊，Verizon，Zayo 群組 |

 **+**表示即將推出

### <a name="national-cloud-environments"></a>美國國家雲端環境

#### <a name="us-government-cloud"></a>美國政府雲端

| **位置**  |**服務提供者** |
|---------------|--------------------|
| **芝加哥** | 在與 T NetBond，Equinix，層級 3 通訊，Verizon |
| **達拉斯** |  Equinix Verizon + |
| **紐約** | Equinix 階層 3 通訊 +、 Verizon |
| **華盛頓州 DC** | 在與 T NetBond，Equinix，層級 3 通訊，Verizon |

#### <a name="china"></a>China （中國)

| **位置**  | **服務提供者** |
|---------------|-----------------------|
| **北京** | 中國電信規劃 |
| **上海** |  中國電信規劃 |
若要深入瞭解，請參閱[中國的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>德國

| **位置**  | **服務提供者** |
|---------------|-----------------------|
| **柏林** | 柯特 +、 e 屋 |
| **Frankfurt** | 柯特，Equinix，Interxion |

## <a name="nonpartners"></a>透過服務提供者未列出的連線

如果您的連線提供者並未列在前一節，您仍然可以建立的連線。

- 請洽詢您的連線提供者，請參閱如果連線到任何以上表格中的往來。 您可以檢查下列收集 exchange 提供者所提供的服務的相關詳細資訊連結。 已連接至乙太網路交換數個連線提供者。

    - [Equinix 雲端 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- 有延伸您選擇的對等的位置的網路連線提供者。
    - 請確定，您的連線提供者延伸連線高度可用的方式，讓沒有單一點失敗。
- 訂購的 ExpressRoute 電路與 exchange，為您的連線提供者連線至 Microsoft。
    - [建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)設定連線的步驟。

|**位置**|**Exchange**|**連線提供者**|
|-------------|------------|-------------------------|
| **紐約** | Equinix | Lightower |
| **西雅圖** | Equinix | 阿拉斯加通訊 |
| **矽谷** | Equinix | XO 通訊 |
| **新加坡** | Equinix | 1CLOUDSTAR |
| **華盛頓州 DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合

啟用符合您需求的私人連線可以容易，根據您的網路的小數位數。 您可以使用任何系統整合來協助您登入至 ExpressRoute 如下表所示。

|**大陸**|**系統整合**|
|-------------|---------------------|
| **亞洲** | Avanade Inc.OneAs1a|
| **在 Europe** | Avanade Inc.Dotnet 解決方案|
| **我們** | Avanade Inc.、 Equinix 專業服務、 Perficient 專案領導|

## <a name="next-steps"></a>後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
- 請確定的符合所有的先決條件。 請參閱[ExpressRoute 的先決條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地圖"
