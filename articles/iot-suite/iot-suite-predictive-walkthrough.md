<properties
 pageTitle="預測進行的維修作業逐步解說 |Microsoft Azure"
 description="Azure IoT 預測維護的逐步解說預先設定的解決方案。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>預先設定的預測維護解決方案逐步解說

## <a name="introduction"></a>簡介

IoT 套件預先設定的預測維護解決方案是預測點，可能會發生失敗時該商務案例的端對端解決方案。 您可以主動使用此預先設定的解決方案，例如最佳化進行的維修作業的活動。 解決方案結合主要 Azure IoT 套件服務，包括[Azure 電腦學習][lnk_machine_learning]工作區。 此工作區包含實驗，根據預測剩餘實用生活 (RUL) 的飛機引擎公用範例資料集。 解決方案完全實作 IoT 商務案例，為您計劃和實作解決方案符合特定的業務需求的起點。

## <a name="logical-architecture"></a>邏輯架構

下圖會列出預先設定的解決方案的邏輯元件︰

![][img-architecture]

藍色的項目為您佈建預先設定的解決方案時，您選取的位置佈建的 Azure 服務。 您可以提供東亞美國、 北歐洲或東亞區域中預先設定的解決方案。

在區域內您佈建預先設定的解決方案，無法使用某些資源。 在圖表中的橘色項目代表佈建在最接近可用區域 （南部美國中部、 歐洲西部或東南部亞洲） 指定所選的區域中的 Azure 服務。

綠色的項目是一種模擬的裝置，代表飛機引擎。 您可以進一步瞭解這些模擬的裝置下, 一節。

灰色的項目表示實作*裝置管理*功能的元件。 預先設定的預測維護解決方案的目前版本不會提供這些資源。 若要進一步瞭解裝置管理，請參閱[遠端監視預先設定的解決方案][lnk-remote-monitoring]。

## <a name="simulated-devices"></a>模擬的裝置

在預先設定的解決方案中，以模擬的裝置代表飛機引擎。 對應到單一飛機的兩個引擎是提供給解決方案。 每個引擎會發出四種類型的遙測︰ 感應器 9、 感應器 11、 感應器 14 和感應器 15 提供電腦學習模型，來計算剩餘實用生活 (RUL) 引擎所需的資料。 每個模擬的裝置會將下列遙測訊息傳送至 IoT 中心︰

*循環計數*。 循環圖表示已完成的飛行的 2-10 小時的遙測資料擷取航班的每個半小時的時間長度。

*遙測*。 有四個感應器代表引擎屬性。 感應器一般標記為感應器 9、 感應器 11、 感應器 14 及感應器 15。 這些 4 感應器代表遙測不足，無法從電腦學習模型取得 RUL 有用的結果。 此模型會建立包含實數引擎感應器資料的公用資料集。 如需有關如何從原始資料集建立模型的詳細資訊，請參閱[Cortana 智慧庫預測進行的維修作業範本][lnk-cortana-analytics]。

模擬的裝置可以處理寄件者 IoT 中樞的下列命令︰

| ] 命令 | 描述 |
|---------|-------------|
| StartTelemetry | 控制模擬的狀態。<br/>開始傳送遙測的裝置     |
| StopTelemetry  | 控制模擬的狀態。<br/>停止傳送遙測的裝置 |

IoT 中心提供裝置命令通知。

## <a name="azure-stream-analytics-job"></a>Azure 資料流分析工作

**工作︰ 遙測**傳入使用兩個陳述式的裝置遙測資料流上的運作方式。 第一個選取所有遙測從裝置，並傳送此資料至 blob 從其的視覺化的 web 應用程式中的儲存空間。 第二個陳述式經過兩分鐘滑動視窗計算平均感應器值，並將此資料到 [事件] 中心傳送到 [**事件處理器**。

## <a name="event-processor"></a>事件處理器

**事件處理器**所需完成的循環圖平均感應器值。 它在通過 api 公開電腦學習這些值訓練模型，以計算引擎 RUL。

## <a name="azure-machine-learning"></a>Azure 機器學習

如需有關如何從原始資料集建立模型的詳細資訊，請參閱[Cortana 智慧庫預測進行的維修作業範本][lnk-cortana-analytics]。

## <a name="lets-start-walking"></a>現在就讓我們開始說明

本節會逐步引導您進行解決方案的元件、 說明預定的使用大小寫，並提供範例。

### <a name="predictive-maintenance-dashboard"></a>預測進行的維修作業儀表板

此頁面，web 應用程式中的使用中 JavaScript 控制項 (請參閱[中視覺效果存放庫][lnk-powerbi]) 若要以視覺化方式呈現︰

- Blob 儲存體中的資料流分析工作輸出資料。
- 每個飛機引擎 RUL 與循環圖計數。

### <a name="observing-the-behavior-of-the-cloud-solution"></a>觀察雲端解決方案的行為

在 Azure 入口網站，瀏覽至您選擇要檢視您能夠的資源的方案名稱的資源群組。

![][img-resource-group]

當您佈建預先設定的方案時，您會收到包含電腦學習工作區的連結的電子郵件。 您也可以從[azureiotsuite.com]瀏覽至電腦學習區[lnk-azureiotsuite]能夠解決方案時，它是在**準備好**的狀態] 頁面。

![][img-machine-learning]

在方案入口網站，您可以看到範例佈建四個模擬裝置來代表兩個飛機飛機，各有四個感應器每兩個引擎。 當您第一次瀏覽至方案入口網站時，就會停止模擬。

![][img-simulation-stopped]

按一下 [**開始模擬**開始模擬其中您看到 RUL、 循環、 感應器歷程記錄]，RUL 歷程記錄填入儀表板]。

![][img-simulation-running]

當 RUL 小於 160 （選擇的示範用任意閥值），解決方案入口網站會顯示警告符號 RUL 顯示] 旁的，並醒目提示黃色飛機引擎。 請注意，RUL 值需要一般向下趨勢整體，但似乎經常犯彈入向上和向下。 從不同的循環圖長度和模型精確度結果這種行為。

![][img-simulation-warning]

完整的模擬需要大約 35 分鐘內完成 148 循環。 第一次在約 5 分鐘符合 160 RUL 閥值時，這兩個引擎，大約 8 分鐘點擊臨界值。

模擬 148 週期執行完整的資料集，然後 settles RUL 和循環圖的最後一個值。

您可以停止任一點模擬，但按一下**啟動模擬**會重新顯示資料集的模擬開始。

## <a name="next-steps"></a>後續步驟

現在您已執行預先設定的預測維護方案，您可能會想要加以修改，請參閱[自訂預先設定的解決方案的指導方針][lnk-customize]。

[IoT 套件-底下顯示進階設定-預測維護](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx)TechNet 部落格文章提供關於預先設定的預測維護解決方案的其他詳細資料。

您也可以瀏覽的一些其他功能和預先設定的 IoT 套件解決方案的功能︰

- [常見問題集 IoT 套件][lnk-faq]
- [從頭 IoT 安全性][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
