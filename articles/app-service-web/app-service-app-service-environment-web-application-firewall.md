<properties 
    pageTitle="設定 Web 應用程式防火牆 (WAF) 應用程式服務環境" 
    description="瞭解如何設定您的應用程式服務環境前面的 web 應用程式防火牆。" 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>設定 Web 應用程式防火牆 (WAF) 應用程式服務環境

## <a name="overview"></a>概觀 ##
Web 應用程式防火牆等[的 Azure Barracuda WAF](https://www.barracuda.com/programs/azure)上[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/)可協助保護您的 web 應用程式，以檢查連入封鎖 SQL 資料隱碼攻擊，跨網站指令碼、 惡意程式碼上傳與應用程式 DDoS 及其他攻擊的網路流量。 它也盡可能地壓縮的後端 web 伺服器回應的資料遺失防止 (DLP)。 結合與隔離和其他縮放提供的應用程式服務環境，如此主機商務重要的 web 應用程式需要承受惡意邀請和大量流量理想的環境。

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>設定 ##
我們將會設定此文件前多個載入我們應用程式服務環境平衡 Barracuda WAF 的執行個體，以便僅從 WAF 流量連絡應用程式服務環境並不會從 DMZ 存取。 我們也會有 Azure 流量管理員前面 Azure 資料中心以及區域負載平衡我們 Barracuda WAF 執行個體。 安裝程式的高層級圖表如下所示下方顯示的內容。

![架構][Architecture] 

> 附註︰ 使用的[應用程式服務環境支援 ILB](app-service-environment-with-internal-load-balancer.md)簡介中,，您可以設定無法從 DMZ，並只可用於私人網路 ASE。 

## <a name="configuring-your-app-service-environment"></a>設定您的應用程式服務環境 ##
若要設定應用程式服務環境請參閱[我們的說明文件](app-service-web-how-to-create-an-app-service-environment.md)主題。 建立應用程式服務環境之後，您可以將所有受前我們下一節中設定 WAF 此環境中建立[Web 應用程式](app-service-web-overview.md)、 [API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)及[行動應用程式](../app-service-mobile/app-service-mobile-value-prop.md)。

## <a name="configuring-your-barracuda-waf-cloud-service"></a>設定您的 Barracuda WAF 雲端服務 ##
Barracuda 有部署中 Azure 虛擬機器其 WAF[詳細的文章](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure)。 但我們想要重複，並產生單一的失敗，因為您想要遵循這些指示時，至少 2 WAF 執行個體 Vm 部署到相同的雲端服務。

### <a name="adding-endpoints-to-cloud-service"></a>加入至雲端服務的端點 ###
一旦您雲端服務中有 2 或更多 WAF VM 執行個體您可以使用[Azure 入口網站](https://portal.azure.com/)，若要新增 HTTP 和 HTTPS 的端點，下圖所示，可使用您的應用程式。

![設定結束點][ConfigureEndpoint]

如果您的應用程式使用其他端點，請務必將它們新增至這份清單。 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>設定 Barracuda WAF 透過其管理入口網站 ###
Barracuda WAF 使用 TCP 連接埠 8000 透過其管理入口網站的設定。 因為您將需要每個 VM 執行個體重複這些步驟 WAF Vm 的多個執行個體。 


> 注意︰ 當您完成 WAF 設定之後時，請移除為保護您 WAF 您所有的 WAF Vm 中 TCP/8000 端點。

新增管理端點，如下圖所示下方圖像中設定您的 Barracuda WAF。

![新增管理端點][AddManagementEndpoint]
 
您可以使用瀏覽器，瀏覽至您的雲端服務的管理端點。 如果您的雲端服務呼叫 test.cloudapp.net，您會在瀏覽至 http://test.cloudapp.net:8000 存取此端點。 您應該會看到登入頁面下方像您可以登入使用 WAF VM 設定階段中所指定的認證。

![管理登入頁面][ManagementLoginPage]

一次您登入您應該會看到在儀表板為下方圖像中會呈現基本統計 WAF 保護。

![管理儀表板][ManagementDashboard]

按一下 [服務] 索引標籤上，可讓您設定服務保護您 WAF。 如需詳細資訊，設定您的 Barracuda WAF 中，您可以查閱[其文件](https://techlib.barracuda.com/waf/getstarted1)。 在下方 Azure Web 應用程式的範例中已設定做 HTTP 和 HTTPS 的流量。

![管理新增的服務][ManagementAddServices]

> 附註︰ 根據您的應用程式的設定方式，哪些功能會使用您的應用程式服務環境中，您必須將轉寄流量 TCP 連接埠 80 和 443、 以外例如如果您有 IP SSL 設定 Web 應用程式。 在應用程式服務環境中使用的網路連接埠的清單，請參閱[控制連入流量文件的](app-service-app-service-environment-control-inbound-traffic.md)網路連接埠一節。

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>設定 Microsoft Azure 流量管理員 （選用） ##
如果您的應用程式有多個區域，然後您想要將載入 [餘額其背後[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)。 若要執行此作業中，您可以新增端點在[Azure 傳統入口網站](https://manage.azure.com)流量管理員設定檔中使用您 WAF 的雲端服務名稱下, 圖所示。 

![流量管理員結束點][TrafficManagerEndpoint]

如果您的應用程式需要驗證，請確定您有一些資源，不需要任何驗證，流量管理員才能偵測 （ping） 應用程式的可用性。 您可以設定 [設定] 區段下的 URL [Azure 傳統入口網站](https://manage.azure.com)上，如下所示。

![設定流量管理員][ConfigureTrafficManager]

若要從您 WAF 轉寄流量管理員 ping，您的應用程式，必須先設定網站翻譯您 Barracuda WAF 上若要將流量轉寄給您的應用程式在下面的範例所示。

![網站翻譯][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>保護應用程式服務環境使用網路安全性群組 (NSG) 的流量##
您可以依照[控制連入流量文件](app-service-app-service-environment-control-inbound-traffic.md)上您的應用程式服務環境限制流量來自 WAF 只使用雲端服務的 VIP 位址。 以下是 TCP 連接埠 80 執行這項工作的 Powershell 命令範例。


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

SourceAddressPrefix 換成您 WAF 雲端服務的虛擬 IP 位址 (VIP)。

> 注意︰ 您的雲端服務 VIP 會變更當您刪除並重新建立雲端服務。 請務必更新之後您的網路資源] 群組中的 IP 位址。 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
