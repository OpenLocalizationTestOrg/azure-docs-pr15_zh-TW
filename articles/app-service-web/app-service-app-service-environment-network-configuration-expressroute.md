<properties 
    pageTitle="使用快速傳送的網路設定的詳細資料" 
    description="網路設定的詳細資料執行應用程式服務環境中虛擬的網路連線到 ExpressRoute 電路。" 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>應用程式服務環境中使用 ExpressRoute 網路設定的詳細資料 

## <a name="overview"></a>概觀 ##
客戶可以連線的[Azure ExpressRoute] [ExpressRoute]虛擬網路基礎結構，因此將其內部部署網路延伸至 Azure 電路。  應用程式服務環境可以建立在此[虛擬網路]的子網路[virtualnetwork]基礎結構。  執行在應用程式服務環境的應用程式可以僅透過 ExpressRoute 連線，然後建立易存取的後端資源的安全連線。  

您可以建立應用程式服務環境中**任一**Azure 資源管理員虛擬網路，**或**在傳統的部署模型虛擬網路。  進行年 6 月 2016年中的最近變更，ASEs 也現在可部署到 （亦即使用公用位址範圍或 RFC1918 地址空格的虛擬網路 私人的地址）。 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>必要的網路連線 ##
有可能最初符合連線到 ExpressRoute 虛擬網路中的應用程式服務環境的網路連線需求。  應用程式服務環境會需要所有動作才能正常運作︰


