<properties
 pageTitle="如何管理 Azure Web 應用程式/雲端服務、 ASP.NET 及 IIS 中 Azure CDN 內容到期 |Microsoft Azure"
 description="說明如何管理雲端服務中的內容 Azure CDN 的到期日"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>如何管理 Azure CDN Azure Web 應用程式/雲端服務、 ASP.NET 或 IIS 內容的到期日

> [AZURE.SELECTOR]
- [Azure Web 應用程式/雲端服務、 ASP.NET 或 IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure 儲存 blob 服務](cdn-manage-expiration-of-blob-content.md)

任何公開存取的原始網頁伺服器的檔案可以快取中 Azure CDN，直到其存留時間 (TTL) 經過。  TTL 取決於來源伺服器的 HTTP 回應[*快取控制*標頭](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)。  本文將說明如何設定`Cache-Control`Azure Web 應用程式、 Azure Cloud Services，ASP.NET 應用程式，及 Internet Information Services 網站，這些都同樣設定的標題。

>[AZURE.TIP] 您可以選擇設定沒有 TTL 檔案。  在此情況下，Azure CDN 會自動套用 [TTL] 的七天的預設值。
>
>如需有關 Azure CDN 加快存取檔案及其他資源的運作方式的詳細資訊，請參閱[Azure CDN 概觀](./cdn-overview.md)。

## <a name="setting-cache-control-headers-in-configuration"></a>在 [設定中設定快取控制標頭

靜態內容，例如圖像和樣式表，您可以控制更新頻率修改**applicationHost.config**或**web.config** web 應用程式的檔案。  設定檔中的**system.webServer\staticContent\clientCache**項目會設定`Cache-Control`標頭，為您的內容。 對於**web.config**，設定的設定將會影響所有項目中的資料夾及所有子資料夾，除非的子資料夾層級，覆寫。  例如，您可以設定的預設時間 live 根目錄中有 3 天，快取的所有靜態內容，但有 6 小時的快取設定更多變數內容的子資料夾。  **ApplicationHost.config**，在網站上的所有應用程式會受到影響，但可以覆寫**web.config**應用程式中的檔案。

下列 XML 顯示和設定**clientCache**指定的期限 3 天的範例︰  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

指定**UseMaxAge**新增`Cache-Control: max-age=<nnn>`回應標頭根據**CacheControlMaxAge**屬性中指定的值。 時段的格式是**cacheControlMaxAge**屬性是<days>。<hours>:<min>:<sec>. 如需有關**clientCache**節點的詳細資訊，請參閱[用戶端快取<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)。  

## <a name="setting-cache-control-headers-in-code"></a>設定 [程式碼中的 [快取控制標頭

ASP.NET 應用程式，您可以設定**HttpResponse.Cache**屬性設定以程式設計方式快取行為 cdn 到底。 如需有關**HttpResponse.Cache**屬性的詳細資訊，請參閱[HttpResponse.Cache 屬性](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)和[HttpCachePolicy 類別](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  

如果您想要以程式設計方式快取中 ASP.NET 應用程式內容，請確定內容被標示為快取設定 HttpCacheability*公用*。 此外，確定快取驗證設定。 快取驗證程式可上次修改日期時間戳記設定，則可電話 SetLastModified 或 etag 值設定，則可電話 SetETag。 或者，您也可以指定快取的到期時間，則可電話 SetExpires，或您可以使用這份文件稍早所述的預設快取 heuristics。  

例如，若要快取內容一小時，新增下列︰  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>後續步驟

- [閱讀關於**clientCache**元素的詳細資訊](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [閱讀**HttpResponse.Cache**屬性的文件](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [閱讀**HttpCachePolicy 類別**的文件](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)。  
