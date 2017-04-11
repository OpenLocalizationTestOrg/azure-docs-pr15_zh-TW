<properties
    pageTitle="快取中的媒體服務延伸原則的 CDN"
    description="本主題提供 cdn 到底的概觀快取中的媒體服務延伸原則。"
    services="media-services,cdn"
    documentationCenter=".NET"
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="juliako"/>
 
#<a name="cdn-caching-policy-in-media-services-extension"></a>快取中的媒體服務延伸原則的 CDN

Azure 媒體服務會提供 HTTP 根據彈性資料流與漸進下載。 HTTP 基礎資料流是彈性與 proxy 和 CDN 圖層，以及用戶端快取中的快取的優點。 串流端點提供一般串流及功能也 HTTP 快取標頭的設定。 串流端點設定 HTTP 快取控制︰ max 年齡和到期日標頭。 您可以從[W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)取得 HTTP 快取標頭的詳細資訊。

##<a name="default-caching-headers"></a>預設快取標題

預設串流端點套用視串流資料 （實際的媒體片段/區塊） 及 manifest(playlist) 3 天快取標題。 即時資料流串流結束點套用 3 天快取標題的資料 （實際的媒體片段/區塊），2 的秒數快取 manifest(playlist) 要求的頁首。 當即時程式即會變成點播 （即時封存） 視串流快取標題套用。

##<a name="azure-cdn-integration"></a>Azure CDN 整合

Azure 媒體服務串流端點提供[整合式 cdn 到底](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/)。 為 CDN 串流端點啟用串流結束點，以相同的方式適用於快取控制標題。 Azure 串流端點的 CDN 用途設定快取的值，以定義內部快取物件的存留時間，也會使用這個值來設定傳送快取標題。 使用 CDN 啟用串流結束點時，不建議來設定小型快取值。 設定較小的值將會減少效能，並減少 CDN 的優點。 不允許將快取標題小於 600 秒的 CDN 啟用串流結束點。

>[AZURE.IMPORTANT] **從 Verizon Azure CDN**實作 Azure CDN azure 媒體服務整合。  如果您想要**從 Akamai Azure CDN**用於 Azure 媒體服務，您必須[以手動方式設定端點](cdn-create-new-endpoint.md)。  如需有關 Azure CDN 功能的詳細資訊，請參閱[CDN 概觀](cdn-overview.md)。

##<a name="configuring-cache-headers-with-azure-media-services"></a>透過 Azure 媒體服務設定快取標題

您可以使用 Azure 管理入口網站或 Azure 媒體服務 Api 設定快取標頭值。

1. 若要設定快取標題使用 [管理入口網站請參閱[如何管理串流端點](../media-services/media-services-portal-manage-streaming-endpoints.md)區段串流端點的設定。
2. Azure 媒體服務 REST API， [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl)。
3. Azure 媒體服務.NET sdk，您可以[StreamingEndpointCacheControl 屬性](http://go.microsoft.com/fwlink/?LinkId=615302)。

##<a name="cache-configuration-precedence-order"></a>快取設定優先順序

1. Azure 媒體服務設定快取值會覆寫預設值。
2. 如果不有任何手動設定，適用於預設值。
3. 依預設 2 的秒數快取標題適用於 live 串流 manifest(playlist) 無論 Azure 媒體或 Azure 儲存體設定和覆寫此值，則無法使用。
