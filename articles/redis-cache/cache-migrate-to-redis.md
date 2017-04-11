<properties 
    pageTitle="快取移轉到意指 |Microsoft Azure"
    description="瞭解如何移轉 Azure Redis 快取管理快取服務應用程式"
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
    ms.date="09/30/2016"
    ms.author="sdanie" />

# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>受管理的快取服務移轉到 Azure 意指快取

移轉至 Azure Redis 快取使用 Azure 管理快取服務應用程式可以利用應用程式，根據快取應用程式所用的受管理的快取服務功能的最小變更。 雖然 Api 不完全相同他們類似，，其中許多的現有的程式碼使用受管理的快取服務存取快取的可重複使用的最小的變更。 本主題說明如何進行必要的設定和移轉使用 Azure Redis 的快取管理快取服務應用程式的應用程式變更，並示範如何使用 Azure Redis 快取功能來實作管理快取服務快取的功能。

## <a name="migration-steps"></a>移轉步驟

下列步驟，才能移轉使用 Azure Redis 快取管理快取服務應用程式。

-   管理快取服務功能對應至 Azure Redis 快取
-   選擇 [快取服務
-   建立的快取
-   將快取用戶端設定
    -   移除受管理的快取服務設定
    -   設定使用 StackExchange.Redis NuGet 套件的快取用戶端
-   移轉管理快取服務代碼
    -   連線到使用 ConnectionMultiplexer 類別的快取
    -   存取快取中的基本資料類型
    -   使用.NET 快取中的物件
-   ASP.NET 工作階段狀態和 Azure Redis 快取的輸出快取移轉 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>管理快取服務功能對應至 Azure Redis 快取

Azure 管理快取服務和 Azure Redis 快取類似，但其功能實作不同的方式。 本節說明一些差異，並提供的指引 Azure Redis 快取中執行管理快取服務的功能。

|受管理的快取服務功能|受管理的快取服務支援|Azure Redis 快取支援|
|---|---|---|
|命名快取|設定預設快取，然後標準和進階版快取中最九個額外的命名快取的產品，您可以設定視需要。|Azure 意指快取會有哪個多個可用於執行類似的功能命名快取的資料庫 （預設的 16）。 如需詳細資訊，請參閱[預設 Redis 伺服器設定](cache-configure.md#default-redis-server-configuration)。|
|可用性|在 [一般] 及 [進階版的快取服務快取中的項目，提供高可用性。 如果項目遺失，因為失敗，備份複本的快取中的項目是仍可使用。 第二個快取寫入進行同步。|使用標準和進階版快取方案，有兩個節點主要/複本設定 （進階版快取中的每個晶怪有主要/複本組） 的可用性。 非同步進行寫入複本。 如需詳細資訊，請參閱[Azure Redis 快取價格](https://azure.microsoft.com/pricing/details/cache/)。|
|通知|允許在命名快取的各種不同的快取作業發生時收到非同步通知的用戶端。|用戶端應用程式可以使用意指發行/子程序或[再通知](cache-configure.md#keyspace-notifications-advanced-settings)來達成通知類似的功能。|
|本機快取|儲存在本機額外快速存取用戶端上快取物件的複本。|用戶端應用程式需要實作這項功能使用字典或類似的資料結構。|
|收回原則|無或 LRU。 預設的原則是 LRU。|Azure Redis 快取支援下列收回原則︰ 靜態 lru、 allkeys lru、 動態隨機、 allkeys 隨機，動態 ttl noeviction。 預設的原則是靜態 lru。 如需詳細資訊，請參閱[預設 Redis 伺服器設定](cache-configure.md#default-redis-server-configuration)。|
|到期原則|預設的到期原則絕對值，而預設的到期時間間隔會十分鐘。 滑動和永不原則也可供使用。|依預設快取中的項目不會過期，但可以在 [使用快取設定多載的每個寫入基礎設定到期日。 如需詳細資訊，請參閱[新增和擷取物件快取](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache)。|
|區域和標記|區域是子的快取的項目。 地區也支援其他稱為標籤的描述性字串的註釋的快取的項目。 區域支援能夠執行任何標記的項目上的搜尋作業，在該區域。 在區域內的所有項目位於的快取叢集單一節點。|意指快取包含單一節點 （除非啟用意指叢集），不適用於管理快取服務區域的概念。 擷取機碼，以便將內嵌於機碼名稱描述性的標籤，並用於稍後擷取的項目時，請 redis 支援搜尋和萬用字元的操作。 實作使用意指標記解決方案的範例，請參閱[實作與意指的快取標記](http://stackify.com/implementing-cache-tagging-redis/)。|
|序列化|受管理的快取支援 NetDataContractSerializer、 BinaryFormatter，以及自訂序列化程式使用。 預設值為 NetDataContractSerializer。|負責之前先將它們放入快取中，以選取的用戶端應用程式開發人員序列化序列化.NET 物件的用戶端應用程式。 如需的資訊與範例程式碼，請參閱[使用.NET 物件快取中](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)。|
| 快取模擬器 | 受管理的快取提供本機快取模擬器。 | Azure Redis 快取沒有模擬器，但您可以[執行的本機意指-server.exe MSOpenTech 建置](cache-faq.md#cache-emulator)提供模擬器體驗。 |

## <a name="choose-a-cache-offering"></a>選擇 [快取服務

Microsoft Azure Redis 快取適用於下列層︰

-   **基本**– 單一節點。 多個大小 53 GB。
-   **標準**– 兩個節點主要/複本。 多個大小 53 GB。 99.9 %SLA。
-   **進階版**– 兩個節點主要/具有最多 10 個擊碎複本。 多個大小從 6 GB 530 GB （與我們連絡的詳細資訊）。 所有的標準層功能及更多包括支援[Redis 叢集](cache-how-to-premium-clustering.md)、 [Redis 持續性](cache-how-to-premium-persistence.md)，及[Azure 虛擬網路](cache-how-to-premium-vnet.md)。 99.9 %SLA。

每一層不同的功能和價格。 功能覆蓋稍後的本指南，以及定價詳細資訊，請參閱[快取定價詳細資料](https://azure.microsoft.com/pricing/details/cache/)。

開始移轉是選擇符合先前的受管理的快取服務快取大小的大小，然後根據您的應用程式的需求來調整 [向上或向下。 如需更多選擇正確的 Azure Redis 快取服務的詳細指示，請參閱[哪些意指快取服務和大小我應該使用？](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

## <a name="create-a-cache"></a>建立的快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>將快取用戶端設定

快取會建立並設定下, 一步是移除受管理的快取服務設定，並新增新增 Azure Redis 快取設定和參照，好讓快取用戶端可以存取快取。

-   移除受管理的快取服務設定
-   設定使用 StackExchange.Redis NuGet 套件的快取用戶端

### <a name="remove-the-managed-cache-service-configuration"></a>移除受管理的快取服務設定

之前的用戶端應用程式可設定為 Azure Redis 快取，現有的受管理的快取服務設定和組件參考必須解除安裝的受管理的快取服務 NuGet 套件，即可移除。

若要解除安裝的受管理的快取服務 NuGet 套件，以滑鼠右鍵按一下 [用戶端專案**總管**] 中的，選擇 [**管理 NuGet 套件**。 選取**已安裝套件**節點，並輸入 W**indowsAzure.Caching**搜尋已安裝的套件方塊。 選取**Windows** **Azure 快取**（或**Windows**版本的 NuGet 套件根據 [**快取 Azure** ），按一下 [**解除安裝**，，然後按一下 [**關閉**]。

![解除安裝 Azure 受管理的快取服務 NuGet 套件](./media/cache-migrate-to-redis/IC757666.jpg)

解除安裝的受管理的快取服務 NuGet 套件 app.config 或 web.config 的用戶端應用程式中移除受管理的快取服務組件及管理快取服務項目。 解除安裝 NuGet 套件時，可能不會移除某些自訂的設定，因為 web.config 或 app.config 開啟，並確保下列項目會完全移除。

確定`dataCacheClients`項目會從移除`configSections`項目。 不會移除整個`configSections`項目。只移除`dataCacheClients`項目，如果有的話。

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

確定`dataCacheClients`] 區段會移除。 `dataCacheClients` ] 區段會類似下列範例。

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

管理快取服務設定會移除後，您可以設定快取用戶端下, 一節所述。

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>設定使用 StackExchange.Redis NuGet 套件的快取用戶端

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>移轉管理快取服務代碼

StackExchange.Redis 快取用戶端的 API 很類似管理快取服務。 本節提供差異的概觀。

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>連線到使用 ConnectionMultiplexer 類別的快取

在受管理的快取服務中快取連線是由處理`DataCacheFactory`和`DataCache`類別。 Azure Redis 的快取中由管理這些連線`ConnectionMultiplexer`類別。

新增下列使用您想要存取快取的任何檔案的最上方的陳述式。

    using StackExchange.Redis
                                
如果仍無法解決此命名空間，請確定您已新增 StackExchange.Redis NuGet 封裝[設定快取用戶端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)所述。

>[AZURE.NOTE] 請注意 StackExchange.Redis 用戶端需要.NET Framework 4 或更新版本。

若要連線到 Azure Redis 快取執行個體，請連絡 [靜態`ConnectionMultiplexer.Connect`方法和階段的結束點及索引鍵。 若要共用的其中一個方法`ConnectionMultiplexer`應用程式中的執行個體是有連線的執行個體，類似下列範例會傳回的靜態屬性。 能夠安全執行緒初始化只連線的單一`ConnectionMultiplexer`執行個體。 在此範例中`abortConnect`設為 false，這表示會成功通話，即使不會建立快取的連線。 一個主要功能`ConnectionMultiplexer`它會自動還原連線至快取一次網路問題，或其他原因有解決。

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

您可以從您的快取執行個體**Redis 快取**刀取得快取端點、 金鑰和連接埠。 如需詳細資訊，請參閱[Redis 快取屬性](cache-configure.md#properties)。

建立連線後，會傳回意指快取資料庫的參照，則可電話`ConnectionMultiplexer.GetDatabase`方法。 從傳回的物件`GetDatabase`方法是輕量型傳遞物件並不需要儲存。

    IDatabase cache = Connection.GetDatabase();
    
    // Perform cache operations using the cache object...
    // Simple put of integral data types into the cache
    cache.StringSet("key1", "value");
    cache.StringSet("key2", 25);
    
    // Simple get of data types from the cache
    string key1 = cache.StringGet("key1");
    int key2 = (int)cache.StringGet("key2");

StackExchange.Redis 用戶端使用`RedisKey`和`RedisValue`存取及儲存快取中的項目類型。 這些類型對應至最基本的語言類型，包括字串，通常不直接使用。 Redis 和字串最基本的意指值，並且將最多可以包含許多類型的資料，包括序列的二進位資料流時，您可能無法使用類型直接，您會使用包含的方法`String`的名稱。 最基本的資料類型的儲存及擷取項目從快取使用`StringSet`和`StringGet`方法，除非您儲存集合或其他意指資料類型的快取中。 

`StringSet`與`StringGet`，兩者十分相似管理快取服務`Put`和`Get`方法，使用主要差異在於設定，並取得.NET 物件快取到之前您必須將其序列化第一次。 

呼叫時`StringGet`，如果物件存在，則會傳回，如果沒有出現，則傳回 null。 在此情況下，可以從您要的資料來源擷取的值並將其儲存在後續使用快取。 這就是所謂的快取離題模式。

若要指定快取中的到期日的項目，請使用`TimeSpan`的參數`StringSet`。

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Azure Redis 快取可以使用.NET 物件，以及基本資料類型，但可以快取.NET 物件之前必須序列化。 這是應用程式開發人員的責任。 此為開發人員彈性序列化工具的選項。 如需的資訊與範例程式碼，請參閱[使用.NET 物件快取中](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)。

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>ASP.NET 工作階段狀態和 Azure Redis 快取的輸出快取移轉

Azure Redis 快取有 ASP.NET 工作階段狀態和網頁輸出快取的提供者。 若要移轉應用程式，使用這些提供者管理快取服務版本，先從您的 web.config 中移除現有的節，然後設定 [提供者中 Azure Redis 快取的版本。 如需使用 Azure Redis 快取 ASP.NET 提供者的指示，請參閱[Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)和[Azure Redis 快取的 ASP.NET 輸出快取提供者](cache-aspnet-output-cache-provider.md)。

## <a name="next-steps"></a>後續步驟

教學課程，範例、 視訊和其他項目的探索[Azure Redis 快取文件](https://azure.microsoft.com/documentation/services/cache/)。

