<properties
    pageTitle="Azure 服務應用程式與虛擬機器、 服務結構雲端服務比較 |Microsoft Azure"
    description="瞭解如何選擇 Azure 應用程式服務、 虛擬機器、 服務結構和雲端服務裝載 web 應用程式。"
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Azure 服務應用程式與虛擬機器、 服務結構雲端服務的比較

## <a name="overview"></a>概觀

Azure 提供數種方式可主機的網站︰ [Azure 應用程式服務][]與[虛擬機器][]、[服務布料的轉印圖樣][]、 [Cloud Services][]。 本文可協助您瞭解選項及適合 web 應用程式。

Azure 應用程式服務是大部分的 web 應用程式的最佳選擇。 部署及管理整合平台網站處理高負載時，可以快速縮放與內建的負載平衡與流量管理員提供高可用性。 移動現有的網站 Azure 應用程式服務輕鬆地使用[online 移轉工具](https://www.migratetoazure.net/)，使用 [Web 應用程式] 庫中，開啟來源應用程式或建立新的網站使用架構與您所選擇的工具。 [WebJobs][]功能可讓您更輕鬆地將背景工作處理至您的應用程式服務 web 應用程式。

如果您在建立新的應用程式，或重新撰寫現有的應用程式使用 microservice 架構，服務布料的轉印圖樣是很不錯的選擇。 應用程式，在共用資料庫的電腦執行，可以開始，然後放大數百或數以千計的電腦需要大量縮放。 設定狀態服務輕鬆一致且確實儲存應用程式狀態，服務布料的轉印圖樣自動您及管理服務分割、 縮放比例和可用性。  服務布料的轉印圖樣也支援開啟 Web 介面 WebAPI.NET (OWIN) 和 ASP.NET 核心。  相較於應用程式服務，服務布料的轉印圖樣也會提供更多的控制或直接存取權的基礎結構。 您可以將您的伺服器的遠端或設定伺服器啟動工作。 雲端服務類似服務結構中的控制項與易於使用，但現在舊版的服務，而服務布料的轉印圖樣建議，為新的開發。

如果您需要執行服務應用程式或服務布料的轉印圖樣大幅修改現有的應用程式，您可以選擇以簡化移轉至雲端的虛擬機器。 不過，正確設定、 保護及維護 Vm 需要更多的時間和 IT 專業知識與 Azure 應用程式服務和服務布料的轉印圖樣。 如果您考慮 Azure 虛擬機器，請確定您考慮進行中進行的維修作業所需的努力修補、 更新及管理 VM 環境。 Azure 虛擬機器是基礎結構-為-的-服務 (IaaS)，當應用程式服務與服務布料的轉印圖樣的平台為-的-服務 (Paas)。 

## <a name="features"></a>功能比較

下表將比較應用程式服務、 雲端服務，虛擬機器和服務布料的轉印圖樣的功能，以協助您進行的最佳選擇。 目前 SLA 每個選項的詳細資訊，請參閱[Azure 服務等級協定](/support/legal/sla/)。

功能|應用程式服務 （web 應用程式）|雲端服務 （web 角色）|虛擬機器|服務布料的轉印圖樣|備忘稿
---|---|---|---|---|---
附近立即部署|X|||X|將應用程式或應用程式更新部署至雲端服務，或建立 VM，需要幾分鐘的時間至少;部署到 web 應用程式的應用程式所需的時間 （秒）。
不按比例縮放較大的電腦，而不重新部署的機會|X|||X|
網頁伺服器執行個體共用內容並設定，這表示您沒有重新部署，或重新設定當您不按比例縮放。|X|||X|
多個部署環境 （生產和臨時）|X|X||X|服務布料的轉印圖樣可讓您有多個環境的應用程式或部署不同版本的您應用程式-並存。
自動 OS 更新管理|X|X|||自動 OS 更新的未來服務布料的轉印圖樣發行計劃。
順暢的平台切換 （32 位元與 64 位元之間輕鬆地移動）|X|X|||
部署給，FTP 的程式碼|X||X||
使用 Web 部署部署程式碼|X||X||雲端服務支援使用 Web 部署部署至個別的角色執行個體的更新。 不過，您不能使用的角色，初始部署，如果您是使用 Web 部署更新必須分別部署角色的每個執行個體。 必須要有多個執行個體，才有資格的雲端服務 SLA 生產環境。
WebMatrix 支援|X||X||
存取服務等服務匯流排、 儲存、 SQL 資料庫|X|X|X|X|
Host （主機） 網頁或 web 服務層的多層架構|X|X|X|X|
Host （主機） 中間層的多層架構|X|X|X|X|應用程式服務 web 應用程式可以輕鬆地將主控 REST API 的中間層] 與 [ [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226)功能可以主控背景處理工作。 您可以執行 WebJobs 達到層獨立延展性專用網站中。 [預覽] [API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)功能提供更多的功能，裝載其餘服務。
整合式的 MySQL 為-的-服務支援|X|X|X||雲端服務可以整合 MySQL 為-的-服務，透過 ClearDB 的內容，但未歸類為 Azure 入口網站工作流程的一部分。
ASP.NET 傳統 ASP、 Node.js、 PHP Python 支援|X|X|X|X|服務布料的轉印圖樣支援前端網頁建立使用[ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ，或者您可以為[來賓可執行](../service-fabric/service-fabric-deploy-existing-app.md)部署任何類型的應用程式 （Node.js、 Java 等）。
延伸為多個執行個體，而不重新部署的機會|X|X|X|X|虛擬機器可以延伸為多個執行個體，但必須處理此延展撰寫執行這些服務。 您必須設定負載平衡器路由要求跨電腦，並建立相關性群組，以防止因為進行的維修作業或硬體失敗的所有執行個體同時重新啟動。
支援 SSL|X|X|X|X|應用程式服務 web 應用程式，才支援的自訂網域名稱的 SSL 基本版和標準模式。 使用 SSL web 應用程式的相關資訊，請參閱[設定 Azure 網站的 SSL 憑證](../app-service-web/web-sites-configure-ssl-certificate.md)。
Visual Studio 整合|X|X|X|X|
遠端偵錯|X|X|X||
部署 TFS 的程式碼|X|X|X|X|
網路隔離與[Azure 虛擬網路](/services/virtual-network/)|X|X|X|X|另請參閱[Azure 網站虛擬網路的整合](/blog/2014/09/15/azure-websites-virtual-network-integration/)
[Azure 流量管理員](/services/traffic-manager/)的支援|X|X|X|X|
整合式的端點監控|X|X|X||
遠端桌面存取伺服器||X|X|X|
安裝任何自訂 MSI||X|X|X|服務布料的轉印圖樣可讓您裝載任何可執行檔[可執行來賓](../service-fabric/service-fabric-deploy-existing-app.md)身分或您可以在 Vm 上安裝任何應用程式。
若要定義/執行啟動工作的功能||X|X|X|
可以聆聽 ETW 事件||X|X|X|

## <a name="scenarios"></a>案例與建議

以下是一些常見的應用程式案例的哪些 Azure 存虛擬主機選項可能是最適合的建議。

- [我需要使用背景處理和資料庫後端執行商務應用程式與整合部署資產 web 前端。](#onprem)
- [我需要可靠的方式來裝載我佳的公司網站，並提供全域達到。](#corp)
- [我有 IIS6 應用程式在 Windows Server 2003 上執行。](#iis6)
- [我是小型企業主，我需要經濟的方式來裝載我的網站，但未來的成長的注意事項。](#smallbusiness)
- [我可以在 web 或圖形的設計工具，但想設計和建立網站的 [我的客戶。](#designer)
- [我正在移轉使用前端網頁我多層應用程式至雲端。](#multitier)
- [我的應用程式而定高度自訂的 Windows，或 Linux 環境而想要將其移至雲端。](#custom)
- [我的網站使用開啟來源軟體，而且希望將其主控 Azure 中。](#oss)
- [我已連線至公司網路需要的線條的商務應用程式。](#lob)
- [我要主控的 REST API 或行動用戶端的 web 服務。](#mobile)


### <a id="onprem"></a>我需要使用背景處理和資料庫後端執行商務應用程式與整合部署資產 web 前端。

Azure 應用程式服務是複雜的商務應用程式的最佳解決方案。 可讓您開發不按比例縮放的負載平衡平台上的自動受到 Active Directory 與連線到您的內部部署資源的應用程式。 它，讓管理透過世界級入口網站和 Api，輕鬆地那些應用程式，並可讓您獲得深入了解如何客戶使用其應用程式充分工具。 [Webjobs][]功能可讓您執行背景處理程序，並屬於 web 層，同時混合式連線與 VNET 功能的工作會使其易於連線到內部部署資源。 Azure 應用程式服務會提供三個 9 SLA web 應用程式，並可讓您︰

* 自我修復、 自動修正雲端平台可靠執行您的應用程式。
* 不按比例縮放自動全域網路的資料中心。
* 備份及還原損毀修復。
* 是 ISO SOC2，與 PCI 相容。
* Active Directory 與整合

### <a id="corp"></a>我需要可靠的方式來裝載我佳的公司網站，並提供全域達到。

Azure 應用程式服務是裝載公司網站的最佳解決方案。 讓縮放快速且輕鬆地以符合需求的資料中心全域網路上的 web 應用程式。 提供本機範圍、 容錯和智慧型流量管理。 所有在平台提供世界級管理工具，可讓您掌握網站的健全狀況和網站流量快速且輕鬆地。 Azure 應用程式服務會提供三個 9 SLA web 應用程式，並可讓您︰

* 自我修復、 自動修正雲端平台可靠執行您的網站。
* 不按比例縮放自動全域網路的資料中心。
* 備份及還原損毀修復。
* 記錄，並管理流量使用整合式的工具。
* 是 ISO SOC2，與 PCI 相容。
* Active Directory 與整合

### <a id="iis6"></a>我有 IIS6 應用程式在 Windows Server 2003 上執行。

Azure 應用程式服務讓您更容易避免移轉較舊的 IIS6 應用程式相關聯的基礎結構成本。 Microsoft 建立[容易使用的移轉工具和詳細的移轉指南](https://www.movemetowebsites.net/)，可讓您檢查相容性，並識別需要進行任何變更。 整合 Visual Studio、 TFS，與一般 CMS 工具讓您更容易部署直接 IIS6 雲端應用程式。 部署，Azur 入口網站提供的強大的管理工具，可讓您管理成本和最符合需求視縮小。 使用移轉工具，您可以︰

* 快速且輕鬆地將移轉舊版 Windows Server 2003 web 應用程式至雲端。
* 選擇此選項，讓您附加的 SQL 資料庫內部部署建立混合式部署的應用程式。
* 自動移動您的舊版應用程式以及 SQL 資料庫。

### <a id="smallbusiness"></a>我是小型企業主，我需要經濟的方式來裝載我的網站，但未來的成長的注意事項。

Azure 應用程式服務是這個案例中的最佳解決方案，因為您可以開始使用免費取得，並在需要時，然後新增更多的功能。 每一個免費的 web 應用程式隨附 Azure 所提供的網域 (*your_company*。 azurewebsites.net)，並在平台包含整合的部署及管理工具，以及輕鬆，即可開始使用應用程式組件庫。 有許多其他服務和允許網站，以增加的使用者要求發展的縮放比例選項。 Azure 應用程式服務時，您可以︰

- 開始與免費層，然後視需要調整。
- 若要快速設定常用的 web 應用程式，例如 WordPress 使用應用程式庫。
- 視需要新增其他 Azure 服務和功能應用程式。
- 保護您的 web 應用程式，使用 HTTPS。

### <a id="designer"></a>我可以在 web 或圖形的設計工具，但想設計和建立網站的 [我的客戶

為 web 開發人員和設計工具，Azure 應用程式服務整合輕鬆地使用各種不同的架構與工具、 部署支援給和 FTP，並提供緊密整合工具與 Visual Studio 和 SQL 資料庫等服務。 使用應用程式服務，您可以︰

- 用於[自動化的工作]的命令列工具[scripting]。
- 使用 [熱門的語言，例如[.Net][dotnet]， [PHP][]、 [Node.js][nodejs]，及[Python][]。
- 選取三個不同縮放比例層級的調整，以更高的容量。
- 與其他 Azure 服務，例如[SQL 資料庫]整合[sqldatabase]，[服務匯流排][servicebus]和[儲存空間][]或從[Azure 市集]合作夥伴產品或服務[azurestore]，例如 [MySQL 和 MongoDB。
- 整合工具，例如 Visual Studio、 給、 WebMatrix、 WebDeploy、 TFS 及 FTP。

### <a id="multitier"></a>我正在移轉使用前端網頁我多層應用程式至雲端

如果您執行的多層應用程式，例如網頁伺服器的連線至資料庫，Azure 應用程式服務是很好的選項，提供緊密整合 Azure SQL 資料庫。 然後，您可以使用 [WebJobs 功能執行後端處理程序。

如果您需要更多控制伺服器環境中，例如能夠遠端到您的伺服器或設定伺服器啟動工作，請選擇服務布料的轉印圖樣的一或多個您層。

如果您想要使用您自己的電腦圖像或執行伺服器軟體或服務布料的轉印圖樣無法設定您的服務，請選擇虛擬機器的一或多個您層。

### <a id="custom"></a>我的應用程式而定高度自訂的 Windows，或 Linux 環境而想要將其移至雲端。

如果您的應用程式需要複雜的安裝或設定軟體和作業系統，虛擬機器可能是最佳解決方案。 使用虛擬機器中，您可以︰

- 若要使用的作業系統，例如 Windows 或 Linux，開始使用虛擬機器庫，然後加以自訂的應用程式的需求。
- 建立並上傳現有的內部部署伺服器中 Azure 虛擬機器上執行的自訂圖像。

### <a id="oss"></a>我的網站是使用開啟來源軟體，而且想要 Azure 中將其主控

如果您開啟來源架構支援應用程式服務，語言與應用程式所需的架構會為您自動設定。 應用程式服務可讓您︰

- 使用許多熱門開啟來源語言，例如[.NET][dotnet]， [PHP][]、 [Node.js][nodejs]，及[Python][]。
- 設定 WordPress、 Drupal、 Umbraco、 DNN 及許多其他協力廠商 web 應用程式。
- 移轉現有的應用程式，或建立新的應用程式庫。

如果您開啟來源架構不支援服務應用程式中，您可以在其中一個其他 Azure 虛擬主機選項執行。 虛擬機器與您上安裝和設定軟體電腦圖像，可以在 Windows 或 Linux 為基礎。

### <a id="lob"></a>我已連線至公司網路需要的線條的商務應用程式

如果您想要建立的線條的商務應用程式，您的網站可能需要直接存取服務或公司網路上的資料。 這是應用程式服務、 服務布料的轉印圖樣，以及使用[Azure 虛擬網路服務](/services/virtual-network/)的虛擬機器。 應用程式服務，您可以使用[VNET 整合功能](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)，讓您 Azure 的應用程式，以執行您的公司網路上。

### <a id="mobile"></a>我要主控的 REST API 或行動用戶端的 web 服務

Http web 服務可讓您支援各種不同的用戶端，包括行動用戶端。 ASP.NET Web API 像架構整合，讓您輕鬆建立及使用的其餘服務的 Visual Studio 中。  這些服務公開從 web 端點，因此可以使用任何虛擬主機 Azure 的技術支援這種情況。 不過，應用程式服務是非常適合用於主控 REST Api。 使用應用程式服務，您可以︰

- 快速建立[行動應用程式](../app-service-mobile/app-service-mobile-value-prop.md)或裝載在其中一個 Azure 的全域分散式資料中心的 HTTP web 服務的[API 應用程式](../app-service-api/app-service-api-apps-why-best-platform.md)。
- 移轉現有的服務，或建立新的文件。
- 達成 SLA 可用性單一的執行個體，或延伸為專用的多部電腦。
- 提供給任何 HTTP 用戶端，包括行動用戶端的 REST Api 使用已發佈的網站。

> [AZURE.NOTE]
> 如果您想要開始使用 Azure 應用程式服務註冊帳戶之前，請移至<a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，可以讓您立即建立短暫入門應用程式中 Azure 應用程式服務的可用。 沒有所需的信用卡，沒有承諾。

## <a id="nextsteps"></a>後續步驟

如需有關的三個網頁裝載選項，請參閱[介紹 Azure](../fundamentals-introduction-to-azure.md)。

若要開始使用您選擇為您的應用程式的選項，請參閱下列資源︰

* [Azure 應用程式服務](/documentation/services/app-service/)
* [Azure 雲端服務](/documentation/services/cloud-services/)
* [Azure 虛擬機器](/documentation/services/virtual-machines/)
* [服務布料的轉印圖樣](/documentation/services/service-fabric)

<!-- URL List -->

[Azure 應用程式服務]: /services/app-service/
[雲端服務]: http://go.microsoft.com/fwlink/?LinkId=306052
[虛擬機器]: http://go.microsoft.com/fwlink/?LinkID=306053
[服務布料的轉印圖樣]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[儲存空間]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
