<properties
    pageTitle="Azure CDN 概觀 |Microsoft Azure"
    description="瞭解什麼 Azure 內容傳遞網路 」 (CDN)，以及如何使用它來進行高頻寬內容快取 blob 和靜態內容。"
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/30/2016"
    ms.author="casoper"/>

# <a name="overview-of-the-azure-content-delivery-network-cdn"></a>Azure 內容傳遞網路 」 (CDN) 的概觀

> [AZURE.NOTE] 本文將說明 Azure 內容傳遞網路 (CDN) 是什麼，它的運作方式，與每個 Azure CDN 產品的功能。  如果您想要略過這項資訊並直接移至教學課程，說明如何建立 CDN 結束點，請參閱[使用 Azure cdn 到底](cdn-create-new-endpoint.md)。  如果您想要查看目前的 CDN 節點位置的清單，請參閱[Azure CDN POP 位置](cdn-pop-locations.md)。

Azure 內容傳遞網路 」 (CDN) 快取策略性放置的位置，以提供最大處理量將內容傳送給使用者的靜態網頁內容。  CDN 提供開發人員全域快取世界各地的實體節點的內容傳遞高頻寬內容的解決方案。 

使用 [快取網站資產 CDN 的優點包括︰

- 更佳的效能和使用者體驗的使用者，尤其是使用多個來回所需載入內容的應用程式。
- 縮放比例，使其更大處理瞬間完成的效能高負載想產品啟動事件的開頭。
- 發送使用者要求，並提供服務的邊緣伺服器內容，較少的流量會傳送至來源。


## <a name="how-it-works"></a>運作方式

![Cdn 到底概觀](./media/cdn-overview/cdn-overview.png)

1. 使用者 (Alice) 要求 （也稱為 「 資產 」） 檔案 URL 使用特殊的網域名稱，例如`<endpointname>.azureedge.net`。  DNS 路由至最佳的執行點的狀態 (POP) 位置的要求。  通常這是使用者地理區域最接近的快。

2. 如果 POP 的邊緣伺服器沒有其快取中的檔案，edge server 會從原點要求檔案。  原點可以是 Azure Web 應用程式、 Azure 雲端服務，Azure 儲存體帳戶或任何公開存取網頁伺服器。

3. 來源傳回 edge server，包括選擇性 HTTP 標題，說明檔案的時間存留 (TTL) 檔案。

4. Edge server 快取檔案，並回到原始的要求者 (Alice) 中的檔案。  TTL 到期之前，都會維持 edge server 上快取檔案。  如果來源未指定 TTL，請 TTL 是七天的預設值。

5. 額外的使用者，然後可能會要求使用該相同的 URL，相同的檔案，並可能也會導向至該相同的快顯。

6. 如果檔案 TTL 尚未過期，請 edge server 會傳回的檔案從快取。  這會更快、 更有回應使用者體驗。


## <a name="azure-cdn-features"></a>Azure CDN 功能

有三種 Azure CDN 產品︰ **Azure CDN 標準從 Akamai**、 **Azure CDN 標準從 Verizon**以及**從 Verizon Azure CDN 進階版**。  下表列出每個產品的可用功能。

|       | 標準 Akamai | 標準 Verizon | 進階版 Verizon |
|-------|-----------------|------------------|-----------------|
| 輕鬆地整合 Azure 服務，例如[儲存空間](cdn-create-a-storage-account-with-cdn.md)、[雲端服務](cdn-cloud-service-with-cdn.md)， [Web 應用程式](../app-service-web/cdn-websites-with-cdn.md)，與[媒體服務](../media-services/media-services-portal-manage-streaming-endpoints.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;**|
| 透過[REST API](https://msdn.microsoft.com/library/mt634456.aspx)、 [.NET](./cdn-app-dev-net.md)、 [Node.js](./cdn-app-dev-node.md)或[PowerShell](./cdn-manage-powershell.md)管理。 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| HTTPS 支援 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| 負載平衡 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)保護 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| IPv4/IPv6 雙堆疊 | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [自訂網域名稱的支援](cdn-map-content-to-custom-domain.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [查詢字串快取](cdn-query-string.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [地理篩選](cdn-restrict-access-by-country.md) |  | **& #x 2713;** | **& #x 2713;** |
| [快速清除](cdn-purge-endpoint.md) | **& #x 2713;** | **& #x 2713;** | **& #x 2713;** |
| [資產預先載入](cdn-preload-endpoint.md) |  | **& #x 2713;** | **& #x 2713;** |
| [核心狀況分析](cdn-analyze-usage-patterns.md) |  | **& #x 2713;** | **& #x 2713;** |
| [HTTP/2 支援](https://msdn.microsoft.com/library/mt762901.aspx) | **& #x 2713;**  |  |  |
| [進階的 HTTP 報表](cdn-advanced-http-reports.md) | | | **& #x 2713;** |
| [即時統計資料](cdn-real-time-stats.md) | | | **& #x 2713;** |
| [即時通知](cdn-real-time-alerts.md) | | | **& #x 2713;** |
| [可自訂的規則為基礎的內容傳遞引擎](cdn-rules-engine.md) | | | **& #x 2713;** |
| 快取/標頭設定 （使用[規則引擎](cdn-rules-engine.md)）  | | | **& #x 2713;** |
| URL 重新導向/好 （使用[規則引擎](cdn-rules-engine.md)） | | | **& #x 2713;** |
| 行動裝置規則 （使用[規則引擎](cdn-rules-engine.md)）  | | | **& #x 2713;** |

>[AZURE.TIP] 有您想要在 Azure CDN 中看到的功能嗎？  [提供意見反應](https://feedback.azure.com/forums/169397-cdn)！ 

## <a name="next-steps"></a>後續步驟

若要開始使用 CDN，請參閱[使用 Azure cdn 到底](./cdn-create-new-endpoint.md)。

如果您是現有的 CDN 客戶，您現在可以管理 CDN 端點透過[Microsoft Azure 入口網站](https://portal.azure.com)，或使用[PowerShell](cdn-manage-powershell.md)。

若要查看的實際操作 CDN，請查看[我們建立 2016年的工作階段的視訊](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/)。

瞭解如何將自動化[.NET](./cdn-app-dev-net.md)或[Node.js](./cdn-app-dev-node.md)Azure cdn 到底。

價格資訊，請參閱[CDN 價格](https://azure.microsoft.com/pricing/details/cdn/)。
