<properties 
    pageTitle="如何使用 Azure 意指快取 |Microsoft Azure" 
    description="瞭解如何改善 Azure Redis 快取 Azure 應用程式的效能" 
    services="redis-cache,app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="08/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache"></a>如何使用 Azure 意指快取

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

本指南為您示範如何開始使用**Azure Redis 快取**。 Microsoft Azure Redis 快取為基礎的常用開啟來源 Redis 快取。 其可讓您存取安全的專用意指快取，由 Microsoft 管理。 從 Microsoft Azure 中的任何應用程式存取建立使用 Azure Redis 快取的快取。

Microsoft Azure Redis 快取適用於下列層︰

-   **基本**– 單一節點。 多個大小 53 GB。
-   **標準**– 兩個節點主要/複本。 多個大小 53 GB。 99.9 %SLA。
-   **進階版**– 兩個節點主要/具有最多 10 個擊碎複本。 多個大小從 6 GB 530 GB （與我們連絡的詳細資訊）。 所有的標準層功能及更多包括支援[Redis 叢集](cache-how-to-premium-clustering.md)、 [Redis 持續性](cache-how-to-premium-persistence.md)，及[Azure 虛擬網路](cache-how-to-premium-vnet.md)。 99.9 %SLA。

每一層不同的功能和價格。 定價詳細資訊，請參閱[快取定價詳細資料][]。

本文說明如何使用[StackExchange.Redis][]用戶端使用 C\#程式碼。 涵蓋的案例包括**建立與設定快取**、**設定快取用戶端**與**新增及移除物件快取**。 如需有關如何使用 Azure Redis 快取的詳細資訊，請參閱[下一步][]] 區段。 建置 ASP.NET MVC Redis 快取 web 應用程式的逐步教學課程，瞭解[如何建立 Web 應用程式與 Redis 快取](cache-web-app-howto.md)。

<a name="getting-started-cache-service"></a>
## <a name="get-started-with-azure-redis-cache"></a>開始使用 Azure 意指快取

快速入門 Azure Redis 快取很容易。 若要開始，您可以佈建和設定快取。 接下來，使他們可以存取快取設定快取用戶端。 快取用戶端的設定之後，就可以開始使用它們。

-   [建立快取][]
-   [將快取用戶端設定][]

<a name="create-cache"></a>
## <a name="create-a-cache"></a>建立的快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### <a name="to-access-your-cache-after-its-created"></a>建立之後，存取您的快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

如需更多關於設定您的快取的詳細資訊，請參閱[如何設定 Azure Redis 快取](cache-configure.md)。

<a name="NuGet"></a>
## <a name="configure-the-cache-clients"></a>將快取用戶端設定

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

