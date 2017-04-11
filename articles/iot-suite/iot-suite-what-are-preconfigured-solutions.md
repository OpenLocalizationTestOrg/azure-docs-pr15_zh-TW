<properties
 pageTitle="Azure IoT 預先設定的解決方案 |Microsoft Azure"
 description="Azure IoT 描述預先設定解決方案與他們架構與其他資源連結。"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>預先設定的 Azure IoT 套件解決方案有哪些？

預先設定的 Azure IoT 套件解決方案是實作常見的 IoT 解決方案模式，您可以部署至 Azure 使用您的訂閱。 您可以使用的預先設定的解決方案︰

- 為您自己的 IoT 解決方案的起點。
- 若要深入了解 IoT 解決方案設計和開發中的常見模式。

每個預先設定的解決方案是產生遙測使用模擬的裝置的端對端，實作。

除了部署及執行解決方案 Azure 中，您可以下載的完整原始程式碼，然後自訂及延伸解決方案，以符合您特定 IoT 需求。

> [AZURE.NOTE] 若要將其中一個預先設定的解決方案部署，請造訪[Microsoft Azure IoT 套件][lnk-azureiotsuite]。 [開始使用的預先設定的 IoT 解決方案]的文件[lnk-getstarted-preconfigured]提供有關如何部署及執行方案的詳細資訊。

下表顯示如何解決方案對應到 IoT 的特定功能︰

| 解決方案 | 資料 Ingestion | 裝置身分識別 | [命令及控制項 | 規則和動作 | 預測狀況分析 |
|------------------------|-----|-----|-----|-----|-----|
| [遠端監控][lnk-getstarted-preconfigured] | [是] | [是] | [是] | [是] | -   |
| [預測進行的維修作業][lnk-predictive-maintenance] | [是] | [是] | [是] | [是] | [是] |

- *資料 ingestion*︰ 輸入的資料至雲端的小數位數。
- *裝置身分識別*︰ 管理唯一識別每個連接的裝置。
- *命令和控制*︰ 從雲端導致採取行動裝置，傳送至裝置的郵件。
- *規則和動作*︰ 方案後端使用規則在特定裝置至雲端的資料。
- *預測狀況分析*︰ 方案後端適用於分析裝置至雲端的資料，以預測時特定應該執行的動作的位置。 例如，分析飛機引擎遙測來決定何時到期引擎進行的維修作業。

## <a name="remote-monitoring-preconfigured-solution-overview"></a>遠端監控預先設定的解決方案概觀

我們已選擇討論本文遠端監控預先設定的方案，因為它說明了許多常見的設計元素的其他方案共用。

下圖說明遠端監視解決方案的關鍵元素。 以下各節提供這些元素的詳細資訊。

![遠端監控預先設定的解決方案架構][img-remote-monitoring-arch]

## <a name="devices"></a>裝置

當您部署遠端監視預先設定的解決方案時，四個模擬的裝置是預先能夠模擬冷卻裝置的方案中。 這些模擬的裝置有內建的溫度和濕度模型發出遙測。 這些模擬的裝置包含說明端對端的資料，透過方案，並提供方便的來源遙測和目標的命令，如果您是使用開始算起的解決方案，自訂實作後端開發人員。

當裝置第一次連線到 IoT 中心遠端監控預先設定的方案中時，傳送給 IoT 中樞的裝置資訊訊息列舉裝置可以回應的命令清單。 在遠端監控預先設定解決方案中，就的命令︰ 

- *偵測 （ping） 裝置*︰ 裝置回應通知包含此命令。 這是很適合用於檢查裝置仍作用中及聆聽。
- *開始遙測*︰ 指示開始傳送遙測裝置。
- *停止遙測*︰ 若要停止傳送遙測裝置的指示。
- *變更設定點溫度*︰ 控制項的裝置傳送的模擬的溫度遙測值。 這是很適合用於測試後端邏輯。
- *診斷遙測*︰ 控制如果裝置應該傳送遙測外部的溫度。
- *變更裝置狀態*。: 設定裝置報表裝置狀態中繼資料屬性。 這是很適合用於測試後端邏輯。

您可以新增更多模擬的裝置方案的發出相同遙測和回應的相同的命令。 

## <a name="iot-hub"></a>IoT 中心

在此預先設定的解決方案，IoT 中心執行個體會對應到*雲端閘道器*在一般[IoT 解決方案架構][lnk-what-is-azure-iot]。

IoT 中樞接收遙測在單一端點的裝置。 IoT 中心也會保留裝置特定的端點，每個裝置可擷取會傳送給該命令的位置。

IoT 中心可讓您可以透過閱讀端點服務端遙測收到的遙測。

## <a name="azure-stream-analytics"></a>Azure 資料流狀況分析

預先設定的方案使用三個[Azure 資料流分析][ lnk-asa] (ASA) 工作，以篩選遙測資料流從裝置︰


- *DeviceInfo 工作*-輸出路由裝置註冊特定的郵件，傳送到解決方案裝置登錄 （DocumentDB 資料庫） 的第一次連接裝置時，或**變更裝置狀態**] 命令來回應事件中心內的資料。 
- *遙測工作*-將所有原始遙測傳送至 Azure blob 儲存體冷儲存空間，並計算解決方案儀表板中顯示的遙測彙總。
- *規則工作*-篩選值的超過任何規則臨界值，並將輸出資料以事件中樞的遙測資料流。 當規則啟動時，[解決方案] 入口網站的儀表板檢視就會顯示此事件為警示記錄資料表中的新資料列，並觸發動作以定義解決方案入口網站中的 [規則及動作] 檢視上的設定。

