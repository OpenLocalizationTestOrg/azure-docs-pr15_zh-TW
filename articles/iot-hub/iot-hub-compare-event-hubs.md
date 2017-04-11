<properties
 pageTitle="比較 Azure 事件集線器 Azure IoT 中樞 |Microsoft Azure"
 description="醒目提示功能差異及使用案例 Azure IoT 中心和 Azure 事件集線器服務的比較。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT 中心和 Azure 事件集線器比一比

IoT 中樞的主要的使用案例的是從裝置收集遙測。 因此， [Azure 事件集線器][]通常會比較 IoT 中心。 IoT] 中心內，例如事件集線器是處理可讓您低延遲與高可靠性事件與遙測輸入龐大的小數位數，在雲端服務的事件。

不過，服務有下表所述的許多差異。

| 區域 | IoT 中心 | 事件集線器 |
| ---- | ------- | ---------- |
| 通訊圖樣 | 啟用裝置至雲端和雲端-裝置訊息。 | 僅可讓事件輸入 （通常是裝置至雲端案例視為）。 |
| 裝置通訊協定支援 | 在 [WebSockets，並 HTTP 支援 MQTT、 AMQP AMQP。 此外，IoT 中心搭配[Azure IoT 通訊協定閘道器][lnk-azure-protocol-gateway]，可自訂的通訊協定的閘道器實作支援自訂通訊協定。 | 支援 AMQP，在 WebSockets，並 HTTP AMQP。 |
| 安全性 | 提供每個裝置身分識別與可撤銷之存取控制。 請參閱[IoT 中心開發人員指南的 [安全性] 區段]。 | 提供事件集線器全[共用的存取原則][Event Hubs - security]，與有限撤銷支援透過[發行者的原則][Event Hubs publisher policies]。 IoT 解決方案通常，才能實作自訂解決方案，可支援每個裝置的認證與反詐騙量值。 |
| 監控的作業 | 可讓 IoT 解決方案訂閱豐富的裝置身分識別管理及連線的事件，例如個別裝置驗證錯誤、 節流，以及不正確的格式例外狀況。 這些事件可讓您快速找出在個別的裝置層級的連線問題。 | 公開只彙總的指標。 |
| 縮放比例 | 已最佳化而適合支援數百萬的同時連線的裝置。 | 可支援更多有限的同時連線--最 5000 個 AMQP 連線，依照[Azure 服務匯流排配額][]。 另一方面，事件集線器可讓您指定的磁碟分割傳送每封郵件。 |
| 裝置 Sdk | 提供[裝置 Sdk] [Azure IoT Hub SDKs]大型各式各樣的平台和語言。 | 支援.NET，c。也會提供 AMQP 和 HTTP 傳送介面。 |
| 檔案上傳 | 可讓使用者上傳至雲端的檔案從裝置 IoT 解決方案。 包含工作流程整合和作業監視類別偵錯支援的檔案通知的端點。 | 使用的宣告核取樣式至手動要求從裝置的檔案，並提供交易的儲存空間索引鍵的裝置。 |

總之，不論是否僅使用案例裝置至雲端遙測輸入 IoT 中心提供的服務特別設計 IoT 裝置連線。 若要展開值建議，這些案例 IoT 特定功能會繼續。 事件集線器被針對在大量的縮放比例，同時間資料中心和內部資料中心案例的內容中的事件輸入。

您無法很使用 IoT 中心] 和 [事件集線器在相同的方案，IoT 中心處理裝置至雲端的通訊，及事件集線器到即時處理引擎處理更新階段事件輸入位置。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解規劃 IoT 中心部署，請參閱[縮放比例與 HA DR][lnk-scaling]。

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

[Azure 事件集線器]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT 中心開發人員指南的 [安全性] 區段]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure 服務匯流排配額]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
