<properties 
    pageTitle="Azure Redis 快取範例 |Microsoft Azure" 
    description="瞭解如何使用 Azure Redis 快取" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-samples"></a>Azure Redis 快取範例 

本主題提供 Azure Redis 快取範例，其中涵蓋案例，例如從快取，快取、 讀取及撰寫資料連線，以及使用 ASP.NET Redis 快取提供者的清單。 某些範例可下載的專案，而且部分提供逐步指引並包括程式碼片段，但不要連結至可下載的專案。

## <a name="hello-world-samples"></a>認識全球範例

本節中的範例顯示的連接至 Azure Redis 快取執行個體和讀取和寫入資料快取使用不同語言的基本概念，Redis 用戶端。

[好](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)範例會示範如何執行各種使用[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET 用戶端的快取作業。

此範例顯示的方式︰

-   使用各種不同的連線選項
-   讀取和寫入與使用同步和非同步作業的快取的物件
-   使用 Redis MGET/MSET 命令傳回指定的索引鍵的值
-   執行意指交易作業
-   使用意指清單與排序的集
-   使用 JsonConvert 序列化程式存放區.NET 物件
-   使用意指設定實作標記
-   使用意指叢集

如需詳細資訊，請參閱[StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis)文件上 github，及更多的使用狀況分析藍本請參閱[StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests)單位測試。

[如何使用 Azure Redis 快取 Python](cache-python-get-started.md)示範如何快速入門 Azure Redis 快取使用 Python 和[意指 py](https://github.com/andymccurdy/redis-py)用戶端。

[使用.NET 物件快取中](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)會顯示序列化.NET 物件，讓您撰寫他們與他們讀取 Azure Redis 快取執行個體的其中一個方法。 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>使用 ASP.NET SignalR 作為延展後擋板 Redis 快取

[使用 Redis 快取延展後擋板 ASP.NET SignalR 的](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane)範例會示範如何使用 Azure Redis 快取為 SignalR 後擋板。 如需有關後擋板的詳細資訊，請參閱[使用意指 SignalR Scaleout](http://www.asp.net/signalr/overview/performance/scaleout-with-redis)。

## <a name="redis-cache-customer-query-sample"></a>Redis 快取客戶查詢範例

此範例會示範存取快取中的資料與存取資料從持續儲存空間之間的比較效能。 此範例會有兩個專案。

-   [示範如何 Redis 快取時，可以快取資料，以提升效能](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-   [種子資料庫及快取的示範](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET 工作階段狀態和輸出快取

[使用 Azure Redis 快取儲存 ASP.NET 工作階段狀態和網頁或](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching)範例會示範如何使用 Azure Redis 快取儲存 ASP.NET 工作階段和意指使用的工作階段狀態與網頁或提供者的輸出快取。

## <a name="manage-azure-redis-cache-with-maml"></a>管理 MAML Azure 意指快取

[管理 Azure Redis 快取使用 Azure 管理文件庫](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)的範例會示範如何管理-使用 Azure 管理文件庫 (建立 / 更新 / 刪除) 快取。 

## <a name="custom-monitoring-sample"></a>自訂監控範例

[存取 Redis 快取監視資料](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)範例會示範如何存取監視資料的 Azure Redis 快取外部 Azure 入口網站。

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>使用 PHP 和意指撰寫 Twitter 樣式複本

[Retwis](https://github.com/SyntaxC4-MSFT/retwis)範例是 Redis 什麼。 這是最小的 Twitter 樣式社交網路複製撰寫使用意指和 PHP 使用[Predis](https://github.com/nrk/predis)用戶端。 程式碼是設計用來很簡單，以顯示不同同時 Redis 資料結構。

## <a name="bandwidth-monitor"></a>頻寬監視器

[頻寬監視器](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor)範例可讓您監視用戶端上的頻寬。 若要測量的頻寬，快取用戶端電腦上執行範例的來電至] 快取，然後由頻寬監視範例報告的頻寬可以一同觀看。
