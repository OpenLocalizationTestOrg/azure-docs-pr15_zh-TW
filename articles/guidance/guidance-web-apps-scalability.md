<properties
   pageTitle="擴充 web 應用程式 |Azure 參考架構 |Microsoft Azure"
   description="改善擴充 web 應用程式在 Microsoft Azure 中執行。"
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>改善擴充 web 應用程式 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明改善延展性和 Microsoft Azure 上執行的 web 應用程式中的效能的建議的架構。 基礎架構，這是在[Azure 參考架構︰ 基本的 web 應用程式][basic-web-app]。 建議及該文章的考量套用這種以及架構。

>[AZURE.NOTE] Azure 有兩種不同的部署模型︰ 資源管理員] 及 [傳統。 本文所用的資源管理員，Microsoft 建議新的部署。

## <a name="architecture-diagram"></a>架構圖表

![[0]][0]

架構具有下列元件︰

- **資源群組**]。 [資源群組][ resource-group] Azure 資源的邏輯容器。 

- ** [Online] [app-service-web-app]**和**[API 應用程式][app-service-api-app]**。 一般的新式應用程式可能會包含一個或多個 RESTful web Api 和網站。 AJAX 透過瀏覽器用戶端、 原生的用戶端應用程式，或用戶端應用程式，可能會耗用網路 API。 設計 web Api 考量，請參閱[API 設計指南][api-guidance]。    

- **WebJob**。 使用[Azure WebJobs] [webjobs]背景中執行較長時間執行工作。 處理的排程，代理者，或在回應觸發程序，例如將郵件放在佇列中，可以執行 WebJobs。 WebJob 執行為背景的程序應用程式服務應用程式的內容中。 

- **佇列**。 應用程式佇列中此處所示的架構，將郵件放到[Azure 佇列中儲存]背景工作[queue-storage]佇列。 訊息會觸發 WebJob 中的函數。 

    或者，您可以使用服務匯流排佇列。 比較相關資訊，請參閱[Azure 佇列及服務匯流排佇列-比較及相對於][queues-compared]。

- **快取**。 半靜態資料儲存於[Azure Redis 快取][azure-redis]。  

- **Cdn 到底**。 使用[Azure 內容傳遞網路][ azure-cdn] (CDN) 快取可公開使用的內容，較低的延遲和更快速地傳遞的內容。

- **資料儲存區。** 使用[Azure SQL 資料庫][sql-db]關聯式資料。 非關聯式資料，請考慮 NoSQL 商店，例如 Azure 資料表儲存體] 或 [ [DocumentDB][documentdb]。

- **Azure 搜尋**。 使用[Azure 搜尋][azure-search]若要新增的搜尋功能，包括搜尋建議模糊搜尋，與特定語言的搜尋。 Azure 搜尋通常是搭配其他資料存放區，尤其是如果主索引的資料存放區需要嚴格的一致性。 在這種方式，您將其他資料存放區中的授權資料，並將搜尋索引放入 Azure 搜尋。 Azure 搜尋也可用來合併彙算的單一的搜尋索引，從多個資料儲存區。  

- **電子郵件/SMS**。 如果您的應用程式需要傳送電子郵件或簡訊，使用協力廠商服務，例如 SendGrid 或 Twilio，而非直接將應用程式建立這項功能。

## <a name="recommendations"></a>建議

### <a name="app-service-apps"></a>應用程式服務應用程式 

我們建議您建立 web 應用程式和 web API 為另一個應用程式服務應用程式。 此設計可讓您在另一個應用程式服務方案，請執行這些，因此可讓您調整其大小。 如果您不需要的層級延展性在第一次，您可以將應用程式部署到相同的方案，並將其移至另一個方案之後，如有需要。 （Basic、 標準和進階版方案，是向您收費 VM 執行個體中的方案，不是每應用程式。 請參閱[應用程式服務價格][app-service-pricing])

如果您想要使用的*簡單的表格*或應用程式服務行動應用程式的*簡單的 Api*功能，建立另一個應用程式服務應用程式的意思。  這些功能需要一個特定應用程式架構，來啟用這些。

### <a name="webjobs"></a>WebJobs

如果 WebJob 大量的資源，請考慮部署到另一個應用程式服務計劃，以提供 WebJob 專用的執行個體中的空白應用程式服務應用程式。 請參閱[背景工作指南][webjobs-guidance]。  

### <a name="cache"></a>快取

您可以使用[Azure Redis 快取]提升效能與延展性[azure-redis]快取一些資料。 請考慮使用 Redis 快取︰

- 半靜態交易的資料。

- 工作階段狀態。

- HTML 成果。 這可以用來呈現複雜的 HTML 輸出的應用程式。 

如需詳細設計快取的策略指引，請參閱[快取指引][caching-guidance]。

### <a name="cdn"></a>CDN 