在此預先設定的解決方案中，ASA 工作表單的組件的**IoT 方案後端**一般[IoT 解決方案架構]中[lnk-what-is-azure-iot]。

## <a name="event-processor"></a>事件處理器

在此預先設定的解決方案中，事件處理器表單部分**IoT 方案後端**一般[IoT 解決方案架構]中[lnk-what-is-azure-iot]。

**DeviceInfo**與**規則**ASA 工作傳送其輸出到傳送郵件給其他的後端服務事件。 方案使用[EventPocessorHost] [lnk-event-processor]執行個體，在[WebJob]執行[lnk-web-job]，以從這些事件集線器讀取的訊息。 **EventProcessorHost**更新裝置資料庫中的資料 DocumentDB，使用**DeviceInfo**資料，並使用**規則**資料叫用邏輯應用程式，並更新顯示解決方案入口網站中的通知]。

## <a name="device-identity-registry-and-documentdb"></a>裝置身分識別登錄和 DocumentDB

每個 IoT 中心包含[裝置身分識別登錄][lnk-identity-registry]儲存裝置鍵。 IoT 中樞使用這項資訊驗證裝置-必須註冊和有有效的金鑰，才能連線到中樞的裝置。

此方案儲存裝置，例如狀態、 其所支援的命令和其他中繼資料的其他資訊。 解決方案使用 DocumentDB 資料庫來儲存此方案特定裝置資料，然後從顯示與編輯此 DocumentDB 資料庫解決方案入口網站中擷取資料。

解決方案必須同時將裝置身分識別登錄機碼 DocumentDB 資料庫的內容與同步處理的資訊。 **EventProcessorHost**使用**DeviceInfo**串流分析工作中的資料管理同步處理。

## <a name="solution-portal"></a>解決辦法入口網站

![解決辦法儀表板][img-dashboard]

解決方案入口網站是網頁式的 UI 預先設定的解決方案部署至雲端。 其可讓您︰

- 在儀表板檢視遙測和警示歷程記錄。
- 佈建新裝置。
- 管理及監視裝置。
- 傳送到特定裝置的命令。
- 管理規則及動作。

在此預先設定的解決方案中，[解決方案] 入口網站表單的**IoT 方案後端**組**處理及商務連線**一般[IoT 解決方案架構]中[lnk-what-is-azure-iot]。

## <a name="next-steps"></a>後續步驟

如需有關 IoT 方案架構的詳細資訊，請參閱[Microsoft Azure IoT 服務︰ 參考架構][lnk-refarch]。

現在您已經知道哪些預先設定的解決方案是，您可以開始部署*遠端監控*預先設定的解決辦法︰[開始使用的預先設定的解決方案][lnk-getstarted-preconfigured]。

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md