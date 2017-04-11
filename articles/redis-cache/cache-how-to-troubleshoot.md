<properties 
    pageTitle="如何疑難排解 Azure Redis 快取 |Microsoft Azure" 
    description="瞭解如何解決 Azure Redis 快取的常見問題。" 
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>如何疑難排解 Azure Redis 快取

本文提供的指導方針下列類別的 Azure Redis 快取問題的疑難排解。

-   [用戶端側疑難排解](#client-side-troubleshooting)-此節提供在識別和解決問題所造成的應用程式連線至 Azure Redis 快取的方針。
-   [伺服器端疑難排解](#server-side-troubleshooting)-此節提供在識別和解決問題所造成 Azure Redis 快取伺服器端的方針。
-   [StackExchange.Redis 逾時例外](#stackexchangeredis-timeout-exceptions)本節提供疑難排解問題時使用 StackExchange.Redis 用戶端的相關資訊。


>[AZURE.NOTE] 本指南中的疑難排解步驟的數個包含指示執行意指命令，並監控各種效能指標。 如需詳細資訊與指示，請參閱 [[其他資訊](#additional-information)] 區段中的文章。

## <a name="client-side-troubleshooting"></a>用戶端側疑難排解


本節將說明疑難排解條件在用戶端應用程式上會發生的問題。

-   [在用戶端的記憶體壓力](#memory-pressure-on-the-client)
-   [傳送的流量](#burst-of-traffic)
-   [高的用戶端 CPU 使用率](#high-client-cpu-usage)
-   [用戶端側頻寬超出](#client-side-bandwidth-exceeded)
-   [大型的要求回應大小](#large-requestresponse-size)
-   [我在意指中的資料有何改變？](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>在用戶端的記憶體壓力

#### <a name="problem"></a>問題

在用戶端電腦的記憶體壓力會導致效能問題的可以延遲的意指執行個體，而不會任何延遲送出資料的處理所有類型。 當記憶體壓力，系統通常有頁面資料從虛擬記憶體磁碟上的實體記憶體。 此*頁面發生錯誤*時，會造成大幅降低系統。

#### <a name="measurement"></a>度量單位 

1.  監控記憶體使用量上，確定不超過可用的記憶體。 
2.  監視器`Page Faults/Sec`效能計數器。 大部分的系統會有一些分頁錯誤，甚至在正常運作，因此請觀賞此頁面錯誤效能計數器逾時與對應的波峰。

#### <a name="resolution"></a>解決方法

將您的用戶端升級為較大的用戶端虛擬記憶體大小的更多記憶體或深入探究以減少記憶體 consuption 您記憶體使用量模式。


### <a name="burst-of-traffic"></a>傳送的流量

#### <a name="problem"></a>問題

結合不佳的流量高速量`ThreadPool`設定可能會導致處理資料 Redis 伺服器已傳送，但尚未在用戶端耗用的延遲。

#### <a name="measurement"></a>度量單位 

監視器如何您`ThreadPool`統計資料變更一段時間使用的程式碼[如下](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)。 您也可以查看`TimeoutException`StackExchange.Redis 郵件。 以下是範例︰

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在上述的郵件中，有會感興趣的幾個問題︰

 1. 請注意，在`IOCP`區段和`WORKER`區段必須`Busy`值大於`Min`值。 這表示您`ThreadPool`設定需要調整。
 2. 您也可以查看`in: 64221`。 這表示 64211 位元組已接收核心通訊端層級，但尚未尚未讀取的應用程式 (例如 StackExchange.Redis)。 這通常表示的應用程式不從網路盡伺服器會傳送給您讀取資料。

#### <a name="resolution"></a>解決方法

設定[集區設定](https://gist.github.com/JonCole/e65411214030f0d823cb)，請確定您的執行緒集區會快速縮放，底下突發案例。


### <a name="high-client-cpu-usage"></a>高的用戶端 CPU 使用率

#### <a name="problem"></a>問題

用戶端上的高 CPU 使用率是表示，系統不能掌握有要求您執行的工作。 這表示的用戶端可能無法處理意指及時的回應，即使意指快速傳送回應。

#### <a name="measurement"></a>度量單位

監控系統寬 CPU 使用率透過 Azure 入口網站或相關聯的效能計數器。 請小心不要監控*程序*CPU，因為單一程序可以整體系統 CPU 可能高的同時有 CPU 使用率太低。 請觀賞特殊圖文集的 CPU 使用率與逾時。 當做高 CPU，您可能也會看到高`in: XXX`值在`TimeoutException`錯誤訊息的[尖峰流量的](#burst-of-traffic)一節所述。

>[AZURE.NOTE] StackExchange.Redis 1.1.603 及更新版本包括`local-cpu`公制中`TimeoutException`錯誤訊息。 請確定您使用最新版的[StackExchange.Redis NuGet 套件](https://www.nuget.org/packages/StackExchange.Redis/)。 有不斷地在程式碼，使其更強大至逾時，最新版本很重要，以便修正問題。

#### <a name="resolution"></a>解決方法

升級到較大的虛擬記憶體大小的更多的 CPU 容量或調查導致 CPU 特殊圖文集]。 



### <a name="client-side-bandwidth-exceeded"></a>用戶端側頻寬超出

#### <a name="problem"></a>問題

不同大小的用戶端電腦有限制多少網路頻寬有提供。 如果用戶端超過可用的頻寬，然後資料將不會處理用戶端盡伺服器加以傳送。 這可能會導致逾時。

#### <a name="measurement"></a>度量單位

監控您的頻寬使用量如何變更一段時間使用的程式碼[如下](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)。 請注意，將此程式碼可能無法在某些具有限制權限 （例如 Azure 網站） 的環境中順利執行。

#### <a name="resolution"></a>解決方法 

增加用戶端虛擬記憶體大小或減少網路頻寬使用量。


### <a name="large-requestresponse-size"></a>大型的要求回應大小

#### <a name="problem"></a>問題

大型要求/回應，可能會導致逾時。 舉例來說，假設您的用戶端的設定將會出現逾時值 1 第二個。 應用程式要求 （例如 「 A 」 和 「 B 」） 的兩個索引鍵，同時 （使用相同的實體網路連線）。 大部分的用戶端支援 「 Pipelining 」 的要求，例如 'A 」 和 「 B 」 這兩個要求會傳送至伺服器的網路上一個而不需等待回應。 伺服器會傳送回應的順序相同。 如果回應 」 的 「 很大不夠它可以吃設定大部分的後續要求的逾時。 

下列範例會示範這種情況。 在此案例中，要求 「 A 」 和 「 B 」 快速傳送、 伺服器啟動傳送回覆 」 A 」 和 「 B 」 快速，但資料傳輸的時間，因為 'B' 陷入前其他要求並逾時即使伺服器快速回應。

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>度量單位

這是一個難以測量。 基本上必須追蹤記錄您追蹤大型邀請和回應的用戶端程式碼。 

#### <a name="resolution"></a>解決方法

1.  意指最適合大量的較小的值，而不是幾個較大的值。 慣用的解決方案是中斷設定您的資料，將相關較小的值。 請參閱什麼是理想的值大小的範圍意指[嗎？為 100 KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)如需詳細資訊，周圍為什麼建議使用較小值的文章。
2.  放大您 VM （適用於用戶端和 Redis 快取伺服器），以取得更高的頻寬功能，減少較大的回應資料傳輸的時間。 請注意，取得更多的頻寬，只要在伺服器上，或只在用戶端可能沒有足夠的。 測量您頻寬使用量，並將其 VM 您目前擁有的大小的功能比較。
3.  增加數`ConnectionMultiplexer`不同連線上使用的循環要求物件您。


### <a name="what-happened-to-my-data-in-redis"></a>我在意指中的資料有何改變？

#### <a name="problem"></a>問題

某些預期會在我 Azure Redis 快取執行個體中的資料，但它未似乎不存在。

##### <a name="resolution"></a>解決方法

請參閱[我意指中的資料有何改變？](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)可能的原因與解決方案。


## <a name="server-side-troubleshooting"></a>伺服器端疑難排解

本節將說明疑難排解快取伺服器上條件會發生的問題。

-   [在伺服器上的記憶體壓力](#memory-pressure-on-the-server)
-   [高 CPU 使用率 / 伺服器載入](#high-cpu-usage-server-load)
-   [伺服器端頻寬超出](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>在伺服器上的記憶體壓力

#### <a name="problem"></a>問題

伺服器端的記憶體壓力會導致效能問題的延遲要求的處理所有類型。 當記憶體壓力，系統通常有頁面資料從虛擬記憶體磁碟上的實體記憶體。 此*頁面發生錯誤*時，會造成大幅降低系統。 有多個可能的原因，此記憶體壓力的︰ 

1.  您已填入資料快取完整的容量。 
2.  意指會看到高記憶體分散-最常所致儲存大型物件 （意指最佳化的小型的物件，請參閱[什麼是理想的值大小的範圍意指嗎？為 100 KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)如需詳細資訊張貼）。 

#### <a name="measurement"></a>度量單位

意指公開可以協助您找出此問題的兩個指標。 第一個是`used_memory`，另一個是`used_memory_rss`。 [下列指標](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)，可在 Azure 入口網站，或透過[Redis 資訊](http://redis.io/commands/info)] 命令。

#### <a name="resolution"></a>解決方法

有數個可能的變更，您可以對有助於防止記憶體使用量健全︰

1. [設定記憶體原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)並設定您的按鍵的到期時間。 請注意，這可能沒有足夠，是否您有片段。
2. [設定 maxmemory 保留值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)夠大，無法記憶體分散賠償。
3. 分割成較小的相關物件的大快取物件。
4. 較大的快取大小的[小數位數](cache-how-to-scale.md)。
5. 如果您使用的[與意指叢集啟用進階版快取](cache-how-to-premium-clustering.md)可以[增加擊碎的數字](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache)。

### <a name="high-cpu-usage--server-load"></a>高 CPU 使用率 / 伺服器載入

#### <a name="problem"></a>問題

高 CPU 使用率可以有意義的用戶端可以無法處理意指及時的回應，即使意指快速傳送回應。

#### <a name="measurement"></a>度量單位

監控系統寬 CPU 使用率透過 Azure 入口網站或相關聯的效能計數器。 請小心不要監控*程序*CPU，因為單一程序可以整體系統 CPU 可能高的同時有 CPU 使用率太低。 請觀賞特殊圖文集的 CPU 使用率與逾時。

#### <a name="resolution"></a>解決方法

[縮放比例](cache-how-to-scale.md)較大的快取層的更多的 CPU 容量或調查導致 CPU 波峰。 

### <a name="server-side-bandwidth-exceeded"></a>伺服器端頻寬超出

#### <a name="problem"></a>問題

不同大小的快取執行個體有限制多少網路頻寬有提供。 如果伺服器超出可用的頻寬，然後資料將不會傳送至快用戶端。 這可能會導致逾時。

#### <a name="measurement"></a>度量單位

您可以監視`Cache Read`公制，也就是資料的讀取快取以百萬位元組期間每秒 (MB/s) 指定報告的時間間隔。 此值會對應到使用此快取的網路頻寬。 如果您想要設定提醒的伺服器端網路頻寬限制，您可以建立使用此`Cache Read`計數器。 您可以比較您讀取與價格層及大小的各種快取的觀察的頻寬限制[此表格](cache-faq.md#cache-performance)中的值。

#### <a name="resolution"></a>解決方法

如果您是一致的觀察的最大頻寬附近價格層和快取大小，請考慮[縮放](cache-how-to-scale.md)至價格層或具有更大的網路頻寬，使用[此表格](cache-faq.md#cache-performance)中的值，作為輔助線的大小。


## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 逾時例外狀況

StackExchange.Redis 使用組態設定命名`synctimeout`的預設值的 1000 ms 同步作業。 如果同步呼叫無法完成約定時間，StackExchange.Redis 用戶端會擲回類似下面的範例，會出現逾時錯誤。

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


此錯誤訊息的內容指標，可協助您指向原因，以及可能的解析度問題。 下表包含錯誤訊息計量的詳細資料。

| 錯誤訊息公制 | 詳細資料                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inst       | 在最後一個時間量︰ 已發行 0 的命令                                                                                                                                                                                              |
| 管理員        | 通訊端管理員正在執行`socket.select`表示這要求表示已經有執行; 通訊端 OS基本上︰ 讀者不主動讀取從網路因為它不認為有關係 |
| 佇列中      | 有 73 總進行中作業                                                                                                                                                                                                        |
| qu         | 6 進行中作業的是未佇列中，而且尚未寫入到輸出的網路                                                                                                                                                           |
| qs         | 他正在進行中作業的 67 已傳送到伺服器，但回應尚無法使用。 回應可能是`Not yet sent by the server`或`sent by the server but not yet processed by the client.`                                                   |
| qc         | 0 的 [進行中] 作業過回覆，但尚未標示為完成，因為等候完成循環                                                                                                                                      |
| wr         | 有作用中的作者 （亦即不會忽略 6 未的要求） 位元組/activewriters                                                                                                                                                   |
| 在         | 有沒有作用中的讀者而且零個位元組都可讀取在 NIC 位元組/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>調查的步驟

1. 時的最佳作法請確認您使用下列模式時使用 StackExchange.Redis 用戶端連線。


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    如需詳細資訊，請參閱[連線至使用 StackExchange.Redis 快取](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)。

2. 確定 Azure Redis 快取及用戶端應用程式中 Azure 中相同的區域。 例如，您可能會收到逾時當您的快取位於東亞美國但用戶端位於西美國，邀請不在內完成`synctimeout`間隔或您可能會收到逾時從本機開發電腦偵錯。 

    強烈建議您有快取和相同的 Azure 區域中的用戶端。 如果您有包含交叉地區呼叫的情況，您應該設定`synctimeout`間隔值高於預設 1000 ms 間隔，包括`synctimeout`連線字串中的屬性。 下列範例會示範 StackExchange.Redis 快取連線字串的程式碼片段`synctimeout`2000 ms。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. 請確定您使用最新版的[StackExchange.Redis NuGet 套件](https://www.nuget.org/packages/StackExchange.Redis/)。 有不斷地在程式碼，使其更強大至逾時，最新版本很重要，以便修正問題。

4. 如果沒有收到繫結伺服器或用戶端的頻寬限制的要求，會耗費的完成，因此會導致逾時。 由於在伺服器上的網路頻寬是否您逾時，請參閱[超過伺服器端頻寬](#server-side-bandwidth-exceeded)。 由於用戶端網路頻寬是否您逾時，請參閱[用戶端側頻寬超出](#client-side-bandwidth-exceeded)。

6. 您收到 CPU 繫結在伺服器上或用戶端？
    -   如果您收到繫結 CPU 可能會導致邀請中不會處理用戶端上核取`synctimeout`間隔，因而造成逾時。 移到較大的用戶端或發佈載入可以控制此協助。 
    -   如果您已準備 CPU 的核取結合在伺服器上監控`CPU`[快取效能公制](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)。 繫結的 CPU 意指時傳入要求可能會導致這些要求逾時。 若要解決這個問題，您可以分散進階版快取中的多個擊碎載入或升級為較大的大小或價格層。 如需詳細資訊，請參閱[伺服器端頻寬超過](#server-side-bandwidth-exceeded)。

7. 有命令花很長的時間處理程序在伺服器上嗎？ 長時間執行的需要花費很長的時間處理意指伺服器上的命令，可能會導致逾時。 長時間執行命令的一些範例`mget`大量鍵`keys *`或不良寫入 lua 指令碼。 您可以連線到您 Azure Redis 快取的執行個體使用意指 cli 用戶端或使用[Redis 主控台](cache-configure.md#redis-console)並執行[SlowLog](http://redis.io/commands/slowlog)命令，以查看是否有時間比預期的要求。 針對許多小的要求，而不是較少的大型要求最佳化意指伺服器和 StackExchange.Redis。 將您的資料分割成較小的區塊，可能會改善以下項目。 

    連線到使用意指 cli 和 stunnel Azure Redis 快取 SSL 端點的詳細資訊，請參閱[宣佈使用 ASP.NET 工作階段狀態的提供者 Redis 預覽版本](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)部落格文章。 如需詳細資訊，請參閱[SlowLog](http://redis.io/commands/slowlog)。

8. 高意指伺服器載入可能會導致逾時。 您可以監控伺服器負載監控`Redis Server Load`[快取效能公制](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)。 100 （最大值） 伺服器載入代表，意指伺服器已忙碌，沒有閒置時間，處理要求。 如果某些要求佔用所有伺服器功能，請執行 [SlowLog] 命令前, 一段落中所述。 如需詳細資訊，請參閱[高 CPU 使用率 / 伺服器載入](#high-cpu-usage-server-load)。

9. 可能因為網路 blip 用戶端上已有任何其他事件？ 如果發生的事件，例如 [縮放比例用戶端執行個體數目，向上或向下或已啟用部署的用戶端或自動調整新版本，請檢查在用戶端 （網頁、 工作者角色或 Iaas VM）？我們發現自動調整大小或縮放漲跌可能會導致我們測試輸出的網路連線很遺失幾秒鐘。 StackExchange.Redis 程式碼會同時這類事件，並會重新連接的連接線。 在此期間內重新連線的佇列中的任何要求可以逾時。

10. 已有 Redis 快取逾時的前面幾個小要求的大要求？ 參數`qs`錯誤訊息會告訴您多少要求已從用戶端傳送到伺服器，但尚未處理回應。 可以保留成長此值，因為 StackExchange.Redis 使用單一 TCP 連線，只可以讀取一次一個的回應。 即使第一項作業逾時，它不會停止從伺服器時，所傳送的資料及其他要求遭到封鎖，直到此完成後，導致逾時。 解決方法之一是減少逾時，確保您的快取夠大，針對您的工作負載分割較小的區塊為較大的值。 若要使用的集區的另一個可能的解決方案是`ConnectionMultiplexer`物件在您的用戶端，然後選擇 [至少載入`ConnectionMultiplexer`時，傳送新的要求。 這應避免單一逾時造成其他要求也會出現逾時。

11. 如果您使用的`RedisSessionStateprovider`，確保您已正確設定重試逾時。 `retrytimeoutInMilliseconds`應該高於`operationTimeoutinMilliseonds`，否則將會發生任何重試。 在下列範例`retrytimeoutInMilliseconds`已設定為 3000。 如需詳細資訊，請參閱[Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)，並[瞭解如何使用的工作階段狀態提供者和輸出快取提供者設定參數](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)。


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. 檢查 Azure Redis 快取伺服器上的記憶體使用量[監控](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)`Used Memory RSS`和`Used Memory`。 就地收回原則時，會啟動意指收回方向鍵時`Used_Memory`達到快取大小。 理想的情況下，`Used Memory RSS`應只會稍微高於`Used memory`。 大型的差異，即記憶體分散 （內部或外部。 當`Used Memory RSS`是小於`Used Memory`，即表示已由作業系統交換快取記憶體的一部分。 如果這是您可以預期的一些重要的延遲時間。 因為意指沒有控制如何將其配置對應到高的記憶體頁面`Used Memory RSS`，通常會記憶體使用量特殊圖文集的結果。 當意指釋放記憶體時，記憶體回提供給配置，或配置可能會記憶體回系統。 可能有不一致`Used Memory`值和記憶體使用量由作業系統的報告。 可能會因為記憶體已使用和發行意指，但不是指定一步] 系統。 若要協助減輕記憶體耗盡問題，您可以執行下列步驟。
    -   升級，好讓您不執行進而記憶體限制系統上較大的快取。
    -   設定到期時間按鍵就主動收回較舊的值。
    -   監視器`used_memory_rss`快取公制。 此值接近其快取大小，您可能會開始看到效能問題。 如果您正在使用進階版快取，或將升級為較大的快取，則您可以分散多個擊碎資料。

    如需詳細資訊，請參閱[在伺服器上的記憶體壓力](#memory-pressure-on-the-server)。

## <a name="additional-information"></a>其他資訊

-   [要使用哪些 Redis 快取服務和大小？](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [如何基準測試，測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [我要如何執行意指命令？](cache-faq.md#how-can-i-run-redis-commands)
-   [如何監視 Azure Redis 快取](cache-how-to-monitor.md)