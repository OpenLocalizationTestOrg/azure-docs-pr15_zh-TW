<properties
 pageTitle="IoT 中心診斷指標"
 description="Azure IoT 中心度量，讓使用者能夠存取其資源的整體健康情況的概觀"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-diagnostic-metrics"></a>診斷指標簡介

診斷指標提供您較佳的 Azure 資源的狀態相關的資料中 Azure 訂閱。 指標可讓您在評估整體的及連線至該裝置的服務健康狀況。 具使用者統計資料很重要，因為它們可以協助您查看原因 IoT 中心 」 和 「 說明根本原因問題，並不需要與 Azure 支援人員連絡。

您可以啟用診斷指標從 Azure 入口網站。

## <a name="how-to-enable-diagnostic-metrics"></a>如何啟用診斷指標

1. 建立 IoT 中心。 您可以找到相關指示，如何建立 IoT 中心中的[快速入門][lnk-get-started]指南。

2. 開啟您 IoT 中樞的刀。 從該位置，按一下 [**診斷**]。

    ![][1]

3. 設定您的診斷設定**的**狀態，然後選取 [儲存診斷資料 Azure 儲存體帳戶。 核取**指標**，然後再按一下 [**儲存**。 請注意，必須提前建立 Azure 儲存體帳戶，而且您會分別會儲存空間。 您也可以選擇將您的診斷資料傳送至事件集線器端點。

    ![][2]

4. 您已設定診斷後，返回**概觀**IoT 中心刀。 指標資訊會填入刀**監控**一節中。 按一下圖表以開啟您可以在此設定 IoT 中心檢視指標資訊的摘要及編輯圖表內顯示的指標的選取範圍中的 [指標] 窗格。 您也可以設定度量值為基礎的通知。

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>指標及使用方式

IoT 中心提供多個度量，讓您概略瞭解您中樞和裝置連線至該總數的狀況。 您可以結合來自多個度量來繪製大方向 IoT 中樞的狀態資訊。 下表描述每個 IoT 中心追蹤，指標，以及每個公制如何關聯 IoT 中樞的整體狀態。

| 公制 | 公制描述 | 使用什麼度量 |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | 在所有裝置上所傳送的郵件的計數 | 傳送郵件的概觀資料 |
| d2c.telemetry.ingress.success | 插入集線器計數的成功的所有郵件 | 成功的訊息輸入到中心的概觀 |
| c2d.commands.egress.complete.success | 在所有裝置上接收裝置所完成的所有命令訊息數目 | 與上別抱任何] 和 [拒絕標準，可讓整體 C2D 命令成功率的概觀 |
| c2d.commands.egress.abandon.success | 成功放棄接收裝置在所有裝置上的所有郵件的字數統計 | 如果郵件快速比預期更頻繁放棄醒目提示可能發生的問題 |
| c2d.commands.egress.reject.success | 所有成功被拒絕的訊息接收裝置在所有裝置上的字數統計 | 如果郵件快速比預期更頻繁拒絕醒目提示可能發生的問題 |
| devices.totalDevices | 平均值、 最小值] 和最大值的裝置註冊 IoT 中樞的數目 | 裝置註冊中樞的數目 |
| devices.connectedDevices.allProtocol | 平均值、 最小值] 和數字的同時連線裝置的最大值 | 裝置連接到集線器數目的概觀 |

## <a name="next-steps"></a>後續步驟

現在，您所見診斷指標的概觀，請遵循此連結以深入瞭解管理 Azure IoT 中心︰

- [監控的作業][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
