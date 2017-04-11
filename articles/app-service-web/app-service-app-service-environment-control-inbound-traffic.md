<properties 
    pageTitle="如何控制輸入的流量應用程式服務環境" 
    description="瞭解如何設定網路安全性規則來控制應用程式服務環境輸入的流量。" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>如何控制輸入的流量應用程式服務環境

## <a name="overview"></a>概觀 ##
您可以建立應用程式服務環境中**任一**Azure 資源管理員虛擬網路，**或**在傳統的部署模型[虛擬網路][virtualnetwork]。  可以在建立應用程式服務環境時定義新的虛擬網路和新的子網路。  或者，可以建立應用程式服務環境中的已存在的虛擬網路和先前既子網路。  進行年 6 月 2016年中的最近變更，ASEs 現在可部署到 （亦即使用公用位址範圍或 RFC1918 地址空格的虛擬網路 私人的地址）。  如需建立的應用程式服務環境的詳細資訊，請參閱[如何建立應用程式服務環境][HowToCreateAnAppServiceEnvironment]。

永遠必須將應用程式服務環境建立子網路內，因為子網路提供網路邊界可用鎖定輸入流量前上游裝置和服務，例如 HTTP 和 HTTPS 的流量會只接受來自特定上游的 IP 位址。

使用[網路安全性群組]控制子網路上的輸入與輸出網路流量[NetworkSecurityGroups]。 控制輸入的流量需要在網路安全性群組，建立網路安全性規則，然後再指派網路安全性群組子網路之應用程式服務環境。

後網路安全性群組指派給子網路，向內的傳輸至應用程式服務環境中的應用程式是允許/封鎖根據允許，並拒絕網路安全性群組中定義的規則。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>在應用程式服務環境中使用的網路連接埠 ##
先鎖定輸入網路流量與網路安全性群組，很重要事項必要及選用的網路使用的連接埠應用程式服務環境設定。  不小心關閉某些連接埠流量導向關閉，可能會導致影響應用程式服務環境中的功能。

以下是使用應用程式服務環境的連接埠的清單。 除非另行清楚註明，所有的連接埠是**TCP**:

- 454︰**所需連接埠**Azure 基礎結構用於管理及維護透過 SSL 應用程式服務環境。  不會封鎖流量到這個連接埠。  此連接埠永遠繫結的 ASE 公用 vip。
- 455︰**所需連接埠**Azure 基礎結構用於管理及維護透過 SSL 應用程式服務環境。  不會封鎖流量到這個連接埠。  此連接埠永遠繫結的 ASE 公用 vip。
- 80︰ 預設應用程式中的應用程式服務環境中的應用程式服務方案執行輸入 HTTP 流量的連接埠。  在 ILB 啟用的 ASE，此連接埠繫結到 ASE ILB 地址。
- 443︰ 預設應用程式中的應用程式服務環境中的應用程式服務方案執行輸入 SSL 流量的連接埠。  在 ILB 啟用的 ASE，此連接埠繫結到 ASE ILB 地址。
- 21: ftp 控制頻道。  如果無法使用 FTP 可以安全地封鎖此連接埠。  在 ILB 啟用的 ASE，此連接埠可以繫結到 ILB 地址的 ASE。
- 990︰ 控制頻道的 FTP。  如果沒有使用 FTP 可以安全地封鎖此連接埠。  ILB 啟用的 ASE，在此連接埠可以繫結至 ILB 地址的 ASE。
- 10001 10020: ftp 資料頻道。  就跟控制頻道，下列連接埠可能會安全地封鎖，如果無法使用 FTP。  ILB 啟用的 ASE，在此連接埠可以繫結至 ASE ILB 地址。
- 4016︰ 用於遠端偵錯時，使用 Visual Studio 2012。  如果無法使用的功能，就可以安全地封鎖此連接埠。  在 ILB 啟用的 ASE，此連接埠繫結到 ASE ILB 地址。
- 4018︰ 用於遠端偵錯時，使用 Visual Studio 2013。  如果無法使用的功能，就可以安全地封鎖此連接埠。  在 ILB 啟用的 ASE，此連接埠繫結到 ASE ILB 地址。
- 4020︰ 用於遠端偵錯時，使用 Visual Studio 2015。  如果無法使用的功能，就可以安全地封鎖此連接埠。  在 ILB 啟用的 ASE，此連接埠繫結到 ASE ILB 地址。

## <a name="outbound-connectivity-and-dns-requirements"></a>輸出連線與 DNS 需求 ##
應用程式服務正常環境，需要不同的結束點的輸出存取。 完整外部端點 ASE 所使用的清單是[ExpressRoute 的網路設定](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)文件的 「 所需的網路連線能力] 區段中。

