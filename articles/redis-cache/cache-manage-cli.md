<properties 
    pageTitle="如何建立及管理 Azure Redis 快取使用 Azure 命令列介面 (Azure CLI) |Microsoft Azure" 
    description="瞭解如何安裝任何平台上的 Azure CLI、 如何用來連線至 Azure 帳戶，以及如何建立及管理意指快取從 Azure CLI。" 
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

# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>如何建立及管理 Azure Redis 快取使用 Azure 命令列介面 (Azure CLI)

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Azure CLI 可從任何平台管理 Azure 基礎結構的好方法。 本文將示範如何建立和管理您使用 Azure CLI Azure Redis 快取的執行個體。

## <a name="prerequisites"></a>必要條件

若要建立並管理使用 Azure CLI Azure Redis 快取執行個體，您必須完成下列步驟。

-   您必須擁有 Azure 帳戶。 如果您沒有帳戶，您可以建立一段時間的[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。
-   [安裝 Azure CLI](../xplat-cli-install.md)。
-   連接 Azure CLI 安裝個人的 Azure 帳戶，或公司或學校 Azure 帳戶，並從 Azure CLI 使用登入`azure login`] 命令。 若要瞭解差異，並選擇，請參閱[連線至 Azure 訂閱從 Azure 命令列介面 (Azure CLI)](../xplat-cli-connect.md)。
-   執行任何下列命令，之前，先切換 Azure CLI 成資源管理員模式執行`azure config mode arm`] 命令。 如需詳細資訊，請參閱[設定 Azure 資源管理員模式](../xplat-cli-azure-resource-manager.md#set-the-azure-resource-manager-mode)。

## <a name="redis-cache-properties"></a>Redis 快取屬性

建立及更新 Redis 快取執行個體，會使用下列屬性。

| 屬性            | 切換                                  | 描述                                                                                                                                                                                                                                          |
|---------------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱                | n、-名稱                              | 意指快取的名稱。                                                                                                                                                                                                                             |
| 資源群組      | -g、-資源群組                    | 資源群組的名稱。                                                                                                                                                                                                                          |
| 位置            | -l、-位置                          | 若要建立快取的位置。                                                                                                                                                                                                                            |
| 大小                | z、-大小                              | 意指快取大小。 有效的值: [C0、 C1、 C2、 C3、 C4、 C5、 C6、 P1、 P2、 第 3 頁，第 4 頁]                                                                                                                                                                  |
| sku                 | x，-sku                               | Redis SKU。 應該是下列其中一項: [基本、 標準、 付費]                                                                                                                                                                                             |
| EnableNonSslPort    | -e、-啟用非-ssl-連接埠               | Redis 快取 EnableNonSslPort 屬性。 如果您想要啟用非 SSL 連接埠的快取，則新增這個旗標                                                                                                                                    |
| Redis 設定 | -c、-意指設定               | Redis 設定。 輸入 JSON 格式字串設定索引鍵和以下的值。 格式: 「 {」 」: 「 」、 「 」: 「 「} 」                                                                                                                                     |
| Redis 設定 | -f、-意指設定檔          | Redis 設定。 輸入包含設定索引鍵和值以下的檔案的路徑。 檔案項目的格式: {」 」: 「 」、 「 」: 」]}                                                                                                                |
| 晶怪計數         | -r，-晶怪計數                       | 建立進階版叢集快取與叢集擊碎數目。                                                                                                                                                                               |
| 虛擬網路     | v、-虛擬網路                   | 當裝載您的快取 VNET 中，指定的部署意指快取中的虛擬網路完全 ARM 資源識別碼。 格式範例︰ /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 索引鍵的類型            | -t，-按鍵輸入                          | 輸入的金鑰來更新。 有效的值: [主要、 次要]                                                                                                                                                                                             |
| StaticIP            | -p、-靜態 ip < 靜態 ip >             | 主控您的快取中 VNET，指定子網路快取以唯一的 IP 位址。 如果未提供，將其中一個選擇適合您子網路。                                                                                                |
| 子網路              | t、-子網路<subnet>                    | 主控您的快取 VNET 中，指定要部署快取子網路的名稱。                                                                                                                                                    |
| VirtualNetwork      | v、-虛擬-< 虛擬網路 > | 當裝載您的快取 VNET 中，指定的部署意指快取中的虛擬網路完全 ARM 資源識別碼。 格式範例︰ /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 訂閱        | -s、-訂閱                      | 訂閱識別碼。                                                                                                                                                                                                                         |

## <a name="see-all-redis-cache-commands"></a>查看所有 Redis 快取命令

若要查看所有 Redis 快取命令及參數，請使用`azure rediscache -h`] 命令。

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>建立意指快取

若要建立 Redis 的快取，請使用下列命令︰

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

如需此命令的詳細資訊，請執行`azure rediscache create -h`] 命令。

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>刪除現有的 Redis 快取

若要刪除 Redis 的快取，請使用下列命令︰

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

如需此命令的詳細資訊，請執行`azure rediscache delete -h`] 命令。

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>列出所有的 Redis 快取，您的訂閱或資源群組

若要所有的 Redis 快取，您的訂閱或資源群組] 清單中，使用下列命令︰

    azure rediscache list [options]

如需此命令的詳細資訊，請執行`azure rediscache list -h`] 命令。

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>顯示現有 Redis 快取的屬性

若要顯示的現有 Redis 快取屬性，請使用下列命令︰

    azure rediscache show [--name <name> --resource-group <resource-group>]

如需此命令的詳細資訊，請執行`azure rediscache show -h`] 命令。

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>
## <a name="change-settings-of-an-existing-redis-cache"></a>變更現有的 Redis 快取設定

若要變更的現有 Redis 快取設定，請使用下列命令︰

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

如需此命令的詳細資訊，請執行`azure rediscache set -h`] 命令。

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>更新現有的 Redis 快取驗證索引鍵

若要更新現有的 Redis 快取驗證金鑰，使用下列命令︰

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

指定`Primary`或`Secondary`的`key-type`。

如需此命令的詳細資訊，請執行`azure rediscache renew-key -h`] 命令。

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>清單主要及次要索引鍵的現有 Redis 快取

若要清單主要和次要鍵的現有 Redis 快取，使用下列命令︰

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

如需此命令的詳細資訊，請執行`azure rediscache list-keys -h`] 命令。

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
