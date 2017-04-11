<properties 
    pageTitle="篩選及應用程式的深入見解 sdk 前置處理 |Microsoft Azure" 
    description="撰寫遙測處理器和遙測初始設定式 SDK 的篩選，或將內容加入資料遙測會傳送至應用程式的深入見解入口網站。" 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>篩選和前置處理應用程式的深入見解 sdk 遙測

*應用程式的深入見解位於預覽。*

您可以撰寫或設定自訂遙測如何擷取及處理才會傳送至應用程式的深入見解服務應用程式的深入見解 SDK 的外掛程式。 

目前這些功能可供 ASP.NET SDK。

* [取樣](app-insights-sampling.md)減少遙測的音量，而不會影響您的統計資料。 保持在一起相關資料點，好讓您可以瀏覽之間診斷的問題。 在入口網站，總數量的以修正取樣相乘。
* [遙測處理器篩選](#filtering)可讓您選取或修改遙測 sdk，再將其傳送至伺服器。 例如，您可能會減少遙測大量機器人排除要求。 但篩選是以其他基本的方法來降低比取樣流量。 讓您進一步控制什麼傳輸，但您必須注意到會影響您的統計資料-例如，如果您篩選出所有的成功要求。
* [遙測初始新增內容](#add-properties)至任何遙測傳送您的應用程式，包括遙測從標準模組。 例如，您可以在其中新增計算的值。或用來篩選資料，在入口網站的版本號碼。
* [SDK API](app-insights-api-custom-events-metrics.md)用來傳送自訂事件和指標。


開始之前︰

* 安裝[應用程式的深入見解 SDK ASP.NET v2](app-insights-asp-net.md)應用程式中。 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>篩選︰ ITelemetryProcessor

這項技巧可以讓您更直接控制什麼是包含或排除遙測資料流。 您可以使用它搭配範例，或個別。

若要篩選遙測，您可以撰寫遙測處理器，並登錄 SDK。 所有遙測都移到您的處理器，您可以選擇置放資料流，或新增內容。 這包含標準模組 HTTP 要求收集和相依性行程，例如從遙測及遙測撰寫您自己。 您，例如，篩選出遙測相關要求機器人或成功的相依性來電。 

> [AZURE.WARNING] 篩選寄件者 SDK 遙測使用處理器可以扭曲您在入口網站，請參閱統計資料，讓您難以遵循相關項目。
> 
> 請考慮改用[範例](app-insights-sampling.md)。

### <a name="create-a-telemetry-processor"></a>建立遙測處理器

1. 確認您專案中的應用程式的深入見解 SDK 2.0.0 版本或更新版本。 以滑鼠右鍵按一下您在 Visual Studio 方案總管中的專案，然後選擇 [管理 NuGet 套件。 在 NuGet 套件管理員] 中，核取 Microsoft.ApplicationInsights.Web。

1. 若要建立篩選，實作 ITelemetryProcessor。 這是另一個擴充功能，例如遙測模組、 遙測初始設定，與遙測頻道。 

    請注意遙測處理器建構處理鏈結。 當您產生遙測處理器時，您會傳遞給鏈中的下一個處理器的連結。 當程序方法傳遞遙測資料點時，它會其工作，並接著呼叫鏈中的 [下一步遙測處理器。

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. 插入 ApplicationInsights.config 中︰ 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

（這是您將初始化取樣篩選同一節）。

您可以從.config 檔案傳遞字串值，提供課堂中公用命名的屬性。 

> [AZURE.WARNING] 請注意類型名稱和程式碼中的類別和屬性名稱.config 檔案中的任何屬性名稱相符。 如果.config 檔案參考不存在於類型或屬性，SDK 火車可能無法傳送任何遙測。

 
**或者，**您可以初始化程式碼的篩選。 在適當的初始化類別-例如中 Global.asax.cs AppStart-中插入鏈中的處理器︰

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

建立此之後 TelemetryClients 會使用您的處理器。

### <a name="example-filters"></a>範例篩選

#### <a name="synthetic-requests"></a>綜合要求

篩選出 bot 和 web 測試。 雖然指標總管可讓您查看合成來源篩選的選項，此選項會減少篩選其在 SDK 流量。

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>驗證失敗

篩選出含 「 401 」 回應的要求。 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>篩選出快速遠端相依性通話

如果您只想診斷通話的速度很慢，篩選出快速項目。 

> [AZURE.NOTE] 這會扭曲您看到入口網站的統計資料。 相依性圖表看起來如下是否相依性通話所有失敗。

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>診斷相依性問題

[此部落格](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/)說明專案，自動傳送給相依性的 [一般 ping 診斷相依性問題。


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>新增內容︰ ITelemetryInitializer

使用遙測初始定義所有遙測; 與傳送的通用屬性並覆寫標準遙測模組的選取的行為。 

例如，網頁套件應用程式深入資訊收集遙測 HTTP 要求的相關。 根據預設，它會標示為失敗任何要求與回應碼 > = 400。 但如果您想要 400 視為成功，您可以提供設定成功屬性的遙測初始設定。

如果您提供的遙測初始設定，則稱為時的任何 Track*() 方法稱為。 這包含標準遙測模組所呼叫的方法。 根據慣例，這些模組沒有設定任何已設定的初始設定的屬性。 

**定義您的初始設定**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**載入您的初始設定**

在 [ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*或者，*您可以產生的程式碼，例如在 Global.aspx.cs 初始設定︰


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[查看更多本範例。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>JavaScript 遙測初始

*JavaScript*

您有從入口網站的初始化程式碼之後，立即插入遙測初始設定︰ 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

非自訂可用的內容上 telemetryItem 摘要，請參閱[資料模型](app-insights-export-data-model.md#lttelemetrytypegt)。

您可以新增任何數量的初始視。 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 和 ITelemetryInitializer

遙測處理器與遙測初始之間的差異為何？

* 有一些重疊在您可以進行的作業︰ 兩者都可以用來將內容新增至遙測。
* TelemetryInitializers 一律執行 TelemetryProcessors 之前。
* TelemetryProcessors 可讓您完全取代或放棄遙測項目。
* TelemetryProcessors 不處理效能計數器遙測。



## <a name="persistence-channel"></a>持續性頻道 

如果您的應用程式執行位置的網際網路連線不是使用或變得很慢，請考慮使用持續通道，而不預設記憶體內頻道。 

預設記憶體內頻道將會遺失任何關閉應用程式的時間有尚未傳送的遙測。 雖然您可以使用`Flush()`要嘗試傳送剩餘緩衝中任何資料，仍會遺失資料如果沒有網際網路連線，或如果應用程式之前關閉傳輸已完成。

相反地，持續頻道緩衝遙測在 [檔案]，然後再傳送以入口網站。 `Flush()`可確保資料會儲存在檔案中。 如果任何資料不會傳送關閉應用程式時，它會保持在檔案中。 應用程式重新啟動時，資料會傳送然後，如果沒有網際網路連線。 資料累積中的檔案，只要有必要，直到您也可以連線。 

### <a name="to-use-the-persistence-channel"></a>若要使用持續頻道

1. 匯入 NuGet 套件[Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3)。
2. 將此程式碼加入您的應用程式，適合初始化位置︰
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. 使用`telemetryClient.Flush()`之前您應用程式會關閉，以確保資料入口網站傳送或儲存至檔案。

    請注意，Flush() 同步持續頻道，但非同步其他頻道。

 
持續性頻道最適合裝置案例、 位置的應用程式所產生的事件的數字是較小，且連接通常不可靠。 此通道將事件寫入到磁碟可靠的儲存空間第一次，然後加以傳送。 

#### <a name="example"></a>範例

例如，假設您想要監視處理的例外狀況。 您訂閱`UnhandledException`事件。 您可以在 [回撥，包含靠在通話，請確定遙測會保存。
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

應用程式當關閉時，您會看到中的檔案`%LocalAppData%\Microsoft\ApplicationInsights\`，其中包含壓縮的事件。 
 
下次啟動應用程式中，頻道將選擇 [此檔案，並進行遙測應用程式獲得深入見解，如果可以。

#### <a name="test-example"></a>測試範例

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


持續性頻道的程式碼是[github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel)。 


## <a name="reference-docs"></a>參照文件

* [API 概觀](app-insights-api-custom-events-metrics.md)

* [ASP.NET 參照](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>SDK 程式碼

* [ASP.NET 核心 SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>後續步驟


* [搜尋事件和記錄檔][diagnostic]
* [範例](app-insights-sampling.md)
* [疑難排解][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