-  Azure 儲存體上兩個連接埠 80 和 443 全球的端點的輸出的網路連線。  這包含位於相同的區域，如應用程式服務環境中，以及儲存端點位於**其他**Azure 區域的結束點。  Azure 儲存體端點解決下的 [下列 DNS 網域︰ *table.core.windows.net*、 *blob.core.windows.net*、 *queue.core.windows.net*和*file.core.windows.net*。  
-  在連接埠 445 Azure 檔案服務的輸出的網路連線。
-  輸出的網路連線至位於相同的區域為應用程式服務環境中的 Sql DB 端點。  Sql DB 端點解決下列網域下︰ *database.windows.net*。  這需要開啟連接埠 1433 11000 11999 和 14000 14999 存取。  如需詳細資訊請參閱[本文 Sql 資料庫 V12 連接埠流量](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)。
-  Azure 管理平面端點 （ASM 和 ARM 端點） 輸出的網路連線。  這包含*management.core.windows.net*和*management.azure.com*輸出連線。 
-  *Ocsp.msocsp.com*、 *mscrl.microsoft.com*和*crl.microsoft.com*輸出的網路連線。  這需要支援 SSL 功能。
-  虛擬網路的 DNS 設定必須能夠解決的所有結束點和舊版點中所提及的網域。  如果無法解決這些端點，應用程式服務環境建立嘗試將會失敗，與現有的應用程式服務環境會被標示為不佳。
-  需要與 DNS 伺服器通訊的連接埠 53 輸出存取權。
-  如果自訂的 DNS 伺服器上的另一端閘道，必須包含應用程式服務環境子網路連線到 DNS 伺服器。 
-  輸出網路路徑無法通過內部公司 proxy，也可以是強制通道內部部署。  這樣會從應用程式服務環境變更輸出網路流量的有效 NAT 地址。  變更應用程式服務環境的輸出網路流量的 NAT 地址會列於上方的結束點的連線失敗。  這會導致失敗的應用程式服務環境建立嘗試，以及您在先前健全的應用程式服務環境標示為不佳。  
-  輸入所需的連接埠網路存取，必須允許應用程式服務環境，此[文件]中所述的[requiredports]。

可以符合 DNS 需求，以確保設定和維護的虛擬網路有效的 DNS 基礎結構。  如果基於任何原因建立應用程式服務環境後，會變更 DNS 設定，開發人員可以強制應用程式服務環境，以選擇 [新增 DNS 設定。  觸發循環的環境重開機，使用 [重新啟動 」 圖示位於頂端的 [在[Azure 入口網站]應用程式服務環境管理刀[NewPortal]會導致環境設定，選擇 [新增 DNS 設定。

輸入的網路存取需求可以符合藉由設定[網路安全性群組][NetworkSecurityGroups]允許必要的存取，此[文件]中所述的應用程式服務環境的子網路上[requiredports]。

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>啟用輸出的網路連線的應用程式服務環境##
根據預設，新建立的 ExpressRoute 電路通知的預設路由，可讓輸出網際網路連線。  使用此設定應用程式服務環境能連線至其他 Azure 結束點。

不過常見的客戶設定是定義自己強制輸出網際網路流量改為內部部署的預設路由 (0.0.0.0 / 0)。  此流量側面中斷應用程式服務環境，因為輸出流量任一封鎖內部部署，或 NAT 就像不再使用各種 Azure 結束點的地址的無法辨識組合。

解決方法是包含應用程式服務環境的子網路上定義一個 （或更多） 的使用者定義路由 (UDRs)。  UDR 定義子網路特定路由，而不是預設路由，將會套用。

如果可能的話，建議您使用下列設定︰

- ExpressRoute 設定通知 0.0.0.0/0，依預設強制通道所有外寄流量內部部署。
- 套用至子網路之應用程式服務環境 UDR 定義 0.0.0.0/0 與網際網路 （例如，是本文中向下久） 的下一個躍點類型。

合併的效果，這些步驟是，子網路層級 UDR 會優先於 ExpressRoute 強制通道，以確保從應用程式服務環境的輸出網際網路存取。

> [AZURE.IMPORTANT] 路由定義在 UDR，**必須**是不夠優先於任何路由 ExpressRoute 設定通知。  下面的範例中使用主要 0.0.0.0/0 地址的範圍，，因此您可以可能不小心覆寫路由廣告使用更具體的地址範圍。
>
>應用程式服務環境不支援 ExpressRoute 設定**跨通知中的公用等路徑至私人對等的路徑**。  已設定，請公用對等的 ExpressRoute 設定會從 Microsoft 接收路由廣告的大量的 Microsoft Azure IP 位址範圍。  如果這些位址範圍是私人的對等路徑上跨通知，最後的結果會是從應用程式服務環境的子網路的所有輸出網路封包，將會強制通道客戶的內部部署網路基礎結構。  與應用程式服務環境目前不支援此網路流量。  若要停止從公用等路徑的跨廣告路由至私人對等的路徑是一個解決這個問題。

使用者定義路由背景資訊是用於此[概觀][UDROverview]。  

建立及設定使用者定義路由的詳細資訊是用於此[如何指南][UDRHowTo]。

## <a name="example-udr-configuration-for-an-app-service-environment"></a>應用程式服務環境的範例 UDR 設定 ##

**必要條件**

1. 從[Azure 下載頁面]安裝 Powershell 的 Azure[ AzureDownloads] （年 6 月 2015年或更新版本）。  在 「 命令列工具] 下，沒有 [安裝] 下的連結 「 Windows Powershell 」，安裝最新的 Powershell 指令程式。

2. 建議的唯一子網路單獨使用的應用程式服務環境建立。  如此一來，可確保套用至子網路 UDRs 會只開啟應用程式服務環境外寄流量。
3. **重要事項**︰ 不要**之後**下列設定步驟之後，直到部署應用程式服務環境。  如此一來，可確保輸出的網路連線有之前，先部署應用程式服務環境。

**步驟 1︰ 建立具名的路由表格**

下列程式碼片段會建立一個路由表格稱為 「 DirectInternetRouteTable 「 中西部美國 Azure 區域︰

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**步驟 2︰ 建立一或多個路由傳送表格中**

您將需要新增一或多個路由至 [路線] 資料表中，才能啟用輸出網際網路存取。  

設定輸出網際網路存取權的建議的作法是定義 0.0.0.0/0，如下所示的路由。
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

請記住，0.0.0.0/0 是較大的地址的範圍，因此會覆寫以更具體的位址範圍的 ExpressRoute 通知。  若要重新重複前面的建議，與 0.0.0.0/0 路由 UDR 應搭配只通知以及 0.0.0.0/0 ExressRoute 設定。 

或者，您可以下載 CIDR 範圍中使用 Azure 全面涵蓋所有內容和更新清單。  Xml 檔案包含所有 Azure IP 位址範圍是可從[Microsoft 下載中心][DownloadCenterAddressRanges]。  

但是請注意，這些範圍變更一段時間，因此強制執行保持同步的使用者定義路由定期手動更新。  此外，由於在單一 UDR 100 路由的預設上限，「 摘要 」 的 Azure IP 位址範圍以符合 100 傳送限制，您將需要由您 ExpressRoute 記住 UDR 定義比路由更詳細的路由需要通知。  


**步驟 3︰ 建立包含應用程式服務環境的子網路路由表格的關聯**

設定最後一個步驟是建立子網路路由表格之間的關聯應用程式服務環境部署的位置。  下列命令之間建立關聯 「 DirectInternetRouteTable 」 以 「 ASESubnet 」 最後會包含應用程式服務環境。

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**步驟 4︰ 最後一個步驟**

一旦路由表繫結至子網路，建議您先測試並確認想要的效果。  例如，將虛擬機器部署到子網路，並確認的︰


- Azure 和非 Azure 端點的外寄流量先前所述的 ExpressRoute 電路下**不**會傳送本文中。  務必以驗證這個問題，因為如果子網路的外寄流量仍強制通道內部部署，應用程式服務環境建立一定會失敗。 
- 結束點的 DNS 查閱上述所有解析正確。 

後確認上述步驟之後，您會需要刪除虛擬機器，因為子網路需要 「 空白 」 應用程式服務環境建立時間。
 
然後繼續建立的應用程式服務環境 ！

## <a name="getting-started"></a>快速入門
所有的文件及如何-的應用程式服務環境都是在[讀我檔案的應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)中使用。

若要開始使用應用程式服務環境，請參閱[應用程式服務環境簡介][IntroToAppServiceEnvironment]

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務][AzureAppService]。

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