應用程式服務環境需要有效 DNS 基礎結構的虛擬網路設定。  如果基於任何原因建立應用程式服務環境後，會變更 DNS 設定，開發人員可以強制應用程式服務環境，以選擇 [新增 DNS 設定。  觸發循環的環境重開機，使用 [重新啟動 」 圖示位於頂端的 [在[Azure 入口網站]應用程式服務環境管理刀[NewPortal]會導致環境設定，選擇 [新增 DNS 設定。

建議 vnet 上的任何自訂 DNS 伺服器會提前之前建立的應用程式服務環境的設定。  如果已建立的應用程式服務環境時，會變更虛擬網路的 DNS 設定，就會導致的應用程式服務環境建立程序失敗。  同樣地，如果自訂的 DNS 伺服器存在於另一端的 VPN 閘道器，而且 DNS 伺服器是無法連線或無法使用，，應用程式服務環境建立程序將也會失敗。

## <a name="creating-a-network-security-group"></a>建立網路安全性群組 ##
如果在網路安全性如何群組工作的完整詳細資料，請參閱下列[資訊][NetworkSecurityGroups]。  在觸控式以下詳細資訊，醒目提示網路安全性群組，並將焦點放在設定及套用網路安全性群組包含應用程式服務環境的子網路上。

**附註︰**您可以設定網路安全性群組，以圖形方式使用[Azure 入口網站](https://portal.azure.com)或透過 PowerShell 的 Azure。

網路安全性群組是第一次建立為獨立的實體與訂閱相關聯。 因為網路安全性群組建立 Azure 區域中，請確定網路安全性群組皆會建立應用程式服務環境為相同的區域。

以下示範如何建立網路安全性群組︰

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

網路安全性群組建立後，會新增一或多個網路安全性規則。  一段時間，可能會變更一組規則，因為建議查看用於規則的優先順序，使其更容易插入其他規則一段時間的編號配置空間。

下列範例顯示明確授與存取所需的可用於管理及的環境中應用程式服務的 Azure 基礎結構的管理連接埠的規則。  請注意，所有的管理流量流向透過 SSL 受到用戶端憑證，因此即使開啟連接埠是任何非 Azure 管理基礎結構的實體無法存取。


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

當鎖定的存取權連接埠 80 和 443 「 隱藏 」 應用程式服務環境上游裝置或服務後，您必須知道的上游的 IP 位址。  例如，如果您使用的 web 應用程式防火牆 (WAF)，WAF 會有時使用它自己的 IP 位址 （或地址） 下游的應用程式服務環境代理流量。  您必須在*SourceAddressPrefix*參數的網路安全性規則中使用此 IP 位址。

在下面的範例中，明確允許輸入的流量從特定上游 IP 位址。  地址*1.2.3.4*作為版面配置區上游 WAF 的 IP 位址。  變更以符合您上游裝置或服務所使用的位址值。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
如果想要 FTP 支援，可以使用下列規則另存為範本將 FTP 控制連接埠和資料的存取權授予頻道連接埠。  由於 FTP 狀態的通訊協定，您可能無法將 FTP 流量路由傳送到傳統的 HTTP/HTTPS 防火牆或 proxy 裝置。  在此情況下，您必須設定為不同的值-例如 IP 位址範圍的開發人員或部署電腦的 FTP 用戶端執行的*SourceAddressPrefix* 。 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**附註︰**資料頻道連接埠範圍可能會變更預覽期間。)

如果使用 Visual Studio 與遠端偵錯時，下列規則會示範如何授與存取權。  由於每個版本可用於不同的連接埠遠端偵錯有 Visual Studio 的每一個受支援版本的另一個規則。  就跟 FTP 存取遠端偵錯流量可能不會正確透過傳統 WAF 或 proxy 裝置。  若要執行 Visual Studio 開發人員電腦的 IP 位址範圍時，可以改為設定*SourceAddressPrefix* 。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>子網路指派網路安全性群組 ##
網路安全性群組已拒絕所有的外部資料存取預設安全性規則。  從結合網路安全性規則上述，而預設安全性規則會封鎖連入的資料傳輸，則結果為從來源位址*允許*動作相關聯的範圍將能夠流量傳送至應用程式的應用程式服務環境中執行的唯一流量。

網路安全性群組填入安全性規則之後，就必須被指派到子網路之應用程式服務環境。  [工作分派] 命令參照的名稱的虛擬的網路應用程式服務環境所在的位置，以及建立應用程式服務環境子網路的名稱。  

下列範例顯示網路安全性群組被指派到子網路和虛擬網路︰


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

網路安全性群組指派成功後 （工作分派是長時間執行作業及可能需要幾分鐘，才能完成） 只輸入對應*儲存格內允許*規則的流量會成功達到 office 應用程式服務環境的應用程式。

完整性下列範例會示範如何移除，因此不關聯子網路安全性群組︰


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>明確的 IP SSL 的特殊考量 ##
如果應用程式設定明確的 IP SSL 地址 （只有公用 VIP ASEs 適用），而不是使用預設的 IP 位址的應用程式服務環境，同時 HTTP 和 HTTPS 流量進入子流程不同資料集的連接埠 80 和 443 以外的連接埠。

使用每個 SSL IP 位址的連接埠的個別組可以從應用程式服務環境的詳細資料 UX 刀入口網站的使用者介面中找到。  選取 [所有設定]--都 > [IP 位址 」。  [IP 位址 」 刀顯示應用程式服務的環境，以及用來將每個 IP SSL 地址與相關聯的 HTTP 和 HTTPS 流量路由傳送特殊的連接埠組資料表的所有明確設定的 IP SSL 地址。  這是需要使用 DestinationPortRange 參數的網路安全性群組中設定規則時，此連接埠組。

當 ASE 上的應用程式設定為使用 IP SSL 時，外部客戶就不會看到，而不需要擔心特殊的連接埠組對應。  到的應用程式的流量會以正常方式設定的 SSL IP 位址。  特殊的連接埠對翻譯自動發生內部路由流量的最後階段到包含 ASE 子網路。 

## <a name="getting-started"></a>快速入門

若要開始使用應用程式服務環境，請參閱[應用程式服務環境簡介][IntroToAppServiceEnvironment]

所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

熟悉安全地連線後端資源從應用程式服務環境的應用程式的詳細資訊，請參閱[安全地連接到從應用程式服務環境的後端資源][SecurelyConnecttoBackend]

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
