<properties
    pageTitle="Microsoft Azure 訂閱及服務限制，配額和限制"
    description="提供一般 Azure 訂閱及服務限制，配額、 限制式的清單。 這包含有關如何增加限制，以及最大值。"
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 訂閱及服務限制，配額和限制

## <a name="overview"></a>概觀

這份文件指定一些最常見的 Microsoft Azure 限制。 此目前並未涵蓋所有 Azure 服務。 一段時間，這些限制會展開和覆蓋其他平台的更新。

請若要進一步瞭解 Azure 價格[Azure 價格的概觀](https://azure.microsoft.com/pricing/)，造訪。 那里，您可以在其中估計成本使用[價格計算機](https://azure.microsoft.com/pricing/calculator/)或瀏覽定價詳細資料頁面的服務 (例如， [Windows Vm](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows))。

> [AZURE.NOTE] 如果您想要提高上方**的預設限制**限制，您可以[開啟免費的線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 限制無法升級上方下方資料表中的**最大限制**值。 如果沒有**最大限制**的資料行，然後指定的資源沒有可調整的限制。

## <a name="limits-and-the-azure-resource-manager"></a>限制和 Azure 資源管理員

您現在可以合併多個 Azure 中的資源到單一 Azure 資源群組。 使用資源群組，一次是全域的限制成為管理層級地區與 Azure 資源管理員。 如需有關 Azure 資源群組的詳細資訊，請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)。

下列限制在新的資料表已新增至使用 Azure 資源管理員時，反映任何限制的差異。 例如，沒有**訂閱限制**表格和**訂閱限制-Azure 資源管理員**資料表。 時的限制會套用至這兩種案例中，只會顯示在第一個資料表中。 除非指示，否則限制是全域的跨所有的區域。

> [AZURE.NOTE] 請務必強調的配額 Azure [資源] 群組中的資源的每個區域可以存取您的訂閱，而不是每個訂閱，因為服務管理配額。 現在就讓我們的範例使用核心配額。 如果您要邀請的核心支援配額增加，您必須決定多少核心您想要使用哪一個區域中，然後再進行的數量與您想要的區域的 [Azure 資源群組的核心配額的特定的要求。 因此，如果您需要使用 30 核心西歐中的那里; 執行應用程式您應該特別要求 30 核心西歐中。 但您將不會有任何其他區域增加核心配額--僅限西歐必須 30 核心配額。
<!-- -->
如此一來，您可能會很有幫助您判斷您 Azure 資源群組的配額需要在任何一個區域中，您工作負載的並要求到您考慮部署各區域中的量。 請參閱[部署問題的疑難排解](./resource-manager-common-deployment-errors.md)的更多協助探索您目前的配額的特定區域。

## <a name="service-specific-limits"></a>服務的限制

- [Active Directory](#active-directory-limits)
- [API 管理](#api-management-limits)
- [應用程式服務](#app-service-limits)
- [應用程式的閘道器](#application-gateway-limits)
- [應用程式的深入見解](#application-insights-limits)
- [自動化](#automation-limits)
- [Azure 意指快取](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [備份](#backup-limits)
- [批次](#batch-limits)
- [BizTalk 服務](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [雲端服務](#cloud-services-limits)
- [資料工廠](#data-factory-limits)
- [資料湖狀況分析](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [事件集線器](#event-hubs-limits)
- [IoT 中心](#iot-hub-limits)
- [索引鍵保存庫](#key-vault-limits)
- [媒體服務](#media-services-limits)
- [行動裝置的互動](#mobile-engagement-limits)
- [行動裝置的服務](#mobile-services-limits)
- [監控](#monitoring-limits)
- [多重因素驗證](#multi-factor-authentication)
- [網路](#networking-limits)
- [通知中心服務](#notification-hub-service-limits)
- [操作的深入見解](#operational-insights-limits)
- [資源群組](#resource-group-limits)
- [排程器](#scheduler-limits)
- [搜尋](#search-limits)
- [服務匯流排](#service-bus-limits)
- [網站復原](#site-recovery-limits)
- [SQL 資料庫](#sql-database-limits)
- [儲存空間](#storage-limits)
- [StorSimple 系統](#storsimple-system-limits)
- [資料流狀況分析](#stream-analytics-limits)
- [訂閱](#subscription-limits)
- [流量管理員](#traffic-manager-limits)
- [虛擬機器](#virtual-machines-limits)
- [虛擬機器縮放設定](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>訂閱限制
#### <a name="subscription-limits"></a>訂閱限制
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>訂閱限制-Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，就會套用下列限制。 下面就不會列出維持不變與 Azure 資源管理員的限制。 請參閱這些限制的前一個資料表。

處理限制在資源管理員的要求的相關資訊，請參閱[節流資源管理員的要求](resource-manager-request-limits.md)。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>資源群組限制

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>虛擬機器限制
#### <a name="virtual-machine-limits"></a>虛擬機器限制
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>虛擬機器限制-Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，就會套用下列限制。 下面就不會列出維持不變與 Azure 資源管理員的限制。 請參閱這些限制的前一個資料表。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>虛擬機器縮放比例設定限制

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>網路限制

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>網路限制
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>應用程式閘道限制

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>流量管理員限制

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>DNS 限制

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>儲存限制

儲存帳戶限制的其他詳細資訊，請參閱[Azure 儲存體延展性和效能目標](../articles/storage/storage-scalability-targets.md)。

#### <a name="storage-service-limits"></a>服務的儲存限制

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>虛擬機器磁碟限制

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

如需詳細資訊，請參閱[虛擬機器大小](../articles/virtual-machines/virtual-machines-linux-sizes.md)。

**標準儲存帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**進階版儲存的帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>資源提供者的儲存限制

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>雲端服務限制

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>應用程式服務限制
下列應用程式服務限制包含 Web 應用程式與 Mobile 應用程式、 API 應用程式，邏輯應用程式的限制。

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>排程器限制

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>批次限制

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>BizTalk 服務限制
下表顯示 Azure Biztalk 服務限制。

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>DocumentDB 限制

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

使用星號 （*），[可以調整連絡 Azure 支援](./documentdb/documentdb-increase-limits.md)所列的配額。

### <a name="mobile-engagement-limits"></a>行動互動限制

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>搜尋限制

定價層決定容量和您的搜尋服務的限制。 層包括︰

- *免費*多租用戶服務，供評估與小型開發專案其他 Azure 訂閱者與共用。
- *基本*會提供專用的計算資源的生產負載在較小的小數位數，與高度可用的查詢工作負載的三個複本。
- *標準 （S1、 S2、 S3，S3 密度）*適用於較大的生產工作量。 讓您可以選擇特定案例的資源設定內的標準層存在於多個層級。

**每個訂閱的限制**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**每個搜尋服務的限制**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

有關微調其他限制，包括文件大小，查詢每個第二個、 鍵、 要求和回應，請參閱[Azure 搜尋服務限制](search/search-limits-quotas-capacity.md)。

### <a name="media-services-limits"></a>媒體服務限制

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Cdn 到底限制

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>行動電話服務限制

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>監控限制

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>通知中心服務限制

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>事件集線器限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>服務匯流排限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT 中心限制

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>資料工廠限制

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>資料湖分析限制
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>資料流分析限制
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory 限制

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Azure RemoteApp 限制

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 系統限制

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>操作的深入見解限制

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>備份的限制

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>網站復原限制

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>應用程式的深入見解限制

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API 管理限制

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Azure Redis 快取限制

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>索引鍵保存庫限制

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>多重因素驗證
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>自動化限制
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>SQL 資料庫限制

SQL 資料庫限制，請參閱[SQL 資料庫資源限制](sql-database/sql-database-resource-limits.md)。

## <a name="see-also"></a>另請參閱

[了解 Azure 限制也會增加](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[虛擬機器和 Azure 雲端服務的大小](virtual-machines/virtual-machines-linux-sizes.md)

[雲端服務的大小](cloud-services/cloud-services-sizes-specs.md)
