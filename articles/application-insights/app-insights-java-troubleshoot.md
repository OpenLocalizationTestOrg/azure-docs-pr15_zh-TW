<properties 
    pageTitle="疑難排解 Java web 專案中的應用程式的深入見解" 
    description="疑難排解指南-監視的應用程式的深入見解的即時 Java 應用程式。" 
    services="application-insights" 
    documentationCenter="java"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/01/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>疑難排解與應用程式獲得深入見解 java 問與 A

問題或問題[Java Visual Studio 的應用程式獲得深入見解][java]嗎？ 以下是一些秘訣。


## <a name="build-errors"></a>建立錯誤

*在蝕，新增應用程式的深入見解 SDK 透過 Maven 或 Gradle 時，我可以取得建立或檢查值驗證錯誤。*

* 如果相依性<version>項目會使用萬用字元的模式 (例如 (Maven)`<version>[1.0,)</version>`或 (Gradle) `version:'1.0.+'`)，請嘗試改為指定特定的版本，例如`1.0.2`。 請參閱[版本資訊](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)的最新版本。

## <a name="no-data"></a>沒有資料 

*我成功新增應用程式的深入見解，並執行我的應用程式，但我從未入口網站中的資料。*

* 等一下，然後按一下 [重新整理]。 圖表會定期更新本身，但是您也可以手動更新。 重新整理的間隔取決於圖表的時間範圍。
* 檢查您有 ApplicationInsights.xml 檔案 （在專案中的 [資源] 資料夾） 中定義儀器索引鍵
* 確認有無`<DisableTelemetry>true</DisableTelemetry>`xml 檔案中的節點。
* 在您的防火牆，您可能必須開啟 TCP 連接埠 80 和 443 dc.services.visualstudio.com 至外寄流量的。 請參閱[防火牆例外狀況的完整清單](app-insights-ip-addresses.md)
* Microsoft Azure 中啟動區時，查看服務狀態地圖。 如果有一些通知的指示，請等候他們回到 [確定]，然後關閉並重新開啟您的應用程式的深入見解應用程式刀。
* 藉由新增記錄至 IDE 主控台] 視窗中，開啟`<SDKLogger />`根節點中 ApplicationInsights.xml 檔案 （在 [資源] 資料夾在專案中），並核取的項目開頭的 [錯誤] 下的項目。
* 請確定的正確的 ApplicationInsights.xml 檔案已經順利載入 Java sdk，您可以藉由查看主控台的輸出訊息的 「 設定檔成功找到 」 陳述式。
* 如果找不到設定檔，核取位置設定檔時進行搜尋，請參閱輸出郵件，並確認 ApplicationInsights.xml 位於其中一個搜尋地點。 為法則，您可以將設定檔應用程式的深入見解 SDK 罐和附近。 例如︰ 在 Tomcat，這表示 WEB/INF 文件庫資料夾。



#### <a name="i-used-to-see-data-but-it-has-stopped"></a>我使用以查看資料，但它已停止

* 核取[狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
* 已按下您的資料點的每月配額嗎？ 開啟 [設定/配額和價格，找出。 如果是這樣，您可以升級您的方案，或支付額外的容量。 請參閱[價格配置](https://azure.microsoft.com/pricing/details/application-insights/)。

#### <a name="i-dont-see-all-the-data-im-expecting"></a>我看不到我我預期的所有資料

* 開啟 [配額和[取樣](app-insights-sampling.md)作業是否價格刀並核取。 （100%傳輸表示取樣不作業）。接受只有遙測送達您應用程式的部分，可以設定的應用程式的深入見解服務。 這可協助您將維持內的遙測您每月配額。 

## <a name="no-usage-data"></a>沒有使用資料

*我看到邀請和回應的時間，但沒有網頁] 檢視中，瀏覽器中，資料或使用者資料]。*

您已成功設定您的應用程式從伺服器傳送遙測。 現在您的下一步可[設定您的網頁，若要傳送的網頁瀏覽器遙測][usage]。

或者，如果您的用戶端是在[手機或其他裝置]的應用程式[platforms]，您可以從這裡傳送遙測。 

使用相同的儀器鍵可設定用戶端與伺服器的遙測。 資料會出現在相同的應用程式的深入見解資源，然後您就可以從用戶端與伺服器的事件互相關聯。



## <a name="disabling-telemetry"></a>停用遙測

*如何停用遙測集合？*

在 [程式碼︰

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**或** 

更新 ApplicationInsights.xml （在專案中的 [資源] 資料夾）。 新增根節點下的下列動作︰

    <DisableTelemetry>true</DisableTelemetry>

使用 XML 方法時，您必須變更的值時，請重新啟動應用程式。

## <a name="changing-the-target"></a>變更目標

*如何變更我的專案會傳送至資料的 Azure 資源？*

* [取得新的資源的儀器索引鍵。][java]
* 如果您新增應用程式的深入見解至專案使用 Azure 工具組蝕，以滑鼠右鍵按一下網頁專案，請選取**Azure****設定應用程式深入資訊**，然後變更索引鍵。
* 否則，更新 ApplicationInsights.xml 在專案中的 [資源] 資料夾中的索引鍵。


## <a name="the-azure-start-screen"></a>Azure 開始畫面

*我正在尋找在[Azure 入口網站](https://portal.azure.com)。沒有地圖告訴我項目我的應用程式？*

否，它會顯示 Azure 伺服器的健康情況世界各地的。

*從 Azure 開始版 （主畫面） 如何找到資料我的應用程式？*

假設您[設定您的應用程式的應用程式的深入見解][java]，請按一下 [瀏覽、 選取應用程式的深入見解然後選取您建立的應用程式的應用程式資源。 若要取得那里更快速未來，您可以固定您的應用程式開始版面。

## <a name="intranet-servers"></a>內部網路伺服器

*可以在我的網路上監控伺服器？*

是的提供您的伺服器可以傳送遙測透過公用網際網路應用程式的深入見解入口網站。 

在您的防火牆，您可能必須開啟 TCP 連接埠 80 和 443 dc.services.visualstudio.com 和 f5.services.visualstudio.com 的外寄流量的。

## <a name="data-retention"></a>資料保留資料 

*資料多久保留在入口網站？資料是否安全？*

請參閱[資料保留資料和隱私權][data]。

## <a name="next-steps"></a>後續步驟

*設定應用程式的深入見解的我 Java 伺服器應用程式。我可以做什麼？*

* [監視網頁的可用性][availability]
* [監視網頁使用情況][usage]
* [追蹤的使用和診斷在您裝置的應用程式中的問題][platforms]
* [撰寫程式碼來追蹤您的應用程式的使用方式][track]
* [擷取診斷記錄][javalogs]


## <a name="get-help"></a>取得說明

* [堆疊溢位](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 