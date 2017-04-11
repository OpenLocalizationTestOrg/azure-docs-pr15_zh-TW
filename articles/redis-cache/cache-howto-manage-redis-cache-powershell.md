<properties
    pageTitle="管理與 Azure PowerShell 的 Azure 意指快取 |Microsoft Azure"
    description="了解如何執行 Azure Redis 快取使用 PowerShell 的 Azure 的系統管理工作。"
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

# <a name="manage-azure-redis-cache-with-azure-powershell"></a>管理與 Azure PowerShell 的 Azure 意指快取

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

本主題說明您執行一般工作例如建立，更新，並不按比例縮放您 Azure Redis 快取的執行個體，如何重新產生便捷鍵，以及如何檢視您的快取的相關資訊。 Azure Redis 快取 PowerShell 指令程式的完整清單，請參閱[Azure Redis 快取 cmdlet](https://msdn.microsoft.com/library/azure/mt634513.aspx)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)][傳統的模型](../resource-manager-deployment-model.md#classic-deployment-characteristics)。

## <a name="prerequisites"></a>必要條件

如果您已安裝 PowerShell 的 Azure，您必須 PowerShell 的 Azure 1.0.0 版本或更新版本。 您可以檢查 PowerShell 的 Azure 使用這個 PowerShell 的 Azure 命令提示字元] 命令，您所安裝的版本。

    Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

首先，您必須登入 Azure 使用此命令。

    Login-AzureRmAccount

在 Microsoft Azure 登入] 對話方塊中，指定您 Azure 帳戶和其密碼的電子郵件地址。

接下來，如果您有多個 Azure 訂閱時，您需要設定 Azure 訂閱。 若要查看您目前訂閱的清單，請執行此命令。

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

若要指定訂閱，請執行下列命令。 在下列範例中，是訂閱名稱`ContosoSubscription`。

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

您可以使用 Windows PowerShell 的 Azure 資源管理員之前，您需要下列項目︰

