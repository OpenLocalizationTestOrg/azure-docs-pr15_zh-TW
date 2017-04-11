<properties
    pageTitle="使用 [開發人員分析行動 web 應用程式的效能監視 |Microsoft Azure"
    description="應用程式的效能及使用方式監控行動應用程式開發人員。 桌面、 web 服務與 HockeyApp 與應用程式的深入見解的後端應用程式。"
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>行動 HockeyApp 與應用程式的深入見解的分析

監控效能及使用方式的 beta 測試和部署行動和桌面應用程式，使用[HockeyApp](https://hockeyapp.net/)。 監控支援 web 服務與後端應用程式搭配使用[Visual Studio 應用程式深入資訊](app-insights-overview.md)。 分析資料從用戶端和伺服器應用程式中分析資料，並顯示彼此圖表 Azure 儀表板中。

Microsoft 開發人員分析提供兩種解決方案的應用程式效能監視︰

* **Mobile HockeyApp**和桌面應用程式。
 * 散發給所選的使用者測試版本。
 * 當機分析。
 * 自訂的使用狀況分析遙測。
* **應用程式獲得深入見解的網站**和服務，以及後端應用程式。
 * 效能及使用方式的指標及通知。
 * 例外狀況報告及診斷追蹤。
 * 搜尋診斷的篩選及相關遙測連結]。

提供兩種解決方案︰

 * 功能強大的**[分析查詢語言](app-insights-analytics.md)**診斷和分析。
 * **[匯出資料](app-insights-export-telemetry.md)**至您自己的儲存空間。
 * **整合式儀表板**顯示的分析圖表和表格。

## <a name="monitor-your-app-components"></a>監控您的應用程式元件

請遵循這些網頁程式碼中安裝 SDK 並開始追蹤您的應用程式中的指示進行。

### <a name="web-apps---application-insights"></a>Web 應用程式的應用程式的深入見解

* [ASP.NET web 應用程式](app-insights-asp-net.md) 
* [Java web 應用程式](app-insights-java-get-started.md)
* [Node.js web 應用程式](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Azure 雲端服務](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>行動應用程式-HockeyApp

* [iOS 應用程式](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X 應用程式](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android 應用程式](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [通用的 Windows 應用程式](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 和 8.1 應用程式](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows 簡報 Foundation 應用程式](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Windows 桌面應用程式，我們建議您 HockeyApp。 但您也可以[傳送遙測從應用程式獲得深入見解的 Windows 應用程式](app-insights-windows-desktop.md)。 您可能會想要的下嘗試使用應用程式的深入見解。


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>分析及 HockeyApp 遙測的匯出

您可以調查 HockeyApp 自訂並登入遙測使用[橋](app-insights-hockeyapp-bridge-app.md)設定應用程式的深入見解的分析及連續匯出功能。




