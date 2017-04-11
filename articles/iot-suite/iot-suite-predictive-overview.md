<properties
 pageTitle="預測進行的維修作業預先設定的解決方案 |Microsoft Azure"
 description="Azure IoT 預先設定的預測維護解決方案的描述。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>預先設定的預測維護解決方案概觀

預先設定的*預測進行的維修作業*解決方案是其中一個[預先設定的解決方案][lnk_preconfigured_solutions]發行[Microsoft Azure IoT 套件]的一部分[lnk_iot_suite]。 此方案與使用[Azure 電腦學習]建立預測模型整合即時裝置遙測集合[lnk_machine_learning]。


Azure IoT 套件，企業可以快速且輕鬆地連線至和監視資產，與分析即時資料。 預先設定的預測維護解決方案的資料，並使用豐富型的儀表板與視覺效果企業提供新智慧可驅動效率和增強營收資料流。

## <a name="the-scenario"></a>此案例

Fabrikam 是地區的航空公司的重點在於很棒的客戶體驗，在競爭力價格。 班機延誤的其中一個原因是進行的維修作業問題，而特別挑戰飛機引擎進行的維修作業。 必須計成本，避免引擎航班失敗，以便 Fabrikam 定期檢查其引擎，並遵循維護程式。 不過，飛機引擎不一定是哪一種相同。 引擎會執行一些不需要進行的維修作業。 更重要的會發生問題，其中可以地面飛機，直到執行進行的維修作業。 這會使高延遲，尤其是如果飛機位於右上技術人員或零件無法使用的位置。

有哪些監視航班引擎條件感應器控制的 Fabrikam 的飛機引擎。 Fabrikam 使用 Azure IoT 套件收集飛行期間收集感應器資料。 累積的年數引擎操作，資料失敗，Fabrikam 的資料科學家有建立模型的預測剩餘實用生活 (RUL) 飛機引擎的方式。 他們擁有識別是四個與潛在客戶最終失敗引擎穿著引擎感應器中的資料之間的相互關聯。 Fabrikam 繼續同時執行一般的操作，以確保安全，它現在可以使用模型來計算每個引擎 RUL 後使用遙測每個航班引擎從收集飛行期間。 Fabrikam 現在可以預測未來點失敗，以及維護計劃，並修復事先。

> [AZURE.NOTE] 解決方案模型使用實際引擎穿著資料。

預測點，需要進行的維修作業時，Fabrikam 可以最佳化以減少成本其作業。 進行的維修作業協調使用規劃與飛機停在特定位置，並確保登出服務，不會導致排程中斷飛機充分的時間，同時進行的維修作業的排程器。 Fabrikam 可以依此; 排程的技術人員確保飛機是沒有等待時間有效率地提供服務。 庫存控制項管理員會收到維護計劃，讓他們可以最佳化其排序的程序，並備用組件庫存。 這可讓 Fabrikam 最小化飛機地上時間並減少作業的成本，同時確保安全的乘客和人員。

若要瞭解如何[Azure IoT 套件][lnk_iot_suite]提供功能客戶必須發現可能會預測進行的維修作業，請檢閱此[infographic][lnk_infographic]。

## <a name="how-the-predictive-maintenance-solution-is-built"></a>建置預測進行的維修作業解決方案的方式

解決方案會運用現有 Azure 電腦學習模型可用另存為範本，以顯示從裝置遙測收集透過 IoT 套件服務使用這些功能。 Microsoft 具有內建的[迴歸模型][lnk_regression_model]飛機引擎的發佈完成的範本，資料和<sup>\[1\]</sup>，及如何使用此模型的逐步指引。

Azure IoT 預先設定的預測維護方案使用此範本中; 從所建立的迴歸模型部署至 Azure 訂閱，並透過自動產生的 API 公開。 方案包含代表 4 （的總 100) 測試資料的子集引擎及 4 (21 總) 提供正確的結果，從訓練模型感應器資料流。

*\[1\] A Saxena 和 K Goebel (2008)。「 Turbofan 引擎降低模擬資料集 」、 NASA Ames Prognostics 資料存放庫 (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/)、 NASA Ames 參考資料中心 Moffett 欄位 CA*

## <a name="next-steps"></a>後續步驟

若要進一步瞭解如何 Azure IoT 讓預測維護狀況，閱讀[擷取網際網路的項目值][lnk_capture_value]。

需要[逐步解說][lnk-predictive-walkthrough]預測所進行的維修作業的預先設定的解決方案。

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

您也可以瀏覽的一些其他功能和預先設定的 IoT 套件解決方案的功能︰

- [常見問題集 IoT 套件][lnk-faq]
- [從頭 IoT 安全性][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