- Windows PowerShell，版本 3.0 或 4.0。 若要尋找的 Windows PowerShell 的版本，請輸入︰`$PSVersionTable`及驗證的值`PSVersion`3.0 或 4.0。 若要安裝相容的版本，請參閱[Windows 管理 Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)或[Windows 管理架構 4.0](http://www.microsoft.com/download/details.aspx?id=40855)。

若要取得您在本教學課程中看到任何 cmdlet 的詳細的說明，請使用取得說明指令程式。

    Get-Help <cmdlet-name> -Detailed

例如，若要取得說明`New-AzureRmRedisCache`cmdlet，類型︰

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-azure-government-cloud-or-azure-china-cloud"></a>如何連線至 Azure 政府雲端或 Azure china （中國） 雲端

根據預設 Azure 環境是`AzureCloud`，用來表示全域 Azure 雲端執行個體。 若要連線到不同的執行個體，請使用`Add-AzureRmAccount`命令與`-Environment`或-`EnvironmentName`所需的環境或環境名稱的命令列參數。

若要查看可用的環境的清單，請執行`Get-AzureRmEnvironment`指令程式。

### <a name="to-connect-to-the-azure-government-cloud"></a>連線至 Azure 政府雲端

若要連線至 Azure 政府雲端，使用下列命令。

    Add-AzureRMAccount -EnvironmentName AzureUSGovernment

或

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

若要建立 Azure 政府雲端快取中使用下列的位置。

-   USGov 維吉尼亞州
-   USGov 稍後

如需有關 Azure 政府雲端的詳細資訊，請參閱[Microsoft Azure 政府版](https://azure.microsoft.com/features/gov/)和[Microsoft Azure 政府開發人員指南](../azure-government-developer-guide.md)。

### <a name="to-connect-to-the-azure-china-cloud"></a>連線至雲端 Azure china （中國）

若要連線至 Azure china （中國） 雲端，使用下列命令。

    Add-AzureRMAccount -EnvironmentName AzureChinaCloud

或

    Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

若要建立 Azure china （中國） 雲端中的快取，請使用下列位置。

-   中國東亞
-   北美 china （中國）

如需有關 Azure china （中國） 雲端的詳細資訊，請參閱[Azure 中國的 21Vianet 所營運的 AzureChinaCloud](http://www.windowsazure.cn/)。

### <a name="properties-used-for-azure-redis-cache-powershell"></a>用於 Redis 快取 PowerShell 的 Azure 的屬性

下表包含屬性和常用參數時建立及管理您使用 Azure PowerShell 的 Azure Redis 快取執行個體的描述。

| 參數          | 描述                                                                                                                                                                                                        | 預設值  |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| 名稱               | 快取的名稱                                                                                                                                                                                                  |          |
| 位置           | 快取的位置                                                                                                                                                                                              |          |
| ResourceGroupName  | 資源群組名稱來建立快取                                                                                                                                                                   |          |
| 大小               | 快取大小。 有效的值︰ P1、 P2、 第 3 頁、 第 4 頁、 C0、 C1、 C2、 C3、 C4、 C5、 C6、 250 MB、 1 GB，2.5 GB，6 GB、 13 GB、 26 GB、 53 GB                                                                     | 1 GB      |
| ShardCount         | 若要建立時建立進階版快取啟用叢集擊碎數目。 有效的值︰ 1、 2、 3、 4、 5、 6、 7、 8、 9、 10                                                                                                      |          |
| SKU                | 指定 SKU 的快取。 有效的值︰ 基本、 標準、 付費                                                                                                                                         | 標準 |
| RedisConfiguration | 指定意指設定的設定。 如需每項設定的詳細資訊，請參閱下[RedisConfiguration 屬性](#redisconfiguration-properties)表。 |          |
| EnableNonSslPort   | 指出是否已啟用的非 SSL 連接埠。                                                                                                                                                                     | False    |
| MaxMemoryPolicy    | 這個參數已被取代-改為使用 RedisConfiguration。                                                                                                                                              |          |
| StaticIP           | 主控您的快取中 VNET，指定子網路快取以唯一的 IP 位址。 如果未提供，將其中一個選擇為您子網路。                                                                                                                     |          |
| 子網路             | 主控您的快取 VNET 中，指定要部署快取子網路的名稱。                                                                                                                  |          |
| VirtualNetwork     | 主控您的快取 VNET 中，指定要部署快取 VNET 的資源識別碼。                                                                                                             |          |
| KeyType            | 指定可重新產生更新便捷鍵時的便捷鍵。 有效的值︰ 主要、 次要 |  |                                                                                                                                                                                                              |          |


### <a name="redisconfiguration-properties"></a>RedisConfiguration 屬性

| 屬性                      | 描述                                                                                                          | 價格層       |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------|---------------------|
| rdb 備份啟用            | 是否啟用[Redis 資料持續性](cache-how-to-premium-persistence.md)                                     | 僅限進階版        |
| rdb 儲存空間-連接字串 | [Redis 資料持續](cache-how-to-premium-persistence.md)儲存帳戶之連線字串       | 僅限進階版        |
| rdb 備份頻率          | [Redis 資料保存](cache-how-to-premium-persistence.md)備份頻率                               | 僅限進階版        |
| maxmemory 保留            | 設定非快取程序的[記憶體保留](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) | 標準和付費 |
| maxmemory 原則              | 設定 [快取[收回原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)           | 所有價格層   |
| 通知再事件        | 設定[再通知](cache-configure.md#keyspace-notifications-advanced-settings)                     | 標準和付費 |
| 雜湊-最大值-ziplist-項目      | 設定小型彙總的資料類型的[記憶體最佳化](http://redis.io/topics/memory-optimization)          | 標準和付費 |
| 雜湊最大值-ziplist 值        | 設定小型彙總的資料類型的[記憶體最佳化](http://redis.io/topics/memory-optimization)          | 標準和付費 |
| 設定-最大值-intset-項目        | 設定小型彙總的資料類型的[記憶體最佳化](http://redis.io/topics/memory-optimization)          | 標準和付費 |
| zset-最大值-ziplist-項目      | 設定小型彙總的資料類型的[記憶體最佳化](http://redis.io/topics/memory-optimization)          | 標準和付費 |
| zset 最大值-ziplist 值        | 設定小型彙總的資料類型的[記憶體最佳化](http://redis.io/topics/memory-optimization)          | 標準和付費 |
| 資料庫                     | 設定資料庫的數目。 您可以只在建立快取設定此屬性。                          | 標準和付費 |

## <a name="to-create-a-redis-cache"></a>若要建立 Redis 快取

使用[新增 AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx)指令程式來建立新的 Azure Redis 快取執行個體。

>[AZURE.IMPORTANT] 您可以建立意指快取訂閱，使用 Azure] 入口網站中的第一次入口網站註冊`Microsoft.Cache`命名空間的訂閱。 如果您嘗試建立的第一個意指快取中使用 PowerShell 訂閱，您必須先登錄使用下列命令; 命名空間例如，否則 cmdlet`New-AzureRmRedisCache`和`Get-AzureRmRedisCache`失敗。
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

若要查看可用的參數和描述的清單及其的`New-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help New-AzureRmRedisCache -detailed
    
    NAME
        New-AzureRmRedisCache
    
    SYNOPSIS
        Creates a new redis cache.
    
    
    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]
    
    
    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.
    
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to create.
    
        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.
    
        -Location <String>
            Location in which to create the redis cache.
    
        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}
    
        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

若要使用預設參數建立快取，請執行下列命令。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName``Name`，及`Location`必要的參數，但其他是選擇性的有預設值。 執行上一個命令，則會以指定的名稱、 位置和大小，以停用非 SSL 連接埠 1 GB 的資源群組建立標準 SKU Azure Redis 快取執行個體。

若要建立進階版的快取，請指定大小為 P1 (6 GB 60 GB)，P2 (13 GB 130 GB)，第 3 頁 (26 GB 260 GB) 或第 4 頁 (53 GB-530 GB)。 若要啟用叢集，指定使用晶怪計數`ShardCount`參數。 下列範例會建立一個 P1 進階版快取，使用 3 擊碎。 P1 進階版快取 6 GB 的大小，而因為我們指定三個擊碎的總大小會 18 GB (3 x 6 GB)。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

若要指定的值`RedisConfiguration`參數，括住內值`{}`為索引鍵/值組等`@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`。 下列範例會建立使用標準 1 GB 快取`allkeys-random`maxmemory 原則] 和 [再通知設定與`KEA`。 如需詳細資訊，請參閱[再通知 （進階設定）](cache-configure.md#keyspace-notifications-advanced-settings)和[Maxmemory 原則和 maxmemory 保留](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>
## <a name="to-configure-the-databases-setting-during-cache-creation"></a>若要設定資料庫] 期間建立快取設定

`databases`設定可以只在建立快取設定。 下列範例會建立進階版第 3 頁 (26 GB) 快取 48 資料庫時，使用 [[新增 AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx)指令程式。

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

如需有關`databases`屬性，請參閱[預設 Azure Redis 快取伺服器設定](cache-configure.md#default-redis-server-configuration)。 如需有關如何建立使用[新增 AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx)指令程式快取的詳細資訊，請參閱 [先前[建立 Redis 的快取](#to-create-a-redis-cache)] 區段。

## <a name="to-update-a-redis-cache"></a>若要更新意指快取

使用[設定 AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx)指令程式就會更新 azure Redis 快取執行個體。

若要查看可用的參數和描述的清單及其的`Set-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed
    
    NAME
        Set-AzureRmRedisCache
    
    SYNOPSIS
        Set redis cache updatable parameters.
    
    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]
    
    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to update.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
    
        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
    
        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
    
        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.
    
        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.
    
        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzureRmRedisCache`指令程式可用來更新屬性，例如`Size`， `Sku`， `EnableNonSslPort`，以及`RedisConfiguration`值。 

下列命令會更新 maxmemory 原則，名為 myCache Redis 快取。

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## <a name="to-scale-a-redis-cache"></a>若要縮放意指快取

`Set-AzureRmRedisCache`可用於不按比例縮放 Azure Redis 快取執行個體何時`Size`， `Sku`，或`ShardCount`修改內容。 

>[AZURE.NOTE]調整快取中使用 PowerShell 受到相同的限制和指導方針，以調整快取中的從 Azure 入口網站。 您可以調整到不同的價格層，使用下列的限制。
>
>-  您無法從較高的價格層調整至較低的價格層。
>    -    您無法從下**標準****進階版**快取或**基本**的快取調整。
>    -    您無法從**標準**的快取**基本**的快取下調整。
>-  您可以調整從**基本**的快取**標準**的快取，但您無法同時變更的大小。 如果您需要不同的大小，您可以在後續的縮放比例運算需要的大小。
>-  您無法容納從**基本**的快取直接**進階版**快取。 您必須縮放從**基本**至**標準**一個的縮放比例動作，然後從**標準**在後續的縮放比例運算中的**進階版**。
>-  您無法從較大的大小，向下調整**C0 (250 MB)**大小。
>
>如需詳細資訊，請參閱[如何比例 Azure Redis 快取](cache-how-to-scale.md)。

下列範例顯示如何調整快取中名為`myCache`2.5 GB 快取。 請注意，可以使用這個命令兩個基本或標準的快取。

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

這個命令會發出之後，會傳回快取的狀態 (類似電話`Get-AzureRmRedisCache`)。 請注意，`ProvisioningState`是`Scaling`。

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
    
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

完成之後，[縮放比例] 作業時`ProvisioningState`變更為`Succeeded`。 如果您需要進行後續的縮放比例作業，例如從基本變更為標準，然後變更 [大小]，您必須等到完成前一個作業或您會收到如下錯誤。

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>若要取得意指快取資訊

您可以取得有關使用[取得 AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx)指令程式快取資訊。

若要查看可用的參數和描述的清單及其的`Get-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed
    
    NAME
        Get-AzureRmRedisCache
    
    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.
    
    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.
    
        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
    
        If no parameters are given than it will return details about all caches the current subscription.
    
    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

若要傳回目前的訂閱中的所有快取的相關資訊，請執行`Get-AzureRmRedisCache`不具任何參數。

    Get-AzureRmRedisCache

若要傳回特定的資源群組中的所有快取的相關資訊，請執行`Get-AzureRmRedisCache`與`ResourceGroupName`參數。

    Get-AzureRmRedisCache -ResourceGroupName myGroup

若要傳回特定的快取的相關資訊，請執行`Get-AzureRmRedisCache`與`Name`參數包含快取中的名稱和`ResourceGroupName`參數與包含該快取的資源群組。

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>若要擷取的意指快取便捷鍵

若要擷取的快取便捷鍵，您可以使用[取得 AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx)指令程式。

若要查看可用的參數和描述的清單及其的`Get-AzureRmRedisCacheKey`，執行下列命令。

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
    
    NAME
        Get-AzureRmRedisCacheKey
    
    SYNOPSIS
        Gets the accesskeys for the specified redis cache.
    
    
    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
    
    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

若要擷取您的快取的機碼，請連絡`Get-AzureRmRedisCacheKey`指令程式，並在您的快取傳遞包含快取的資源群組的名稱。

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>若要重新產生意指快取的便捷鍵

若要重新產生的快取便捷鍵，您可以使用 [[新增 AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx)指令程式。

若要查看可用的參數和描述的清單及其的`New-AzureRmRedisCacheKey`，執行下列命令。

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
    
    NAME
        New-AzureRmRedisCacheKey
    
    SYNOPSIS
        Regenerates the access key of a redis cache.
    
    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
    
    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache.
    
        -ResourceGroupName <String>
            Name of the resource group for the cache.
    
        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
    
        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    
若要重新產生快取的主要或第二個機碼，連絡`New-AzureRmRedisCacheKey`cmdlet 和傳入名稱]，[資源] 群組中，指定`Primary`或`Secondary`的`KeyType`參數。 在下列範例中，重新產生快取中的次要便捷鍵。

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
    
    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    
    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>若要刪除意指快取

若要刪除意指快取，請使用[移除 AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx)指令程式。

若要查看可用的參數和描述的清單及其的`Remove-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
    
    NAME
        Remove-AzureRmRedisCache
    
    SYNOPSIS
        Remove redis cache if exists.
    
    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
    
    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
    
    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.
    
        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.
    
        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.
    
        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

在下列範例中，命名快取`myCache`會移除。

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
    
    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>若要匯入意指快取

您可以將資料匯入 Azure Redis 快取執行個體使用`Import-AzureRmRedisCache`指令程式。

>[AZURE.IMPORTANT] 匯入/匯出只適用於[進階版層](cache-premium-tier-intro.md)快取。 如需有關匯入/匯出的詳細資訊，請參閱[匯入及匯出 Azure Redis 快取中的資料](cache-how-to-import-export-data.md)。

若要查看可用的參數和描述的清單及其的`Import-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed
    
    NAME
        Import-AzureRmRedisCache
    
    SYNOPSIS
        Import data from blobs to Azure Redis Cache.
    
    
    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.
    
        -PassThru
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

從 Azure Redis 快取到 SA uri 所指定的 blob 下列命令匯入的資料。


    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>若要匯出意指快取

您可以將資料匯出 Azure Redis 快取執行個體使用`Export-AzureRmRedisCache`指令程式。

>[AZURE.IMPORTANT] 匯入/匯出只適用於[進階版層](cache-premium-tier-intro.md)快取。 如需有關匯入/匯出的詳細資訊，請參閱[匯入及匯出 Azure Redis 快取中的資料](cache-how-to-import-export-data.md)。

若要查看可用的參數和描述的清單及其的`Export-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed
    
    NAME
        Export-AzureRmRedisCache
    
    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.
    
    
    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -Prefix <String>
            Prefix to use for blob names.
    
        -Container <String>
            SAS url of container where data should be exported.
    
        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.
    
        -PassThru
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


下列命令將資料匯出至容器 SA uri 指定 Azure Redis 快取執行個體。


        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>若要重新啟動意指快取

您可以重新啟動您 Azure Redis 快取執行個體使用`Reset-AzureRmRedisCache`指令程式。

>[AZURE.IMPORTANT] 重新啟動只適用於[進階版層](cache-premium-tier-intro.md)快取。 如需有關重新啟動您的快取的詳細資訊，請參閱[快取管理-重新啟動](cache-administration.md#reboot)。

若要查看可用的參數和描述的清單及其的`Reset-AzureRmRedisCache`，執行下列命令。

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed
    
    NAME
        Reset-AzureRmRedisCache
    
    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.
    
    
    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]
    
    
    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.
    
    
    PARAMETERS
        -Name <String>
            The name of the cache.
    
        -ResourceGroupName <String>
            The name of the resource group that contains the cache.
    
        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".
    
        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.
    
        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.
    
        -PassThru
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.
    
        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
    

下列命令重新啟動指定的快取的兩個節點。

    
        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force
    

## <a name="next-steps"></a>後續步驟

若要進一步瞭解如何使用 Windows PowerShell 的 Azure，請參閱下列資源︰

- [MSDN 上的 azure Redis 快取 cmdlet 文件](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Azure 資源管理員 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765)︰ 瞭解如何 AzureResourceManager 模組中使用 cmdlet。
- [使用資源群組來管理您的 Azure 資源](../resource-group-template-deploy-portal.md)︰ 了解如何建立及管理 Azure 入口網站中的 [資源群組。
- [Azure 部落格](http://blogs.msdn.com/windowsazure)︰ 深入瞭解 Azure 中的新功能。
- [Windows PowerShell 部落格](http://blogs.msdn.com/powershell)︰ 深入瞭解在 Windows PowerShell 中的新功能。
- [」 的指令碼 Guy ！ 」部落格](http://blogs.technet.com/b/heyscriptingguy/)︰ 從 Windows PowerShell 社群取得實際的秘訣及竅門。
