<properties 
    pageTitle="如何設定 Azure Redis 快取 |Microsoft Azure"
    description="了解 Azure Redis 快取的預設意指設定，並瞭解如何設定您的 Azure Redis 快取執行個體"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>如何設定 Azure Redis 快取

本主題說明如何檢閱及更新您的 Azure Redis 快取執行個體的設定，並說明 Azure Redis 快取執行個體的預設意指伺服器設定。

>[AZURE.NOTE] 設定和使用進階快取功能的詳細資訊，請參閱[如何設定進階版 Azure Redis 快取持續性](cache-how-to-premium-persistence.md)、[如何設定叢集進階版 Azure Redis 快取](cache-how-to-premium-clustering.md)，以及[如何設定進階版 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)。

## <a name="configure-redis-cache-settings"></a>設定意指快取設定

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis 快取**設定**刀提供下列設定。

![Redis 快取設定](./media/cache-configure/redis-cache-settings.png)



-   [支援與疑難排解設定](#support-amp-troubleshooting-settings)
-   [一般設定](#general-settings)
    -   [屬性](#properties)
    -   [便捷鍵](#access-keys)
    -   [進階的設定](#advanced-settings)
    -   [Redis 快取顧問](#redis-cache-advisor)
-   [縮放設定](#scale-settings)
    -   [價格層](#pricing-tier)
    -   [Redis 叢集大小](#cluster-size)
-   [資料管理設定](#data-management-settings)
    -   [Redis 資料持續性](#redis-data-persistence)
    -   [匯入/匯出](#importexport)
-   [管理設定](#administration-settings)
    -   [重新啟動](#reboot)
    -   [排程更新](#schedule-updates)
-   [診斷程式設定](#diagnostics-settings)
-   [網路設定](#network-settings)
-   [資源管理設定](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>支援與疑難排解設定

**支援 + 疑難排解**區段中的設定為您提供解決問題的快取的選項。

![支援 + 疑難排解](./media/cache-configure/redis-cache-support-troubleshooting.png)

按一下 [**診斷和解決問題**提供的一般問題及策略解決問題。

按一下 [檢視您的快取上執行的動作的**活動記錄**。 您也可以使用篩選，以展開此檢視包含其他的資源。 如需有關使用稽核記錄檔的詳細資訊，請參閱[檢視事件和稽核記錄檔](../monitoring-and-diagnostics/insights-debugging-with-events.md)和[稽核作業與資源管理員](../resource-group-audit.md)。 如需有關如何監視 Azure Redis 快取事件的詳細資訊，請參閱[作業和通知](cache-how-to-monitor.md#operations-and-alerts)。

**資源狀況**會檢查您的資源，並會告訴您是否正在執行如預期般。 如需有關 Azure 資源狀況服務的詳細資訊，請參閱[Azure 資源狀況概觀](../resource-health/resource-health-overview.md)。

>[AZURE.NOTE] 資源狀況是目前無法裝載於虛擬網路 Azure Redis 快取執行個體的狀況報告。 如需詳細資訊，請參閱[執行所有快取功能的運作方式裝載快取中 VNET 時？](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

按一下 [**新增支援要求**]，即可開啟 [快取的支援要求。

## <a name="general-settings"></a>一般設定

在 [**一般**] 區段中的設定可讓您存取並設定您的快取的下列設定。

![一般設定](./media/cache-configure/redis-cache-general-settings.png)

-   [屬性](#properties)
-   [便捷鍵](#access-keys)
-   [進階的設定](#advanced-settings)
-   [Redis 快取顧問](#redis-cache-advisor)

### <a name="properties"></a>屬性

按一下 [**屬性**]，以檢視您的快取，包括快取端點和連接埠的相關資訊。

![Redis 快取屬性](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>便捷鍵

按一下**便捷鍵**以檢視或重新產生的快取便捷鍵。 連線到您的快取的用戶端以及 [主機名稱] 和 [從**屬性**刀的連接埠使用這些按鍵。

![Redis 快取便捷鍵](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>進階的設定

**進階設定**刀是設定下列設定。

-   [存取連接埠](#access-ports)
-   [Maxmemory 原則和 maxmemory 保留](#maxmemory-policy-and-maxmemory-reserved)
-   [再通知 （進階設定）](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>存取連接埠

根據預設，非 SSL access 會停用新的快取。 若要啟用的非 SSL 連接埠，按一下 [**否**]，請在 [**進階的設定刀****透過 SSL 只允許存取**，然後再按一下 [**儲存**。

![意指快取存取連接埠](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Maxmemory 原則和 maxmemory 保留

**進階設定**刀**Maxmemory 原則**] 和 [ **maxmemory 保留**設定設定快取的記憶體原則。 **Maxmemory**原則設定快取的收回原則和**maxmemory 保留**設定保留做為非快取程序的記憶體。

![Redis 快取 Maxmemory 原則](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory 原則**可讓您選擇下列收回原則。

-   動態 lru-這是預設值。
-   allkeys lru
-   動態隨機
-   allkeys 隨機
-   動態 ttl
-   noeviction

如需有關 maxmemory 原則的詳細資訊，請參閱[收回原則](http://redis.io/topics/lru-cache#eviction-policies)。

**Maxmemory 保留**設定設定為非快取作業，例如複寫容錯移轉時保留 MB 的記憶體。 它也可當有高片段比例。 將此值，可讓您有更為一致的意指伺服器體驗，當您載入不盡相同。 此值應該是撰寫大量的工作負載的設定。 記憶體保留給這類作業時，無法使用的快取資料的儲存空間。

>[AZURE.IMPORTANT] **Maxmemory 保留**設定只適用於標準，及進階版快取。

### <a name="keyspace-notifications-advanced-settings"></a>再通知 （進階設定）

Redis 的再通知設定**進階設定**刀。 再通知讓用戶端發生特定事件時收到通知。

![Redis 快取進階設定](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] 再通知和**通知再事件**設定只適用於標準，及進階版快取。

如需詳細資訊，請參閱[Redis 再通知](http://redis.io/topics/notifications)。 程式碼範例，請參閱[好](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)樣本中的[KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs)檔案。

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis 快取顧問

**建議**刀會顯示您的快取的建議。 在標準作業時，會不顯示任何建議。 

![建議](./media/cache-configure/redis-cache-no-recommendations.png)

如果您的快取，例如高記憶體使用量、 網路頻寬或伺服器載入的作業過程中發生任何條件，通知會顯示**Redis 快取**刀上。

![建議](./media/cache-configure/redis-cache-recommendations-alert.png)

在**建議**刀找的詳細資訊。

![建議](./media/cache-configure/redis-cache-recommendations.png)

您可以監視下列指標[監控圖表](cache-how-to-monitor.md#monitoring-charts)和[使用圖表](cache-how-to-monitor.md#usage-charts)的章節的**Redis 快取**刀上。

每一個價格的層級會有不同的用戶端連線、 記憶體和頻寬的限制。 如果您的快取接近這些眼的最大容量持續一段時間的會建立建議。 如需有關的指標及檢閱者**建議**工具的限制的詳細資訊，請參閱下表。

| Redis 快取公制      | 如需詳細資訊，請參閱                                                  |
|-------------------------|---------------------------------------------------------------------------|
| 網路頻寬使用量 | [快取效能-可用的頻寬](cache-faq.md#cache-performance) |
| 連線的用戶端       | [預設意指伺服器設定-請對 [maxclients](#maxclients)            |
| 伺服器負載             | [使用圖表-Redis 伺服器負載](cache-how-to-monitor.md#usage-charts)  |
| 記憶體使用量            | [快取效能-大小](cache-faq.md#cache-performance)                |

若要升級您的快取，請按一下要變更的[價格層](#pricing-tier)快取不按比例縮放的 [**立即升級**。 更多選擇價格層的詳細資訊，請參閱[哪些意指快取服務和大小我應該使用？](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

## <a name="scale-settings"></a>縮放設定

在 [**縮放比例**] 區段中的設定可讓您存取並設定您的快取的下列設定。

![網路](./media/cache-configure/redis-cache-scale.png)

-   [價格層](#pricing-tier)
-   [Redis 叢集大小](#cluster-size)

### <a name="pricing-tier"></a>價格層

按一下 [檢視或變更您的快取的價格層**價格層**]。 如需有關按比例縮放的詳細資訊，請參閱[如何刻度 Azure Redis 快取](cache-how-to-scale.md)。

![Redis 價格層的快取](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Redis 叢集大小

按一下 [ **（預覽版本） Redis 叢集大小**]，若要變更的叢集大小為執行進階版快取啟用叢集。

>[AZURE.NOTE] 請注意，同時 Azure Redis 快取進階版層發行，現在開放 Redis 叢集大小功能目前預覽中。

![Redis 叢集大小](./media/cache-configure/redis-cache-redis-cluster-size.png)

若要變更的叢集大小，請使用滑桿或**晶怪計數**的 [文字] 方塊中輸入數字 1 和 10 之間，按一下**[確定**] 儲存。

>[AZURE.IMPORTANT] 意指叢集只適用於付費的快取。 如需詳細資訊，請參閱[如何設定叢集進階版 Azure Redis 快取](cache-how-to-premium-clustering.md)。


## <a name="data-management-settings"></a>資料管理設定

在 [**資料管理**] 區段中的設定可讓您存取並設定您的快取的下列設定。

![資料管理](./media/cache-configure/redis-cache-data-management.png)

-   [Redis 資料持續性](#redis-data-persistence)
-   [匯入/匯出](#importexport)

### <a name="redis-data-persistence"></a>Redis 資料持續性

按一下 [啟用、 停用或設定您的進階版快取資料保存 [ **Redis 資料保存**。

![Redis 資料持續性](./media/cache-configure/redis-cache-persistence-settings.png)

若要啟用意指持續性，請按一下 [啟用 RDB （意指資料庫） 備份的 [**啟用**]。 若要停用意指持續性，請按一下 [**停用**]。

若要設定的備份時間間隔，請從下拉式清單中選取**備份的頻率**。 選擇包含**15 分鐘**、 **30 分鐘**、 **60 分鐘**、 **6 小時**、 **12 小時**及**24 小時的時間**。 這段開始計算向下，在先前的備份作業成功完成後，經過時啟動新的備份。

按一下以選取要使用，請儲存帳戶**儲存的帳戶**，然後選擇 [**主索引鍵**] 或 [**次要鍵**使用**儲存空間鍵**下拉式]。 您必須選擇儲存帳戶快取中，為相同的區域，建議**進階版儲存**帳戶，因為進階版儲存較高處理量。 隨時儲存持續帳戶並重新產生金鑰，所以您必須重新從**儲存鍵**下拉式選擇所需的索引鍵。

按一下**[確定**] 儲存持續設定。

>[AZURE.IMPORTANT] 意指資料保存只適用於付費的快取。 如需詳細資訊，請參閱[如何設定持續進階版 Azure Redis 快取](cache-how-to-premium-persistence.md)。

### <a name="importexport"></a>匯入/匯出

匯入/匯出是 Azure Redis 快取資料管理作業可讓您將 Azure Redis 快取資料匯入或匯出 Azure Redis 快取中的資料，以匯入及匯出從進階版快取 Redis 快取資料庫 (RDB) 快照，以在 Azure 儲存體帳戶中的頁面 blob。 這可讓您移轉之間不同的 Azure Redis 快取執行個體，或填入資料之前使用快取。

匯入可從任何執行中的任何雲端或環境，包括意指 Linux、 Windows，或任何雲端提供者，例如 Amazon Web 服務與其他人所執行的意指伺服器開啟意指相容 RDB 檔案。 匯入資料時輕鬆建立預先填入資料快取。 匯入程序期間 Azure Redis 快取從 Azure 儲存體載入記憶體 RDB 檔案，然後插入快取中的按鍵。

匯出可讓您匯出 Azure Redis 快取以 Redis 相容 RDB 檔案中儲存的資料。 若要將資料從 Azure Redis 快取執行個體之一，到另一個或另一個意指伺服器，您可以使用這項功能。 暫存檔案會建立 VM 上在匯出程序的裝載 Azure Redis 快取伺服器執行個體，並指定的儲存帳戶上傳檔案。 匯出作業完成後任一狀態為成功或失敗，請刪除暫存檔案。

>[AZURE.IMPORTANT] 匯入/匯出只適用於進階版層快取。 如需詳細資訊與指示，請參閱[匯入及匯出 Azure Redis 快取中的資料](cache-how-to-import-export-data.md)。


## <a name="administration-settings"></a>管理設定

在 [**管理**] 區段中的設定可讓您的進階版快取中執行下列管理工作。 

![管理](./media/cache-configure/redis-cache-administration.png)

-   [重新啟動](#reboot)
-   [排程更新](#schedule-updates)

>[AZURE.IMPORTANT] 此區段中的設定只適用於進階版層快取。

### <a name="reboot"></a>重新啟動

**重新啟動**刀可讓您重新啟動您的快取的一或多個節點。 這可讓您測試您的應用程式，恢復失敗。

![重新啟動](./media/cache-configure/redis-cache-reboot.png)

如果您有啟用叢集進階版快取，您可以選取哪些擊碎快取重新啟動電腦。

![重新啟動](./media/cache-configure/redis-cache-reboot-cluster.png)

若要重新啟動一或多個節點的快取，選取所要的節點，然後按一下 [**重新啟動**。 如果您有啟用叢集進階版快取，請選取 [重新開機，然後按一下 [**重新啟動**shard(s)]。 請稍候幾分鐘，選取的節點重開機，與線上備份稍後幾分鐘。

>[AZURE.IMPORTANT] 重新啟動只適用於進階版層快取。 如需詳細資訊與指示，請參閱[Azure Redis 快取管理-重新啟動](cache-administration.md#reboot)。

### <a name="schedule-updates"></a>排程更新

**排程更新**刀可讓您指定的意指伺服器更新您的快取進行的維修作業] 視窗。 

>[AZURE.IMPORTANT] 請注意，進行的維修作業] 視窗僅適用於 Redis 伺服器的更新，並不到任何 Azure 更新或更新的 Vm 主控快取的作業系統。

![排程更新](./media/cache-configure/redis-schedule-updates.png)

若要指定進行的維修作業] 視窗，請核取您要的天指定每一天，進行的維修作業] 視窗開始小時，並按一下**[確定**]。 請注意，進行的維修作業] 視窗時間以 utc 表示。 

>[AZURE.IMPORTANT] 排程更新只適用於進階版層快取。 如需詳細資訊與指示，請參閱[Azure Redis 快取管理-排程更新](cache-administration.md#schedule-updates)。

## <a name="diagnostics-settings"></a>診斷程式設定

[**診斷**] 區段，可讓您針對 Redis 快取設定診斷。

![診斷程式](./media/cache-configure/redis-cache-diagnostics.png)

按一下 [[設定儲存帳戶](cache-how-to-monitor.md#enable-cache-diagnostics)用來儲存快取診斷程式的 [**診斷**]。

![Redis 快取診斷程式](./media/cache-configure/redis-cache-diagnostics-settings.png)

若要[檢視指標](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts)快取和**通知的規則**設定[提醒的規則](cache-how-to-monitor.md#operations-and-alerts)，按一下 [ **Redis 指標**]。

如需有關 Azure Redis 快取診斷的詳細資訊，請參閱[如何監視 Azure Redis 快取](cache-how-to-monitor.md)。


## <a name="network-settings"></a>網路設定

在 [**網路**] 區段中的設定可讓您存取並設定您的快取的下列設定。

![網路](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] 虛擬網路設定只適用於已設定進階版快取的快取建立期間 VNET 支援。 如需建立進階版快取 VNET 支援及更新其設定，請參閱[如何設定虛擬網路支援進階版 Azure Redis 快取](cache-how-to-premium-vnet.md)。

## <a name="resource-management-settings"></a>資源管理設定

![資源管理](./media/cache-configure/redis-cache-resource-management.png)

[**標籤**] 區段中，可協助您組織您的資源。 如需詳細資訊，請參閱[使用標籤以組織 Azure 資源](../resource-group-using-tags.md)。

[**鎖定**] 區段中，可讓您鎖定要防止其他使用者不小心刪除或修改重要的資源，您組織中訂閱、 資源群組或資源。 如需詳細資訊，請參閱[鎖定資源與 Azure 資源管理員](../resource-group-lock-resources.md)。

[**使用者**] 區段來協助組織符合其存取管理需求，只要和確實 Azure 入口網站中的角色型存取控制 (RBAC) 提供支援。 如需詳細資訊，請參閱[Azure 入口網站中的角色型存取控制](../active-directory/role-based-access-control-configure.md)。

按一下 [建立和匯出您的部署資源未來部署範本的 [**匯出範本**]。 如需使用範本的詳細資訊，請參閱[使用 Azure 資源管理員範本部署資源](../resource-group-template-deploy.md)。

## <a name="default-redis-server-configuration"></a>預設意指伺服器設定

使用下列的預設意指設定值設定新的 Azure Redis 快取執行個體。

>[AZURE.NOTE] 此區段中的設定無法使用變更`StackExchange.Redis.IServer.ConfigSet`方法。 如果這個方法呼叫的其中一個本節中的命令，就會發生類似以下的例外狀況︰  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>可設定，例如 [**最大值的記憶體原則**的任何值都可透過 Azure 入口網站或命令列管理工具例如 Azure CLI 或 PowerShell 設定。

|設定|預設值|描述|
|---|---|---|
|資料庫|16|資料庫的預設數字是 16，但您可以設定不同的數字，根據價格層。<sup>1</sup>的預設資料庫 DB 0，您可以選取每個連線的基礎使用另一個`connection.GetDatabase(dbid)`dbid 哪裡之間的數字`0`和`databases - 1`。|
|請對 [maxclients|取決於價格層<sup>2</sup>|這是連線的用戶端同時允許的最大數目。 一旦達到限制意指會關閉傳送錯誤最大的數用戶端接聽 」 的所有新連線。|
|maxmemory 原則|動態 lru|Maxmemory 原則是設定意指會選取處理移除達到 maxmemory （提供您選取當您建立快取的快取大小）。 Azure Redis 快取與預設值是動態-lru，這與使用 LRU 演算法設定到期移除機碼。 Azure 入口網站中，可以設定此設定。 如需詳細資訊，請參閱[Maxmemory 原則和 maxmemory 保留](#maxmemory-policy-and-maxmemory-reserved)。|
|maxmemory 範例|3|LRU 和最小的 TTL 演算法不精確演算法，但計算值演算法 （若要儲存的記憶體），您也可以選取檢查樣本大小。 執行個體的預設意指會檢查三個機碼，然後挑選所最近使用過較少的項目。|
|lua 時間限制|5000|最大值 Lua 指令碼以毫秒為單位的執行時間。 如果達到最大的執行時間意指將會記錄的指令碼仍在執行中最大值允許時間後就會開始回覆發生錯誤的查詢。|
|lua 事件限制|500|這是指令碼事件佇列中的最大的大小。|
|用戶端輸出-緩衝限制 normalclient 輸出-緩衝限制 pubsub|0 0 032 mb 8 mb 60|用戶端輸出緩衝限制可用來強制執行的不資料從伺服器讀取速度因故 （常見的原因會是發行/子用戶端無法使用以 publisher 產生的速度的郵件） 的用戶端中斷連線。 如需詳細資訊，請參閱[http://redis.io/topics/clients](http://redis.io/topics/clients)。|

<a name="databases"></a>
<sup>1</sup>限制`databases`的每個 Azure Redis 快取價格層不同，且可以在建立快取設定。 如果沒有`databases`設定指定快取建立時，預設值為 16。

-   基本和標準的快取
    -   C0 (250 MB) 快取-最 16 的資料庫
    -   C1 1 GB 的快取-最 16 的資料庫
    -   C2 (2.5 GB) 快取-最 16 的資料庫
    -   C3 (6 GB) 快取-最 16 的資料庫
    -   C4 (13 GB) 快取-進位至 32 的資料庫
    -   C5 (26 GB) 快取-最 48 資料庫
    -   C6 (53 GB) 快取-最多 64 資料庫
-   進階版的快取
    -   P1 (6 GB 60 GB)-最 16 的資料庫
    -   P2 (13 GB 130 GB)-進位至 32 的資料庫
    -   第 3 頁 (26 GB 260 GB)-最 48 資料庫
    -   第 4 頁 (53 GB 530 GB)-最多 64 資料庫
    -   所有的進階版快取與意指叢集啟用-意指叢集只支援使用資料庫 0，因此`databases`限制啟用意指叢集任何進階版快取實際上是 1，不允許[選取](http://redis.io/commands/select)的命令。 如需詳細資訊，請參閱[我需要進行任何變更我的用戶端應用程式，以使用叢集？](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] `databases`設定有可能只在建立快取設定並只使用 PowerShell、 CLI 或其他管理用戶端。 如需設定的範例`databases`期間使用 PowerShell 建立快取，請參閱[新增 AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases)。


<a name="maxclients"></a>
<sup>2</sup> `maxclients`是不同的每個 Azure Redis 快取價格層。

-   基本和標準的快取
    -   C0 (250 MB) 快取-最 256 連線
    -   C1 1 GB 的快取-最 1000 的連線
    -   C2 (2.5 GB) 快取-最多 2000 的連線
    -   C3 (6 GB) 快取-最 5000 個連線
    -   C4 (13 GB) 快取-最多 10000 個連線
    -   C5 (26 GB) 快取-最 15000 連線
    -   C6 (53 GB) 快取-最 20000 的連線
-   進階版的快取
    -   P1 (6 GB 60 GB)-最 7,500 的連線
    -   P2 (13 GB 130 GB)-最 15000 連線
    -   第 3 頁 (26 GB 260 GB)-進位至 30000 連線
    -   第 4 頁 (53 GB 530 GB)-最 40000 連線

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis Azure Redis 快取中不支援的命令

>[AZURE.IMPORTANT] 因為設定及管理的 Azure Redis 快取執行個體由 Microsoft 管理下列命令已停用。 如果您嘗試叫用您會收到錯誤訊息類似`"(error) ERR unknown command"`。
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  設定
>-  偵錯
>-  移轉
>-  儲存
>-  關閉
>-  SLAVEOF
>-  叢集，-叢集的寫入命令已停用，但唯讀叢集允許命令。

如需有關意指命令的詳細資訊，請參閱[http://redis.io/commands](http://redis.io/commands)。

## <a name="redis-console"></a>Redis 主控台

您可以安全地發行至您 Azure Redis 快取的執行個體使用**Redis 主控台**，這是標準的可用的命令，及進階版快取。

>[AZURE.IMPORTANT] Redis 主控台不適用於 VNET，叢集、 和 0 以外的資料庫。 
>
>-  [VNET](cache-how-to-premium-vnet.md) -VNET 一部分快取時，在 VNET 的用戶端可以存取快取。 因為 Redis 主控台使用裝載於不屬於您 VNET 的 Vm 意指 cli.exe 用戶端，無法連線至您的快取。
>-  [叢集](cache-how-to-premium-clustering.md)Redis 主控台使用不支援這次叢集意指 cli.exe 用戶端。 在 GitHub 意指存放庫[不穩定](http://redis.io/download)分支中的意指 cli 公用程式實作基本支援啟動時`-c`切換。 如需詳細資訊請參閱上[http://redis.io](http://redis.io) [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial)中的[遊戲與叢集](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)。
>-  Redis 主控台可讓您的新連線，的每當您提交命令資料庫 0。 您無法使用`SELECT`命令，以選取不同的資料庫，因為資料庫會重設為 0，每一個命令。 如需執行意指命令，包括變更為不同的資料庫，請參閱[如何執行意指命令？](cache-faq.md#how-can-i-run-redis-commands)

若要存取 Redis 主控台，按一下 [從**Redis 快取**刀**主控台**。

![Redis 主控台](./media/cache-configure/redis-console-menu.png)

要針對您的快取執行個體的命令，只要輸入您要的命令中主控台。

![Redis 主控台](./media/cache-configure/redis-console.png)

如已停用 Azure Redis 快取的意指命令的清單，請參閱[Redis 命令 Azure Redis 快取中不支援](#redis-commands-not-supported-in-azure-redis-cache)的前一節。 如需有關意指命令的詳細資訊，請參閱[http://redis.io/commands](http://redis.io/commands)。 

## <a name="move-your-cache-to-a-new-subscription"></a>將您的快取移到新的訂閱

您可以移至新訂閱的快取，即可**移動**。

![移動意指快取](./media/cache-configure/redis-cache-move.png)

在 [資源從某個資源群組到另一個來回移動一個訂閱到另一個的資訊，請參閱[移動到新的資源群組或訂閱的資源](../resource-group-move-resources.md)。

## <a name="next-steps"></a>後續步驟
-   如需有關使用意指命令的詳細資訊，請參閱[如何執行意指命令？](cache-faq.md#how-can-i-run-redis-commands)。
