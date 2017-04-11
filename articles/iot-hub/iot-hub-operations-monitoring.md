<properties
 pageTitle="監控 IoT 中心作業"
 description="概略瞭解 Azure IoT 中心作業監視，讓您可以監視 IoT 中心即時作業的狀態"
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

# <a name="introduction-to-operations-monitoring"></a>監控作業簡介

監控 IoT 中心作業可讓您可以監視 IoT 中心即時作業的狀態。 IoT 中心追蹤事件跨多個類別的作業，並且您可以選擇從一或多個類別的事件傳送至處理您 IoT 中樞的端點。 您可以監視錯誤的資料，或設定更複雜的處理根據資料的模式。

IoT 中心監視五個類別的事件︰

- 裝置身分識別作業
- 裝置遙測
- 雲端-裝置命令
- 連線
- 上傳的檔案

## <a name="how-to-enable-operations-monitoring"></a>如何啟用監控的作業

1. 建立 IoT 中心。 您可以找到相關指示，如何建立 IoT 中心中的[快速入門][lnk-get-started]指南。

2. 開啟您 IoT 中樞的刀。 從該位置，按一下 [**監視作業**]。

    ![][1]

3. 選取您希望您監控，然後再按一下 [**儲存**的監控類別。 事件可供讀取**監控設定**] 中列出的事件中心相容端點。 IoT 中心端點稱為`messages/operationsmonitoringevents`。

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>事件類別及使用方式

每個作業監視類別追蹤不同類型的互動 IoT] 中心內，與每個監控類別有結構描述定義該類別的事件結構的方式。

### <a name="device-identity-operations"></a>裝置身分識別作業

裝置身分識別作業類別追蹤當您嘗試建立、 更新或刪除 IoT 中樞的身分識別登錄中的項目時，發生錯誤。 追蹤此類別是很適合用於佈建案例。

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>裝置遙測

裝置遙測類別追蹤發生 IoT 中心及遙測管線相關的錯誤。 此類別包括發生錯誤時遙測事件 （例如節流） 傳送及接收遙測事件 （例如未經授權的閱讀程式）。 請注意此類別不能掌握本身的裝置上執行的程式碼所造成的錯誤。

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>雲端-裝置命令

雲端-裝置命令] 類別中追蹤發生 IoT 中心及裝置命令管線相關的錯誤。 此類別包括發生錯誤時傳送 （例如未經授權的寄件者） 的命令、 接收命令 （例如傳遞計數超過上限），及接收] 命令的意見反應 （例如意見反應過期）。 此類別不會攔截從裝置的正確處理命令，如果命令已成功傳遞的錯誤。

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>連線

[連線] 類別追蹤的裝置連線或中斷 IoT 中心時，發生錯誤。 追蹤此類別是連線的適用於識別未經授權的連線以及追蹤的裝置在區域中的 [不佳中斷連線時。

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>上傳的檔案

檔案上傳類別追蹤發生 IoT 中心及檔案上傳功能相關的錯誤。 此類別包括 SA uri （例如何時到期之前裝置通知完成上傳的中樞） 發生的錯誤，報告的裝置，而且當檔案的上傳失敗找不到的儲存空間中 IoT 中心通知訊息建立期間。 請注意此類別不能掌握直接發生錯誤時使用的裝置已上傳檔案儲存空間。

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>後續步驟

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md