<properties
    pageTitle="如何使用 Node.js Azure Redis 快取 |Microsoft Azure"
    description="開始使用 Azure Redis 快取使用 Node.js 和 node_redis。"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>如何使用 Node.js Azure Redis 快取

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取安全的專用意指快取，由 Microsoft 管理。 從 Microsoft Azure 中的任何應用程式存取您的快取。

本主題說明如何開始使用 Azure Redis 快取使用 Node.js。 另一個範例使用 Node.js Azure Redis 快取的詳細資訊，請參閱[建立具有 Azure 網站上的 Socket.IO Node.js 交談應用程式](../app-service-web/web-sites-nodejs-chat-app-socketio.md)。


## <a name="prerequisites"></a>必要條件

安裝[node_redis](https://github.com/mranney/node_redis):

    npm install redis

本教學課程中使用[node_redis](https://github.com/mranney/node_redis)。 如需使用其他 Node.js 用戶端的範例，請參閱列出[Node.js Redis](http://redis.io/clients#nodejs)用戶端 Node.js 用戶端的個別文件。

## <a name="create-a-redis-cache-on-azure"></a>Azure 上建立意指快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>擷取主機名稱] 與 [存取鍵

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>連線至安全地使用 SSL 快取

最新[node_redis](https://github.com/mranney/node_redis)提供支援，連線到 Azure Redis 快取使用 SSL。 下列範例會示範如何連線至 Azure Redis 快取使用 6380 SSL 端點。 取代`<name>`快取的名稱和`<key>`使用您的主要或次要金鑰為前一個[擷取主機名稱] 與 [存取鍵](#retrieve-the-host-name-and-access-keys)一節所述。

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## <a name="add-something-to-the-cache-and-retrieve-it"></a>將項目新增至快取，並擷取它

下列範例會示範如何連線至 Azure Redis 快取執行個體，以及儲存和擷取快取中的項目。 更多範例意指使用[node_redis](https://github.com/mranney/node_redis)用戶端的詳細資訊，請參閱[http://redis.js.org/](http://redis.js.org/)。

     var redis = require("redis");
    
      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
    
    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });
    
    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

輸出︰

    OK
    value


## <a name="next-steps"></a>後續步驟

- [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)您可以[監控](cache-how-to-monitor.md)您的快取的狀況。
- 閱讀[Redis 文件](http://redis.io/documentation)的正式。



