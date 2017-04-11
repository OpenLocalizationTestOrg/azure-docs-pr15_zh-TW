<properties 
    pageTitle="疑難排解與應用程式的深入見解的相關問題" 
    description="內容在 Visual Studio 應用程式的深入見解語意不明，或不使用？ 請嘗試以下。" 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>ASP.NET 的應用程式深入見解的問題-

## <a name="configuration-problems"></a>設定問題

*我會遇到無法我︰*

* [.NET 應用程式](app-insights-asp-net-troubleshoot-no-data.md)
* [監視已執行應用程式](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Azure 診斷程式](app-insights-azure-diagnostics.md)
* [Java web 應用程式](app-insights-java-troubleshoot.md)
* [其他平台](app-insights-platforms.md)

*從 [我的伺服器取得資料*

* [設定防火牆例外狀況](app-insights-ip-addresses.md)
* [ASP.NET 伺服器設定](app-insights-monitor-performance-live-website-now.md)
* [Java 伺服器設定](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>是否可以使用應用程式使用的深入見解...？

[請參閱平台][platforms]


## <a name="is-it-free"></a>這是免費？

* 是的如果您選擇[價格層](app-insights-pricing.md)免費。 您取得大部分功能及大方的配額的資料。 
* 您必須提供您的信用卡資料註冊 Microsoft Azure，但沒有費用將會成為，除非您使用另一個付款的 Azure 服務，或您明確升級至付費層。
* 如果您的應用程式免費層傳送的每月配額比更多資料，它會停止記錄。 此時，您可以選擇即可開始支付，或等到配額會重設結尾的月份。
* 基本使用狀況] 和 [工作階段的資料不會配額。
* 此外，還有 30-天免費試用版，就要免費的付費的功能。
* 每個應用程式資源的個別的配額] 中，並設定其價格層亦任何其他人。

#### <a name="what-do-i-get-if-i-pay"></a>如果我使用，取得什麼？

* 較大的[每月配額的資料](https://azure.microsoft.com/pricing/details/application-insights/)。
* 支付 'overage 「 若要繼續超過每月配額收集資料的選項。 如果您的資料超過配額，您就被會每 Mb。
* [[接續本頁匯出](app-insights-export-telemetry.md)]。


## <a name="q14"></a>應用程式的深入見解者會在專案中什麼修改？

詳細資料的專案類型而定。 Web 應用程式︰


+ 將這些檔案新增至您的專案︰

 + ApplicationInsights.config。 
 + ai.js


+ 安裝這些 NuGet 套件︰

 -  *應用程式的深入見解 API*核心 API

 -  *Web 應用程式的應用程式的深入見解 API* -用於從伺服器傳送遙測

 -  *JavaScript 應用程式的應用程式的深入見解 API* -用來傳送遙測從用戶端

    套件包含這些組件︰

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 將插入項目︰

 - Web.config

 - packages.config

+ (新只有-專案，如果您[新增的應用程式的現有專案的深入見解][start]，您必須手動執行此動作。)插入初始化應用程式的深入見解資源識別碼的用戶端與伺服器的程式碼片段 例如，MVC 應用程式，程式碼會插入 Views/Shared/_Layout.cshtml 的主版頁面


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何將舊版 SDK 從升級？

請參閱[版本資訊](app-insights-release-notes.md)SDK 適合用於您的應用程式類型。 



## <a name="update"></a>如何變更我的專案會傳送至資料的 Azure 資源？

在方案總管中，以滑鼠右鍵按一下`ApplicationInsights.config`，然後選擇 [**更新應用程式深入資訊**。 您可以傳送資料至現有或新資源 Azure 中。 [更新] 精靈變更儀器中的索引鍵 ApplicationInsights.config 決定伺服器 SDK 要傳送給您的資料。 除非您取消選取 [全部更新 」 時，它也會變更出現在網頁中的索引鍵。


#### <a name="data"></a>資料多久保留在入口網站？ 資料是否安全？

看看[資料保留資料和隱私權][data]。

## <a name="logging"></a>記錄

#### <a name="post"></a>如何檢視文章搜尋診斷] 中的資料？

我們不會自動記錄文章資料，但您可以使用 TrackTrace 打電話︰ 資料放在訊息參數。 雖然您無法在其上篩選，這會有較長的大小限制，比字串屬性的限制。 

## <a name="security"></a>安全性

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>為我的資料安全入口網站中？ 多久是將其保留？

請參閱[資料保留和隱私權][data]。


## <a name="q17"></a>我已啟用的深入見解應用程式中的所有項目？

<table border="1">
<tr><th>您應該會看到</th><th>如何取得</th><th>在您要的原因</th></tr>
<tr><td>可用的圖表</td><td><a href="../app-insights-monitor-web-app-availability/">Web 測試</a></td><td>知道您的 web 應用程式設定</td></tr>
<tr><td>伺服器應用程式的效能︰ 回應的時間，...
</td><td><a href="../app-insights-asp-net/">新增應用程式的深入見解至專案</a><br/>或 <br/><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a>（或追蹤<a href="../app-insights-api-custom-events-metrics/#track-dependency">相依性</a>撰寫您自己的程式碼）</td><td>偵測效能問題</td></tr>
<tr><td>相依性遙測</td><td><a href="../app-insights-monitor-performance-live-website-now/">在伺服器上安裝 AI 狀態監視器</a></td><td>診斷資料庫或其他外部元件問題</td></tr>
<tr><td>取得堆疊追蹤的例外狀況</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">在您的程式碼中插入 TrackException 呼叫</a>（但部分會自動報告）</td><td>偵測與診斷例外狀況</td></tr>
<tr><td>搜尋記錄追蹤</td><td><a href="../app-insights-search-diagnostic-logs/">新增記錄介面卡</a></td><td>診斷例外]，[效能問題</td></tr>
<tr><td>用戶端使用基本概念︰ 頁面檢視、 工作階段，...</td><td><a href="../app-insights-javascript/">在網頁中的 JavaScript 初始設定</a></td><td>使用狀況分析</td></tr>
<tr><td>用戶端自訂指標</td><td><a href="../app-insights-api-custom-events-metrics/">追蹤通話網頁</a></td><td>加強使用者經驗</td></tr>
<tr><td>伺服器自訂指標</td><td><a href="../app-insights-api-custom-events-metrics/">追蹤通話伺服器程式碼</a></td><td>商務智慧功能</td></tr>
</table>


## <a name="automation"></a>自動化

您可以建立及更新應用程式的深入見解資源的 [[書寫 PowerShell 指令碼](app-insights-powershell.md)。

## <a name="more-answers"></a>更多解答

* [應用程式的深入見解論壇](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 