<properties 
    pageTitle="如何監視 Azure Redis 快取 |Microsoft Azure" 
    description="瞭解如何監視健康狀況和效能您 Azure Redis 快取的執行個體" 
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
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>如何監視 Azure Redis 快取

Azure Redis 快取提供數種選項監控您的快取執行個體。 您可以檢視指標、 釘選到 Startboard 指標圖表、 自訂日期及時間範圍監控圖表、 新增和移除圖表中的指標以及設定提醒，符合特定條件時。 這些工具可讓您監視您 Azure Redis 快取的執行個體的狀況，協助您管理快取應用程式。

快取診斷啟用時，Azure Redis 快取執行個體的指標收集約每 30 秒及儲存，讓他們可以顯示的指標圖表與評估通知的規則。

使用意指收集快取指標[資訊](http://redis.io/commands/info)] 命令。 如需不同的資訊所使用的每個值快取度量，請參閱[可用的指標及報告的時間間隔](#available-metrics-and-reporting-intervals)。

若要檢視快取指標，[瀏覽](cache-configure.md#configure-redis-cache-settings)至您的快取執行個體中[Azure 入口網站](https://portal.azure.com)。 Azure Redis 快取執行個體的指標**Redis 指標**刀上存取。

![Redis 指標][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] 如果**Redis 指標**刀中顯示下列訊息，請遵循啟用快取診斷程式的 [[啟用快取診斷](#enable-cache-diagnostics)] 區段中的步驟進行。
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

**Redis 指標**刀有顯示 [快取指標的**監控**圖表。 為每個圖表可以進行自訂，以新增或移除指標和變更報告的時間間隔。 檢視和作業和通知設定， **Redis 快取**刀有**作業**區段會顯示**事件**的快取及**提醒的規則**。

## <a name="enable-cache-diagnostics"></a>啟用快取診斷程式

Azure Redis 快取可讓您將診斷資料儲存在儲存帳戶，您可以使用任何您想要存取並直接處理資料的工具。 收集的快取診斷讓，儲存，並顯示在 Azure 入口網站，必須設定儲存帳戶。 在相同的地區碼和訂閱的快取共用相同的診斷儲存帳戶，並設定變更時，適用於所有快取訂閱中的區域中的。

若要啟用並設定快取診斷，瀏覽至您的快取執行個體**Redis 快取**刀。 如果尚未啟用診斷，會顯示一則訊息，而不是診斷圖表。

![啟用快取診斷程式][redis-cache-enable-diagnostics]

按一下要顯示**公制**刀，按一下 [**診斷設定**以啟用及設定的快取服務執行個體的診斷設定的訊息。

![診斷程式設定][redis-cache-diagnostic-settings]

![設定診斷程式][redis-cache-configure-diagnostics]

按一下 [**開啟**] 按鈕以啟用 [快取診斷並顯示 [診斷設定]。

按一下右邊的**儲存空間帳戶**選取存放診斷資料的儲存空間帳戶箭號。 最佳效能，請選取儲存帳戶中的快取為相同的區域。

一旦設定診斷設定，請按一下 [**儲存**] 儲存設定]。 請注意，可能需要等候幾分鐘，變更才會生效。

>[AZURE.IMPORTANT] 在相同的地區碼和訂閱的快取共用相同的診斷儲存設定，並設定時變更 （診斷啟用或停用或變更儲存帳戶） 適用於所有快取訂閱中的區域中的。

若要檢視儲存的指標，檢查資料表中開始使用您儲存帳戶名稱`WADMetrics`。 如需存取儲存的指標 Azure 入口網站以外的詳細資訊，請參閱[Access Redis 快取監控資料](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)範例。

>[AZURE.NOTE] 儲存在選取的儲存帳戶的指標會顯示在 Azure 入口網站。 如果您變更儲存帳戶時，先前設定的儲存空間帳戶中的資料，仍然可以下載時，但不是會顯示在 Azure 入口網站。  

## <a name="available-metrics-and-reporting-intervals"></a>可用的指標及報告的時間間隔

使用多個報表的時間間隔，包括**過去小時**、 **Today**、**週**，以及**自訂**報告快取指標。 針對每個指標圖表**公制**刀顯示每個公制的平均值、 最小值與最大值在圖表中，並一些指標顯示總計的報告的時間間隔。 

每個公制包含兩個版本。 一個公制測量效能的整個快取，以及使用[叢集](cache-how-to-premium-clustering.md)，包括公制的第二個版本的快取`(Shard 0-9)`名稱量值的效能的快取中的單一晶怪。 例如，如果快取有 4 擊碎，`Cache Hits`是整個快取，點擊的總數量和`Cache Hits (Shard 3)`是只針對該晶怪快取的點擊。

>[AZURE.NOTE] 即使當快取閒置時沒有連線作用中的用戶端應用程式，您可能會看到一些快取活動，例如連線的用戶端、 記憶體使用量和正在執行的作業。 此活動時標準 Azure Redis 快取執行個體的作業。

| 公制            | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 快取點擊率        | 成功的關鍵查閱期間指定報告的時間間隔數目。 這樣會對應到`keyspace_hits`意指[資訊](http://redis.io/commands/info)的命令。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 快取遺失      | 指定報告的時間間隔期間失敗的主要查閱數目。 這樣會對應到`keyspace_misses`從 Redis 資訊] 命令。 快取遺漏並不一定代表快取發生問題。 例如，使用時的快取離題程式設計模式，應用程式，看起來第一個快取的項目中。 如果項目不在 （快取遺失），項目會從資料庫中擷取及下一次加入快取。 快取遺漏的快取離題程式設計模式的標準行為。 如果有比預期更高的快取遺失的數值，，檢查填入與讀取快取的應用程式邏輯。 如果項目會被收回從因為記憶體壓力快取然後可能有一些快取遺失，但就會更好的度量單位，若要監視的記憶體壓力`Used Memory`或`Evicted Keys`。 |
| 連線的用戶端 | 指定報告的時間間隔期間的用戶端連線至快取的數字。 這樣會對應到`connected_clients`從 Redis 資訊] 命令。 一旦達到[連線限制](cache-configure.md#default-redis-server-configuration)就快取的後續連線嘗試將會失敗。 請注意，即使沒有任何作用中的用戶端應用程式，有可能仍連線的用戶端，因為內部處理程序和連線幾個執行個體。                                                                                                                                                                                                                                                                                                                                                                                                                          |
| 收回索引鍵      | 指定報告的時間間隔，因為期間收回從快取的項目數目`maxmemory`限制。 這樣會對應到`evicted_keys`從 Redis 資訊] 命令。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| 過期的按鍵      | 在指定報告的時間間隔，從快取過期的項目數。 此值會對應到`expired_keys`從 Redis 資訊] 命令。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 取得              | 從快取的取得作業期間指定報告的時間間隔數目。 此值為下列總和值從 Redis 資訊所有命令︰ `cmdstat_get`， `cmdstat_hget`， `cmdstat_hgetall`， `cmdstat_hmget`， `cmdstat_mget`， `cmdstat_getbit`，及`cmdstat_getrange`，及相當於總和快取點擊和遺漏期間報告的時間間隔。                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis 伺服器負載 | 循環的意指伺服器正在處理，並不會等待閒置郵件的百分比。 如果這個 [計數器達到的 100，表示意指伺服器已經達到效能 ceiling 和 CPU 無法處理更快速任何工作。 如果您看到高 Redis 伺服器載入時，您會看到逾時在用戶端的例外狀況。 在此情況下，您應該考慮縮放，或將資料分割成多個快取。                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 設定              | 設定快取中的作業期間指定報告的時間間隔數目。 此值為下列總和值從 Redis 資訊所有命令︰ `cmdstat_set`， `cmdstat_hset`， `cmdstat_hmset`， `cmdstat_hsetnx`， `cmdstat_lset`， `cmdstat_mset`， `cmdstat_msetnx`， `cmdstat_setbit`， `cmdstat_setex`， `cmdstat_setrange`，及`cmdstat_setnx`。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 總作業  | 伺服器處理的快取期間指定報告的時間間隔] 命令的總數。 此值會對應到`total_commands_processed`從 Redis 資訊] 命令。 請注意當 Azure Redis 快取完全用於發行/子會發生的任何指標`Cache Hits`， `Cache Misses`， `Gets`，或`Sets`，但會有`Total Operations`反映發行/子操作的快取使用量的指標。                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 使用的記憶體       | 用於 mb 快取中的金鑰/值組期間指定報告的時間間隔的快取記憶體量。 此值會對應到`used_memory`從 Redis 資訊] 命令。 這不包含的中繼資料還是片段。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 使用的記憶體 RSS   | 快取記憶體 mb 用於指定報告的時間間隔，包括片段和中繼資料量。 此值會對應到`used_memory_rss`從 Redis 資訊] 命令。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| CPU               | 期間的 CPU 使用率 Azure Redis 快取伺服器的百分比指定報告的時間間隔。 此值會對應到作業系統`\Processor(_Total)\% Processor Time`效能計數器。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 快取已讀取        | 讀取以百萬位元組快取期間每秒 (MB/s) 指定報告的時間間隔的資料量。 此值被衍生自支援虛擬機器主控快取，不是特定的意指網路介面卡。 **此值會對應到使用此快取的網路頻寬。如果您想要設定的伺服器端網路頻寬限制提醒，然後建立並使用這`Cache Read`計數器。請參閱[下表](cache-faq.md#cache-performance)的各種價格層及大小的快取的觀察的頻寬限制。**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 快取寫入       | 寫入每秒 (MB/s) 在指定期間的快取中 Mb 的資料量報告時間間隔。 此值被衍生自支援虛擬機器主控快取，不是特定的意指網路介面卡。 此值會對應到資料從用戶端傳送至快取的網路頻寬。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>如何檢視標準和自訂圖表

您可以檢視上**Redis 指標**刀快取指標的概觀。 若要存取**Redis 指標**刀選擇**所有設定** > **Redis 指標**。

![Redis 指標][redis-cache-redis-metrics]


**Redis 指標**刀包含下列的圖表。

| Redis 標準圖表 | 顯示的指標     |
|------------------|-------------------|
| 快取讀取] 和 [快取撰寫 | 快取已讀取 |
|                            | 快取寫入 |
| 連線的用戶端      | 連線的用戶端 |
| 點擊和遺漏  | 快取點擊率        |
|                  | 快取遺失      |
| 總命令   | 總作業  |
| 取得和集    | 取得              |
|                  | 設定              |
| CPU 使用率         | CPU           |
| 記憶體使用量      | 使用的記憶體   |
|                   | 使用的記憶體 RSS |
| Redis 伺服器負載 | 伺服器負載   |
| 計算機碼 | 總索引鍵 |
|           | 收回索引鍵 |
|           | 過期的按鍵 |


取得更詳細檢視指標在特定的圖表，以及如何自訂圖表，按一下 [從顯示圖表**公制**刀**Redis 指標**刀想要的圖表]。

![公制刀][redis-cache-metric-blade]

在圖表所顯示的指標設定任何通知會列出圖表**公制**刀底部。

若要新增或移除指標或變更報告的時間間隔，請選擇 [**編輯圖表**。

若要新增或移除圖表中的指標，請按一下 [度量名稱旁的核取方塊。 若要變更報告的時間間隔，請按一下想要的間隔。 若要變更**圖表類型**，請按一下您要的樣式。 當您要的變更時，請按一下 [**儲存**]。 

![編輯圖表][redis-cache-edit-chart]

當您按一下 [**儲存**您的變更會保留直到您離開**公制**刀。 當您回來時，您會看到原始公制和時間範圍內一次。 如需有關如何自訂圖表的詳細資訊，請參閱[監視器服務指標](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

若要檢視特定期間內的度量，在圖表上，將滑鼠停留在其中一個特定的列或上的對應到所要的時間，圖表的點，並顯示該間隔的指標。

![檢視圖表的詳細資料][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>如何監視與叢集進階版快取

有[叢集](cache-how-to-premium-clustering.md)啟用的進階版快取可以有最多 10 個擊碎。 每個晶怪有自己的度量，並提供整個快取的指標彙總這些指標。 每個公制包含兩個版本。 一個公制測量整個快取和度量，其中包含的第二個版本的效能`(Shard 0-9)`名稱量值的效能的快取中的單一晶怪。 例如，如果快取有 3 擊碎，`Cache Hits`是整個快取，點擊的總數量和`Cache Hits (Shard 2)`是只針對該晶怪快取的點擊。

為每個監控圖表會顯示最上層的指標以及每個快取晶怪矩陣的快取。

![監視器][redis-cache-premium-monitor]

將滑鼠游標移至資料點顯示時間的該點的詳細資料。 

![監視器][redis-cache-premium-point-summary]

較小的值時晶怪個別矩陣的最大值通常是快取的彙總值。 請注意，在此範例中，有三個擊碎快取點擊平均分佈擊碎。

![監視器][redis-cache-premium-point-shard]

若要查看詳細資訊，請按一下圖表以檢視**公制**刀在展開的檢視。

![監視器][redis-cache-premium-chart-detail]

依預設為每個圖表包含最上層的快取效能計數器，以及個別擊碎的效能計數器。 您可以自訂這些上**編輯圖表**刀。

![監視器][redis-cache-premium-edit]

如需可用的效能計數器的詳細資訊，請參閱[可用的標準和報告的時間間隔](#available-metrics-and-reporting-intervals)。


## <a name="operations-and-alerts"></a>作業及通知

**Redis 快取**刀上的 [**作業**] 區段會有**事件**] 及 [**提醒的規則**] 區段。

![Oeprations][redis-cache-operations-events]

若要查看最近的快取作業的清單，按一下 [**事件**圖表以顯示**事件**刀]。 作業的範例包括擷取及重新產生便捷鍵，並啟動和通知的規則的解析度。 如需有關每項事件的詳細資訊，請按一下 [**事件**刀中的事件]。

如需有關事件的詳細資訊，請參閱[檢視事件和稽核記錄](../monitoring-and-diagnostics/insights-debugging-with-events.md)。

**通知的規則**] 區段會顯示通知快取執行個體的計數。 警示規則可讓您監控您的快取執行個體和特定公制值到達臨界值定義規則時，收到電子郵件。 

通知規則的評估約每五分鐘，警示規則啟動時，傳送任何設定的通知。 已啟用通知的規則及通知不會處理立即;可能有數個分鐘的時間才警示規則會啟動和傳送通知延遲。

可以檢視和設定為特定的監控圖表**公制**刀或**提醒規則**刀通知的規則。

通知規則有下列屬性。

| 通知的規則] 屬性                 | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 資源                            | 通知規則評估資源。 建立提醒規則時從意指快取，快取是資源。                                                                                                                                                                                                                                                                                                                                                  |
| 名稱                                | 唯一識別目前快取執行個體中的通知規則的名稱。                                                                                                                                                                                                                                                                                                                                                                                       |
| 描述                         | 選用的警示規則的詳細說明。                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 公制                              | 監視通知規則公制。 如需快取指標的清單，請參閱可用的指標和報告的時間間隔。                                                                                                                                                                                                                                                                                                                                             |
| 條件                           | 通知規則的條件運算子。 可能的選項會︰ 大於、 大於或等於、 小於較小於或等於                                                                                                                                                                                                                                                                                                                             |
| 閥值                           | 用來使用條件屬性所指定的運算子公制與比較的值。 根據度量，此值可能會在位元組/第二個位元組、 %或計數。                                                                                                                                                                                                                                                                                     |
| 期間                              | 指定的期間的平均的度量值用於警示規則比較。 例如，如果期間的最後一個小時上方，移至前一個小時間隔公制的平均值用於比較。 如果您想要臨界值因為特殊圖文集符合活動時收到通知，則為適當短。 若要維持活動大於閾值時收到通知，使用較長期間。 |
| 電子郵件服務與共同管理員 | 為 true 時，服務系統管理員和共同管理員會透過電子郵件傳送提醒啟動時。                                                                                                                                                                                                                                                                                                                                                                    |
| 其他管理員的電子郵件      | 通知提醒啟動時，其他系統管理員的選用的電子郵件地址。                                                                                                                                                                                                                                                                                                                                                                    |

只有一個通知會傳送通知規則啟動每。 一旦超過臨界值的規則時，系統會傳送通知規則不會重新評估直到公制低於臨界值。 如果公制後續超過閥值，提醒就會重新啟動，並傳送新的通知。

若要檢視所有通知您快取的執行個體的規則，按一下 [ **Redis 快取**刀**通知規則**組件]。 若要檢視僅限使用特定度量單位，提醒的規則，瀏覽至含有該公制圖表**公制**刀。

![通知的規則][redis-cache-alert-rules]

若要新增提醒的規則，按一下 [從**公制**刀或**提醒的規則**刀的 [**新增提醒**]。 

**新增提醒**規則刀中輸入所要的規則條件，然後按一下**[確定]**。 

![新增提醒的規則][redis-cache-add-alert]

>[AZURE.NOTE] **公制**刀中按一下 [**新增提醒**建立提醒的規則時，只顯示於圖表中的刀指標會出現在 [**公制**] 下拉式清單中。 按一下 [**新增提醒****通知規則**刀建立提醒的規則時，所有的快取指標，可**公制**] 下拉式清單中。

一旦警示規則會儲存它會顯示**通知規則**刀及 [顯示度量單位，用通知的規則中的任何圖表**公制**刀。 若要編輯提醒的規則，請按一下以顯示 [**編輯規則**刀通知規則的名稱。 從 [**編輯規則**刀，您可以編輯的規則屬性、 刪除或停用通知的規則，或重新啟用規則，如果先前已停用。

>[AZURE.NOTE] 規則的屬性所做的變更可能會花點之前反映**通知規則**刀或**公制**刀上。

警示規則啟動時，設定通知的規則，根據傳送電子郵件，通知圖示會顯示在**Redis 快取**刀**通知規則**組件。

警示規則會被視為通知的條件不會再評估為 true 時，才能解決問題。 解決警示規則條件後，[提醒] 圖示會取代核取記號。 通知啟動和解決方案的詳細資訊，按一下 [若要在**事件**刀檢視事件**Redis 快取**刀**事件**組件]。

如需關於 Azure 中的通知的詳細資訊，請參閱[接收提醒通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

## <a name="metrics-on-the-redis-cache-blade"></a>在 Redis 快取刀上的指標

**Redis 快取**刀會顯示下列指標的類別。

-   [監控圖表](#monitoring-charts)
-   [使用圖表](#usage-charts)

### <a name="monitoring-charts"></a>監控圖表

**監控**區段**結果，並且遺漏**] 有**取得及設定**、**連線**和**總命令**圖表。

![監控圖表][redis-cache-monitoring-part]

**監控**圖表會顯示下列指標。

| 監控圖表 | 快取指標     |
|------------------|-------------------|
| 點擊和遺漏  | 快取點擊率        |
|                  | 快取遺失      |
| 取得和集    | 取得              |
|                  | 設定              |
| 連線      | 連線的用戶端 |
| 總命令   | 總作業  |

檢視標準和自訂本節中的個別圖表的資訊，請參閱[如何檢視標準和自訂指標圖表](#how-to-view-metrics-and-customize-charts)的小節。

### <a name="usage-charts"></a>使用圖表

**使用情況**] 區段會有**Redis 伺服器載入**、**記憶體使用量**、**網路頻寬**， **CPU 使用率**圖，以及，而且也會顯示**價格層**快取執行個體。

![使用圖表][redis-cache-usage-part]

**價格層**顯示快取價格層，並可用於快取到不同的價格層[小數位數](cache-how-to-scale.md)。

**使用**圖表會顯示下列指標。

| 使用圖表       | 快取指標 |
|-------------------|---------------|
| Redis 伺服器負載 | 伺服器負載   |
| 記憶體使用量      | 使用的記憶體   |
| 網路頻寬 | 快取寫入   |
| CPU 使用率         | CPU           |

檢視標準和自訂本節中的個別圖表的資訊，請參閱[如何檢視標準和自訂指標圖表](#how-to-view-metrics-and-customize-charts)的小節。
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



