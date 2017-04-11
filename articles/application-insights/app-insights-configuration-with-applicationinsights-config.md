<properties 
    pageTitle="設定應用程式的深入見解 SDK ApplicationInsights.config 或.xml" 
    description="啟用或停用集合模組資料，並新增效能計數器和其他參數" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>設定應用程式的深入見解 SDK ApplicationInsights.config 或.xml

應用程式的深入見解.NET SDK 組成 NuGet 套件的數字。 [核心套件](http://www.nuget.org/packages/Microsoft.ApplicationInsights)提供 API 傳送遙測應用程式獲得深入見解。 [其他封包](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights)提供遙測_模組_和_初始_自動追蹤遙測，從您的應用程式和其內容。 藉由調整設定檔，您可以啟用或停用遙測模組及初始設定式，並將其中一些設定參數。

設定檔名為`ApplicationInsights.config`或`ApplicationInsights.xml`、 應用程式的類型而定。 它會自動新增至您的專案時[安裝最新版 SDK][start]。 它也會加入至 web app [IIS 伺服器上]的狀態監視器[redfield]，或當您選取 Appplication 獲得深入見解[Azure 網站或 VM 副檔名](app-insights-azure-web-apps.md)。

不相等的檔案，以控制[在網頁上的 SDK][client]。

這份文件描述您看到設定檔案，它們如何控制 SDK 的元素，然後哪些 NuGet 套件載入這些元件的區段。

## <a name="telemetry-modules-aspnet"></a>遙測模組 (ASP.NET)

每個遙測模組收集特定類型的資料，並使用核心 API 傳送資料。 安裝模組以不同的 NuGet 套件，也.config 檔案中新增必要的線條。

在每一個模組的設定檔中沒有節點。 若要停用模組，請刪除節點或查看註解。



### <a name="dependency-tracking"></a>追蹤的相依性

[相依性追蹤](app-insights-dependencies.md)收集遙測資料庫並外部服務資料庫的應用程式可讓您的來電。 若要允許使用 IIS 伺服器此模組，您必須[安裝狀態監視器][redfield]。 若要在 Azure web 應用程式或 Vm，[選取 [應用程式的深入見解副檔名為](app-insights-azure-web-apps.md)使用它。

您也可以撰寫您自己的相依性追蹤使用[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)的程式碼。


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 套件。

### <a name="performance-collector"></a>效能行程

[收集系統效能計數器](app-insights-performance-counters.md)，例如從 IIS 安裝的 CPU、 記憶體和網路負載。 您可以指定哪些計數器收集，包括您已設定您自己的效能計數器。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet 套件。


### <a name="application-insights-diagnostics-telemetry"></a>應用程式的深入見解診斷遙測

`DiagnosticsTelemetryModule`報表中的應用程式的深入見解儀器程式碼本身的錯誤。 例如，如果程式碼無法存取效能計數器或`ITelemetryInitializer`會產生例外狀況。 本單元所追蹤的追蹤遙測會出現在[診斷搜尋][diagnostic]。 傳送診斷資料至 dc.services.vsallin.net。
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 套件。 如果您只會安裝此套件，不會自動建立 ApplicationInsights.config 檔案。 

### <a name="developer-mode"></a>開發人員模式

`DeveloperModeWithDebuggerAttachedTelemetryModule`強制應用程式的深入見解`TelemetryChannel`傳送立即資料一個遙測項目，一次，當偵錯工具附加至應用程式程序。 當您的應用程式會追蹤遙測，而會出現在應用程式的深入見解入口網站上時，這會減少森積差之間的時間量。 它會導致嚴重開銷 CPU 和網路頻寬。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [應用程式的深入見解 Windows 伺服器](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)NuGet 套件

### <a name="web-request-tracking"></a>追蹤 web 要求

報表的 HTTP 要求[回應的時間和結果的程式碼](app-insights-asp-net.md)。 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 套件

### <a name="exception-tracking"></a>追蹤的例外狀況

`ExceptionTrackingTelemetryModule`追蹤未處理 web 應用程式中的例外狀況。 請參閱[失敗及例外][exceptions]。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet 套件


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-追蹤[unobserved 的工作例外狀況](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)。
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-追蹤處理的例外狀況的工作者角色與 windows 服務主控台應用程式。
* [應用程式的深入見解 Windows 伺服器](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)NuGet 套件。

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

Microsoft.ApplicationInsights 套件提供[核心 API](https://msdn.microsoft.com/library/mt420197.aspx) SDK。 其他遙測模組使用此功能，與您也可以[用來定義自己遙測](app-insights-api-custom-events-metrics.md)。

* 沒有 ApplicationInsights.config] 中的項目。
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet 套件。 如果您只要安裝此 NuGet，則會產生沒有.config 檔案。

## <a name="telemetry-channel"></a>遙測頻道

遙測頻道管理緩衝和應用程式的深入見解服務遙測傳輸。 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`是預設頻道的服務。 它在記憶體中緩衝資料。
* `Microsoft.ApplicationInsights.PersistenceChannel`是主控台應用程式的替代方案。 當您的應用程式關閉，並將其傳送給，再次啟動應用程式時，就可以節省 unflushed 的任何資料到永久儲存區。


## <a name="telemetry-initializers-aspnet"></a>遙測初始 (ASP.NET)

遙測初始設定內容屬性與遙測的每個項目一併傳送。 

您可以[撰寫您自己的初始](app-insights-api-filtering-sampling.md#add-properties)設定屬性的內容。

標準初始所有設為網頁或 WindowsServer NuGet 套件︰


* `AccountIdTelemetryInitializer`設定 [AccountId 屬性。
* `AuthenticatedUserIdTelemetryInitializer`設定 [AuthenticatedUserId 屬性所設定的 JavaScript SDK。
* `AzureRoleEnvironmentTelemetryInitializer`更新`RoleName`和`RoleInstance`的屬性`Device`內容所有遙測項目，以從 Azure 執行階段環境中擷取的資訊。
* `BuildInfoConfigComponentVersionTelemetryInitializer`更新`Version`屬性`Component`的值從展開所有遙測項目內容`BuildInfo.config`檔案所產生的 MS 建立。
* `ClientIpHeaderTelemetryInitializer`更新`Ip`屬性`Location`的遙測的所有項目內容根據`X-Forwarded-For`HTTP 標頭的要求。
* `DeviceTelemetryInitializer`更新下列屬性的`Device`遙測的所有項目內容。
 - `Type`設定為 「 電腦 」
 - `Id`設定為電腦的網域名稱的執行 web 應用程式的位置。
 - `OemName`設定為從擷取值`Win32_ComputerSystem.Manufacturer`欄位使用 WMI。
 - `Model`設定為從擷取值`Win32_ComputerSystem.Model`欄位使用 WMI。
 - `NetworkType`設定為從擷取值`NetworkInterface`。
 - `Language`設定名稱為`CurrentCulture`。
* `DomainNameRoleInstanceTelemetryInitializer`更新`RoleInstance`屬性`Device`遙測的所有項目使用網域名稱執行的電腦的 web 應用程式的內容。
* `OperationNameTelemetryInitializer`更新`Name`屬性`RequestTelemetry`和`Name`屬性`Operation`遙測的所有項目的內容會根據 HTTP 方法，以及 ASP.NET MVC 控制器和啟動處理要求動作的名稱。
* `OperationIdTelemetryInitializer`或`OperationCorrelationTelemetryInitializer`更新`Operation.Id`處理具有自動產生的要求時，追蹤所有遙測項目的內容屬性`RequestTelemetry.Id`。
* `SessionTelemetryInitializer`更新`Id`屬性`Session`從擷取的值的所有遙測項目的內容`ai_session`在使用者的瀏覽器中執行 ApplicationInsights JavaScript 儀器程式碼所產生的 cookie。 
* `SyntheticTelemetryInitializer`或`SyntheticUserAgentTelemetryInitializer`更新`User`，`Session`和`Operation`處理從綜合來源的要求，例如可用性測試，或搜尋引擎傀儡時，所有遙測項目的內容屬性來追蹤。 根據預設，[指標總管](app-insights-metrics-explorer.md)不會顯示綜合遙測。 

    `<Filters>`設定識別要求的屬性。
* `UserAgentTelemetryInitializer`更新`UserAgent`屬性`User`的遙測的所有項目內容根據`User-Agent`HTTP 標頭的要求。
* `UserTelemetryInitializer`更新`Id`和`AcquisitionDate`的屬性`User`從擷取的值的所有遙測項目的內容`ai_user`cookie 產生的使用者的瀏覽器中執行的應用程式的深入見解 JavaScript 儀器程式碼。
* `WebTestTelemetryInitializer`設定使用者識別碼、 工作階段 id 和 HTTP 要求來自[可用性測試](app-insights-monitor-web-app-availability.md)綜合來源屬性。
`<Filters>`設定識別要求的屬性。

## <a name="telemetry-processors-aspnet"></a>遙測處理器 (ASP.NET)

遙測處理器，可以篩選並修改之前從 SDK 傳送到入口網站的每個遙測項目。

您可以[撰寫您自己的遙測處理器](app-insights-api-filtering-sampling.md#filtering)。


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>（從 2.0.0-beta3) 自動調整取樣遙測處理器

此選項預設為啟用。 如果您的應用程式傳送給許多遙測，這個處理器移除的某些部分。

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

參數提供演算法嘗試達成的目標。 每個執行個體 SDK 的運作方式獨立，如此數個機器叢集的伺服器時，會依此乘以實際的遙測音量。

[深入瞭解範例](app-insights-sampling.md)。



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>（從 2.0.0-beta1) 固定工資率取樣遙測處理器

此外，還有標準[取樣遙測處理器](app-insights-api-filtering-sampling.md#sampling)（從 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>頻道參數 (Java)

這些參數會影響 Java SDK 應該儲存及清除收集遙測資料的方式。

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

可以儲存在 SDK 的記憶體內儲存的遙測項目數目。 達到這個數字時，清除遙測緩衝-也就是將遙測項目傳送到應用程式的深入見解伺服器。

-   最小值︰ 1
-   最大值︰ 1000年
-   預設︰ 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

決定頻率的資料儲存在記憶體內儲存，應清除 （已傳送的應用程式獲得深入見解）。

-   最小值︰ 1
-   最大值︰ 300
-   預設︰ 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

決定分配持續儲存在本機磁碟上的 mb 大小上限。 此儲存空間可用於無法傳送至應用程式的深入見解的端點保存遙測項目。 當已符合儲存空間大小時，將會捨棄新遙測項目。

-   最小值︰ 1
-   最大值︰ 100
-   預設︰ 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

這會決定您的資料會出現的應用程式的深入見解資源。 通常您建立不同的資源，以不同的金鑰，每個應用程式。

如果您想要設定鍵，以動態方式-例如如果您想要從您的應用程式到不同的資源-傳送結果您可以略過設定檔中，從鍵，並設定程式碼。

若要設定金鑰 TelemetryClient 的所有執行個體，包括標準遙測模組設定鍵 TelemetryConfiguration.Active 中。 在初始設定方法，例如 global.aspx.cs ASP.NET 服務中執行這項操作︰

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

如果您只是要傳送至其他資源的一組特定的事件，您可以為特定的 TelemetryClient 來設定機碼︰

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[深入瞭解 API][api]。

若要取得新按鍵，[建立新的應用程式的深入見解入口網站中的資源][new]。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

