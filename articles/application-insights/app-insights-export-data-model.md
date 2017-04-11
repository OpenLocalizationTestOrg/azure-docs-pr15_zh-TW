<properties 
    pageTitle="應用程式的深入見解資料模型" 
    description="說明中 JSON，連續匯出從匯出，用來作為篩選的屬性。" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>應用程式的深入見解匯出資料模型

下表列出遙測從[應用程式的深入見解](app-insights-overview.md)Sdk 傳送入口網站的屬性。 您會看到這些屬性中[連續匯出](app-insights-export-telemetry.md)的輸出資料。
他們也會出現在[公制總管](app-insights-metrics-explorer.md)] 和 [[診斷搜尋](app-insights-diagnostic-search.md)中的內容篩選器。

請注意的事項︰

* `[0]`下列表格中表示必須插入索引; path 中的點但並不一定 0。
* 時間工期會以 0.1 微秒，因此 10000000 = = 1 秒。
* 日期和時間 UTC，而會取得 ISO 格式`yyyy-MM-DDThh:mm:ss.sssZ`

有幾個[範例](app-insights-export-telemetry.md#code-samples)說明如何使用它們。



## <a name="example"></a>範例

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>內容

所有類型的遙測都附有內容] 區段。 而非所有這些欄位會傳送的每個資料點。



|路徑|類型|備忘稿|
|---|---|---|
| context.custom.dimensions [0]  | [物件]  | 關鍵值字串組設定自訂屬性參數。 索引鍵的最大長度 100，最大長度 1024年值。 更多不要超過 100 個唯一值的屬性可搜尋，但無法用於分割。 每個 ikey 的最大值 200 鍵。  |
| context.custom.metrics [0]  | [物件]  | 設定自訂的度量單位參數及 TrackMetrics 關鍵值組。 索引鍵的最大長度 100，值可能會是數字。 |
| context.data.eventTime | 字串 | UTC |
| context.data.isSynthetic | 布林值 | 邀請會顯示來自傀儡或 web 測試。 |
| context.data.samplingRate | 數字 | 遙測 SDK 傳送給入口網站所產生的百分比。 範圍 0.0 100.0。|
| context.device | 物件 | 用戶端裝置 |
| context.device.browser | 字串 | 例如，Chrome... |
| context.device.browserVersion | 字串 | Chrome 48.0... |
| context.device.deviceModel | 字串 | |
| context.device.deviceName | 字串 | |
| context.device.id | 字串 | |
| context.device.locale | 字串 | 短 GB，de DE... |
| context.device.network | 字串 | |
| context.device.oemName | 字串 | |
| context.device.osVersion | 字串 | 主機作業系統 |
| context.device.roleInstance | 字串 | Server host （主機） 的識別碼 |
| context.device.roleName | 字串 | |
| context.device.type | 字串 | 電腦上，在瀏覽器... |
| context.location | 物件 | 從 clientip。 |
| context.location.city | 字串 | 如果已知，從 clientip，  |
| context.location.clientip | 字串 | 最後一個八邊形被匿名 0。 |
| context.location.continent | 字串 | |
| context.location.country | 字串 | |
| context.location.province | 字串 | 縣/市 |
| context.operation.id | 字串 | 有相同的作業 id 的項目會顯示為相關項目，在入口網站。 通常要求 id。 |
| context.operation.name | 字串 | url 或要求的名稱 |
| context.operation.parentId | 字串 | 可讓巢狀相關的項目。 |
| context.session.id | 字串 | 群組的來源的相同的作業的識別碼。 不執行動作的 30 分鐘一段表示工作階段的結束。 |
| context.session.isFirst | 布林值 | |
| context.user.accountAcquisitionDate | 字串 | |
| context.user.anonAcquisitionDate | 字串 | |
| context.user.anonId | 字串 | |
| context.user.authAcquisitionDate | 字串 | [經過驗證的使用者](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | 布林值 | |
| internal.data.documentVersion | 字串 | |
| internal.data.id | 字串 | |



## <a name="events"></a>事件

自訂[TrackEvent()](app-insights-api-custom-events-metrics.md#track-event)所產生的事件。 


|路徑|類型|備忘稿|
|---|---|---|
| 事件 [0] 計數 | 整數 | 100 / （[取樣](app-insights-sampling.md)工資率）。 4，例如 =&gt; 25%。 |
| [0] 事件名稱 | 字串 | 事件名稱。  最大 250 長度。 |
| 事件 [0] url | 字串 | |
| 事件 [0] urlData.base | 字串 | |
| 事件 [0] urlData.host | 字串 | |

## <a name="exceptions"></a>例外狀況

報表[的例外狀況](app-insights-asp-net-exceptions.md)伺服器和在瀏覽器中。 


|路徑|類型|備忘稿|
|---|---|---|
| basicException [0] 組件 | 字串 | |
| basicException [0] 計數 | 整數 | 100 / （[取樣](app-insights-sampling.md)工資率）。 4，例如 =&gt; 25%。 |
| [0] basicException exceptionGroup | 字串 | |
| [0] basicException exceptionType | 字串 | |字串 | |
| [0] basicException failedUserCodeMethod | 字串 | |
| [0] basicException failedUserCodeAssembly | 字串 | |
| [0] basicException handledAt | 字串 | |
| [0] basicException hasFullStack | 布林值 | |
| basicException [0] 識別碼 | 字串 | |
| basicException [0] 方法 | 字串 | |
| basicException [0] 訊息 | 字串 | 例外狀況訊息。 最大長度 10 k。|
| [0] basicException outerExceptionMessage | 字串 | |
| [0] basicException outerExceptionThrownAtAssembly | 字串 | |
| [0] basicException outerExceptionThrownAtMethod | 字串 | |
| [0] basicException outerExceptionType | 字串 | |
| [0] basicException outerId | 字串 | |
| [0] basicException parsedStack [0] 組件 | 字串 | |
| [0] basicException parsedStack [0] 檔案名稱 | 字串 | |
| [0] basicException parsedStack [0] 層級 | 整數 | |
| [0] basicException parsedStack [0] 列 | 整數 | |
| [0] basicException parsedStack [0] 方法 | 字串 | |
| 堆疊 basicException [0] | 字串 | 最大長度 10 k|
| typeName basicException [0] | 字串 | |



## <a name="trace-messages"></a>追蹤訊息

傳送[TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)，及[介面卡的記錄](app-insights-asp-net-trace-logs.md)。


|路徑|類型|備忘稿|
|---|---|---|
| [0] 訊息 loggerName | 字串 ||
| [0] 的訊息參數 | 字串 ||
| 訊息 [0] raw | 字串 | 記錄訊息最大的長度 10 k。 |
| [0] 訊息嚴重性層級 | 字串 | |



## <a name="remote-dependency"></a>遠端相依性

傳送 TrackDependency。 用來報表效能及使用方式伺服器] 中的[來電至] 相依性](app-insights-asp-net-dependencies.md)，並 AJAX 通話瀏覽器中。

|路徑|類型|備忘稿|
|---|---|---|
| remoteDependency [0] 非同步 | 布林值 | |
| [0] remoteDependency baseName | 字串 |  |
| remoteDependency [0] commandName | 字串 | 比方說 「 常用/索引 」 |
| remoteDependency [0] 計數 | 整數 | 100 / （[取樣](app-insights-sampling.md)工資率）。 4，例如 =&gt; 25%。 |
| [0] remoteDependency dependencyTypeName | 字串 | HTTP SQL... |
| [0] remoteDependency durationMetric.value | 數字 | 從通話回應的相依性的完成時間 |
| remoteDependency [0] 識別碼 | 字串 | |
| remoteDependency [0] 名稱 | 字串 | Url。 最大 250 長度。|
| [0] remoteDependency resultCode | 字串 | 從 HTTP 相依性 |
| remoteDependency [0] 成功 | 布林值 | |
| remoteDependency [0] 類型 | 字串 | Http Sql... |
| remoteDependency [0] url | 字串 |  最大長度 2000 |
| [0] remoteDependency urlData.base | 字串 | 最大長度 2000 |
| [0] remoteDependency urlData.hashTag | 字串 | |
| [0] remoteDependency urlData.host | 字串 | 最大長度 200 |


## <a name="requests"></a>要求

傳送[TrackRequest](app-insights-api-custom-events-metrics.md#track-request)。 標準模組使用此選項報表伺服器回應時間，以在伺服器上。 


|路徑|類型|備忘稿|
|---|---|---|
| 要求 [0] 計數 | 整數 | 100 / （[取樣](app-insights-sampling.md)工資率）。 例如︰ 4 =&gt; 25%。 |
| 要求 [0] durationMetric.value | 數字 | 從 [要求送達回應的時間。 1e7 = = 1 |
| 要求 [0] 識別碼 | 字串 | 操作識別碼 |
| 要求 [0] 名稱 | 字串 | 取得/文章 + 基底的 url。  最大 250 長度 |
| 要求 [0] responseCode | 整數 | HTTP 回應傳送給客戶 |
| 要求 [0] 成功 | 布林值 | 預設 = = (responseCode &lt; 400) |
| 要求 [0] url | 字串 | 不包括 host （主機） |
| 要求 [0] urlData.base | 字串 | |
| 要求 [0] urlData.hashTag | 字串 |  |
| 要求 [0] urlData.host | 字串 | |


## <a name="page-view-performance"></a>頁面檢視效能

在瀏覽器來傳送。 測量處理頁面，以顯示完整 （不包括非同步 AJAX 通話） 要求使用者的時間。

內容值顯示用戶端作業系統和瀏覽器版本。 


|路徑|類型|備忘稿|
|---|---|---|
| [0] clientPerformance clientProcess.value | 整數 | 從接收的 HTML 顯示頁面的結束時間。 |
| clientPerformance [0] 名稱 | 字串 | |
| [0] clientPerformance networkConnection.value | 整數 | 若要建立的網路連線所花費的時間。 |
| [0] clientPerformance receiveRequest.value | 整數 | 要求傳送給回覆接收 HTML 結束時間。 |
| [0] clientPerformance sendRequest.value | 整數 | 從時間移至傳送 HTTP 要求。 |
| [0] clientPerformance total.value | 整數 | 開始傳送邀請以顯示頁面的時間。 |
| clientPerformance [0] url | 字串 | 此要求的 URL |
| [0] clientPerformance urlData.base | 字串 | |
| [0] clientPerformance urlData.hashTag | 字串 | |
| [0] clientPerformance urlData.host | 字串 | |
| [0] clientPerformance urlData.protocol | 字串 | |

## <a name="page-views"></a>頁面檢視

由 trackPageView() 或[stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)傳送

|路徑|類型|備忘稿|
|---|---|---|
| 檢視 [0] 計數 | 整數 | 100 / （[取樣](app-insights-sampling.md)工資率）。 4，例如 =&gt; 25%。 |
| 檢視 [0] durationMetric.value | 整數 | 您也可以在設定 trackPageView() 或 startTrackPage()-值 stopTrackPage()。 不完全相同 clientPerformance 值。 |
| 檢視 [0] 名稱 | 字串 | 頁面標題。  最大 250 長度 |
| 檢視 [0] 的 url | 字串 | |
| 檢視 [0] urlData.base | 字串 | |
| 檢視 [0] urlData.hashTag | 字串 | |
| 檢視 [0] urlData.host | 字串 | |



## <a name="availability"></a>顯示狀態

報表[可用性 web 測試](app-insights-monitor-web-app-availability.md)。

|路徑|類型|備忘稿|
|---|---|---|
| 顯示 [0] 狀態 availabilityMetric.name | 字串 | 顯示狀態 |
| 顯示 [0] 狀態 availabilityMetric.value | 數字 |1.0 或 0.0 |
| 可用性 [0] 計數 | 整數 | 100 / （[取樣](app-insights-sampling.md)工資率）。 4，例如 =&gt; 25%。 |
| 顯示 [0] 狀態 dataSizeMetric.name | 字串 | |
| 顯示 [0] 狀態 dataSizeMetric.value | 整數 | |
| 顯示 [0] 狀態 durationMetric.name | 字串 | |
| 顯示 [0] 狀態 durationMetric.value | 數字 | 測試持續時間。 1e7 = = 1 |
| 顯示 [0] 狀態訊息 | 字串 | 診斷失敗 |
| 可用性 [0] 結果 | 字串 | 成功或失敗 |
| 顯示 [0] 狀態 runLocation | 字串 | Http 需求的地理資料來源 |
| 顯示 [0] 狀態 testName | 字串 | |
| 顯示 [0] 狀態 testRunId | 字串 | |
| 顯示 [0] 狀態 testTimestamp | 字串 | |




## <a name="metrics"></a>指標

產生 TrackMetric()。

公制值位於 context.custom.metrics[0]

例如︰

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>關於度量值

含標準物件結構報告公制報表和其他地方，度量值。 例如︰

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

目前-不過這可能會變更未來-中所有值報告標準 SDK 模組中，從`count==1`和只`name`和`value`欄位很有用。 如果您撰寫 TrackMetric 來電是唯一的大小寫不同的位置，就會在您設定其他參數。 

其他欄位的目的是允許彙總 SDK，以減少流量入口網站中的指標。 例如，您可能會平均數個連續讀取之前傳送的每個公制報告。 然後您想要計算 min、 max、 標準差及彙總 （加總或平均） 的值，並設定讀取報表所表示的數的計數。 

在上述表格中，我們有省略很少使用的欄位計算、 min、 max、 標準差及 sampledValue。

而不是預設彙總的度量，您可以使用[範例](app-insights-sampling.md)，若要減少的遙測音量。


### <a name="durations"></a>工期

除了明，否則工期會表示 0.1 微秒，以便 10000000.0 代表 1 秒。



## <a name="see-also"></a>另請參閱

* [應用程式的深入見解](app-insights-overview.md) 
* [連續的匯出](app-insights-export-telemetry.md)
* [程式碼範例](app-insights-export-telemetry.md#code-samples)


