<properties 
    pageTitle="從應用程式服務環境安全地連結後端資源" 
    description="進一步瞭解如何安全地連結後端資源從應用程式服務環境。" 
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

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>從應用程式服務環境安全地連結後端資源 #

## <a name="overview"></a>概觀 ##
由於一定建立的應用程式服務環境中**任一**Azure 資源管理員虛擬網路，**或**在傳統的部署模型[虛擬網路][virtualnetwork]，從應用程式服務環境的輸出連線至其他後端資源可以傳送以獨佔模式透過虛擬網路。  進行年 6 月 2016年中的最近變更，ASEs 也可以部署到 （亦即使用公用位址範圍或 RFC1918 地址空格的虛擬網路 私人的地址）。  

例如，可能有的連接埠 1433 鎖定的虛擬機器叢集上執行 SQL Server。  端點可能 ACLd 只允許在相同的虛擬網路上的 [從其他資源的存取。  

另一個範例，機密的結束點可能會執行內部部署，並且連線到任一[網站為網站]透過 Azure[SiteToSite]或[Azure ExpressRoute] [ExpressRoute]連線。  如此一來，只有在連線至網站的網站或 ExpressRoute 通道的虛擬網路中的資源將能夠存取內部部署結束點。

針對您所有的這些案例中，執行的應用程式服務環境的應用程式能夠安全地連結至各種不同的伺服器及資源。  外寄流量的環境中執行應用程式服務至相同的虛擬網路中的私人端點的應用程式的 （或連線到相同的虛擬網路），將只流程虛擬網路上。  私人的結束點的外寄流量不會透過公用網際網路。

從應用程式服務環境虛擬網路中的端點，必須注意適用於輸出流量。  應用程式服務環境無法連絡虛擬機器位於應用程式服務環境**相同**子網路的結束點。  這通常不應該問題，只要將只在應用程式服務環境保留做為單獨使用子網路部署應用程式服務環境。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>輸出連線與 DNS 需求 ##
應用程式服務正常環境，需要不同的結束點的輸出存取。 完整外部端點 ASE 所使用的清單是[ExpressRoute 的網路設定](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)文件的 「 所需的網路連線能力] 區段中。

應用程式服務環境需要有效 DNS 基礎結構的虛擬網路設定。  如果基於任何原因建立應用程式服務環境後，會變更 DNS 設定，開發人員可以強制應用程式服務環境，以選擇 [新增 DNS 設定。  觸發循環的環境重開機，使用 [重新啟動 」 圖示位於頂端的 [在入口網站應用程式服務環境管理刀會導致環境設定，選擇 [新增 DNS 設定。

建議 vnet 上的任何自訂 DNS 伺服器會提前之前建立的應用程式服務環境的設定。  如果已建立的應用程式服務環境時，會變更虛擬網路的 DNS 設定，就會導致的應用程式服務環境建立程序失敗。  同樣地，如果自訂的 DNS 伺服器存在於另一端的 VPN 閘道器，而且 DNS 伺服器是無法連線或無法使用，，應用程式服務環境建立程序將也會失敗。

## <a name="connecting-to-a-sql-server"></a>連線至 SQL Server
常見的 SQL Server 組態具有接聽連接埠 1433年端點︰

![SQL Server 端點][SqlServerEndpoint]

有兩種方法此端點限制流量︰


- [網路存取控制清單][ NetworkAccessControlLists] （網路 Acl）

- [網路安全性群組][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>網路 ACL 的限制存取

連接埠 1433年可以使用網路存取控制清單受到保護。  下的面 whitelists 用戶端範例來自內虛擬網路，並會封鎖所有其他用戶端存取。

![網路存取控制清單範例][NetworkAccessControlListExample]

在應用程式服務環境中執行相同的虛擬 SQL Server 網路中的任何應用程式無法連線至 SQL Server 虛擬機器中使用**VNet 內部**IP 位址的 SQL Server 執行個體。  

下列範例連接字串參照 SQL Server 使用私用的 IP 位址。

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

雖然虛擬機器有了公用的端點，請嘗試使用的公用 IP 位址的連線會被拒絕因為網路 ACL。 

## <a name="restricting-access-with-a-network-security-group"></a>網路安全性群組的限制存取
保護存取另一個方法是使用網路安全性群組。  個別的虛擬機器，或包含虛擬機器子網路，可以套用網路安全性群組。

網路安全性群組必須先建立︰

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

限制存取權 VNet 內部流量是非常簡單的網路安全性群組。  網路安全性群組中的預設規則只允許從網路中其他用戶端相同的虛擬網路的存取。

結果是鎖定存取 SQL Server 是非常簡單，將網路安全性群組與其預設規則套用至任一虛擬機器執行 SQL Server 或包含虛擬機器子網路。

下列範例適用於包含子網路安全性群組︰

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
結果是一組封鎖外部存取]，允許 VNet 內部存取的安全性規則︰

![預設網路安全性規則][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>快速入門
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

若要開始使用應用程式服務環境，請參閱[應用程式服務環境簡介][IntroToAppServiceEnvironment]

控制您的應用程式服務環境輸入的流量周圍的詳細資訊，請參閱[控制輸入的流量到應用程式服務環境][ControlInboundASE]

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
