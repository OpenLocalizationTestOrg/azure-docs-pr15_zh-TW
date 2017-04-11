<properties 
   pageTitle="ExpressRoute 疑難排解指南-取得 ARP 資料表 |Microsoft Azure"
   description="此頁面上取得 ARP 表格 ExpressRoute 電路提供的指示"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="ganesr"/>

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>ExpressRoute 疑難排解指南-資源管理員部署模型中的快速 ARP 資料表

> [AZURE.SELECTOR]
[PowerShell-資源管理員](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell-傳統](expressroute-troubleshooting-arp-classic.md)

本文會引導您瞭解您的 ExpressRoute 電路 ARP 資料表的步驟。 

>[AZURE.IMPORTANT] 這份文件被為了幫助您診斷和修復簡單的問題。 它不是會取代 Microsoft 支援服務。 如果您無法解決問題使用如下所述的指導方針，您必須使用[Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證。

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>地址解析通訊協定 (ARP) 和 ARP 資料表
地址解析通訊協定 (ARP) 是在[RFC 826](https://tools.ietf.org/html/rfc826)中定義的階層 2 通訊協定。 用於 ARP 對應 ip 位址的乙太網路地址 （MAC 地址）。

ARP 表格提供對應的 ipv4 位址和 MAC 的特定的對等的地址。 ExpressRoute 電路對等的 ARP 表格會提供下列資訊 （主要和次要） 每個介面

1. 內部部署路由器介面的 ip 位址的 MAC 地址的對應
2. 對應到 MAC 位址 ExpressRoute 路由器介面 ip 位址
3. 對應的年齡

ARP 表格可以幫助您驗證第 2 層設定和疑難排解基本圖層 2 的連線發生問題。 

範例 ARP 資料表︰ 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


下一節提供有關檢視看到 ExpressRoute 邊緣路由器 ARP 表格的方式。 

## <a name="prerequisites-for-learning-arp-tables"></a>學習 ARP 表格的先決條件

請確定您有下列，進一步進度之前

 - 使用至少有一個對等設定有效的 ExpressRoute 電路。 電路必須完全設定連線提供者。 您 （或您的連線提供者） 必須已設定至少一個 peerings （Azure 私人、 Azure 公開與 Microsoft） 電路上。
 - 設定 peerings （Azure 私人、 Azure 公開與 Microsoft） 所用的 IP 位址範圍。 檢閱 ip 位址工作分派中的範例[ExpressRoute 路由需求頁面](expressroute-routing.md)以瞭解如何 ip 位址對應到您側邊，以及 ExpressRoute 側。 檢閱[ExpressRoute 等設定] 頁面](expressroute-howto-routing-arm.md)，您可以取得對等的設定資訊。
 - 從網路小組資訊 / 連線提供者上 MAC 地址的介面使用這些 IP 位址。
 - 您必須擁有最新的 PowerShell 模組的 Azure （版本 1.50 或更新版本）。

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>取得您 ExpressRoute 電路 ARP 資料表
本節提供指示檢視每個對等使用 PowerShell ARP 資料表的方式。 您或您的連線提供者必須已設定之前進行進一步對等。 每個電路有兩個路徑 （主要和次要）。 您可以檢查 ARP 資料表中，針對每個路徑的大小。

### <a name="arp-tables-for-azure-private-peering"></a>ARP Azure 私人對等的資料表
下列 cmdlet 提供 ARP 表格 Azure 私人對等

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

輸出範例如下所示的其中一個路徑

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP Azure 公用對等的資料表
下列 cmdlet 提供 ARP 表格 Azure 公用對等

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


輸出範例如下所示的其中一個路徑

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 對等 ARP 表格
下列 cmdlet 提供 ARP 表格 Microsoft 對等

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


輸出範例如下所示的其中一個路徑

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>如何使用這項資訊
對等的 ARP 資料表可以用來決定驗證第 2 層設定及連線。 本節提供概觀 ARP 表格的外觀在不同情況下。

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP 資料表操作的狀態 （預期狀態） 電路時

 - ARP 資料表必須 for 有效的 IP 位址和 MAC 地址與內部部署的項目和 Microsoft 側邊的類似項目。 
 - 內部部署的 ip 位址的最後一個八位元一律為奇數頁的數字。
 - Microsoft 的 ip 位址的最後一個八位元一律為偶數。
 - 相同 MAC 位址會出現在 Microsoft 側邊的所有 3 peerings （主要 / 次要）。 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP 表格何時內部部署 / 連線提供者側邊有問題

 - 只有一個項目會出現在 ARP 表格。 這會顯示 MAC 地址與 Microsoft 側用的 IP 位址之間的對應。 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] 開啟您的連線提供者，若要偵錯這類問題支援要求。 


### <a name="arp-table-when-microsoft-side-has-problems"></a>當 Microsoft 方有問題，會產生 ARP 資料表。

 - 您不會看到顯示的對等，如果 Microsoft 方有問題 ARP 表格。 
 -  使用[Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證。 指定您有第 2 層連線發生問題。 

## <a name="next-steps"></a>後續步驟

 - 驗證您的 ExpressRoute 電路 Layer 3 設定
     - 取得傳送摘要決定 BGP 工作階段的狀態 
     - 取得路由表，來決定哪些前置詞通知跨 ExpressRoute
 - 檢閱位元組 / 輸出驗證的資料傳輸
 - 如果仍有問題，請使用[Microsoft 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援票證。
