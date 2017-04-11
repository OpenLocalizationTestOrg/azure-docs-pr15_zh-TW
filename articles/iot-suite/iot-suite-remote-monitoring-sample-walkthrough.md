<properties
 pageTitle="遠端監控預先設定的解決方案逐步解說 |Microsoft Azure"
 description="Azure IoT 描述預先設定的解決方案遠端監控和其架構。"
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
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>遠端監控預先設定的解決方案逐步解說

## <a name="introduction"></a>簡介

監控[預先設定的方案]IoT 套件遠端[lnk-preconfigured-solutions]的端對端實作監視執行遠端位置中的多部電腦的解決方案。 解決方案結合提供的一般商務案例實作 Azure 服務，以及您可以將其作為起點自己實作。 您可以[自訂][lnk-customize]解決方案以符合您自己的特定的業務需求。

本文會逐步引導您進行，讓您瞭解其運作方式遠端監視解決方案的關鍵元素的部分。 此知識庫可協助您︰

- 疑難排解問題的解決辦法。
- 規劃如何自訂解決方案以符合您自己的特定需求。 
- 設計您自己的 IoT 方案使用 Azure 服務。

## <a name="logical-architecture"></a>邏輯架構

下圖會列出預先設定的解決方案的邏輯元件︰

![邏輯架構](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>模擬的裝置

預先設定的解決方案，請在模擬的裝置代表冷卻裝置 （例如建置冷氣或設備 air 處理單位）。 當您部署預先設定的方案時，您也會自動佈建[Azure WebJob]中執行的四個模擬的裝置[lnk-webjobs]。 模擬的裝置，方便您瀏覽而不需要使用部署任何實體裝置解決方案的行為。 若要部署實際實體裝置，請參閱[連線至遠端裝置監視預先設定的解決方案]的[lnk-connect-rm]教學課程。

每個模擬的裝置可以傳送到 IoT 中樞的下列訊息類型︰

| 訊息  | 描述 |
|----------|-------------|
| 啟動  | 裝置啟動時，傳送**裝置資訊**訊息，其中包含的後端本身的相關資訊。 此資料，包括裝置識別碼、 裝置中繼資料的命令清單裝置支援，與目前設定的裝置。 |
| 目前狀態 | 在裝置會定期傳送報告裝置是否可偵測感應器的目前狀態的**目前狀態**訊息。 |
| 遙測 | 在裝置定期傳送**遙測**訊息，報告的溫度和濕度從裝置的模擬感應器收集模擬的值。 |


模擬的裝置**裝置資訊**在郵件中傳送下列裝置屬性︰

| 屬性               |  用途 |
|------------------------|--------- |
| 裝置識別碼              | 提供或指定方案中建立裝置時的識別碼。 |
| 製造商           | 裝置製造商 |
| 模型數字           | 裝置的模型數目 |
| 序列值          | 裝置的序列值 |
| 韌體               | 在裝置上的韌體目前版本 |
| 平台               | 裝置的平台架構 |
| 處理器              | 執行裝置的處理器 |
| 已安裝的 RAM          | 裝置上安裝的 RAM |
| 中心啟用狀態      | 裝置的 IoT 中心狀態屬性 |
| 建立的時間           | 裝置已在方案中建立的時間 |
| 更新的時間           | 上次更新屬性的裝置 |
| 緯度               | 緯度裝置位置 |
| 經緯度              | 裝置經度位置 |

模擬器設這些屬性中的範例值的模擬裝置。  模擬器初始化模擬的裝置，每次裝置張貼至 IoT 中樞的預先定義的中繼資料。 請注意此覆寫任何裝置入口網站中所做的中繼資料更新的方式。


模擬的裝置可以處理寄件者透過 IoT 中樞的解決方案儀表板的下列命令︰

| ] 命令                | 描述                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | _偵測 （ping)_傳送到裝置，以檢查作用   |
| StartTelemetry         | 開始傳送遙測的裝置                 |
| StopTelemetry          | 停止傳送遙測裝置             |
| ChangeSetPointTemp     | 變更設定點值的隨機產生的資料 |
| DiagnosticTelemetry    | 若要傳送的其他遙測值 (externalTemp) 裝置模擬器觸發程序 |
| ChangeDeviceState      | 變更裝置的擴充的狀態屬性，並將裝置資訊訊息傳送從裝置 |

透過 IoT 集線器提供的解決方案的後端裝置命令通知。

## <a name="iot-hub"></a>IoT 中心

[IoT 中心][ lnk-iothub] ingests 從裝置傳送到雲端的資料，並可 Azure 資料流分析 (ASA) 工作。 IoT 中心也會傳送到您的裝置命令代表裝置入口網站。 每個資料流 ASA 工作使用另一 IoT 中心消費者群組讀取郵件的資料流從您的裝置。