使用[Azure CDN] [azure-cdn]快取靜態內容。 Cdn 到底的主要優點是減少延遲的使用者，因為內容會快取*edge server*的地理位置靠近使用者。 Cdn 到底也可以縮減載入到應用程式，因為應用程式沒有正在處理的流量。

- 如果您的應用程式通常包含靜態頁面，請考慮使用快取的整個應用程式的 cdn 到底。 請參閱[使用 Azure 應用程式服務中的 Azure CDN][cdn-app-service]。

- 否則，將靜態內容，例如圖像 CSS 和 HTML 檔案，將 Azure 儲存空間，並使用 CDN 快取這些檔案。 請參閱[整合 CDN 的儲存空間，帳戶][cdn-storage-account]。

> [AZURE.NOTE] Azure CDN 無法提供服務需要驗證的內容。

如需詳細指示，請參閱[內容傳遞網路 」 (CDN) 指南][cdn-guidance]。 

### <a name="storage"></a>儲存空間

新的應用程式通常處理大量的資料。 若要縮放的雲端，請務必選擇正確的儲存空間類型。 以下是一些比較基準建議。  如需詳細指示，請參閱[Polyglot 持續解決方案的評估資料儲存區功能][polyglot-storage]。

您要儲存 | 範例 | 建議的儲存空間
--- | --- | ---
檔案 | 圖像，文件的 Pdf | Azure Blob 儲存體
索引鍵/值組 | 使用者設定檔資料的使用者識別碼查閱 | Azure 資料表儲存體
觸發進一步處理要簡短的訊息 | 順序要求 | Azure 佇列中的儲存空間、 服務匯流排佇列中或服務匯流排主題
非關聯式資料，以彈性的結構描述，需要基本查詢 | 產品目錄 | 文件的資料庫，例如 Azure DocumentDB、 MongoDB 或 Apache CouchDB
需要更豐富的關聯式資料查詢支援、 嚴格的結構描述，及/或強式的一致性 | 產品庫存 | Azure SQL 資料庫

## <a name="scalability-considerations"></a>延展性考量

### <a name="app-service-app"></a>應用程式服務應用程式

如果您的方案包含多個應用程式服務應用程式，請考慮部署至另一應用程式服務方案。 這種方法可讓您調整其大小，因為它們在個別執行個體上執行。 如需有關擴展的詳細資訊，請參閱[延展性考量][ basic-web-app-scalability] ] 區段中的 [[基本的 web 應用程式架構][basic-web-app]。

同樣地，考慮將 WebJob 放到它自己的計劃，讓背景的工作不在上執行相同的執行個體的處理 HTTP 要求。  

### <a name="sql-database"></a>SQL 資料庫

增加*sharding*資料庫的 SQL 資料庫延展性&mdash;亦即水平分割資料庫。 Sharding 可讓您將 out 資料庫水平使用[彈性的資料庫工具][sql-elastic]。 可能的 sharding 優點包括︰

- 更好的交易處理能力。

- 查詢執行較快，透過資料的子集。 

### <a name="azure-search"></a>Azure 搜尋

Azure 搜尋移除的主要的資料存放區中，執行複雜的資料搜尋負荷，就可以調整，以處理載入。 請參閱[查詢和 Azure 搜尋編製索引工作負載的縮放比例資源層級][azure-search-scaling]。

## <a name="security-considerations"></a>安全性考量

### <a name="cross-origin-resource-sharing-cors"></a>十字形，資源共用 (CORS)

如果您建立的網站和網路 API 為另一個應用程式，網站無法進行對 API 的用戶端 AJAX 通話，除非您啟用 CORS。 

> [AZURE.NOTE] 在瀏覽器安全性防止網頁 AJAX 要求到另一個網域。 這項限制稱為相同來源的原則，並從另一個網站讀取 sentitive 資料時，防止惡意網站。 CORS 是 W3C 標準，可讓伺服器放鬆相同來源的原則，並允許一些十字形，要求時拒絕其他人。

應用程式服務會有內建支援 CORS，而不需要撰寫應用程式中的任何程式碼。 請參閱[消耗從使用 CORS JavaScript API 應用程式][cors]。 新增至允許的來源清單中的網站的 API。 

### <a name="sql-database-encryption"></a>SQL 資料庫加密

使用[透明資料加密][sql-encryption]如果您需要加密的資料庫中的其他資料。 此功能會執行即時加密和解密整個資料庫 （包括備份和交易記錄檔），而不需要的應用程式的變更。 加密並新增一些延遲，，以便是好的做法來分隔的資料必須安全到它自己的資料庫，並啟用僅適用於該資料庫加密。  

## <a name="next-steps"></a>後續步驟

- 更高的可用性的部署多個區域中的應用程式，並使用[Azure 流量管理員][tm]容錯移轉。 如需詳細資訊，請參閱[Azure 參考架構︰ Web 應用程式的可用性][web-app-multi-region]。    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Azure 中改良延展性的 web 應用程式"