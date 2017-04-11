<properties 
    pageTitle="如何設定進階版 Azure Redis 快取的虛擬網路支援 |Microsoft Azure" 
    description="瞭解如何建立和管理您的進階版層 Azure Redis 快取執行個體的虛擬網路支援" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>如何設定虛擬網路支援的進階版 Azure Redis 快取
Azure Redis 快取有提供彈性的快取大小和功能，包括新的進階版層選擇不同的快取服務。

Azure Redis 快取進階層功能包括叢集、 保存及虛擬網路 (VNet) 支援。 VNet 是雲端中的私人網路。 當 VNet 設定 Azure Redis 快取執行個體時，它公開定址並不只可以從虛擬機器和 VNet 內的應用程式存取。 本文將說明如何設定進階版 Azure Redis 快取執行個體的虛擬網路支援。

>[AZURE.NOTE] Azure Redis 快取支援這兩個傳統和 ARM VNets。

其他進階快取功能的詳細資訊，請參閱[Azure Redis 快取進階版層簡介](cache-premium-tier-intro.md)。

## <a name="why-vnet"></a>為什麼 VNet 嗎？
[Azure 虛擬網路 (VNet)](https://azure.microsoft.com/services/virtual-network/)部署提供增強的安全性和隔離 Azure Redis 快取，以及子網路、 存取控制 」 原則，及其他功能，以進一步限制存取 Azure Redis 快取。

## <a name="virtual-network-support"></a>虛擬網路支援
在建立快取設定**新 Redis 快取**刀虛擬網路 (VNet) 支援。 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

所選的價格層進階版，您可以設定 Azure Redis 快取 VNet 整合選取 VNet 相同的訂閱和快取的位置。 若要使用新的 VNet，先依照[建立一個虛擬 Azure 入口網站的網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)或[建立虛擬網路 （傳統） 使用 Azure 入口網站](../virtual-network/virtual-networks-create-vnet-classic-portal.md)中的步驟建立，然後返回**新 Redis 快取**刀，可以建立並設定您的進階版快取。

若要設定新的快取的 VNet，按一下 [**虛擬網路**上的**新 Redis 快取**刀，，從下拉式清單中選取所要的 VNet。

![虛擬網路][redis-cache-vnet]

從**子網路**] 下拉式清單中，選取所要的子網路，指定所要的**靜態 IP 位址**。 如果您使用傳統的 VNet [**靜態 IP 位址**] 欄位，而且如果未指定，其中一個從所選的子網路選擇。

>[AZURE.IMPORTANT] ARM VNet 部署 Azure Redis 快取，快取必須不包含除了 Azure Redis 快取執行個體的任何其他資源的專用子網路中。 如果嘗試將 Azure Redis 快取部署至子網路 ARM VNet 包含其他資源，部署失敗。

![虛擬網路][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] 子網路中的前四個地址保留而無法使用。 如需詳細資訊，請參閱[是否有任何限制使用這些子網路中的 IP 位址？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

快取建立之後，您可以檢視 VNet 的設定，即可從**設定**刀**虛擬網路**。

![虛擬網路][redis-cache-vnet-info]


若要使用 VNet 時，連線到您 Azure Redis 快取執行個體，指定快取主機的名稱中的連接字串，如下列範例所示。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Azure 意指快取 VNet 常見問題集

下列清單包含有關 Azure Redis 快取的縮放比例常見問題的解答。

-   [什麼是 Azure Redis 的快取及 VNets 一些常見錯誤設定問題？](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-   [可以使用 VNets 標準或基本的快取嗎？](#can-i-use-vnets-with-a-standard-or-basic-cache)
-   [為什麼會建立意指快取失敗一些子網路，但不使用其他人？](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-   [裝載快取中 VNET 時，所有的快取功能運作正常？](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>什麼是 Azure Redis 的快取及 VNets 一些常見錯誤設定問題？

當在 VNet 裝載 Azure Redis 快取時，請使用下表中的連接埠。 如果這些連接埠遭到封鎖，快取可能無法正常運作。 使用 Azure Redis 快取中 VNet 時，有一或多個封鎖下列連接埠是最常見的錯誤設定問題。

| 連接埠     | 方向        | 傳輸通訊協定 | 用途                                                                           | 遠端 IP                           |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80、 443     | 輸出         | TCP                | Redis 相依性上 Azure 儲存體/PKI （網際網路）                                | *                                   |
| 53          | 輸出         | TCP/UDP            | Redis DNS (網際網路/VNet) 上的相依性                                         | *                                   |
| 6379、 6380  | 連入          | TCP                | 用戶端通訊意指，Azure 負載平衡                               | VIRTUAL_NETWORK AZURE_LOADBALANCER |
| 8443        | 連入/連出 | TCP                | 意指實作詳細資料                                                   | VIRTUAL_NETWORK                     |
| 8500        | 連入          | TCP/UDP            | Azure 負載平衡                                                              | AZURE_LOADBALANCER                  |
| 10221 10231 | 連入/連出 | TCP                | （可限制遠端端點 VIRTUAL_NETWORK） 意指實作詳細資料 | VIRTUAL_NETWORK AZURE_LOADBALANCER |
| 13000 13999 | 連入          | TCP                | 用戶端通訊 Redis 叢集，Azure 負載平衡                      | VIRTUAL_NETWORK AZURE_LOADBALANCER |
| 15000 15999 | 連入          | TCP                | 用戶端通訊 Redis 叢集，Azure 負載平衡                      | VIRTUAL_NETWORK AZURE_LOADBALANCER |
| 16001       | 連入          | TCP/UDP            | Azure 負載平衡                                                              | AZURE_LOADBALANCER                  |
| 20226       | 輸入 + 輸出 | TCP                | 意指叢集實作詳細資料                                          | VIRTUAL_NETWORK                     |


有 Azure Redis 快取可能不最初符合的虛擬網路中的網路連線需求。 Azure Redis 快取需要所有下列項目才能正常運作時虛擬網路中使用。

-  Azure 儲存體端點全球輸出的網路連線。 這包含位於 Azure Redis 快取的執行個體，為相同的區域，以及儲存端點位於**其他**Azure 區域的結束點。 Azure 儲存體端點解決下的 [下列 DNS 網域︰ *table.core.windows.net*、 *blob.core.windows.net*、 *queue.core.windows.net*及*file.core.windows.net*。 
-  *Ocsp.msocsp.com*、 *mscrl.microsoft.com*和*crl.microsoft.com*輸出的網路連線。 需要連線支援 SSL 功能。
-  虛擬網路的 DNS 設定必須能夠解決的所有結束點和舊版點中所提及的網域。 這些 DNS 符合需求，可確保設定和維護的虛擬網路有效的 DNS 基礎結構。



### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>可以使用 VNets 標準或基本的快取嗎？

VNets 僅使用進階版的快取。

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>為什麼會建立意指快取失敗一些子網路，但不使用其他人？

如果您部署 ARM VNet Azure Redis 快取，快取必須在固定的子網路包含其他的資源類型。 如果嘗試部署 Azure Redis 快取到 ARM VNet 子網路包含其他資源，部署失敗。 您必須先刪除現有的資源內子網路，才能建立新的意指快取。

只要您具備足夠可用的 IP 位址，您可以將多個的資源類型部署至傳統的 VNet 中。

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>裝載快取中 VNET 時，所有的快取功能運作正常？

如果您的快取是 VNET 的一部分，VNET 中的用戶端可以存取快取。 如此一來，下列快取管理功能無法運作，這一次。

-   Redis 主控台-Redis 主控台使用裝載於不屬於您 VNET 的 Vm 意指 cli.exe 用戶端，因為其無法連線至您的快取。


## <a name="use-expressroute-with-azure-redis-cache"></a>使用 Azure 意指快取的 ExpressRoute

客戶可以連線的[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)電路虛擬網路基礎結構，因此將其內部部署網路延伸至 Azure。 

根據預設，新建立的 ExpressRoute 電路通知的預設路由，可讓輸出網際網路連線。 使用此設定時，用戶端應用程式可連線至其他 Azure 的結束點，包括 Azure Redis 快取。

不過常見的客戶設定是定義自己強制輸出網際網路流量改為內部部署的預設路由 (0.0.0.0 / 0)。 此流量側面中斷與 Azure Redis 快取的連線，因為輸出流量任一封鎖內部部署，或 NAT 就像不再使用各種 Azure 結束點的地址的無法辨識組合。

解決方法是定義一個 （或更多） 使用者定義路由 (UDRs) 包含 Azure Redis 快取的子網路上。 UDR 定義子網路特定路由，而不是預設路由，將會套用。

如果可能的話，建議您使用下列設定︰

- ExpressRoute 設定通知 0.0.0.0/0，依預設強制通道所有外寄流量內部部署。
- 套用到包含 Azure Redis 快取的子網路 UDR 定義 0.0.0.0/0 與網際網路 （例如，是本文中向下久） 的下一個躍點類型。

合併的效果，這些步驟是，子網路層級 UDR 會優先於 ExpressRoute 強制通道，以確保從 Azure Redis 快取的輸出網際網路存取。

雖然連線至 Azure Redis 快取執行個體從內部部署使用 ExpressRoute 應用程式不是一般的使用案例效能 （適用於獲得最佳效能 Azure Redis 快取用戶端應該 Azure Redis 快取為相同的區域），因為輸出網路路徑無法通過內部公司 proxy，這種情況下，也可以是強制通道內部部署。 這樣會從 Azure Redis 快取變更輸出網路流量的有效 NAT 地址。 Azure Redis 快取的 NAT 地址執行個體的輸出網路流量時，會導致連線失敗，許多以上所列的結束點。 這會導致失敗 Azure Redis 快取建立嘗試。

**重要事項︰** 路由定義在 UDR，**必須**是不夠優先於任何路由 ExpressRoute 設定通知。 在下列使用主要 0.0.0.0/0 位址範圍，因此您可以可能不小心覆寫路由廣告使用更具體的地址範圍。

**非常重要︰** Azure Redis 快取不支援 ExpressRoute 設定**不正確跨-通知中的公用等路徑至私人對等的路徑**。 已設定，請公用對等的 ExpressRoute 設定會從 Microsoft 接收路由廣告的大量的 Microsoft Azure IP 位址範圍。 如果這些位址範圍是不正確跨-通知私人對等的路徑，最後的結果是從 Azure Redis 快取執行個體的子網路的所有輸出網路封包正確強制-建立通道客戶的內部部署網路基礎結構。 這個網路流量線 Azure Redis 快取。 若要停止從公用等路徑的跨廣告路由至私人對等的路徑是解決這個問題。

使用本[概觀](../virtual-network/virtual-networks-udr-overview.md)背景資訊使用者定義的路徑。 

如需有關 ExpressRoute 的詳細資訊，請參閱[ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>後續步驟
瞭解如何使用更多進階快取功能。

-   [Azure Redis 快取進階版層簡介](cache-premium-tier-intro.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

