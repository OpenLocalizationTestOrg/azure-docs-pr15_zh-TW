<properties 
    pageTitle="Azure API 管理原則參照" 
    description="進一步瞭解可用來設定 API 管理原則。" 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Azure API 管理原則參照

本節提供索引的[API 管理原則參照][]中的原則。 新增及設定原則的資訊，請參閱[API 管理原則][]。

除非原則指定，否則，就可以為屬性值或文字值中任何 API 管理原則，使用原則運算式。 某些原則，例如[控制項流程][]和[變數設定][]原則根據原則運算式。 如需詳細資訊，請參閱[進階原則][]和[原則運算式][]

## <a name="policy-reference-index"></a>原則參考索引

-   [存取限制原則][]
    -   [檢查 HTTP 標頭][]-會強制執行是否存在及/或 HTTP 標頭的值。
    -   [限制通話工資率訂閱][]可防止 API 使用狀況尖峰限制通話 rate，每個訂閱為基礎。
    -   [索引鍵的限制通話工資率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey)會防止 API 使用狀況尖峰限制通話 rate，每個索引鍵為基礎。
    -   [限制來電者 IPs][]篩選 （允許/拒絕） 通話從特定的 IP 位址，及/或位址範圍。
    -   [設定以訂閱的使用量配額][]-可讓您強制執行儲值或生命週期通話大量及/或頻寬配額] 中，每個訂閱為基礎。
    -   [設定索引鍵的使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey)-可讓您強制執行儲值或生命週期通話大量及/或頻寬配額] 中，每個索引鍵為基礎。
    -   [驗證 JWT][] -會強制執行存在和 JWT，從 [指定的 HTTP 頁首] 或 [指定的查詢參數擷取的有效性。
-   [進階的原則][]
    -   [控制流程][]-條件適用於結果的布林[運算式][]的評估為基礎的原則陳述式。
    -   [轉寄的邀請][]-轉寄的後端服務的要求。
    -   [記錄事件中心][]-傳送郵件到郵件目標所定義的[記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger)實體指定的格式。
    -   [再試一次](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry)-重試執行圍繞的原則陳述式中，如果，直到符合條件。 執行會重複指定的時間間隔，並再次進位至指定的 [計算。
    -   [傳回回應](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse)-中止管線執行，並傳回指定的回應，直接與來電者。
    -   [傳送其中一個方法要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest)-至指定的 URL 傳送要求而不需等待回應。
    -   [傳送邀請](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)-至指定的 URL 傳送邀請。
    -   [設定要求方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)可讓您變更要求的 HTTP 方法。
    -   [設定狀態](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus)-變更 HTTP 狀態碼指定的值。
    -   [設定變數][]-保存在稍後存取具名的[內容][]變數中的值。
    -   [追蹤](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace)-新增到[API 檢查](../api-management/api-management-howto-api-inspector.md)輸出的字串。
    -   [等候](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait)-等待圍繞傳送邀請，取得值快取，或是控制流程原則完成後，繼續進行。
-   [驗證原則][]
    -   [驗證 basic][] -驗證，以使用基本驗證的後端服務。
    -   [用戶端憑證驗證][]-與後端服務使用用戶端憑證驗證。
-   [快取原則][] 
    -   [取得從快取][]-執行快取查詢，並將有效的快取的回應可用時傳回。
    -   [儲存至快取][]-根據指定的快取控制項設定的快取回應。
    -   [取得快取中的值](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey)-擷取索引鍵的快取的項目。
    -   [快取中的儲存區值](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey)-市集索引鍵快取中的項目。
    -   [移除快取中的值](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey)-移除索引鍵快取中的項目。
-   [跨網域原則][] 
    -   [允許跨網域呼叫][]-讓 API 存取 Adobe Flash 和 Microsoft Silverlight 的瀏覽器型用戶端。
    -   [CORS][] -加入共用 (CORS) 支援作業或允許從瀏覽器型的用戶端的跨網域呼叫 API 的十字形，資源。
    -   [JSONP][] -新增作業的邊框距離 」 (JSONP) 支援的 JSON 或 API 允許跨網域呼叫 JavaScript 瀏覽器型的用戶端。
-   [轉換原則][] 
    -   為 XML，從 JSON 本文[轉換為 XML 的 JSON][] -轉換要求或回應。
    -   [將 XML 轉換為 JSON][] -轉換要求或回應本文從 XML 為 JSON。
    -   [尋找及取代本文中的字串][]-尋找要求或回應的子字串，並將其取代為不同的子字串。
    -   [在內容的遮罩 Url][]重新寫入 （遮罩） 的連結，在回應中本文，使其指向透過閘道器的對等的連結。
    -   [設定後端服務][]-變更內送的要求後端服務。
    -   [設定本文][]-設定內送和外寄要求的郵件本文。
    -   [設定 HTTP 標頭][]-指派至現有的回應，及/或要求標頭的值，或增加新的回應，及/或要求頁首。
    -   [設定查詢字串參數][]-新增，取代值] 或刪除要求查詢字串參數。
    -   [改寫 URL][] -從其公用表單轉換要求 URL 至 web 服務所需的表單。

## <a name="next-steps"></a>後續步驟

如需有關原則運算式的詳細資訊，請參閱以下的影片。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[存取限制原則]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[檢查 HTTP 標頭]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[訂閱限制通話工資率]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[限制 [本機 Ip]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[設定以訂閱的使用量配額]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[驗證 JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[進階的原則]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[控制流程]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[設定變數]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[內容]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[轉寄邀請]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[事件中樞的記錄檔]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[驗證原則]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[基本驗證方法]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[用戶端憑證驗證方法]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[快取原則]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[取得從快取]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[儲存至快取]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[跨網域原則]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[允許跨網域電話]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[轉換原則]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[將 JSON 轉換為 XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[將 XML 轉換成 JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[尋找及取代本文中的字串]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[在內容的遮罩 Url]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[設定後端服務]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[設定本文]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[設定 HTTP 標頭]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[設定查詢字串參數]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL 重新寫入]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[在 [API 管理原則]: api-management-howto-policies.md
[API 管理原則參照]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[原則運算式]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
