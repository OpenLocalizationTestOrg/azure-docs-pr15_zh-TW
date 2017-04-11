<properties 
    pageTitle="層疊在一起的安全性架構與應用程式服務環境" 
    description="實作與應用程式服務環境的層次的安全性架構。" 
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
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>實作與應用程式服務環境的層次的安全性架構

## <a name="overview"></a>概觀 ##
 
因為應用程式服務環境提供部署至虛擬網路隔離執行階段環境，開發人員可以建立的每一實體應用程式層提供的網路存取不同層級的層次的安全性架構。

常見的想是隱藏 API 後端一般網際網路存取，並只允許 Api 呼叫由上游 web 應用程式。  [網路安全性群組 (NSGs)] [NetworkSecurityGroups]可以包含應用程式服務環境子網路上用來限制 API 應用程式公開存取。

下圖顯示的範例架構的應用程式服務環境中部署 WebAPI 基礎應用程式。  三個不同的網站的應用程式執行個體，部署三個不同應用程式服務的環境中，在撥打後端相同 WebAPI 應用程式。

![概念性架構][ConceptualArchitecture] 

綠色加號 （+） 表示包含 「 apiase 」 的子網路上的 [網路] 安全性群組，讓本身的好來電為從上游 web 應用程式中，輸入的呼叫。  不過相同的網路安全性群組明確拒絕從網際網路存取一般輸入流量。 

本主題的其餘部分將引導在包含 「 apiase 」 的子網路設定網路安全性群組所需的步驟。

## <a name="determining-the-network-behavior"></a>決定網路行為 ##
若要知道哪些網路安全性規則會有需要您需要判斷哪一個網路允許的用戶端達到包含 API 應用程式時，應用程式服務環境和用戶端會遭到封鎖。

自[網路安全性群組 (NSGs)] [NetworkSecurityGroups]會套用至子網路和應用程式服務環境部署到子網路、 NSG 中所包含的規則套用至**所有**執行的應用程式服務環境的應用程式。  網路安全性群組套用到包含 「 apiase 」 的子網路之後，請在本文中，使用範例架構，「 apiase 」 應用程式服務環境上執行的所有應用程式會受到相同的安全性規則集。 

- **決定上游來電者的輸出 IP 位址︰** 什麼是 IP 位址或上游來電者的地址？  需要明確 NSG 中允許存取這些地址。  由於應用程式服務環境之間的通話被視為 「 網際網路 」 通話，這表示指派給每個三種上游應用程式服務環境中，「 apiase 」 子網路 NSG 允許存取需求的輸出 IP 位址。   如需詳細資訊決定輸出 IP 位址，如應用程式的應用程式服務環境中執行，請參閱[網路架構][NetworkArchitecture]概觀文章。
- **會需要通話本身的後端 API 應用程式嗎？**  有時忽略和細微點是位置後端應用程式需要通話本身的案例。  如果在應用程式服務環境的後端 API 應用程式需要通話本身，這也被視為為 「 網際網路 」 通話。  在範例架構中需要允許存取從 「 apiase 」 應用程式服務環境以及的輸出 IP 位址。

## <a name="setting-up-the-network-security-group"></a>設定網路安全性群組 ##
一旦已知輸出 IP 位址的設定下, 一步是建構網路安全性群組。  以兩個資源管理員為主的虛擬網路，以及傳統的虛擬網路，您可以建立網路安全性群組。  下列範例顯示建立及設定 NSG 使用 Powershell 傳統虛擬網路上。

範例架構，環境位於南部美國中部，以便在該區域中建立空白的 NSG:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

第一次明確允許 Azure 管理基礎結構如下所述上[輸入的流量]的文件中新增規則[InboundTraffic]應用程式服務環境。

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
接下來，兩個規則會新增至允許 HTTP 和 HTTPS 的電話，從第一個上游應用程式服務環境 (「 fe1ase 」)。

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

確定，並重複的第二個和第三個上游應用程式服務環境 （「 fe2ase 」 和 「 fe3ase 」）。

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

最後，授與存取權的後端 API 的應用程式服務環境的輸出 IP 位址，讓它可以回撥入本身。

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

沒有其他網路安全性規則需要預設設定由於每個 NSG 有一組封鎖輸入的存取網際網路的預設規則。

如下所示的網路的 [安全性] 群組中的規則的完整清單。  請注意，最後一個規則，醒目提示時，如何封鎖輸入的存取所有的來電者以外的已明確授與存取權。

![NSG 設定][NSGConfiguration] 

最後一個步驟是將 NSG 套用至子網路包含 「 apiase 」 應用程式服務環境。  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

使用套用至子網路 NSG，只有三個上游應用程式服務的環境中，並包含 API 後端，應用程式服務環境允許的撥入的 「 apiase 」 環境。


## <a name="additional-links-and-information"></a>其他連結和資訊 ##
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

[網路安全性群組](../virtual-network/virtual-networks-nsg.md)的相關資訊。 

了解[輸出 IP 位址][NetworkArchitecture]和應用程式服務環境。

[網路連接埠][InboundTraffic]使用應用程式服務環境。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
