<properties 
    pageTitle="設定通知中的應用程式的深入見解 |Microsoft Azure" 
    description="取得有關變得很慢回應時間、 例外]，與其他效能或 web 應用程式中的使用方式變更通知。" 
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
    ms.date="10/14/2016" 
    ms.author="awills"/>
 
# <a name="set-alerts-in-application-insights"></a>在 [應用程式的深入見解設定通知

[Visual Studio 應用程式獲得深入見解][start]可讓您變更 web 應用程式中的效能或使用指標。 

應用程式的深入見解監視即時應用程式在[各種不同的平台]上[platforms]可協助您診斷效能問題，並了解使用模式。

有三種通知︰

* **公制通知**會告訴您任何公制時交叉閾值段-例如回應時間、 例外狀況的計算、 CPU 使用率或檢視的網頁。 
* [**Web 測試**][availability]判斷當您的網站是在網際網路上無法使用或回應速度緩慢。 [深入瞭解][availability]。
* [**主動式診斷**](app-insights-proactive-diagnostics.md)會自動設定時通知您有關異常效能模式。

我們著重於公制本文中的通知。

## <a name="set-a-metric-alert"></a>設定公制的通知

開啟通知規則刀，，然後使用 [新增] 按鈕。 

![在通知規則刀中，選擇 [新增提醒]。 為資源量值，提醒中，為提供的名稱，然後選擇 [度量單位，來設定您的應用程式。](./media/app-insights-alerts/01-set-metric.png)

* 設定其他屬性之前的資源。 **選擇 」 （元件） 」 資源**如果您想要效能或使用指標上設定提醒。
* 提醒您提供的名稱必須是唯一資源群組 （而不只是您應用程式）。
* 請小心請注意，系統會要求您輸入的臨界值的單位。
* 如果您核取方塊 」 電子郵件的擁有者...」，將會透過電子郵件收到通知，所有人都能存取此資源群組。 若要展開的人員此設定，請將其新增至[資源群組或訂閱](app-insights-resources-roles-access-control.md)（而非資源）。
* 如果您指定 「 其他電子郵件]，將會收到通知，這些個人或群組 （您核取 」 電子郵件的擁有者...」 的方塊）。 
* 如果您已設定回應通知的 web 應用程式，請設定[webhook 地址](../monitoring-and-diagnostics/insights-webhooks-alerts.md)。 提醒啟動 （也就，觸發） 和時，已解決，則將會呼叫。 （但請注意，在簡報、 查詢參數會不傳送 webhook 屬性）。
* 您可以停用或啟用通知︰ 請參閱刀頂端的按鈕。

*我無法看到 [新增提醒]。* 

- 您目前使用的組織帳戶？ 如果您有擁有者或參與者存取此應用程式資源，您可以設定通知。 看看存取控制刀。 [深入瞭解存取控制][roles]。

> [AZURE.NOTE] 通知刀中，您會看到有已經通知設定︰[積極診斷](app-insights-proactive-failure-diagnostics.md)。 這是自動通知監視特定公制，邀請失敗報酬率。 除非您決定要停用主動式警示，您不需要邀請失敗率上設定您自己的提醒。 

## <a name="see-your-alerts"></a>查看您的通知

您可以取得電子郵件通知變更狀態時停用和使用中文之間。 

目前狀態的每個通知會顯示在通知規則刀。

有通知] 中的最近活動摘要下拉式清單︰

![通知] 下拉式清單](./media/app-insights-alerts/010-alert-drop.png)

狀態變更的歷程記錄是活動記錄中︰

![在概觀刀中，按一下 [設定]，[稽核記錄](./media/app-insights-alerts/09-alerts.png)



## <a name="how-alerts-work"></a>通知的運作方式

* 通知有三種狀態: 「 未啟用 」、 「 啟動 」，及 [解決]。 最後一個評估時，則為 true，啟動所代表的意義您指定的條件。

* 狀態變更通知時，會產生通知。 （如果您建立提醒時，通知條件已，則為 true，您可能不會收到通知條件為 false，直到。）

* 如果您已核取 [電子郵件] 方塊中，或提供電子郵件地址，每個通知會產生電子郵件。 您也可以查看 [通知] 下拉式清單。

* 提醒會評估公制送達時，但不是否則每次。

* 評估彙總上述期間的度量，然後將它與比較臨界值來判斷新狀態。

* 期間，您可以選擇指定的時間間隔的指標彙總。 不會影響提醒會評估頻率︰ 而定的指標的頻率。

* 如果沒有資料的一些時間送達特定度量單位，間距會有不同的效果，通知評估及公制檔案總管] 中的圖表。 在 [公制總管] 中，如果資料不會看到超過圖表的範例間隔，圖表會顯示 0 的值。 但根據相同公制通知不會重新評估，和通知的狀態會保持不變。 

    當資料最後送達時，圖表跳轉可回到非零值。 提醒會根據您指定期間內使用的資料。 僅有一個期間內使用新的資料點時，將彙總依據只在資料點。

* 即使您設定長的時間，提醒可以常見問題閃爍通知和健全狀態之間。 如果游標周圍臨界值的公制的值，也可能會發生。 臨界值中沒有任何 hysteresis: 切換到提醒會在切換到健全值相同。



## <a name="what-are-good-alerts-to-set"></a>什麼是很好的通知，以設定？

應用程式而定。 開始時，最好不要設定太多的指標。 花點時間執行您的應用程式時，若要瞭解如何將其運作正常，查看您公制的圖表。 這可協助您尋找改善效能的方法。 然後設定通知，以告知您當指標移標準區域外的詳細資訊。 

常用的通知包括︰

* [在瀏覽器指標][client]，尤其是在瀏覽器**頁面載入時間**，適合 web 應用程式。 如果您有許多指令碼，您需要注意**瀏覽器的例外狀況**。 若要取得這些指標與通知，您必須設定[網頁監控][client]。
* Web 應用程式的伺服器端**伺服器回應時間**。 設定提醒，以及留意上若要查看是否它會隨著比例高要求率此公制︰ 的可能表示您的應用程式不足的資源。 
* **伺服器例外狀況**-若要查看，就必須執行一些[額外設定](app-insights-asp-net-exceptions.md)。

自動別忘了該[積極失敗工資率診斷](app-insights-proactive-failure-diagnostics.md)監視的應用程式回應使用失敗碼的要求工資率。 

## <a name="automation"></a>自動化

* [使用 PowerShell 來自動化通知設定](app-insights-powershell-alerts.md)
* [使用 webhooks 自動化回應提醒](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="see-also"></a>另請參閱

* [可用性 web 測試](app-insights-monitor-web-app-availability.md)
* [自動化通知設定](app-insights-powershell-alerts.md)
* [積極診斷程式](app-insights-proactive-diagnostics.md) 



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 