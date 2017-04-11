<properties 
    pageTitle="ASP.NET 核心應用程式深入資訊" 
    description="監控 web 應用程式的可用性、 效能及使用方式。" 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="awills"/>

# <a name="application-insights-for-aspnet-core"></a>ASP.NET 核心應用程式深入資訊

[Visual Studio 應用程式深入資訊](app-insights-overview.md)可讓您監控您的 web 應用程式的可用性、 效能及使用方式。 您取得效能和您的應用程式的相關肆虐的意見反應，您可以在每個開發週期讓謹慎的選擇設計的方向。

![範例](./media/app-insights-asp-net-core/sample.png)

您需要[Microsoft Azure](http://azure.com)的訂閱。 使用 Microsoft 帳戶，您可能會有 Windows、 XBox Live 或其他 Microsoft 雲端服務的登入。 您的小組可能 Azure 組織訂閱︰ 要求擁有者將您新增至並使用您的 Microsoft 帳戶。


## <a name="getting-started"></a>快速入門

請依照 [[快速入門指南](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started)。

## <a name="using-application-insights"></a>使用應用程式的深入見解

登入[Microsoft Azure 入口網站](https://portal.azure.com)，瀏覽至資源您建立監控您的應用程式。

在另一個瀏覽器視窗中，您的應用程式，使用一段。 您會看到出現在應用程式的深入見解圖表中的資料。 （您可能需要按一下 [重新整理）。會有只有少量的資料時您正在開發，但這些圖表十分作用您發佈您的應用程式，有許多使用者。 

[概觀] 頁面會顯示效能圖表您最有可能會感興趣的︰ 伺服器回應時間、 頁面載入時間，以及的計數失敗的要求。 按一下任何圖表，請參閱更多圖表與資料。

在入口網站檢視可分為兩種主要類別︰

* [指標 Explorer](app-insights-metrics-explorer.md)有顯示圖形和指標及計算，例如回應時間、 失敗率或您自行建立以[API](app-insights-api-custom-events-metrics.md)的指標。 篩選及區段的屬性值，以了解您的應用程式和其使用者的資料。
* [搜尋檔案總管](app-insights-diagnostic-search.md)列出個別的事件，例如特定的要求、 例外狀況、 記錄追蹤或自己的[API](app-insights-api-custom-events-metrics.md)建立的事件。 篩選，在 [事件] 中搜尋並瀏覽至調查問題的相關事件之間。
* [分析](app-insights-analytics.md)可讓您透過您的遙測執行類似的 SQL 查詢及是功能強大的分析及診斷工具。

## <a name="alerts"></a>通知

* 您會自動取得[主動診斷通知](app-insights-proactive-diagnostics.md)，以告知您在失敗率異常變更及其他指標。
* 設定[可用性測試](app-insights-monitor-web-app-availability.md)，測試您的網站持續從全球的位置，並取得電子郵件，就會立即任何測試失敗。
* 設定[公制通知](app-insights-monitor-web-app-availability.md)知道指標，例如回應時間] 或 [例外狀況費率如果移外部可接受的限制。

## <a name="get-more-telemetry"></a>取得更多遙測

* [新增至網頁的遙測](app-insights-javascript.md)監視頁面的使用情況和效能。
* 若要查看是否其餘、 SQL 或其他外部資源減慢您[監視器相依性](app-insights-dependencies.md)。
* [使用 API](app-insights-api-custom-events-metrics.md)來傳送您自己的事件及指標，取得更詳細檢視您的應用程式效能及使用方式。
* [可用性測試](app-insights-monitor-web-app-availability.md)核取您經常從世界各地的應用程式。 


## <a name="open-source"></a>開啟來源

[閱讀並參與記錄的程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


