<properties
    pageTitle="如何使用 Python Azure Redis 快取 |Microsoft Azure"
    description="Azure Redis 快取使用 Python 快速入門"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# <a name="how-to-use-azure-redis-cache-with-python"></a>如何使用 Python Azure Redis 快取

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

本主題說明如何開始使用 Azure Redis 快取使用 Python。


## <a name="prerequisites"></a>必要條件

安裝[意指 py](https://github.com/andymccurdy/redis-py)。


## <a name="create-a-redis-cache-on-azure"></a>Azure 上建立意指快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>擷取主機名稱] 與 [存取鍵

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-the-non-ssl-endpoint"></a>啟用非 SSL 端點

某些意指用戶端不支援 SSL，依預設和[非 SSL 連接埠停用新的 Azure Redis 快取執行個體](cache-configure.md#access-ports)。 在撰寫時，[意指 py](https://github.com/andymccurdy/redis-py)用戶端不支援 SSL。 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## <a name="add-something-to-the-cache-and-retrieve-it"></a>將項目新增至快取，並擷取它


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


取代`<name>`與您快取的名稱和`key`與您便捷鍵。


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