之後，快取設定用戶端專案，您可以使用 [使用快取下列各節所述的技巧。

<a name="working-with-caches"></a>
## <a name="working-with-caches"></a>使用 [快取

本節中的步驟說明如何執行一般工作，使用快取。

-   [連線至快取][]
-   [新增並擷取快取中的物件][]
-   [使用.NET 快取中的物件](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## <a name="connect-to-the-cache"></a>連線至快取

若要以程式設計方式使用快取，您需要快取的參考。 將下列文字新增到您想要使用 StackExchange.Redis 用戶端存取 Azure Redis 快取的任何檔案的最上方。

    using StackExchange.Redis;

>[AZURE.NOTE] StackExchange.Redis 用戶端需要.NET Framework 4 或更新版本。

Azure Redis 快取連線由管理`ConnectionMultiplexer`類別。 這個類別設計用來共用與重複使用整個用戶端應用程式，並不需要每個作業為基礎建立。 

連線至 Azure Redis 快取，並傳回連線的執行個體`ConnectionMultiplexer`，來電靜態`Connect`方法和階段中的快取端點和鍵像下列範例。 使用密碼參數 Azure 入口網站所產生的金鑰。

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] 警告︰ 永遠不儲存認證原始程式碼中。 若要保留這個範例簡單，我有程式碼中顯示它們。 如需儲存憑證資訊，請參閱[如何應用程式字串和連線字串的工作][]。

如果您不想要使用 SSL，請設定`ssl=false`或省略`ssl`參數。

>[AZURE.NOTE] 新的快取的預設為停用非 SSL 連接埠。 啟用的非 SSL 連接埠的指示，請參閱[Access 連接埠](cache-configure.md#access-ports)。

若要共用的其中一個方法`ConnectionMultiplexer`應用程式中的執行個體是有連線的執行個體，類似下列範例會傳回的靜態屬性。 能夠安全執行緒初始化只連線的單一`ConnectionMultiplexer`執行個體。 在以下範例`abortConnect`設為 false，這表示會成功通話，即使不會建立 Azure Redis 快取的連線。 一個主要功能`ConnectionMultiplexer`它會自動還原連線至快取一次網路問題，或其他原因有解決。

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

如需進階的連線設定選項的詳細資訊，請參閱[StackExchange.Redis 設定模型][]。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

建立連線後，會傳回意指快取資料庫的參照，則可電話`ConnectionMultiplexer.GetDatabase`方法。 從傳回的物件`GetDatabase`方法是輕量型傳遞物件並不需要儲存。

    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);

    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

現在您已經知道如何連線至 Azure Redis 快取執行個體，並傳回快取資料庫的參照，讓我們來看看使用 [快取。

<a name="add-object"></a>
## <a name="add-and-retrieve-objects-from-the-cache"></a>新增並擷取快取中的物件

可以儲存的項目，並使用快取中擷取`StringSet`和`StringGet`方法。

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

Redis 大部分的資料，做為意指字串，但這些字串可以包含許多類型的資料，包括序列的二進位資料，您可以使用儲存.NET 物件快取中的儲存區。

呼叫時`StringGet`，如果物件存在，則會傳回，如果沒有出現，並`null`會傳回。 在此情況下，可以從您要的資料來源擷取的值並將其儲存在後續使用快取。 這就是所謂的快取離題模式。

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

若要指定快取中的到期日的項目，請使用`TimeSpan`的參數`StringSet`。

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## <a name="work-with-net-objects-in-the-cache"></a>使用.NET 快取中的物件

Azure Redis 快取可以快取.NET 物件，以及基本資料類型，但可以快取.NET 物件之前必須序列化。 此負責的應用程式開發人員，以及提供序列化工具選項開發人員彈性。

將物件序列化的其中一個簡單的方法是使用`JsonConvert` [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1)序列化方式和序列化與 JSON。 下列範例會示範取得及設定使用`Employee`物件執行個體。


    class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    
        public Employee(int EmployeeId, string Name)
        {
            this.Id = EmployeeId;
            this.Name = Name;
        }
    }

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## <a name="next-steps"></a>後續步驟

現在，您學到的基本概念，請遵循這些連結，深入瞭解 Azure Redis 快取。

-   請參閱 Azure Redis 快取的 ASP.NET 提供者。
    -   [Azure 意指工作階段狀態提供者](cache-aspnet-session-state-provider.md)
    -   [Azure Redis 快取 ASP.NET 輸出快取提供者](cache-aspnet-output-cache-provider.md)
-   [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)您可以[監控](cache-how-to-monitor.md)您的快取的狀況。 您可以檢視計量 Azure 入口網站中，您也可以[下載並檢閱](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)其使用您所選擇的工具。
-   查看[StackExchange.Redis 快取用戶端文件][]。
    -   Azure Redis 快取可存取的許多意指用戶端和開發語言。 如需詳細資訊，請參閱[http://redis.io/clients][]。
-   Azure Redis 快取也會使用協力廠商服務及工具，例如 Redsmin 和 Redis 桌面管理員中。
    -   如需有關 Redsmin 的詳細資訊，請參閱[如何擷取 Azure Redis 連線字串，並使用它的 Redsmin][]。
    -   存取和檢查與使用[RedisDesktopManager](https://github.com/uglide/RedisDesktopManager)GUI 的 Azure Redis 快取中的資料。
-   請參閱[redis][]文件，並瞭解[redis 資料類型][]和[Redis 資料類型的 15 分鐘簡介][]。



<!-- INTRA-TOPIC LINKS -->
[後續步驟]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[建立快取]: #create-cache
[Configure the cache]: #enable-caching
[將快取用戶端設定]: #NuGet
[Working with Caches]: #working-with-caches
[連線至快取]: #connect-to-cache
[新增並擷取快取中的物件]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[如何擷取 Azure Redis 連線字串，並使用該 Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis 設定模型]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[定價詳細資料的快取]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: ../azure-resource-manager/resource-group-overview.md

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis 快取用戶端文件]: http://github.com/StackExchange/StackExchange.Redis#documentation

[意指]: http://redis.io/documentation
[Redis 資料類型]: http://redis.io/topics/data-types
[意指資料類型 15 分鐘簡介]: http://redis.io/topics/data-types-intro

[應用程式字串和連線字串的運作方式]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/