## <a name="azure-stream-analytics"></a>Azure 資料流狀況分析

在遠端監控方案[Azure 資料流分析][ lnk-asa] (ASA) 分派接收到的其他處理或儲存空間的後端元件 IoT 中樞的裝置郵件。 不同的 ASA 工作執行訊息的內容為基礎的特定功能。

**工作 1︰ 裝置資訊**篩選裝置資訊郵件內送郵件資料流，並將其傳送到事件中心端點。 在啟動和回應**SendDeviceInfo**命令，裝置便會傳送郵件裝置的資訊。 這項工作會使用下列查詢定義，來識別**裝置資訊**的郵件︰

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

這項工作將輸出傳送到事件中心以進一步處理。

**工作 2︰ 規則**評估內送溫度和濕度遙測值針對每個裝置臨界值。 在可用的解決辦法儀表板中的規則編輯器中設定臨界值。 在 [**參考資料**為讀取串流分析 blob 的時間戳記會儲存每個裝置/值組。 工作會比較裝置閾值針對任何非空白值。 它超過 ' > 」 條件，工作輸出**警示**事件，代表臨界值超出，提供裝置、 值和時間戳記的值。 這項工作會使用下列查詢定義，來識別應該觸發的遙測郵件︰

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

工作會將輸出傳送到 [事件] 中心內，以進一步處理，並將每個通知的詳細資料至 blob 儲存體儲存解決方案儀表板可以讀取通知的資訊。

**工作 3︰ 遙測**內送的裝置遙測資料流兩種方式上的運作方式。 第一個會從裝置中所有的遙測郵件傳送給長期儲存空間的常設 blob 儲存體。 第二個經過五分鐘滑動視窗計算平均值、 最小值與最大濕度值，並將此資料傳送至 blob 儲存體。 解決方案儀表板從 blob 儲存體填入圖表讀取遙測資料。 這項工作會使用下列查詢︰

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>事件集線器

**裝置資訊**與**規則**ASA 工作輸出可靠轉寄至執行中 WebJob**事件處理器**事件集線器其資料。

## <a name="azure-storage"></a>Azure 儲存體

解決方案使用 Azure blob 儲存體保留所有原始及摘要遙測資料的方案中的裝置。 儀表板從 blob 儲存體填入圖表讀取遙測資料。 若要顯示通知，儀表板從讀取資料 blob 儲存體以記錄時遙測值超出的設定的臨界值。 方案也會使用 blob 儲存體來記錄您在儀表板中設定的臨界值。

## <a name="webjobs"></a>WebJobs

除了裝載裝置模擬器，方案中的 WebJobs 也會裝載 Azure WebJob 中執行，該郵件的控點裝置資訊和命令回應**事件處理器**。 它會使用︰

- 以目前的裝置資訊更新裝置登錄機碼 （儲存在 DocumentDB 資料庫） 的裝置資訊訊息。
- 更新 （儲存在 DocumentDB 資料庫） 的裝置指令歷程記錄] 命令回覆郵件。

## <a name="documentdb"></a>DocumentDB

方案使用 DocumentDB 資料庫來存放裝置連線至的方案的相關資訊。 這項資訊包含裝置中繼資料] 及 [傳送至 [裝置]，從儀表板] 命令的歷程記錄。

## <a name="web-apps"></a>Web 應用程式

### <a name="remote-monitoring-dashboard"></a>遠端監控儀表板
此頁面，web 應用程式中的使用中 javascript 控制項 (請參閱[中視覺效果 repo](https://www.github.com/Microsoft/PowerBI-visuals)) 以視覺化方式呈現遙測資料從裝置。 方案使用 ASA 遙測工作撰寫遙測資料以 blob 儲存體。


### <a name="device-administration-portal"></a>裝置管理入口網站

此 web 應用程式可讓您︰

- 佈建新的裝置。 這個動作會設定的唯一裝置識別碼，並產生驗證金鑰。 它 IoT 中心身分識別登錄機碼和解決方案專用 DocumentDB 資料庫中將裝置的相關資訊。
- 管理裝置的內容。 這個動作會包含檢視現有的內容，並以新屬性更新。
- 傳送至裝置的命令。
- 檢視裝置的相關命令歷程記錄。
- 啟用和停用的裝置。

## <a name="next-steps"></a>後續步驟

下列 TechNet 部落格文章提供有關遠端監視預先設定的解決方案的更多詳細資料︰

- [IoT 套件-底下-遠端監控](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT 套件-遠端監控-新增即時和模擬裝置](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

您可以繼續閱讀下列文章開始使用 IoT 套件︰

- [將您的裝置連線到遠端監視預先設定的解決方案][lnk-connect-rm]
- [Azureiotsuite.com 網站上的權限][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md