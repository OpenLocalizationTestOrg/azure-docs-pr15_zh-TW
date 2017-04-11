<properties 
    pageTitle="Azure 意指快取的常見問題集 |Microsoft Azure" 
    description="瞭解 Azure Redis 快取的一般問題、 圖樣及最佳作法的答案" 
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
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Azure 意指快取的常見問題集

瞭解 Azure Redis 快取的一般問題、 圖樣及最佳作法的答案。 


## <a name="what-if-my-question-isnt-answered-here"></a>如果我沒有問題解答以下嗎？

如果此處未列出您的問題，請讓我們知道，我們將協助您尋找解答。

-   您可以將問題張貼在[Disqus 執行緒](#comments)此常見問題集的結尾，並加入 Azure 快取小組與本文相關的其他社群成員。
-   若要達到更多的對象，您可以[Azure 快取 MSDN 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache)上張貼問題，並加入 Azure 快取小組與其他社群的成員。
-   如果您想要進行功能要求，您可以提交您的要求和[Azure Redis 快取使用者語音](https://feedback.azure.com/forums/169382-cache)想法。
-   您也可以從在[Azure 快取外部意見反應](mailto:azurecache@microsoft.com)給我們傳送電子郵件。

## <a name="azure-redis-cache-basics"></a>Azure Redis 快取基本概念

本節中的常見問題集涵蓋部分 Azure Redis 快取的基本概念。

-    [什麼是 Azure Redis 快取？](#what-is-azure-redis-cache)
-    [如何開始使用 Azure Redis 快取？](#how-can-i-get-started-with-azure-redis-cache)

下列常見問題集涵蓋基本概念和 Azure Redis 快取的相關問題，並在其中一個其他的常見問題集章節會回答。

-   [要使用哪些 Redis 快取服務和大小？](#what-redis-cache-offering-and-size-should-i-use)
-   [可以使用哪些意指快取用戶端？](#what-redis-cache-clients-can-i-use)
-   [有 Azure Redis 快取的本機模擬器嗎？](#is-there-a-local-emulator-for-azure-redis-cache)
-   [如何監視健康狀況與我的快取的效能？](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>規劃的常見問題集

-   [要使用哪些 Redis 快取服務和大小？](#what-redis-cache-offering-and-size-should-i-use)
-   [Azure Redis 快取的效能](#azure-redis-cache-performance)
-   [在哪些地區應該我找出我快取？](#in-what-region-should-i-locate-my-cache)
-   [我要如何正在計費 Azure Redis 快取？](#how-am-i-billed-for-azure-redis-cache)
-   [可以使用 Azure Redis 快取 Azure 政府雲端或 Azure china （中國） 雲端嗎？](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>開發的常見問題集

-   [StackExchange.Redis 設定選項代表該怎麼做？](#what-do-the-stackexchangeredis-configuration-options-do)
-   [可以使用哪些意指快取用戶端？](#what-redis-cache-clients-can-i-use)
-   [有 Azure Redis 快取的本機模擬器嗎？](#is-there-a-local-emulator-for-azure-redis-cache)
-   [我要如何執行意指命令？](#how-can-i-run-redis-commands)
-   [Azure Redis 快取為什麼沒有等部分 Azure 服務 MSDN 類別庫參考？](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [可以使用 Azure Redis 快取為 PHP 工作階段快取嗎？](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>安全性常見問題集

-   [何時應該啟用非 SSL 的連接埠連線到意指？](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>生產常見問題集

-   [什麼是一些生產的最佳作法？](#what-are-some-production-best-practices)
-   [什麼是注意事項使用一般意指命令時？](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [如何基準測試，測試我快取的效能？](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [集區等比級數相關的重要詳細資料](#important-details-about-threadpool-growth)
-   [啟用伺服器 GC 使用 StackExchange.Redis 時，用戶端上取得更多處理量](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>監視及疑難排解的常見問題集

本節中的常見問題集涵蓋常見的監控與疑難排解問題。 如需有關監視及疑難排解您 Azure Redis 快取的執行個體的詳細資訊，請參閱[如何監控 Azure Redis 快取](cache-how-to-monitor.md)，以及[如何疑難排解 Azure Redis 快取](cache-how-to-troubleshoot.md)。

-   [如何監視健康狀況與我的快取的效能？](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [我快取診斷儲存的帳戶設定變更，有何改變？](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [為什麼診斷啟用一些新的快取，而不是其他？](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [為什麼會看見逾時？](#why-am-i-seeing-timeouts)
-   [為什麼我的用戶端的連線中斷快取？](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>先前的快取服務的常見問題集

-   [適合我是哪一個 Azure 快取服務？](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>什麼是 Azure Redis 快取？

Azure Redis 快取根據[Redis 快取](http://redis.io)熱門開啟來源。 其可讓您存取安全的專用意指快取管理 microsoft 和存取從 Azure 中的任何應用程式。 如需更詳細的概觀，請參閱上 Azure.com 的[Azure Redis 快取](https://azure.microsoft.com/services/cache/)產品頁面。


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>如何開始使用 Azure Redis 快取？

有數種方式，您可以快速入門 Azure Redis 快取。

-    您可以查看其中一個提供我們教學課程[.NET](cache-dotnet-how-to-use-azure-redis-cache.md)、 [ASP.NET](cache-web-app-howto.md)、 [Java](cache-java-get-started.md)、 [Node.js](cache-nodejs-get-started.md)，及[Python](cache-python-get-started.md)。 
-    您可以觀看[如何建立高效能應用程式使用 Microsoft Azure Redis 快取](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)。
-    您可以取出的用戶端文件，與您的專案，瞭解如何使用意指開發語言相符的用戶端。 有許多意指用戶端可以使用 Azure Redis 快取。 如需意指用戶端清單，請參閱[http://redis.io/clients](http://redis.io/clients)。


如果您還沒有 Azure 帳戶，您可以︰

-    [免費開啟 Azure 帳戶](/pricing/free-trial/?WT.mc_id=redis_cache_hero)。 您會收到可以用來嘗試付費 Azure 服務的信用額度總計。 即使將貸項總計用完之後，您可以保留帳戶，使用免費的 Azure 服務和功能]。
-    [啟動 Visual Studio 訂閱者優點](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero)。 您的 MSDN 訂閱可讓您貸項總計付費 Azure 服務，您可以使用每個月。


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>要使用哪些 Redis 快取服務和大小？
每個 Azure Redis 快取服務可提供不同層級的**大小**、**頻寬**、**可用性**和**SLA**選項。

以下是選擇 [快取服務考量。

-   **記憶體**︰ 基本和標準層提供 250 MB – 53 GB。 進階版層提供 530 GB，以提供更[要求](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)。 如需詳細資訊，請參閱[Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。
-   **網路效能**︰ 如果您需要高處理量工作量進階版層提供更多的頻寬為標準或 Basic 比較。 也中每一層較大的大小快取會有由於基礎 VM 裝載快取的更多的頻寬。 請參閱[下表](#cache-performance)的詳細資訊。
-   **處理能力**︰ 進階版層提供最大的可處理量。 如果快取伺服器或用戶端達到的頻寬限制，您會收到在用戶端逾時。 如需詳細資訊，請參閱下表。
-   **高的可用性 SLA**: Azure Redis 快取保證標準/進階版快取可的至少 99.9%的時間。 若要深入瞭解我們 SLA，請參閱[Azure Redis 快取價格](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)。 SLA 只涵蓋連線至快取結束點。 SLA 並未涵蓋防止資料遺失。 建議您使用意指資料持續性功能在進階版層增加恢復資料遺失。
-   **Redis 資料保存**︰ 進階版層可讓您保存在 Azure 儲存體帳戶中的快取資料。 基本/標準快取中只在記憶體中儲存的所有資料。 若基礎結構有問題可以是可能的資料遺失。 建議您使用意指資料持續性功能在進階版層增加恢復資料遺失。 Azure Redis 快取提供 RDB 和很少 （即將推出） 選項中意指保存。 如需詳細資訊，請參閱[如何設定持續進階版 Azure Redis 快取](cache-how-to-premium-persistence.md)。
-   **Redis 叢集**︰ 若要建立快取大於 53 GB，或要跨多個意指節點晶怪資料，您可以使用意指叢集、 已在進階版層。 每個節點組成可用性的主要/複本快取組。 如需詳細資訊，請參閱[如何設定叢集進階版 Azure Redis 快取](cache-how-to-premium-clustering.md)。
-   **增強安全性和網路隔離**︰ Azure 虛擬網路 (VNET) 部署提供增強的安全性和隔離您 Azure Redis 快取，以及子網路存取控制 」 原則，以及其他功能，以進一步限制存取。 如需詳細資訊，請參閱[如何設定進階版 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)。
-   **設定 Redis**︰ 在 [標準] 與 [進階版的階層，您可以設定意指再通知。
-   **用戶端連線的最大值數目**︰ 進階版層提供用戶端可以連線到意指，編號更高的較大的尺寸的快取連線數目上限。 [請參閱定價詳細資料頁面](https://azure.microsoft.com/pricing/details/cache/)。
-   **專用核心 Redis 伺服器**︰ 在進階版層所有快取大小的專用的核心意指。 基本/標準層 C1 大小和上方有專用的核心意指伺服器。
-   **意指是單一執行緒**，有兩個以上的核心不提供另一個優點在有兩個核心，但更大的 VM 大小通常會有更多的頻寬，比較小的大小。 如果快取伺服器或用戶端達到的頻寬限制，您會收到在用戶端逾時。
-   **改善效能**︰ 快取中的進階版層部署上有更快速的處理器硬體與可提供較高的相較於基本或標準層的效能。 進階版層快取有較高的輸出量和較低的延遲時間。

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Azure Redis 快取的效能

下表顯示最大頻寬斷測試各種大小的標準時，使用快取的進階版`redis-benchmark.exe`從 Azure Redis 快取端點針對 Iaas VM。 請注意，這些值不保證有沒有 SLA 這些數字，但應該一般。 您應該載入測試自己的應用程式，來判斷您的應用程式的右快取大小。

從這個資料表中，我們可以繪製下列做出結論。

-   處理快取大小相同的能力較高的相較於標準層的進階版層項目。 例如，6 GB 快取的 P1 就 140 K 每秒要求數量與 49 K C3 的。
-   意指叢集，使用直線的處理量增加您增加擊碎 （節點） 叢集內的數字。 例如，如果您建立的 10 個擊碎第 4 頁叢集，然後使用處理量是 250 K * 10 = 2.5 百萬每秒要求數量。
-   處理能力更大的金鑰大小較高的相較於標準層的進階版層項目。

| 價格層             | 大小   | CPU 核心 | 可用的頻寬                                    | 1 KB 金鑰大小                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **標準的快取大小** |        |           | **每秒數秒 (Mb/s) / Mb 每秒 (MB/s)** | **秒 （每秒要求數量） 的要求**            |
| C0                       | 250 MB | 共用    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GB   | 1         | 100 / 12.5                                             | 12200                                    |
| C2                       | 2.5 GB | 2         | 200 / 25                                               | 介於 24000                                    |
| C3                       | 6 GB   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GB  | 2         | 500 / 62.5                                             | 61000                                    |
| C5                       | 26 GB  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | 53 GB  | 8         | 2000 / 250                                             | 最好不要小於 150000                                   |
| **進階版快取大小**  |        | **每個晶怪 CPU 核心**  |                                         | **要求 （每秒 ！ 要求數量），每個秒晶怪** |
| P1                       | 6 GB   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GB  | 4         | 2000 / 250                                             | 220000                                   |
| 第 3 頁                       | 26 GB  | 4         | 2000 / 250                                             | 220000                                   |
| 第 4 頁                       | 53 GB  | 8         | 4000 / 500                                             | 250000                                   |


如需相關指示，例如下載意指工具`redis-benchmark.exe`，請參閱[如何執行意指命令？](#cache-commands)一節。

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>在哪些地區應該我找出我快取？

為獲得最佳效能和最低的延遲，請與用戶端應用程式快取的同一個區域中找出 Azure Redis 快取。

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>我要如何正在計費 Azure Redis 快取？

Azure Redis 快取價格是[以下](https://azure.microsoft.com/pricing/details/cache/)。 [價格] 頁面會列出時薪為價格。 快取的計費每分鐘為基礎的刪除快取時間建立快取的時間。 有任何停止或暫停快取的帳單的選項。


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>可以使用 Azure Redis 快取 Azure 政府雲端或 Azure china （中國） 雲端嗎？

是的 Azure Redis 快取位於 Azure 政府雲端與 Azure china （中國） 雲端中。 請注意，Azure 政府雲端位於不同的 Url 存取及管理 Azure Redis 快取 Azure china （中國） 雲端相較於 Azure 公用雲端。 如需有關使用 Azure Redis 快取 Azure 政府雲端與 Azure china （中國） 雲端時的考量的詳細資訊，請參閱[Azure 政府版資料庫 Azure Redis 快取](../azure-government/documentation-government-services-database.md#azure-redis-cache)和[Azure china （中國） 雲端 Azure Redis 快取](https://www.azure.cn/documentation/services/redis-cache/)。

使用 Azure 政府雲端和 Azure china （中國） 雲端中的 PowerShell 的 Azure Redis 快取資訊，請參閱[如何連線至 Azure 政府雲端或 Azure china （中國） 雲端](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)。


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>StackExchange.Redis 設定選項代表該怎麼做？

StackExchange.Redis 有許多選項。 本節說話相關的一些常見的設定。 如需詳細瞭解 StackExchange.Redis 選項的詳細資訊，請參閱[StackExchange.Redis 設定](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md)。

ConfigurationOptions|描述|建議
---|---|---
AbortOnConnectFail|當設為 true，連線會連接網路失敗後。|設定為 false，並讓 StackExchange.Redis 自動重新連線。
ConnectRetry|次數重複嘗試連線期間初始連線。| 請參閱以下指導方針。 |
ConnectTimeout|Ms 的逾時連線作業。| 請參閱以下指導方針。 |

在大多數情況下的用戶端的預設值是足夠的。 您可以微調根據您的工作負載的選項。

-   **重試**
    -   ConnectRetry 和 ConnectTimeout 的一般指導方針是快速失敗，然後再試一次。 這根據您的工作量及多少時間計算平均值，會針對您的用戶端意指命令和接收的回應。
    -   讓 StackExchange.Redis 自動重新連線，而不是檢查連線狀態，並重新連線自己。 **避免使用 ConnectionMultiplexer.IsConnected 屬性**。
    -   Snowballing-有時您可能會遇到的問題的您會重試，而此 snowballs 且永不復原。 在此情況下，您應該考慮使用[一般指導方針再試一次](best-practices-retry-general.md)所述的指數輪詢重試演算法發佈 Microsoft 模式與作法群組。
-   **逾時值**
    -   請考慮您的工作量，並依此設定值。 如果您儲存較大的值，將會出現逾時為較高的值。
    -   設定`AbortOnConnectFail`false 和讓 StackExchange.Redis 要重新連接您。
    -   使用單一 ConnectionMultiplexer 執行個體的應用程式。 您可以使用 LazyConnection 建立的連線屬性時，會傳回一個執行個體，[連線到使用 ConnectionMultiplexer 類別的快取](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)中所示。
    -   設定`ConnectionMultiplexer.ClientName`屬性診斷應用程式執行個體唯一名稱。
    -   使用多個`ConnectionMultiplexer`自訂工作負載的執行個體。
    -   如果您有不同的載入應用程式中，您可以遵循此模型。 例如︰
    -   您可以有多工器用來處理大型金鑰。
    -   您可以有多工器用來處理小型鍵。
    -   可以設定的連線逾時的不同值，如您所使用的每個 ConnectionMultiplexer 重試邏輯。
    -   設定`ClientName`每個屬性，協助診斷多工器。
    -   這會導致更精簡的每個延遲`ConnectionMultiplexer`。

### <a name="what-redis-cache-clients-can-i-use"></a>可以使用哪些意指快取用戶端？

其中一個優點意指是有許多用戶端支援許多不同的開發語言。 新的用戶端清單，請參閱[Redis 用戶端](http://redis.io/clients)。 封面數種不同的語言和用戶端的教學課程，瞭解[如何使用 Azure Redis 快取](cache-dotnet-how-to-use-azure-redis-cache.md)，然後按一下所要的語言的語言切換器頂端的 [文件]。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>有 Azure Redis 快取的本機模擬器嗎？

沒有本機模擬器 Azure Redis 快取，但是您可以從[Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/)執行意指 server.exe MSOpenTech 版本，在您的本機電腦上，並連線到本機快取模擬器中，前往類似的體驗，如下列範例所示。


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


您可以選擇設定以深入分析符合您線上 Azure Redis 快取[預設快取設定](cache-configure.md#default-redis-server-configuration)，視需要[redis.conf](http://redis.io/topics/config)檔案。

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>我要如何執行意指命令？

您可以使用任何命令列於[Redis 命令](http://redis.io/commands#)，除了在[Redis Azure Redis 快取中不支援的命令](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache)列的命令。 若要執行意指命令您有幾個選項。

-   如果您有標準或進階版的快取，您可以執行使用[Redis 主控台](cache-configure.md#redis-console)意指命令。 能夠安全 Azure 入口網站中執行意指命令。
-   您也可以使用意指命令列工具。 若要使用它們，請執行下列步驟。
-   下載[Redis 命令列工具](https://github.com/MSOpenTech/redis/releases/)。
-   連線至快取使用`redis-cli.exe`。 傳遞快取結束點，使用-h 切換，以及使用-在下列範例所示的鍵。
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - 請注意，意指命令列工具要使用的 SSL 連接埠，但您可以使用公用程式，例如`stunnel`安全地連線至 SSL 連接埠工具中[宣佈使用 ASP.NET 工作階段狀態的提供者 Redis 預覽版本](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)部落格文章的下列指示。

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Azure Redis 快取為什麼沒有等部分 Azure 服務 MSDN 類別庫參考？

Microsoft Azure Redis 快取為基礎的常用開啟來源 Redis 快取，可以透過各種不同的[Redis 用戶端](http://redis.io/clients)適用許多程式設計語言。 每個用戶端有自己的 API，可讓您的來電至] 使用[Redis 命令](http://redis.io/commands)的意指快取執行個體。

Msdn; 因為每個用戶端不同，有不一個集中的課程參照請改為每個用戶端維護其參照說明文件。 除了參考文件中，有幾個教學課程，顯示如何開始使用 Azure Redis 快取使用不同語言和快取用戶端。 若要存取這些教學課程，瞭解[如何使用 Azure Redis 快取](cache-dotnet-how-to-use-azure-redis-cache.md)，然後按一下所要的語言的語言切換器頂端的 [文件。


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>可以使用 Azure Redis 快取為 PHP 工作階段快取嗎？

是的若要使用 PHP 工作階段快取 Azure Redis 快取，指定您 Azure Redis 快取的執行個體中的連接字串`session.save_path`。

>[AZURE.IMPORTANT] 時使用 PHP 工作階段快取的 Azure Redis 快取，您必須 URL 編碼用來連線到快取，如下列範例所示的安全性鍵。
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>如果鍵不是 URL 編碼，您可能會收到類似下列例外狀況︰`Failed to parse session.save_path`

如需有關如何使用 Redis 快取為 PhpRedis 用戶端 PHP 工作階段快取的詳細資訊，請參閱[PHP 工作階段處理常式](https://github.com/phpredis/phpredis#php-session-handler)。



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>何時應該啟用非 SSL 的連接埠連線到意指？

Redis 伺服器不支援 SSL 不在] 方塊中，但是 Azure Redis 快取。 如果您連線到 Azure Redis 快取及您的用戶端支援 SSL，例如 StackExchange.Redis，您應該使用 SSL。

請注意新 Azure Redis 快取執行個體的預設為停用非 SSL 連接埠。 如果您的用戶端不支援 SSL，您必須按照[Azure Redis 快取中的快取設定](cache-configure.md)文件的[存取權的連接埠](cache-configure.md#access-ports)] 區段中的指示啟用非 SSL 連接埠。

例如 redis 工具`redis-cli`不使用 SSL 連接埠，但您可以使用公用程式，例如`stunnel`安全地連線至 SSL 連接埠工具中[宣佈使用 ASP.NET 工作階段狀態的提供者 Redis 預覽版本](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)部落格文章的下列指示。

如需有關下載意指工具的指示，請參閱[如何執行意指命令？](#cache-commands)一節。



### <a name="what-are-some-production-best-practices"></a>什麼是一些生產的最佳作法？

-   [StackExchange.Redis 最佳作法](#stackexchangeredis-best-practices)
-   [設定和概念](#configuration-and-concepts)
-   [效能測試](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis 最佳作法

-   設定`AbortConnect`為 false，然後讓 ConnectionMultiplexer 自動重新連線。 [如需詳細資訊請參閱](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)。
-   重複使用 ConnectionMultiplexer-不會建立一個新的每個要求。 `Lazy<ConnectionMultiplexer>`強烈建議圖樣[此處所示](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)。
-   具有較小值，最適合 redis 運作，因此，請考慮 chopping 較大的資料，將多個鍵。 在[此 Redis 討論](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)，100 kb 視為 「 大 」。 請閱讀[本文](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size)範例問題的原因可能是較大的值。
-   設定[集區設定](#important-details-about-threadpool-growth)，避免逾時。
-   使用至少 5 秒的預設 connectTimeout。 這會讓 StackExchange.Redis 重新建立連線，若網路 blip 足夠的時間。
-   請注意您執行的不同的作業相關聯的效能成本。 例如， `KEYS` ] 命令的 o （n） 作業，應避免使用。 [Redis.io 網站](http://redis.io/commands/)有每個作業的支援時間複雜度周圍的詳細資料。 按一下以查看每個作業複雜度每一個命令。

#### <a name="configuration-and-concepts"></a>設定和概念

-   使用標準或進階版層生產系統。 基本層是沒有 SLA 與沒有資料複製為單一節點系統。 此外，使用至少 C1 快取。 簡單的開發測試案例其實是 C0 快取。
-   請記住，意指**記憶體內**的資料存放區。 讓知道的案例可能發生資料遺失，請閱讀[本文](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)。
-   開發系統，使其可以處理連線音效[因為修補和容錯移轉](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)。

#### <a name="performance-testing"></a>效能測試

-   開始使用`redis-benchmark.exe`若要瞭解可能的處理量撰寫您自己的效能前先測試。 請注意該意指事先不支援 SSL，因此您將已[啟用透過 Azure 入口網站的非 SSL 連接埠](cache-configure.md#access-ports)之前執行測試。 如需範例，請參閱[如何可以基準測試，測試我快取的效能？](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   用戶端 VM 用來進行測試應該位於您意指快取執行個體以相同的區域。
-   我們建議有較佳的硬體並會提供最佳的結果，使用您的用戶端 Dv2 VM 數列。
-   請確定您的用戶端 VM 您選擇具有至少多運算測試當做快取的頻寬功能。 
-   如果您是在 Windows 上，請在用戶端電腦上啟用 VRSS。 [如需詳細資訊請參閱](https://technet.microsoft.com/library/dn383582.aspx)。
-   付費層意指執行個體會有更佳的網路延遲和處理量因為他們執行的較佳的硬體上 CPU 和網路。

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>什麼是注意事項使用一般意指命令時？

-   您不應該執行某些意指命令需要花很長的時間才能完成不瞭解這些命令的影響。
    -   例如無法執行[鍵](http://redis.io/commands/keys)命令生產環境中時，可能需要很長的時間，以傳回根據按鍵的數目。 意指單一階層式伺服器，而處理一次的命令。 如果您有其他按鍵之後所發行的命令，不進行處理，直到意指處理按鍵] 命令。 [Redis.io 網站](http://redis.io/commands/)有每個作業的支援時間複雜度周圍的詳細資料。 按一下以查看每個作業複雜度每一個命令。
-   金鑰大小-我應該使用較小的金鑰值或較大的金鑰值？ 在 [一般，取決於此案例。 如果您的狀況需要較大的金鑰然後您可以調整 ConnectionTimeout 並再試一次值並調整您重試邏輯。 從意指伺服器觀點來看，較小的值被觀察有較佳的效能。
-   這不表示您無法儲存意指; 在較大的值您必須考量下列事項。 延遲會更大。 如果您有一組資料為較大和較小的其中一個，您可以使用多個 ConnectionMultiplexer 執行個體，每一組不同的逾時與重試] 的值，以[StackExchange.Redis 設定選項做什麼](#cache-configuration)的前一節所述設定。



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>如何基準測試，測試我快取的效能？

-   [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)您可以[監控](cache-how-to-monitor.md)您的快取的狀況。 您可以檢視計量 Azure 入口網站中，您也可以[下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)其使用您所選擇的工具。
-   您可以使用意指 benchmark.exe 載入測試意指伺服器。
-   確定測試用戶端和意指快取載入相同的區域。
-   使用意指 cli.exe，並監控使用 [資訊] 命令的快取。
-   如果您載入導致高記憶體分散然後您應該縮放較大的快取大小。
-   如需有關下載意指工具的指示，請參閱[如何執行意指命令？](#cache-commands)一節。

以下是使用意指 benchmark.exe 的範例。 取得正確的結果，請從 VM 相同的區域，為您的快取中執行此命令。

-   使用 1 k 裝載的測試管線設定要求

    意指 benchmark.exe-h **yourcache**。 redis.cache.windows.net- **yourAccesskey** -t 設定-n 1000000-d 1024 P 50
    
-   測試管線取得要求使用 1 k 內容。 
    注意︰ 執行設定測試上方顯示第一次來填入快取
    
    意指 benchmark.exe-h **yourcache**。 redis.cache.windows.net- **yourAccesskey** -t 取得-n 1000000-d 1024-P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>集區等比級數相關的重要詳細資料

CLR 集區有兩種類型的執行緒-「 工作 」 和 「 I/O 完成連接埠 」 (又稱 IOCP) 往來。 

-   執行緒以用於當項目，例如處理`Task.Run(…)`或`ThreadPool.QueueUserWorkItem(…)`方法。 當工作需要會發生的背景執行緒 CLR 中各種元件也會使用這些執行緒。
-   非同步 IO 發生 （例如從網路讀取） 時，會使用 IOCP 往來。 

執行緒集區提供新的工作執行緒或 I/O 完成執行緒視 （不含任何節流），直到達到執行緒的每個類型的 「 最小值 」 設定。 根據預設，最小的數字的執行緒設定為在系統的處理器數目。 

後的現有 （忙碌） 執行緒數目的執行緒，集區 「 最低 」 的數字會的點擊流速用以率會插入新執行緒 500 毫秒每一個執行緒。 這表示，如果您的系統工作需要 IOCP 對話的一組，會處理的很快。 不過，如果工作的突發大於設定 「 最小值] 設定，會有一些延遲執行緒等待其中一項動作發生時，處理部分工作。

1. 現有的執行緒會變成免費工作處理程序。
1. 任何現有的執行緒會不變成免費 500，因此在建立新的執行緒。

基本上，這表示的忙碌執行緒數目超過 Min 執行緒時，您可能會支付 500年延遲之前的應用程式會處理網路流量。 此外，請務必請注意，當現有的執行緒保持閒置超過 15 秒數 （根據我的記得），將會清理可以重複此循環圖的成長和縮小。

如果我們看看範例錯誤訊息從 StackExchange.Redis (建立 1.0.450 或更新版本)，您會看到現在列印結果集區統計資料 （請參閱 IOCP 和背景工作詳細資料，如下）。

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

在上述範例中，您可以看到 IOCP 執行緒有 6 忙碌的執行緒，系統會設為允許 4 的最小往來。 在此情況下，用戶端會有可能會看到兩個 500 ms 延遲因為 6 > 4。

請注意，是否成長 IOCP 或工作者執行緒取得經流速控制 StackExchange.Redis 可以按逾時。

### <a name="recommendation"></a>建議

提供這項資訊，我們強烈建議客戶將 IOCP 和工作者執行緒最小的設定值設定為大於預設值的項目。 我們無法提供一體指引此值應該是什麼因為一個應用程式的正確值會太高/低的另一個應用程式。 這項設定可以也會影響效能的其他部分的複雜的應用程式，，每位客戶需要微調自己的需求這項設定，讓。 好的起點為 200 或 300，然後測試並視需要調整。

如何設定此設定︰

-   在 ASP.NET 中，使用 [下的[「 minIoThreads 」 設定的設定][]`<processModel>`組態 web.config 中的項目。 如果您執行的 Azure 網站內，此設定不透過設定選項。 不過，您應該仍然可以將此程式設定 （請參閱下方） 中 global.asax.cs 您 Application_Start 方法。

> **重要附註︰**指定此設定項目中的值是*每個核心*設定。 例如，如果您有 4 核心電腦，並且想要在執行階段 200 您 minIOThreads 設定，您會使用`<processModel minIoThreads="50"/>`。

-   外部 ASP.NET，使用 [ [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx)API。

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>啟用伺服器 GC 使用 StackExchange.Redis 時，用戶端上取得更多處理量

啟用伺服器 GC 可以最佳化用戶端，並使用 StackExchange.Redis 時提供更佳的效能和處理量。 如需有關伺服器 GC，以及如何將它啟用的詳細資訊，請參閱下列文章。

-   [若要啟用伺服器 GC](https://msdn.microsoft.com/library/ms229357.aspx)
-   [回收的基本概念](https://msdn.microsoft.com/library/ee787088.aspx)
-   [回收與效能](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>如何監視健康狀況與我的快取的效能？

[Azure 入口網站](https://portal.azure.com)中可以監視 Microsoft Azure Redis 快取執行個體。 您可以檢視指標、 釘選到 Startboard 指標圖表、 自訂日期及時間範圍監控圖表、 新增和移除圖表中的指標以及設定提醒，符合特定條件時。 如需詳細資訊，請參閱[監視器 Azure Redis 快取](cache-how-to-monitor.md)。

Redis 快取**設定**刀**支援 + 疑難排解**區段也包含數種工具監控與疑難排解快取。 

-   **疑難排解**提供常見的問題與策略解決問題的相關資訊。
-   **稽核記錄**提供在您的快取上執行動作的相關資訊。 您也可以使用篩選，以展開此檢視包含其他的資源。
-   **資源狀況**會檢查您的資源，並會告訴您是否正在執行如預期般。 如需有關 Azure 資源狀況服務的詳細資訊，請參閱[Azure 資源狀況概觀](../resource-health/resource-health-overview.md)。
-   **新增支援要求**提供選項] 以開啟 [快取的支援要求。

這些工具可讓您監視您 Azure Redis 快取的執行個體的狀況，協助您管理快取應用程式。 如需詳細資訊，請參閱[支援與疑難排解設定](cache-configure.md#support-amp-troubleshooting-settings)。

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>我快取診斷儲存的帳戶設定變更，有何改變？

在相同的地區碼和訂閱的快取共用相同的診斷儲存設定，並變更 （診斷啟用或停用或變更儲存帳戶） 設定是否適用於所有快取訂閱中的區域中的。 如果您的快取的診斷設定變更，檢查是否有變更的診斷相同的訂閱和地區的其他快取設定。 若要檢視的快取的稽核記錄的其中一個檢查方法是`Write DiagnosticSettings`事件。 如需有關使用稽核記錄檔的詳細資訊，請參閱[檢視事件和稽核記錄檔](../monitoring-and-diagnostics/insights-debugging-with-events.md)和[稽核作業與資源管理員](../resource-group-audit.md)。 如需有關如何監視 Azure Redis 快取事件的詳細資訊，請參閱[作業和通知](cache-how-to-monitor.md#operations-and-alerts)。

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>為什麼診斷啟用一些新的快取，而不是其他？

在相同的地區和訂閱的快取共用相同的診斷儲存設定。 如果您在相同的區域及訂閱已啟用診斷程式的其他快取以建立新的快取，診斷已啟用新的快取使用相同的設定。


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>為什麼會看見逾時？

在您用來與意指的用戶端，發生逾時。 大多數的情況下意指伺服器並不逾時。 當命令傳送至意指伺服器時，命令佇列時，意指伺服器最後挑選] 命令，並執行。 不過在用戶端可以在此程序逾時，如果它例外狀況會引發呼叫端。 如需有關如何疑難排解逾時問題的詳細資訊，請參閱[用戶端側疑難排解](cache-how-to-troubleshoot.md#client-side-troubleshooting)和[StackExchange.Redis 逾時例外狀況](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions)。


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>為什麼我的用戶端的連線中斷快取？

以下是一些常見的快取中斷原因。

-   用戶端的原因
    -   用戶端應用程式已重新部署。
    -   用戶端應用程式執行縮放比例的作業。
        -   在雲端服務或 Web 應用程式，這可能是因為自動調整大小。
    -   在用戶端的網路圖層變更。
    -   在用戶端或用戶端與伺服器間的網路節點，就會發生暫時性錯誤。
    -   已達頻寬臨界值限制。
    -   CPU 繫結作業太久完成。
-   伺服器端的原因
    -   在標準快取提供，Azure Redis 快取服務發起的租用戶容錯移轉從主節點至第二個節點。
    -   Azure 已修補快取部署的位置的執行個體
        -   這可能是意指伺服器更新或一般 VM 進行的維修作業。







### <a name="which-azure-cache-offering-is-right-for-me"></a>適合我是哪一個 Azure 快取服務？

>[AZURE.IMPORTANT]最後一年的[通知](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)，依照 Azure 管理快取服務和 Azure 中的角色快取服務會在 2016 年 11 月 30 日上已停用。 我們建議使用[Azure Redis 快取](https://azure.microsoft.com/services/cache/)。 移轉的詳細資訊，請參閱[從 Azure Redis 快取管理快取服務移轉](cache-migrate-to-redis.md)。

### <a name="azure-redis-cache"></a>Azure 意指快取
Azure Redis 快取，而且通常用於大小 53 GB SLA 99.9%的可用性。 最新的[進階版層](cache-premium-tier-intro.md)提供的大小 530 GB 及支援叢集、 VNET，並持續性，使用 99.9 %sla。

Azure Redis 快取讓客戶能夠使用安全的專用意指快取，由 Microsoft 管理。 使用這項優惠，您都可以利用豐富的功能集和生態提供意指，以及可靠裝載和監視 microsoft。

這只處理關鍵值組傳統快取，與意指則是熱門其高度效能資料類型的項目。 Redis 也支援執行這些類型，例如將字串; 原子作業遞增雜湊; 中的值推入至清單。計算交集與 union 的差異。或是成員的最高排序集中的排名。 其他功能包括支援交易、 發行/子、 Lua 指令碼] 鍵，以限制時間存留及設定的設定，讓意指更類似傳統的快取。

另一個意指成功的關鍵長寬是建置健全、 生動的開啟來源生態。 這會反映在意指用戶端可以使用各種不同的設定多種語言。 這個選項可讓它使用幾乎任何您想要建立 Azure 內的工作量。 

如需有關如何開始使用 Azure Redis 快取的詳細資訊，請參閱[如何使用 Azure Redis 快取](cache-dotnet-how-to-use-azure-redis-cache.md)和[Azure Redis 快取文件](https://azure.microsoft.com/documentation/services/redis-cache/)。

### <a name="managed-cache-service"></a>受管理的快取服務
[管理快取服務已設為 2016 年 11 月 30 日已停用。](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>在 [角色快取
[在 [角色快取設定為 2016 年 11 月 30 日已停用]。](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[「 minIoThreads 」 設定的設定]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
