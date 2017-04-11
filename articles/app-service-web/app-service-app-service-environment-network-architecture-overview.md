<properties 
    pageTitle="應用程式服務環境的網路架構概觀" 
    description="建築概觀網路拓撲 ofApp 服務環境的詳細資訊。" 
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

# <a name="network-architecture-overview-of-app-service-environments"></a>應用程式服務環境的網路架構概觀

## <a name="introduction"></a>簡介 ##
[虛擬網路]的子網路內，則會建立應用程式服務環境[virtualnetwork]-具有相同的虛擬網路拓撲內的私人端點傳達的應用程式的應用程式服務環境中執行。  由於客戶可能鎖定虛擬網路基礎結構的部分，請務必理解的網路通訊流與應用程式服務環境發生的類型。

## <a name="general-network-flow"></a>一般網路流量 ##
 
當應用程式服務環境 (ASE) 使用公用虛擬 IP 位址 (VIP) 應用程式時，所有輸入的流量送達的公用 VIP 上。  這包含 HTTP 和 HTTPS 流量的應用程式，以及其他流量 FTP、 遠端偵錯功能，及 Azure 管理作業。  公用 VIP 上提供的完整清單的特定的連接埠 （必要與選用） 參閱文件上[控制輸入的流量][controllinginboundtraffic]應用程式服務環境。 

應用程式服務環境也支援繫結虛擬網路內部地址，也稱為 ILB （內部負載平衡器） 地址，才能執行應用程式。  在 ILB 啟用的 ASE、 HTTP 和 HTTPS 的流量應用程式，以及遠端偵錯通話到達 ILB 地址。  最常見的 ILB ASE 設定，FTP/FTP 流量也將會送達 ILB 位址。  不過 Azure 管理作業的 ILB 公用 VIP 上的連接埠 454/455 仍會啟用 ASE。

下圖顯示概略瞭解各種傳入和傳出網路流量應用程式服務環境的應用程式位置繫結至公用虛擬 IP 位址︰

![一般網路流量][GeneralNetworkFlows]

應用程式服務環境可以和各種不同的私人客戶端點通訊。  例如，在應用程式服務環境中執行的應用程式可以連線至資料庫伺服器執行相同的虛擬網路拓撲中 IaaS 虛擬機器上。

>[AZURE.IMPORTANT] 查看網狀圖]，在不同的子網路從應用程式服務環境中部署 「 其他計算資源 」。 部署 ASE 使用相同的子網路中的資源將會封鎖連線 ASE 這些資源 （除了特定內部 ASE 路由）]。 部署至不同的子網路改為 （在相同的 VNET)。 應用程式服務環境能夠連線。 任何其他設定，不則需要。

應用程式服務環境也通訊與 Sql DB Azure 儲存體管理及作業應用程式服務環境所需的資源。  其他人位於遠端 Azure 區域時，部分應用程式服務環境進行通訊的 Sql 和儲存資源都位於應用程式服務環境中，為相同的區域。  如此一來，輸出連線到網際網路所需的應用程式服務環境正常運作。 

應用程式服務環境子網路中部署，因為網路安全性群組可用來控制傳入的子網路流量。  如如何控制應用程式服務環境中，輸入的流量的詳細資訊，請參閱下列[文章][controllinginboundtraffic]。

如需如何從應用程式服務環境，允許輸出網際網路連線的詳細資訊，請參閱下列文章︰ 瞭解如何使用 [[快速傳送]的[ExpressRoute]。  使用網站-連線時，適用於本文所述的相同方式，使用強制通道。

## <a name="outbound-network-addresses"></a>輸出網路地址 ##
當應用程式服務環境輸出呼叫，IP 位址是永遠輸出呼叫與相關聯。  使用的特定 IP 位址，取決於呼叫端點是位於虛擬網路拓撲，內或虛擬網路拓撲。

如果呼叫的端點是**外**的虛擬網路拓撲，輸出地址 （又稱輸出 NAT 位址） 的是公用 VIP 的應用程式服務環境。  此位址找不到入口網站的使用者介面中屬性刀應用程式服務環境。
 
![輸出 IP 位址][OutboundIPAddress]

在應用程式服務環境中，建立應用程式，然後在 [應用程式的地址執行*nslookup*只有公用 VIP ASEs 也判斷至此地址。 結果的 IP 位址是公用 VIP，以及應用程式服務環境的輸出 NAT 地址。

如果呼叫的端點**內**的虛擬網路拓撲，呼叫的應用程式的輸出地址會執行的應用程式的個別計算資源的內部 IP 位址。  不過沒有常設對應的應用程式虛擬網路內部 IP 位址。  在不同的計算資源和可用的應用程式服務環境中的資源可以變更因為縮放作業的計算的資料庫應用程式可以移動。

不過，因為應用程式服務環境一律位於子網路，並保證計算資源執行應用程式的內部 IP 位址一律會位於子網路 CIDR 範圍。  如此一來，當來微調 Acl 或網路安全性群組安全存取虛擬網路中其他端點，子網路範圍中包含應用程式服務環境必須授與存取權。

下圖顯示這些概念的更多詳細資料︰

![輸出網路地址][OutboundNetworkAddresses]

在上述的圖表︰

- 由於的應用程式服務環境公用 VIP 192.23.1.2，這是 「 網際網路 」 結束點來電時所使用的輸出 IP 位址。
- 含有子網路應用程式服務環境 CIDR 範圍是 10.0.1.0/26。  相同的虛擬網路基礎結構內的其他端點會看到應用程式的電話路由針對此位址範圍內。

## <a name="calls-between-app-service-environments"></a>應用程式服務環境之間的通話 ##
如果您部署相同的虛擬網路中的多個應用程式服務環境，讓其他應用程式服務環境的另一個應用程式服務環境輸出呼叫，會發生更複雜的案例。  這些類型的交互通話也會被視為 「 網際網路 」 撥號的應用程式服務環境。

下圖顯示在第二個應用程式服務環境 （例如內部後端 API 應用程式不是要從網際網路存取） 呼叫應用程式與上一個應用程式服務環境 （例如 「 大門 」 web 應用程式） 應用程式層級結構的範例。 

![應用程式服務環境之間的通話][CallsBetweenAppServiceEnvironments] 

在上述範例應用程式服務環境 」 ASE 項目] 會有 192.23.1.2 輸出 IP 位址。  如果應用程式執行這個應用程式服務環境，讓應用程式 （「 ASE 兩個 」） 在第二個應用程式服務環境上執行的輸出呼叫位於相同的虛擬網路，輸出通話會被視為 「 網際網路 「 通話。  結果是第二個送達網路流量應用程式服務環境會顯示來自 192.23.1.2 （亦即不子網路位址範圍的第一個應用程式服務環境）。

即使兩種應用程式服務環境位於同一個 Azure 地區網路流量會保留在地區 Azure 網路，而且不實際會公用網際網路上時，不同的應用程式服務環境之間的通話會被視為 「 網際網路 「 通話。  因此您可以使用子網路上的第二個應用程式服務環境的網路安全性群組只允許輸入的呼叫從第一個應用程式服務環境 （其輸出 IP 位址是 192.23.1.2），以確保安全應用程式服務環境之間的通訊。

## <a name="additional-links-and-information"></a>其他連結和資訊 ##
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

詳細資料輸入應用程式服務環境所使用的連接埠，且可使用網路安全性群組控制輸入的流量[以下][controllinginboundtraffic]。

使用使用者的詳細資料定義要授與存取應用程式服務環境有此[文件]路由[ExpressRoute]。 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

