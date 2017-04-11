<properties
   pageTitle="如何使用 Azure Redis 快取 java |Microsoft Azure"
    description="Azure Redis 快取使用 Java 快速入門"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="cache"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/24/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-java"></a>如何使用 Java Azure Redis 快取

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取專用 Redis 快取，由 Microsoft 管理。 從 Microsoft Azure 中的任何應用程式存取您的快取。

本主題說明如何開始使用 Azure Redis 快取使用 Java。

## <a name="prerequisites"></a>必要條件

[Jedis](https://github.com/xetorthio/jedis)意指 Java 用戶端

本教學課程中使用 Jedis，但您可以使用列在[http://redis.io/clients](http://redis.io/clients)任何 Java 用戶端。

## <a name="create-a-redis-cache-on-azure"></a>Azure 上建立意指快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>擷取主機名稱] 與 [存取鍵

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>啟用非 SSL 端點

某些意指用戶端不支援 SSL，依預設和[非 SSL 連接埠停用新的 Azure Redis 快取執行個體](cache-configure.md#access-ports)。 在撰寫時， [Jedis](https://github.com/xetorthio/jedis)用戶端不支援 SSL。 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## <a name="add-something-to-the-cache-and-retrieve-it"></a>將項目新增至快取，並擷取它

    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
    public class App
    {
      public static void main( String[] args )
      {
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>後續步驟

- [啟用快取診斷](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)您可以[監控](https://msdn.microsoft.com/library/azure/dn763945.aspx)您的快取的狀況。
- 閱讀[Redis 文件](http://redis.io/documentation)的正式。

