<properties
    pageTitle="建立和使用內部的負載平衡器與應用程式服務環境 |Microsoft Azure"
    description="建立及使用 ILB ASE"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>使用內部負載平衡器與應用程式服務環境 #

應用程式服務 Environments(ASE) 功能所提供的不是提供多租用戶戳記增強型的設定功能 Azure 應用程式服務的進階版服務] 選項。  ASE 功能基本上部署在您 Azure 虛擬 Network(VNet) Azure 應用程式服務。  若要取得更深入瞭解閱讀的[什麼是應用程式服務環境]的應用程式服務環境所提供的功能[WhatisASE]文件。  如果您不知道優點作業系統中 VNet 閱讀[Azure 虛擬網路常見問題集][virtualnetwork]。  


## <a name="overview"></a>概觀 ##


網際網路存取端點或您 VNet 中的 IP 位址，可以部署 ASE。  若要設定的 IP 位址至您要部署您的內部的載入 Balancer(ILB) ASE VNet 地址。  當您 ASE 設定與 ILB 提供︰

- 您自己的網域或子網域。  若要讓您輕鬆地，這份文件假設子網域，但您可以設定它兩種。  
- 用於 HTTPS 的憑證
- 子網域的 DNS 管理。  


您可以執行下列作業︰

- host （主機） 內部網路的應用程式，如商務應用程式，安全地在雲端的您可以透過網站，網站或 ExpressRoute VPN 存取
- 主機應用程式在雲端，不會列在公用 DNS 伺服器
- 建立的前端應用程式可以安全地整合與網際網路隔離後端應用程式


#### <a name="disabled-functionality"></a>停用的功能 ####

有使用 ILB ASE 時，您無法執行的一些事項。  這些項目包括︰

