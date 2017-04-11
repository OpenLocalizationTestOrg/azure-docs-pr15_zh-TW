<properties 
    pageTitle="Windows 版應用程式的深入見解的版本資訊" 
    description="Windows 市集 SDK 最新的更新。" 
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
    ms.date="02/12/2016" 
    ms.author="joshweb"/>
 
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>應用程式的深入見解 Windows SDK 的版本資訊與儲存

應用程式的深入見解 SDK 會將您即時應用程式的相關遙測傳送到[應用程式的深入見解](https://azure.microsoft.com/services/application-insights/)，您可以在其中分析效能及其使用方式。


## <a name="version-111"></a>1.1.1 版本

### <a name="windows-sdk"></a>Windows SDK

- 使用 Windows Phone 的 Silverlight SDK 時，修正擱置時當機。 此變更後，就會保存晚 ~ 2 秒鐘 WindowsAppInitialier.InitializeAsync(...) 呼叫之後發生任何當機到磁碟] 並將會傳送下一次啟動應用程式。 如果當機發生 ~ 2 （秒） 之後通話之前，它將會忽略。  
- 核心和 Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3) 的特定版本設定 NuGet 的相依性。   

### <a name="core-sdk"></a>核心 SDK

- 核心 github 中管理。 核心 SDK 的未來的版本資訊，請參閱[github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>版本 1.1 （英文）

### <a name="core-sdk"></a>核心 SDK

- SDK 現在會介紹新遙測類型```DependencyTelemetry```其中包含從應用程式的相依性通話的相關資訊
- 新的方法```TelemetryClient.TrackDependency```可讓應用程式從傳送相依性通話的相關資訊

## <a name="version-100"></a>版本 1.0.0

### <a name="windows-app-sdk"></a>Windows 應用程式 SDK

- 初始 for Windows 應用程式的新設定。 新`WindowsAppInitializer`課程與`InitializeAsync()`方法可啟動初始化 SDK 集合。 這項變更移到上一個 ApplicationInsights.config 技巧，允許更精確地控制及嚴重應用程式初始化效能提升。
- DeveloperMode 不會再自動設定]。 若要變更的程式碼，必須指定 DeveloperMode 行為。
- NuGet 套件不再會插入 ApplicationInsights.config。 若要使用新的 WindowsAppInitializer 手動新增 NuGet 套件時，建議您。
- 僅限讀取 ApplicationInsights.config `<InstrumentationKey>`，所有其他設定，會略過的 WindowsAppInitializer 設定喜好設定。
- 存放區市場會自動收集 SDK。
- 多個錯誤修正、 穩定性和效能增強功能。

### <a name="core-sdk"></a>核心 SDK

- ApplicationInsights.config 檔案已不再 requiered。 並不新增 NuGet 套件。 設定可以完全代碼中指定。
- NuGet 套件會不會再將目標檔案新增至您的方案。 這樣會移除 DeveloperMode 偵錯建置時自動設定。 DeveloperMode 應該在 [程式碼手動設定。

## <a name="version-017"></a>介於 0.17 版本

### <a name="windows-app-sdk"></a>Windows 應用程式 SDK

- Windows 應用程式 SDK 現在支援通用針對 Windows 10 technical preview 和與 2015 RC 建立的 Windows 應用程式。

### <a name="core-sdk"></a>核心 SDK

- 初始化 InMemoryChannel TelemetryClient 預設值。
- 新增，新的 API `TelemetryClient.Flush()`。 這個靠方法會觸發的用戶端登入的所有遙測立即封鎖上傳。 如此手動觸發程序關機之前的上傳的項目。
- NuGet 套件新增.Net 4.5 目標。 此目標有沒有外部相依性 （移除 BCL 和 EventSource 相依性）。

## <a name="version-016"></a>版本 0.16 

2015-04 28 預覽

- SDK 現在支援 DNX 目標平台以啟用監控[核心.NET framework](http://www.dotnetfoundation.org/NETCore5)應用程式。
- 執行個體```TelemetryClient```不要快取儀器鍵再。 現在，如果儀器鍵不在中設定```TelemetryClient```明確```InstrumentationKey```會傳回 null。 它可以修正這個問題，當您設定```TelemetryConfiguration.Active.InstrumentationKey```已收集一些遙測遙測模組等相依性行程、 web 要求資料收集與效能計數器收集器會使用新的儀器金鑰之後。

## <a name="version-015"></a>版本 0.15

- 新屬性```Operation.SyntheticSource```現在提供```TelemetryContext```。 現在您可以將您的遙測郵件標示為 「 不實際使用者流量 」，並指定此流量產生的方法。 設定此屬性，例如您可以從您的測試自動化載入測試流量區分流量。
- 頻道邏輯已移至另一個稱為 Microsoft.ApplicationInsights.PersistenceChannel 的 NuGet。 預設頻道現在稱為 InMemoryChannel
- 新的方法```TelemetryClient.Flush```允許同步從緩衝清除遙測項目

## <a name="version-013"></a>版本 0.13

沒有可用的較舊版本的版本資訊。 
