<properties
   pageTitle="ExpressRoute 採用的必要條件 |Microsoft Azure"
   description="本頁面提供之前，您可以排序 Azure ExpressRoute 電路符合需求的清單。"
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


# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute 先決條件與檢查清單  

若要連線至 Microsoft 雲端服務使用 ExpressRoute，必須確認已符合以下各節中所列的下列需求。

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure 帳戶

- 有效且作用中的 Microsoft Azure 帳戶。 這被需要設定 ExpressRoute 電路。 ExpressRoute 電路是 Azure 訂閱中的資源。 Azure 的訂閱是需求，即使連線至非 Azure Microsoft 雲端服務，例如 Office 365 服務與線上 CRM 限制。
- 使用 Office 365 訂閱 （如果使用 Office 365 服務）。 請參閱本文的詳細資訊的[Office 365 特定需求](#office-365-specific-requirements)節。

## <a name="connectivity-provider"></a>連線提供者
- 您可以使用連線至 Microsoft 雲端[ExpressRoute 連線合作夥伴](expressroute-locations.md#partners)。 您可以設定您的內部部署網路與 Microsoft 之間的連線以[三種方式](expressroute-introduction.md#howtoconnect)。 
- 如果您的提供者不 ExpressRoute 連線合作夥伴，您還是可以連接至 Microsoft 雲端透過[雲端 exchange 提供者](expressroute-locations.md#nonpartners)。

## <a name="network-requirements"></a>網路需求
- **重複的連線**︰ 沒有重複性的需求在實體您和您的提供者之間的連線。 Microsoft 會需要設定之間 Microsoft 的路由器與對等的路由器，即使您有只[一個的實體連線至雲端 exchange](expressroute-faqs.md#onep2plink)的多餘 BGP 工作階段。 
- **路由**︰ 根據您如何連線至 Microsoft 雲端，您或您的提供者需要設定及管理[路由](expressroute-circuit-peerings.md)網域 BGP 工作階段。 部分乙太網路連線提供者或雲端 exchange 提供者可能會提供 BGP 管理值新增服務。
- **NAT**: Microsoft 只接受透過 Microsoft 對等的公用 IP 位址。 如果您使用的內部部署網路私用的 IP 位址，您或您要翻譯的公用 IP 私人 IP 位址的提供者需要地址[使用 NAT](expressroute-nat.md)。
- **QoS**: Skype 的商務具有各項服務 （例如語音、 視訊、 文字），需要區分 QoS 處理方式。 您和您的提供者應該追蹤[需求品質](expressroute-qos.md)。
- **網路安全性**︰ 連線至 Microsoft 雲端透過 ExpressRoute 時，您應該考慮[網路安全性](../best-practices-network-security.md)。
 
## <a name="office-365"></a>Office 365

如果您打算啟用 ExpressRoute 上的 Office 365，請檢閱以下的文件的 Office 365 需求的詳細資訊。


- [Office 365 的 ExpressRoute 的概觀](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Office 365 的 ExpressRoute 路由](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Office 365 Url 與 IP 位址範圍](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [網路規劃與效能調整 Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [網路頻寬計算器和工具](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [整合 office 365 與內部部署環境](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>線上 CRM 
如果您打算啟用 CRM Online ExpressRoute 上，請檢閱以下的文件，如需有關 CRM Online

- [CRM Online Url](https://support.microsoft.com/kb/2655102)與[IP 位址範圍](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>後續步驟

- 如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 常見問題集](expressroute-faqs.md)。
- 尋找 ExpressRoute 連線提供者。 請參閱[ExpressRoute 合作夥伴與對等的位置](expressroute-locations.md)。
- 請參閱[路由](expressroute-routing.md)、 [NAT](expressroute-nat.md)及[QoS](expressroute-qos.md)的需求。
- 設定 ExpressRoute 連線。
    - [建立 ExpressRoute 電路](expressroute-howto-circuit-classic.md)
    - [設定路由](expressroute-howto-routing-classic.md)
    - [連結 VNet ExpressRoute 電路](expressroute-howto-linkvnet-classic.md)

