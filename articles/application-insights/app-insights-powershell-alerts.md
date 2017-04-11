<properties
    pageTitle="使用 Powershell 來應用程式的深入見解中設定通知"
    description="自動設定以取得有關公制變更的電子郵件應用程式深入資訊。"
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
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>使用 PowerShell 來應用程式的深入見解中設定通知

您可以自動化的[Visual Studio 應用程式獲得深入見解](app-insights-overview.md)的[通知](app-insights-alerts.md)設定。

此外，您可以[設定自動回覆給通知 webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md)。

## <a name="one-time-setup"></a>一次設定

如果您並未使用 PowerShell 的 Azure 之前訂閱︰

在您要執行指令碼的電腦上安裝 Azure Powershell 模組。

 * 安裝[Microsoft Web 平台安裝程式 (v5 或更新版本)](http://www.microsoft.com/web/downloads/platform.aspx)。
 * 用來安裝 Microsoft Azure Powershell


## <a name="connect-to-azure"></a>連線至 Azure

啟動 Azure PowerShell，並[連線到您的訂閱](../powershell-install-configure.md)︰

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>取得通知

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>新增提醒


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>範例 1

如果 HTTP 要求，平均超過 5 分鐘，伺服器的回應速度低於 1 第二個，則請我電子郵件。 我的應用程式的深入見解資源稱為 IceCreamWebApp，並在資源群組 Fabrikam。 我可以 Azure 訂閱的擁有者。

GUID 是訂閱識別碼 （不的應用程式的儀器按鍵）。

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>範例 2

我有應用程式中使用[TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric)報告度量單位，名為 「 salesPerHour 」。 傳送電子郵件到我的同事如果 」 salesPerHour 」 降到 100，平均超過 24 小時。

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

相同的規則可以用於所使用的[度量單位參數](app-insights-api-custom-events-metrics.md#properties)的另一個追蹤通話，例如 TrackEvent 或 trackPageView 報告的度量。

## <a name="metric-names"></a>單位名稱

單位名稱 | 顯示名稱 | 描述
---|---|---
`basicExceptionBrowser.count`|在瀏覽器的例外狀況|無法攔截擲回瀏覽器中的例外狀況的計數。
`basicExceptionServer.count`|伺服器例外狀況|計數為擲回應用程式的例外狀況
`clientPerformance.clientProcess.value`|用戶端處理時間|接收的文件的最後一個位元組，直到載入 DOM 之間的時間。 可能仍在處理非同步要求。
`clientPerformance.networkConnection.value`|頁面載入網路連線時間| 在瀏覽器連線至網路所花的時間。 如果快取時，就可以為 0。
`clientPerformance.receiveRequest.value`|接收回應時間| 在瀏覽器將邀請傳送至開始接收回應之間的時間。
`clientPerformance.sendRequest.value`|傳送邀請時間| 若要傳送邀請瀏覽器所花費的時間。
`clientPerformance.total.value`|在瀏覽器頁面載入時間|從使用者要求直到 DOM、 樣式表、 指令碼和圖像會載入時間。
`performanceCounter.available_bytes.value`|可用的記憶體|實體記憶體立即處理程序或系統。
`performanceCounter.io_data_bytes_per_sec.value`|程序 IO 工資率|總秒讀取和寫入檔案、 網路和裝置的位元組數。
`performanceCounter.number_of_exceps_thrown_per_sec`|例外狀況工資率|每秒擲回的例外狀況。
`performanceCounter.percentage_processor_time.value`|程序 CPU|經過的時間，所有的程序執行緒執行指令處理器使用應用程式程序的百分比。
`performanceCounter.percentage_processor_total.value`|處理器時間|處理器花在非閒置執行緒的時間的百分比。
`performanceCounter.process_private_bytes.value`|私用程序的位元組|以獨佔模式指定給監視應用程式的程序的記憶體。
`performanceCounter.request_execution_time.value`|ASP.NET 要求執行時間|最近的要求的執行時間。
`performanceCounter.requests_in_application_queue.value`|ASP.NET 要求執行佇列中|應用程式要求佇列中的長度。
`performanceCounter.requests_per_sec`|ASP.NET 要求率|每秒應用程式從 ASP.NET 所有要求的比率。
`remoteDependencyFailed.durationMetric.count`|相依性失敗|失敗的電話所做的外部資源伺服器應用程式的計數。
`request.duration`|伺服器回應時間|接收 HTTP 要求，然後完成傳送回應之間的時間。
`request.rate`|要求工資率|每秒的應用程式的所有要求的比率。
`requestFailed.count`|失敗的要求|計算的 HTTP 要求而導致回應碼 > = 400
`view.count`|頁面檢視|網頁的用戶端使用者要求的計數。 綜合的流量會篩選。
{您自訂的單位名稱}|{您公制的名稱。|您公制值報告[TrackMetric](app-insights-api-custom-events-metrics.md#track-metric)或[追蹤呼叫的度量單位參數](app-insights-api-custom-events-metrics.md#properties)中。

不同的遙測模組所傳送的指標︰

公制] 群組 | 收集模組
---|---
basicExceptionBrowser，<br/>clientPerformance，<br/>檢視 | [在瀏覽器 JavaScript](app-insights-javascript.md)
performanceCounter | [效能](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [相依性](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
邀請，<br/>requestFailed|[伺服器要求](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

您可以[自動化通知您的回應](../monitoring-and-diagnostics/insights-webhooks-alerts.md)。 Azure 會撥打您選擇的網址時警示。

## <a name="see-also"></a>另請參閱


* [若要設定應用程式的深入見解的指令碼](app-insights-powershell-script-create-resource.md)
* [從範本建立應用程式的深入見解和 web 測試資源](app-insights-powershell.md)
* [自動化結合 Microsoft Azure 診斷應用程式獲得深入見解](app-insights-powershell-azure-diagnostics.md)
* [自動化通知您的回應](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
