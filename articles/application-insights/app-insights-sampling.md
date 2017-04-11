<properties 
    pageTitle="在 [應用程式的深入見解的遙測取樣 |Microsoft Azure" 
    description="如何讓遙測掌控的音量。" 
    services="application-insights" 
    documentationCenter="windows"
    authors="vgorbenko" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="awills"/>

#  <a name="sampling-in-application-insights"></a>在 [應用程式的深入見解的範例

*應用程式的深入見解位於預覽。*


取樣是在[Visual Studio 應用程式獲得深入見解](app-insights-overview.md)的功能是減少遙測流量，儲存空間，同時保留正確統計資料分析的應用程式的建議的方式。 篩選選取，好讓您可以瀏覽項目當您進行診斷調查之間相關的項目。
公制計數會顯示在入口網站時, 它們會重新正規化考慮取樣，最小化的統計資料的任何效果。

取樣減少流量，可協助您將維持內每月資料配額，並可協助您避免節流。

## <a name="in-brief"></a>在簡報︰

* 取樣會保留在*n*記錄 1，並捨棄其餘部分。 例如，它可能會保留 1 中 5 事件，20%取樣工資率。 
* 如果您的應用程式將遙測，許多傳送 ASP.NET 網頁伺服器應用程式中，此範例就會自動。
* 您也可以設定取樣以手動方式，請在 [價格] 頁面上，在入口網站或在.config 檔案，ASP.NET SDK，也會減少網路流量。
* 如果您自訂的事件記錄，您想要確定的事件的一組被保留或捨棄放在一起，請確定有 OperationId 值相同。
* 取樣因數*n*報告] 屬性中的每一筆記錄`itemCount`，在搜尋中會出現在 [易記名稱 「 要求計數 」 或 「 事件計數 」。 當作業，不是取樣`itemCount==1`。
* 如果您撰寫查詢分析，您應該[考慮的範例](app-insights-analytics-tour.md#counting-sampled-data)。 特別是，而非只計算記錄，您應該使用`summarize sum(itemCount)`。


## <a name="types-of-sampling"></a>類型的範例


有三種替代取樣方法︰

* **調整取樣**自動調整寄件者 ASP.NET 應用程式中 SDK 遙測的音量。 從 SDK v 2.0.0-beta3 預設值。 目前可供 ASP.NET 伺服器端遙測只。 
* **固定工資率取樣**減少遙測傳送從兩個 ASP.NET 伺服器和使用者的瀏覽器的音量。 您設定的工資率。 會在用戶端與伺服器同步處理其取樣讓該，在搜尋時，您可以瀏覽相關的網頁檢視之間的要求。
* **Ingestion 取樣**減少應用程式的深入見解服務，在您設定的工資保留遙測的音量。 它不會減少遙測流量，但是可以協助您保護您的每月配額。 

如果彈性或固定的工資率取樣作業，請 Ingestion 取樣會停用。

## <a name="ingestion-sampling"></a>Ingestion 範例

這種取樣的運作方式從您的網頁伺服器、 瀏覽器和裝置遙測到達應用程式的深入見解服務端點的位置的點。 雖然它不會減少遙測流量傳送您的應用程式，它會減少處理，並保留 （與費用） 的應用程式的深入見解。

如果您的應用程式通常會介紹每月配額，而且您沒有使用其中一個 SDK 為基礎的類型範例的選項，請使用此類型的範例。 

設定的配額和價格刀取樣率︰

![從應用程式概觀刀中，按一下 [設定]、 [配額、 範例，然後選取取樣率，，然後按一下 [更新。](./media/app-insights-sampling/04.png)

其他類型的範例，例如演算法會保留相關的遙測項目。 例如，當您在搜尋中的遙測檢查，您可以尋找特定的例外狀況相關的要求。 公制計算，例如要求率，並正確保留例外狀況工資率。

不提供任何應用程式的深入見解功能，例如[連續匯出](app-insights-export-telemetry.md)捨棄範例的資料點。

Ingestion 取樣 SDK 自動調整] 或 [固定速率取樣作業時運作。 如果 SDK 取樣比率小於 100%，則會忽略您設定的 ingestion 取樣率。

> [AZURE.WARNING] 圖磚上所顯示的值表示 ingestion 範例為您設定的值。 SDK 取樣作業時，它不代表實際的取樣工資率。


## <a name="adaptive-sampling-at-your-web-server"></a>在您的網頁伺服器的調整範例

調整取樣應用程式的深入見解 SDK ASP.NET v 2.0.0-beta3 或更新版本，且使用預設為啟用。 


調整取樣會影響遙測傳送您的網頁伺服器應用程式至應用程式的深入見解服務的音量。 音量會自動調整，以保留中指定的最大流量率。

它不會在操作低大量遙測，因此中偵錯的應用程式，或不會影響使用率太低的網站。

若要達到目標音量，產生遙測部分捨棄。 但其他類型的範例，例如演算法會保留相關的遙測項目。 例如，當您在搜尋中的遙測檢查，您可以尋找特定的例外狀況相關的要求。 

公制計算，例如要求工資率和例外狀況工資率會調整，以賠償取樣速率，這樣就會顯示公制總管大約正確的值。

**更新您的專案 NuGet**套件，為新的應用程式的深入見解的*預先發行*版本︰ 以滑鼠右鍵按一下 [專案總管] 中的，選擇管理 NuGet 封包，核取**包含搶鮮版**和 Microsoft.ApplicationInsights.Web 搜尋。 

在[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)，您可以調整中的多個參數`AdaptiveSamplingTelemetryProcessor`節點。 顯示的數字是預設值︰

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    目標頻率，以調整演算法針對**每個伺服器主機上**的。 如果您的 web 應用程式執行許多主機上時，減少此值，以保留在您的應用程式的深入見解入口網站流量的目標率內。

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 

    用以遙測目前率會重新評估間隔。 評估為移動平均執行。 您可能會想要縮短此時間間隔，如果您遙測負責突然高速量。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    在取樣百分比值的變更，如何推出之後我們可以降低取樣百分比來擷取較少的資料。

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`

    在取樣百分比值的變更，如何推出之後我們可以增加取樣百分比來擷取更多資料。

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    取樣百分比不盡相同，什麼是可設定的最小值。

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    取樣百分比不盡相同，什麼是可設定的最大值。

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 

    移動平均的計算，粗細分派的最新的值。 使用等於或小於 1] 的值。 較小的值進行演算法較少反應突然要的變更。

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    只要啟動應用程式時所指定的值。 當您正在除錯時不減少此。 

### <a name="alternative-configure-adaptive-sampling-in-code"></a>替代方案︰ 設定程式碼中的 [調整的範例

而非調整.config 檔案中的範例，您可以使用程式碼。 這個選項可讓您指定的取樣率是重新評估叫用的回撥函數。 您可以使用，例如，若要找出哪些取樣率正在使用。

移除`AdaptiveSamplingTelemetryProcessor`.config 檔案中的節點。



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

（[深入了解遙測處理器](app-insights-api-filtering-sampling.md#filtering)）。


<a name="other-web-pages"></a>
## <a name="sampling-for-web-pages-with-javascript"></a>使用 JavaScript 網頁的範例

您可以設定固定工資率取樣從任何伺服器的網頁。 

當您[設定的應用程式的深入見解的網頁](app-insights-javascript.md)，修改您從應用程式的深入見解入口網站取得的程式碼片段。 （ASP.NET 應用程式]，在程式碼片段通常會 _Layout.cshtml。） 插入線路，如`samplingPercentage: 10,`儀器按鍵之前︰

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 
    
    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

取樣百分比，選擇 [是靠近 100/N 其中 N 是整數的百分比]。  目前取樣不支援其他值。

如果您也會啟用固定工資率取樣伺服器，請在用戶端和伺服器會同步，讓該，在搜尋時，您可以瀏覽相關的網頁檢視之間的要求。


## <a name="fixed-rate-sampling-for-aspnet-web-sites"></a>固定工資率的 ASP.NET 網站的範例

固定的工資率取樣減少傳送從網頁伺服器及網頁瀏覽器的流量。 與調整範例，它會減少您決定固定速率遙測。 它也會同步處理用戶端和伺服器取樣以便相關項目會保留-，例如，如果您查看網頁] 檢視中搜尋時，您才能找到其相關的要求。

取樣演算法會保留相關項目。 針對每個 HTTP 要求事件、，以及其相關的事件會捨棄或傳輸。 

指標總管] 中，在費率例如要求與例外狀況的計算被乘以因數以修正取樣工資率，使其大約正確。

1. **更新您的專案 NuGet 套件**，為新的應用程式的深入見解的*預先發行*版本。 以滑鼠右鍵按一下 [專案總管] 中的，選擇 [管理 NuGet 封包，核取 [**包含搶鮮版**和 Microsoft.ApplicationInsights.Web 搜尋。 

2. **停用自動調整範例**︰ 在[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)，移除或註解`AdaptiveSamplingTelemetryProcessor`節點。

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **啟用固定工資率取樣模組。** 新增[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)此程式碼片段︰

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE] 取樣百分比，選擇 [是靠近 100/N 其中 N 是整數的百分比]。  目前取樣不支援其他值。



### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>替代方案︰ 啟用固定工資率伺服器程式碼中的範例


而非設定取樣參數.config 檔案中，您可以使用程式碼。 

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

（[深入了解遙測處理器](app-insights-api-filtering-sampling.md#filtering)）。


## <a name="when-to-use-sampling"></a>何時使用取樣？

如果您是使用 ASP.NET SDK 版本 2.0.0-beta3 自動啟用自動調整取樣或更新版本。 無論您使用何種 SDK 版本，您可以使用 ingestion 取樣 （在我們的伺服器）。

您不需要取樣大部分的小型及中型大小應用程式。 在所有使用者活動收集資料將會取得最有用的診斷資訊和最準確的統計資料。 

 
取樣的主要優點是︰

* 應用程式的深入見解服務降 （「 節流 」） 資料點當您的應用程式傳送遙測更高的率以短時間間隔。 
* 若要保留的資料點，讓您價格層的[配額](app-insights-pricing.md)。 
* 若要降低遙測各種網路流量。 

### <a name="which-type-of-sampling-should-i-use"></a>我應該使用哪種類型的範例？


**使用 ingestion 取樣如果︰**

* 您通常可幫助的遙測您每月配額。
* 您使用不支援取樣-例如 SDK、 Java SDK 或 ASP.NET 版本的版本早於 2。
* 您收到大量遙測從使用者的網頁瀏覽器。

**使用固定的工資率取樣如果︰**

* 您使用的應用程式的深入見解 SDK ASP.NET web 服務版本 2.0.0 或更新版本，和
* 您要同步處理取樣用戶端與伺服器之間，這麼一來，當您正在調查中[搜尋](app-insights-diagnostic-search.md)的事件，您可以瀏覽上用戶端和伺服器，例如 [頁面檢視和 http 要求的相關事件之間。
* 您確定適當取樣百分比的應用程式。 應該夠，以取得正確的指標，但工資率] 下方的超過您價格配額和節流限制。 


**使用自動調整範例︰**

否則，我們建議您調整的範例。 ASP.NET 伺服器 SDK，版本 2.0.0-beta3 預設為啟用或更新版本。 它不確定最小的利率，直到減少流量，讓它不會影響低使用網站。


## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>如何知道取樣是否運算中？

若要探索無論套用位置的實際取樣率、 使用[狀況分析查詢](app-insights-analytics.md)這類︰

    requests | where timestamp > ago(1d)
  	| summarize 100/avg(itemCount) by bin(timestamp, 1h) 
  	| render areachart 

在每個保留記錄中，`itemCount`指出其所代表的原始記錄數目等於 1 + 先前捨棄的記錄數目。 


## <a name="how-does-sampling-work"></a>取樣如何運作？

固定工資率] 和 [自動調整取樣是從開始建立編號 2.0.0 ASP.NET 版本 SDK 的功能。 Ingestion 範例是該應用程式的深入見解服務中的功能，而且如果 SDK 未執行取樣可作業。 

取樣演算法決定哪些遙測項目，若要刪除，並要保留 （無論是在 SDK 或應用程式的深入見解服務）。 取樣決策根據多個規則，以保留所有相關的資料點不變，維護且當中可靠即使使用精簡資料集的應用程式深入見解診斷體驗。 例如，如果失敗要求您的應用程式傳送給其他遙測項目 （例如例外狀況和追蹤此要求登入），取樣會不分割此邀請及其他遙測。 它會保留或一起其去掉。 如此一來，當您檢視應用程式的深入見解的邀請詳細資料，隨時都可以查看的要求，以及其相關聯的遙測項目。 

定義 「 使用者 」 的應用程式 (也就是最常見的 web 應用程式)，取樣決策為基礎的使用者識別碼，這表示的任何特定使用者的所有遙測會保留或捨棄雜湊。 沒有定義 （例如 web 服務） 的使用者的應用程式類型取樣決策為基礎的要求作業識別碼。 最後，都有設定 （適用於範例遙測項目從非同步執行緒沒有 http 內容的報告） 的使用者或作業識別碼遙測項目的範例只會擷取遙測項目，每個類型的百分比。 

當簡報遙測給您，應用程式的深入見解服務會一次的集合，用來修正遺漏的資料點的相同取樣百分比調整指標。 因此，在應用程式的深入見解中遙測時，使用者就會看見非常接近實數的統計正確近似值。

接近的精確度主要取決於已設定的取樣百分比。 此外，精確度增加處理太多，通常類似要求的多個使用者的應用程式。 另一方面，與重要的負載無法運作的應用程式，範例，不需要這些應用程式時不會導致資料遺失從節流保持的配額，通常可以傳送所有其遙測。 

請注意，應用程式的深入見解不範例指標和工作階段遙測類型，自針對這些類型，減少精確度可高度非預期。 

### <a name="adaptive-sampling"></a>調整的範例

調整取樣加入監控傳輸 sdk，您可以從目前率並調整取樣百分比，嘗試以求目標的最大速度的元件。 調整定期間隔，重新計算，並為基礎的外寄傳輸速度的移動平均。

## <a name="sampling-and-the-javascript-sdk"></a>取樣和 JavaScript SDK

用戶端 (JavaScript) SDK 參與固定工資率取樣與伺服器端 SDK 搭配使用。 副稽核的頁面只會傳送用戶端遙測相同的使用者伺服器端所做的決策 」 範例中 」。 此邏輯是設計用來在用戶端-和伺服器-端維護使用者工作階段的完整性。 如此一來，從 [應用程式的深入見解的任何特定遙測項目，您可以尋找這個使用者或工作階段的所有其他遙測項目。 

*我的用戶端與伺服器端遙測不顯示互相搭配的範例，為您說明上方。*

* 請確認您已啟用固定工資率取樣同時伺服器與用戶端。
* 請務必 SDK 版本 2.0 或上方。
* 檢查您的用戶端和伺服器設定的相同的取樣百分比。


## <a name="frequently-asked-questions"></a>常見問題集 

*為何無法取樣簡單 「 收集 X 的每個遙測類型百分比 」？*

 *  雖然此取樣方法會提供更高的精確地在公制近似值，它會中斷互相關聯每位使用者、 工作階段和邀請，相當重要診斷程式診斷資料。 因此，「 收集遙測之所有項目 X 百分比的應用程式使用者 」 或 「 收集的 X 的應用程式要求 %的所有遙測 」 更有效地取樣運作邏輯。 （例如背景非同步處理） 要求與不相關的遙測項目，改為 「 收集 X %的所有項目，為每個遙測類型 」 

*取樣百分比可以變更一段時間嗎？*

 * 是的調整取樣逐漸取樣百分比，根據目前的遙測音量。

 

*如果我使用固定的工資率取樣時，我怎麼知道哪些取樣百分比運作適合我的應用程式？*

* 其中一個方法起始調整範例，瞭解什麼對其 settles （請參閱上述問題），然後切換至 [固定工資率取樣使用該工資率。 

    否則，您必須猜測。 分析 AI 中的您目前遙測使用狀況、 觀察已發生任何節流和估計收集遙測的音量。 這些三個的輸入與您所選的價格層建議多少您可能會想要減少收集遙測的音量。 不過，在您的使用者數目增加或一些其他 shift 量的遙測可能會使您的估計值。

*如果設定取樣百分比太低，會發生什麼情況？*

* 極低取樣百分比 （over-aggressive 取樣） 減少近似值的精確度，當應用程式的深入見解嘗試賠償資料大量縮減資料的視覺效果。 此外，診斷體驗可能會產生負面影響，為不常故障或變得很慢要求的一些可能取樣出。

*如果設定取樣百分比得太高，會發生什麼情況？*

* 設定得太高取樣百分比 （不積極不夠） 會收集遙測大量不足減少。 您仍然遙測資料遺失相關節流，並使用應用程式的深入見解的成本可能會比您計劃，因為 overage 費用。

*何種平台上使用範例？*

* 如果 SDK 未執行取樣 ingestion 取樣會自動的上方特定區，任何遙測。 運作，例如，如果您的應用程式使用 Java 伺服器，或如果您使用的舊版 ASP.NET SDK。

* 如果您使用 ASP.NET SDK 版本 2.0.0 和上方 （裝載 Azure 中或您自己的伺服器上），您收到調整取樣根據預設，但上所述，您可以切換到 [固定工資率。 使用固定的工資率取樣瀏覽器 SDK 自動同步處理取樣相關的事件。 

*有某些少見一律想要查看的事件。我要如何取得這些過去取樣模組？*

 * 初始化 TelemetryClient 使用新的 TelemetryConfiguration （不是預設作用中的項目） 的個別執行個體。 使用的傳送少見事件。



## <a name="next-steps"></a>後續步驟

* [篩選](app-insights-api-filtering-sampling.md)可以提供更多嚴格控制您 SDK 的傳送。
