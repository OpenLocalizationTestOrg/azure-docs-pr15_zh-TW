有一些限制的指標及每個應用程式的事件數 (也就是每個儀器鍵)。 

限制取決於[價格層](https://azure.microsoft.com/pricing/details/application-insights/)您選擇的。

**資源** | **預設的限制** | **最大限制**
-------- | ------------- | -------------
工作階段的資料點<sup>1，2</sup>每個月 | 無限制 | 
每月的要求、 事件、 相依性、 追蹤、 例外狀況和頁面] 檢視的總的資料點 | 5 百萬 | 50 億<sup>3</sup>
[追蹤和記錄](../articles/application-insights/app-insights-search-diagnostic-logs.md)資料工資率 | 200 dp/s | 500 dp/s
[例外狀況](../articles/application-insights/app-insights-asp-net-exceptions.md)資料工資率 | 50 dp/s | 50 dp/s
要求、 事件、 相依性，及頁面檢視遙測加總資料工資率 | 200 dp/s | 500 dp/s
[搜尋](../articles/application-insights/app-insights-diagnostic-search.md)及[分析資料](../articles/application-insights/app-insights-analytics.md)的原始資料保留 | 7 天
彙總的資料保留[指標](../articles/application-insights/app-insights-metrics-explorer.md)總管 | 90 天
[屬性](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名稱計數 | 100 |
屬性名稱長度 | 150 | 
屬性值長度 | 8192 | 
追蹤]、 [例外狀況訊息的長度 | 10000 |
[公制](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties)名稱計數 | 100 |
單位名稱長度 |  150 | 
[可用性測試](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup>的資料點是個別單位值或事件，附加的屬性和度量單位。

<sup>2</sup>的工作階段資料點記錄的開始或結束工作階段，並將記錄使用者身分識別。

<sup>3</sup> ，您可以購買超過 50 萬個額外的容量。
 
[關於價格和中應用程式的深入見解的配額](../articles/application-insights/app-insights-pricing.md)
