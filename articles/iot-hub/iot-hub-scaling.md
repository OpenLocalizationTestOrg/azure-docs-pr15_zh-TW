<properties
 pageTitle="按比例縮放的 azure IoT 中心 |Microsoft Azure"
 description="說明如何不按比例縮放 Azure IoT 中心。"
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
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>縮放比例 IoT 中心

Azure IoT 中心可支援最百萬個同時連線的裝置。 如需詳細資訊，請參閱[IoT 中心價格][lnk-pricing]。 每個 IoT 中心單位可讓每日的郵件數。

若要正確調整您的方案，請考慮特定使用 IoT 中心。 特別是，請考慮下列類別的 [作業所需的最大使用量處理能力︰

* 裝置至雲端的郵件
* 雲端-裝置訊息
* 身分識別的登錄作業

除了此處理量資訊，請參閱[IoT 中心配額和節流][]和據以設計您的方案。

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>裝置至雲端和雲端-裝置郵件處理量

若要調整大小 IoT 中心解決方案的最佳方式是評估每單位為基礎的流量。

裝置至雲端的郵件，請遵循這些持續處理量指導方針。

| 層 | 持續處理量 | 持續的傳送工資率 |
| ---- | -------------------- | ------------------- |
| S1 | 最 1111 KB/分鐘每單位<br/>（1.5 GB/日/單位） | 278 郵件/分鐘每單位的平均值<br/>（400,000 郵件/每單位日） |
| S2 | 最多 16 MB/分鐘每單位<br/>（22.8 GB/日/單位） | 4167 郵件/分鐘每單位的平均值<br/>（6 百萬郵件/天每單位） |
| S3 | 最 814 MB/分鐘每單位<br/>（1144.4 GB/日/單位） | 208,333 郵件/分鐘每單位的平均值<br/>（300 百萬郵件/天每單位） |

## <a name="identity-registry-operation-throughput"></a>身分識別登錄作業處理量

為大多相關裝置佈建 IoT 中樞的身分識別登錄作業不應該要執行階段作業]。

特定的突發效能數字，請參閱[IoT 中心配額和節流][]。

## <a name="sharding"></a>Sharding

雖然單一 IoT 中樞可以調整的裝置，有時候您的方案會需要不保證單一 IoT 中樞的特定的效能特性。 在此情況下，建議您將多個 IoT 集線器分割您的裝置。 多個 IoT 集線器平滑流量高速量，並取得所需的處理量或作業所需的工資率。

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT 中心配額和節流]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
