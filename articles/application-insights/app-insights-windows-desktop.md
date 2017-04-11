<properties 
    pageTitle="監視使用情況和 Windows 桌面應用程式的效能" 
    description="使用情況和 Windows 桌面應用程式 HockeyApp 與應用程式的深入見解的效能分析。" 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>監視使用情況和 Windows 桌面應用程式中的效能

*應用程式的深入見解位於預覽。*

[Visual Studio 應用程式獲得深入見解](app-insights-overview.md)和[HockeyApp](https://hockeyapp.net)可讓您監控部署的應用程式的使用情況和效能。

> [AZURE.IMPORTANT] 我們建議您[HockeyApp](https://hockeyapp.net)分送及發佈監控桌面和裝置的相關應用程式。 使用 HockeyApp，您可以管理通訊群組、 即時測試，及使用者意見反應，以及監視使用情況和損毀的報表。 您也可以[匯出及查詢的使用狀況分析您遙測](app-insights-hockeyapp-bridge-app.md)。

> 雖然遙測傳送至應用程式的深入見解從桌面應用程式，這是來達成偵錯及實驗用途有用。


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>若要從 Windows 應用程式，傳送應用程式獲得深入見解的遙測

1. 在[Azure 入口網站](https://portal.azure.com)，[建立應用程式的深入見解資源](app-insights-create-new-resource.md)。 應用程式類型，選擇 [ASP.NET 應用程式]。
2. 需要儀器索引鍵的複本。 在基本資訊下拉式您剛剛建立的新資源尋找金鑰。 
3. 在 Visual Studio 中，編輯 NuGet 套件的應用程式專案，並新增 Microsoft.ApplicationInsights.WindowsServer。 (或如果您只想不包裝選擇 Microsoft.ApplicationInsights API，沒有標準遙測集合模組。)
4. 在您的程式碼中設定儀器機碼︰

    `TelemetryConfiguration.Active.InstrumentationKey = "`*您的金鑰*`";` 

    或 ApplicationInsights.config （如果您已安裝的標準遙測套件的其中一個）︰
 
    `<InstrumentationKey>`*您的金鑰*`</InstrumentationKey>` 

    如果您使用 ApplicationInsights.config，請確定它在方案總管中的屬性設定為**建立巨集指令 = 內容，複製到輸出目錄 = 複製**。
5. 若要傳送遙測[使用 API](app-insights-api-custom-events-metrics.md) 。
6. 執行您的應用程式，請參閱在您建立 Azure 入口網站中的資源遙測。

## <a name="telemetry"></a>程式碼範例

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>後續步驟

* [建立儀表板](app-insights-dashboards.md)
* [診斷搜尋](app-insights-diagnostic-search.md)
* [探索指標](app-insights-metrics-explorer.md)
* [撰寫查詢分析](app-insights-analytics.md)
 
