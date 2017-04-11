<properties
   pageTitle="ExpressRoute 疑難排解指南︰ 取得 ARP 資料表 |Microsoft Azure"
   description="本頁面提供指示 ExpressRoute 電路取得 ARP 的資料表。"
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

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>ExpressRoute 疑難排解指南︰ 取得 ARP 傳統部署模型中的資料表

> [AZURE.SELECTOR]
[PowerShell-資源管理員](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell-傳統](expressroute-troubleshooting-arp-classic.md)

本文會引導您取得您的 Azure ExpressRoute 電路的地址解析通訊協定 (ARP) 資料表的步驟。

>[AZURE.IMPORTANT] 這份文件被為了幫助您診斷和修復簡單的問題。 它不是會取代 Microsoft 支援服務。 如果您無法使用下列指示解決問題，請使用[Microsoft Azure 說明 + 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援要求。

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>地址解析通訊協定 (ARP) 和 ARP 資料表
ARP 是[RFC 826](https://tools.ietf.org/html/rfc826)中定義的第 2 層通訊協定。 ARP 用來將乙太網路地址 （MAC 地址） 對應到 IP 位址。

ARP 表格提供對應的 IPv4 位址和 MAC 的特定的對等的地址。 ExpressRoute 電路對等的 ARP 表格提供每個介面 （主要和次要） 的下列資訊︰

1. 對應至 MAC 地址的內部部署路由器介面 IP 位址
2. 對應至 MAC 地址的 ExpressRoute 路由器介面 IP 位址
3. 天數的對應

ARP 表格可協助驗證第 2 層設定與基本的第 2 層連線問題進行疑難排解。

下列是 ARP 表格的範例︰

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


下一節提供如何檢視的 ExpressRoute 邊緣路由器看到 ARP 資料表的相關資訊。

## <a name="prerequisites-for-using-arp-tables"></a>使用 ARP 表格的先決條件

請確定您有下列，再繼續︰

 - 有效的 ExpressRoute 電路設定至少有一個對等。 電路必須完全設定連線提供者。 您 （或您的連線提供者） 必須至少一個 peerings （Azure 的私人、 Azure 公用或 Microsoft） 上設定此電路。

 - 用於設定 peerings （Azure 的私人、 Azure 公用和 Microsoft） 的 IP 位址範圍。 檢閱 IP 位址工作分派中的範例[ExpressRoute 路由需求頁面](expressroute-routing.md)以瞭解如何 IP 位址對應到您 aise 及 ExpressRoute 側邊的介面。 您可以檢閱[ExpressRoute 等設定] 頁面](expressroute-howto-routing-classic.md)，以取得對等設定的相關資訊。

 - 從網路小組或連線提供者的相關資訊的 MAC 地址會使用這些 IP 位址的介面。

 - Azure （1.50 或更新版本） 的最新的 Windows PowerShell 模組。

## <a name="arp-tables-for-your-expressroute-circuit"></a>您的 ExpressRoute 電路 ARP 表格
本節提供有關如何檢視使用 PowerShell 對等的每一種 ARP 資料表的相關指示。 在繼續之前，必須設定的對等您或您的連線提供者。 每個電路有兩個路徑 （主要和次要）。 您可以檢查 ARP 資料表中，針對每個路徑的大小。

### <a name="arp-tables-for-azure-private-peering"></a>ARP Azure 私人對等的資料表
下列 cmdlet 提供 ARP 表格 Azure 私人對等︰

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

以下是範例輸出的其中一個路徑︰

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP Azure 公用對等的資料表︰
下列 cmdlet 提供 ARP 表格 Azure 公用對等︰

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

以下是範例輸出的其中一個路徑︰

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


以下是範例輸出的其中一個路徑︰

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 對等 ARP 表格
下列 cmdlet 提供適用於 Microsoft 對等 ARP 資料表︰

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


輸出範例所示的其中一個路徑︰

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>如何使用這項資訊
對等的 ARP 資料表可以用於驗證第 2 層設定及連線。 本節提供 ARP 表格中的外觀不同情況概觀。

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>電路作業 （預期的） 狀態時，ARP 表格

 - ARP 表格中有 for 有效 IP 和 MAC 的地址，與內部部署的項目和 Microsoft 側邊的類似項目。
 - 內部部署的 IP 位址的最後一個八位元永遠是奇數頁的數字。
 - Microsoft 的 IP 位址的最後一個八位元組一律為偶數。
 - 相同 MAC 位址會出現在 Microsoft 端的所有三個 peerings （主要/次要）。


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>內部部署或連線提供者側時有問題時的 ARP 表格

 只有一個項目會出現在 ARP 表格。 它會顯示 MAC 地址與 Microsoft 方用的 IP 位址之間的對應。

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] 如果您遇到問題，像這樣，開啟支援要求的連線提供者來解決問題。


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>當 Microsoft 方有問題的 ARP 表格

 - 您不會看到顯示的對等，如果 Microsoft 方有問題 ARP 表格。
 -  使用[Microsoft Azure 說明 + 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援要求。 指定您有第 2 層連線發生問題。

## <a name="next-steps"></a>後續步驟

 - 驗證您的 ExpressRoute 電路 Layer 3 設定︰
     - 取得路由摘要決定 BGP 工作階段的狀態。
     - 取得傳送資料表，以決定哪些前置詞 ExpressRoute 過通知。
 - 驗證資料傳輸和縮小檢閱位元組。
 - 如果仍有問題，請使用[Microsoft Azure 說明 + 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)開啟支援要求。
