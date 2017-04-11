<properties 
    pageTitle="應用程式服務環境簡介" 
    description="深入了解提供安全、 VNet 加入專用的刻度] 單位執行您的應用程式的所有應用程式服務環境功能。" 
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

# <a name="introduction-to-app-service-environment"></a>應用程式服務環境簡介

## <a name="overview"></a>概觀 ##
應用程式服務環境是[進階版][PremiumTier]服務方案選項的 Azure 應用程式服務，提供以安全地在高小數位數，包括[Web 應用程式]中執行 [Azure 應用程式服務應用程式的完整隔離和專用環境[WebApps]， [Mobile 應用程式][MobileApps]，及[API 應用程式][APIApps]。  

應用程式服務環境非常適合應用程式工作負載的要求︰

- 更高的小數位數
- 隔離及安全的網路存取

客戶可以建立多個應用程式服務環境中單一 Azure 區域，以及跨多個 Azure 的區域。  如此可讓應用程式服務環境理想的水平縮放比例高每秒要求數量負載產生的狀態小於應用程式層。

應用程式服務環境隔離執行只有單一客戶的應用程式，而且可以隨時在部署至虛擬網路。  客戶有兩個傳入和傳出的應用程式的網路流量，細地控制並應用程式可以建立高速安全連線至內部部署企業資源的虛擬網路上。

所有的文件及如何-的相關應用程式服務環境可在[應用程式服務環境的讀我檔案](../app-service/app-service-app-service-environments-readme.md)。

應用程式服務環境如何啟用高的小數位數和安全的概觀網路存取，請參閱[AzureCon 深度剖析][AzureConDeepDive]應用程式服務環境 ！

水平縮放比例使用深度探討的多個應用程式服務環境，請參閱如何設定[所需的地理分散式應用程式]的文章[GeodistributedAppFootprint]。

若要查看如何設定顯示 AzureCon 深度剖析的安全性架構，請參閱在實作[層次安全性架構](app-service-app-service-environment-layered-security.md)與應用程式服務環境。

在 [應用程式服務環境上執行應用程式可以有閘上游的裝置，例如 web 應用程式防火牆 (WAF) 其存取權。  [設定應用程式服務環境 WAF](app-service-app-service-environment-web-application-firewall.md)文章說明這種情況。 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>專用的計算資源 ##
所有的應用程式服務環境中的計算資源的專用獨佔單一的訂閱，與應用程式服務環境可以設定的專用最多為 （50） 計算資源的單一應用程式。

應用程式服務環境被由前端計算資源資料庫，以及一到三個工作者計算資源資料庫。 

前端集區包含計算資源負責 SSL 終止為也自動負載平衡的應用程式服務環境的應用程式要求。 

每個工作者集區包含計算資源配置給[應用程式服務方案][AppServicePlan]，其中包含一或多個 Azure 應用程式服務應用程式。  在應用程式服務環境中可以有三個不同的背景工作資料庫，因為您可以選擇不同的計算資源的每個工作者集區一。  

例如，這可讓您建立一個背景工作集區應用程式服務方案適用於開發或測試應用程式的較少的強大計算資源。  第二個 （或甚至第三個） 工作者資料庫無法使用適用於應用程式服務方案執行生產應用程式的功能更強大計算資源。

如需可用的前端與工作者集區的計算資源的數量的詳細資訊，請參閱[如何設定應用程式服務環境][HowToConfigureanAppServiceEnvironment]。  

如需詳細資訊，在 [可用計算支援的應用程式服務環境中的資源大小、[應用程式服務價格]，請參閱[AppServicePricing]頁面，並檢閱價格層進階版中的應用程式服務環境可用的選項。

## <a name="virtual-network-support"></a>虛擬網路支援 ##
您可以建立應用程式服務環境中**任一**Azure 資源管理員虛擬網路，**或**在傳統的部署模型虛擬網路 ([虛擬網路上的其他資訊][MoreInfoOnVirtualNetworks])。  因為應用程式服務環境一律存在於虛擬網路，且更精確地內虛擬網路的子網路，您可以利用虛擬網路，以控制兩個傳入和傳出的網路通訊的安全性功能。  

應用程式服務環境可以有網際網路圖示的公用 IP 位址或內部對只 Azure 內部負載平衡器 (ILB) 地址。

您可以使用[網路安全性群組][NetworkSecurityGroups]限制到應用程式服務環境的所在位置的子網路輸入的網路通訊。  這個選項可讓您執行後上游裝置和 web 應用程式的防火牆和網路 SaaS 提供者等服務應用程式。

應用程式也會經常需要存取公司的資源，例如內部資料庫及 web 服務。  常見的方法是可讓這些端點只在 Azure 虛擬網路內部網路流量。  當應用程式服務環境加入至相同的虛擬網路內部的服務時，請執行環境中的應用程式可以存取，包括可透過[網站間]的結束點[SiteToSite]和[Azure ExpressRoute] [ExpressRoute]連線。

如需應用程式服務環境虛擬網路與內部部署網路的運作方式的詳細資訊，請參閱下列文章[網路]架構[NetworkArchitectureOverview]，[控制連入流量][ControllingInboundTraffic]，並[安全地連線到 Backends][SecurelyConnectingToBackends]。 

## <a name="getting-started"></a>快速入門

若要開始使用應用程式服務環境，請參閱[如何以建立應用程式服務環境][HowToCreateAnAppServiceEnvironment]

所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

應用程式服務環境網路架構的概觀，請參閱[網路架構概觀][NetworkArchitectureOverview]文章。

如 ExpressRoute，搭配使用的應用程式服務環境的詳細資訊，請參閱下列文章[快速傳送]及應用程式服務環境[NetworkConfigDetailsForExpressRoute]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
