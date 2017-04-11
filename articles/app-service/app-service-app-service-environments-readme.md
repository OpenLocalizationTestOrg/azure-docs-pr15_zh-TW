<properties 
    pageTitle="應用程式服務環境 |Microsoft Azure" 
    description="什麼是 Azure 應用程式服務環境？ 應用程式服務環境簡介。" 
    keywords="azure 應用程式服務環境虛擬網路安全的網路"
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>

# <a name="app-service-environment-documentation"></a>應用程式服務環境文件

應用程式服務環境是[進階版][PremiumTier]服務方案選項的 Azure 應用程式服務，提供以安全地在高小數位數，包括[Web 應用程式]中執行 [Azure 應用程式服務應用程式的完整隔離和專用環境[WebApps]， [Mobile 應用程式][MobileApps]，及[API 應用程式][APIApps]。  

應用程式服務環境非常適合應用程式工作負載的要求︰

- 更高的小數位數
- 隔離及安全的網路存取

客戶可以建立多個應用程式服務環境中單一 Azure 區域，以及跨多個 Azure 區域。  如此可讓應用程式服務環境適合用於水平縮放比例高每秒要求數量負載產生的狀態小於應用程式層。

應用程式服務環境隔離執行只有單一客戶的應用程式，而且可以隨時在部署至虛擬網路。  客戶必須微調控制使用[網路安全性群組]兩個傳入和傳出應用程式的網路流量[NetworkSecurityGroups]。  應用程式也可以建立高速安全連線至內部部署企業資源的虛擬網路上。

應用程式常見問題需要存取公司的資源，例如內部資料庫及 web 服務。  應用程式服務環境上執行應用程式可存取資源可透過[網站-] [ SiteToSite] VPN 和[Azure ExpressRoute] [ExpressRoute]連線。

* [什麼是應用程式服務環境？](../app-service-web/app-service-app-service-environment-intro.md)
* [建立應用程式服務環境](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [在 [應用程式服務環境中建立應用程式](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [建立及使用應用程式服務環境內部負載平衡器](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [設定應用程式服務環境](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [在應用程式服務的環境中的縮放比例應用程式](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [網路安全性和架構](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>如何的

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>視訊
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