- 使用 IPSSL
- 指派給特定的應用程式的 [IP 位址
- 購買並使用透過入口網站應用程式的憑證。  當然仍，您可以取得直接與憑證授權單位的憑證，並使用您的應用程式，只是不是透過 Azure 入口網站。


## <a name="creating-an-ilb-ase"></a>建立 ILB ASE ##

建立 ILB ASE 不以正常方式建立 ASE 不同。  如需更深入的討論區建立 ASE 閱讀[如何建立應用程式服務環境]的[HowtoCreateASE]。  若要建立 ILB ASE 程序是 ASE 建立期間建立 VNet，或是選取現有的 VNet 一樣的。  若要建立 ILB ASE: 

1.  選取 [Azure 入口網站中的 [**新增]-> [Web + Mobile]-> [應用程式服務環境**
2.  選取您的訂閱
3.  選取或建立資源群組
4.  選取或建立 VNet
5.  如果選取 VNet，建立子網路
6.  選取 [**虛擬的網路位置]-> [VNet 設定**，並將 VIP 類型設定為內部
7.  提供子網域名稱 （這是用來在此 ASE 中建立的應用程式的子網域）
8.  選取 [確定]，然後建立


![][1]


虛擬網路刀有 VNet 設定選項。  這可讓您選取外部 VIP 或內部 VIP。  預設值為外部。  如果您將其設定為外部您 ASE 會使用網際網路存取 VIP。  如果您選取內部，將會與您 VNet 內的 IP 位址 ILB 設定您 ASE。  


選取後內部，可將更多的 IP 位址新增至您 ASE 會移除，請改為您需要提供 ASE 的子網域。  與外部的 VIP ASE 中 ASE 名稱用於子網域中的 ASE 中建立的應用程式。  
如果您 ASE 稱為***contosotest***和您的應用程式的 ASE 稱為***mytest***然後子網域想要的格式***contosotest.p.azurewebsites.net*** ，該應用程式的 URL 會是***mytest.contosotest.p.azurewebsites.net***。  
如果您將內部 VIP 類型，您 ASE 名稱不會使用子網域中的 ASE。  子網域是明確地指定的。  如果您的子網域是***contoso.corp.net***和您所做的應用程式的 ASE 名為***timereporting***該應用程式的 URL 就會是***timereporting.contoso.corp.net***。


## <a name="apps-in-an-ilb-ase"></a>在 [ILB ASE 應用程式 ##

建立應用程式中 ILB ASE 會以正常方式建立應用程式中 ASE 相同。  

1. Azure 入口網站中選取 [**新增]-> [Web + Mobile]-> [Web**或**行動電話**或**API 應用程式**
2. 輸入應用程式的名稱
2. 選取 [訂閱]
3. 選取或建立資源群組
4. 選取或建立應用程式服務 Plan(ASP)。  如果建立新的 ASP 然後選取您 ASE 做為位置，然後選取您想要在中建立您 ASP 背景工作集區。  當您建立 ASP 選取您 ASE 為位置，並在工作者集區。  當您指定的應用程式名稱，您會看到您的應用程式名稱下的子網域，您 ASE 的子網域被取代。   
5. 選取 [建立]。  如果您想要顯示在儀表板上的應用程式，您應選取 [**固定至儀表板**核取方塊。  

![][2]


在 [應用程式的名稱底下的子網域名稱會更新以反映您 ASE 的子網域。  


## <a name="post-ilb-ase-creation-validation"></a>文章 ILB ASE 建立驗證 ##

ILB ASE 會稍有不同於非-ILB ASE。  為已記下您需要管理您自己的 DNS，您也可以為 HTTPS 連線提供您自己的憑證。  


建立您 ASE 之後，您會發現當子網域指定而稱為**ILB 憑證**的 [**設定**] 功能表中有新的項目會顯示子網域。  建立自我簽署憑證容易測試 HTTPS ASE。  入口網站會告訴您必須先為 HTTPS 提供您自己的憑證，但這是您會使用您網域的憑證。  

![][3]


如果您只要嘗試項目，並不知道如何建立憑證，您可以使用 IIS MMC 主控台應用程式來建立自我簽署的憑證。  已建立可以將其匯出為.pfx 檔，然後上傳 ILB 憑證 ui 上。 當您存取網站，自我簽署憑證以保護時，您的瀏覽器可讓您存取您的網站不安全由於無法驗證憑證的警告。  如果您想要避免警告必須符合您的子網域，並有可辨識您的瀏覽器的信任關係鏈正確簽署的憑證。

![][6]

如果您想要嘗試使用您自己的憑證流程及測試您 ASE HTTP 和 HTTPS 存取︰

1.  移至 [ASE UI ASE 建立後**ASE]-> [設定]-> [ILB 憑證**
2.  選取憑證 pfx 檔案來設定 ILB 憑證，並提供的密碼。  此步驟會處理一小段時間和縮放比例的作業正在進行中的郵件就會顯示。
3.  取得您 ASE ILB 地址 (**ASE]-> [屬性]-> [虛擬的 IP 位址**)
4.  在 ASE 建立 web 應用程式建立後 
5.  如果您沒有在該 VNET （不在 ASE 或項目符號相同子網路），建立 VM
6.  設定您的子網域的 DNS。  您可以使用您的子網域中的萬用字元，在您的 DNS，或如果您想要做一些簡單的測試，編輯您 VM 設定 web 應用程式名稱為 VIP IP 位址 hosts 檔案。  如果您 ASE 有子網域名稱。 ilbase.com，而使 web 應用程式 mytestapp 就收件者 mytestapp.ilbase.com，然後設定 hosts 檔案中。  （Windows 上 hosts 檔案位於 C:\Windows\System32\drivers\etc\）
7.  在該 VM 上使用的瀏覽器並移至 http://mytestapp.ilbase.com （或內容 web 應用程式名稱會使用您的子網域）
8.  在該 VM 上使用的瀏覽器，然後移至 [https://mytestapp.ilbase.com 則必須以接受的安全性不足，如果使用自我簽署的憑證。  


您 ILB 的 IP 位址列在您的內容虛擬 IP 位址

![][4]


## <a name="using-an-ilb-ase"></a>使用 ILB ASE ##

#### <a name="network-security-groups"></a>網路安全性群組 ####

ILB ASE 可讓您的應用程式的網路隔離，為應用程式不是存取或甚至已知的網際網路。  這是絕佳裝載內部網路網站，例如商務應用程式。  當您需要限制存取更進一步您還是可以使用網路安全性 Groups(NSGs) 來控制網路層級的存取權。 


如果您想要使用 NSGs 進一步限制存取您需要，請確定您不會中斷通訊 ASE 需要才能運作。  即使 HTTP/HTTPS 存取僅透過使用 ASE ILB ASE 仍取決於 VNet 以外的資源。  若要查看哪些網路存取仍需要則查看文件中的資訊上[控制連入流量到應用程式服務環境][ControlInbound]和[網路設定的詳細資料的應用程式服務環境 ExpressRoute]上的文件[ExpressRoute]。  


若要設定您的 NSGs 您必須知道用來管理您的 ASE Azure IP 位址。  如果這可讓網際網路要求的 IP 位址也是從您 ASE 的輸出 IP 位址。  若要尋找此 IP 位址移至 [**設定]-> [內容**和尋找**輸出 IP 位址**。  

![][5]


#### <a name="general-ilb-ase-management"></a>一般 ILB ASE 管理 ####

管理 ILB ASE 主要是以正常方式管理 ASE 相同。  您要不按比例縮放工作者集區裝載更多 ASP 執行個體，和您處理的 HTTP/HTTPS 流量的更的多的前端伺服器不按比例縮放設定。  如需管理的 ASE，設定的一般資訊閱讀文件上[設定應用程式服務環境][ASEConfig]。  


其他管理項目是憑證管理和 DNS 管理。  您需要取得及上傳後 ILB ASE 建立用於 HTTPS 的憑證，並將它取代之前續訂。  由於 Azure 擁有基底網域我們可以與外部的 VIP ASEs 提供的憑證。  子網域使用 ILB ASE 可以是任何內容，因為您需要為 HTTPS 提供您自己的憑證。 


#### <a name="dns-configuration"></a>DNS 設定 ####

使用外部 VIP 時 Azure 會管理 DNS。  在您 ASE 中建立的任何應用程式會自動新增這是公用 DNS Azure dns。  在 ILB ASE 必須管理您自己的 DNS。  將指定的子網域，例如 contoso.corp.net 您需要建立的 DNS 記錄指向您 ILB 地址的︰

    * 
    *.scm ftp 發佈 


## <a name="getting-started"></a>快速入門
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

若要開始使用應用程式服務環境，請參閱[應用程式服務環境簡介][WhatisASE]

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
