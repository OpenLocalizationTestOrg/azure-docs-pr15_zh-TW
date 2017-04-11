<properties
    pageTitle="雲端-裝置以傳送訊息 IoT 中心 |Microsoft Azure"
    description="請遵循此教學課程以瞭解如何傳送 java 使用 Azure IoT 中樞的雲端-裝置訊息。"
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>教學課程︰ 如何傳送 IoT 中心與 Node.js 雲端-裝置訊息

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>簡介

Azure IoT 中心是完全受管理的服務，協助啟用可靠和安全數百萬 IoT 裝置之間的雙向通訊和應用程式回結束。 [快速入門 IoT 中心]教學課程示範如何建立 IoT 中心與佈建裝置身分識別，程式碼模擬的裝置傳送裝置至雲端訊息。

[快速入門 IoT 中心]根據本教學課程。 您將會如何顯示以︰

- 從應用程式雲端後端，傳送雲端-裝置訊息到單一的裝置，透過 IoT 中心。
- 收到的雲端-裝置在裝置上的郵件。
- 從您的應用程式雲端後端，要求傳送通知 （*意見反應*） 從 IoT] 中心傳送至裝置的郵件。

您可以找到雲端至裝置的郵件] 的詳細資訊， [IoT 中心開發人員指南]中[IoT Hub Developer Guide - C2D]。

在本教學課程結尾，您必須執行兩個 Node.js 主控台應用程式︰

* **SimulatedDevice**，修改[IoT 中心快速入門]]，連線到您 IoT 中心及接收雲端至裝置的郵件中建立的應用程式版本。
* **SendCloudToDeviceMessage**，透過 IoT 集線器模擬的裝置傳送雲端-裝置訊息，並再收到其傳送通知。

> [AZURE.NOTE] IoT] 中心內有許多裝置平台和語言 （包括 C、 Java 和 Javascript），透過 Azure IoT 裝置 Sdk 的 SDK 支援。 如需有關如何連接您的裝置，此教學課程的程式碼，並通常 Azure IoT 中樞的逐步指示，請參閱[Azure IoT 開發人員中心]。

若要完成此教學課程中，您需要下列項目︰

+ Node.js 版本 0.10.x 或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

## <a name="receive-messages-on-the-simulated-device"></a>在 [模擬的裝置上接收郵件

在此區段中，您可以修改模擬的裝置應用程式中接收來自 IoT 中心雲端-裝置訊息的 [[快速入門 IoT 中心]所建立。

1. 使用文字編輯器，開啟 SimulatedDevice.js 檔案。

2. 修改**connectCallback**函數，來處理從 IoT] 中心傳送的郵件。 在此範例中，裝置永遠叫用它已經處理訊息時通知 IoT 中心**完成**的函數。 新版本的**connectCallback**函數看起來像這樣︰

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] 如果您使用 HTTP，而非 MQTT 或 AMQP 傳輸，從 IoT 中心不常 （每個 25 分鐘） 的郵件會檢查**DeviceClient**執行個體。 更多關於 MQTT、 AMQP 和 HTTP 支援和 IoT 中心節流之間的差異的詳細資訊，請參閱[IoT 中心開發人員指南][IoT Hub Developer Guide - C2D]。

## <a name="send-a-cloud-to-device-message"></a>傳送雲端-裝置郵件

在此區段中，您可以建立會雲端-裝置訊息傳送給模擬的裝置應用程式的 Node.js 主控台應用程式。 您需要裝置您[開始使用 IoT 中心]教學課程中新增裝置識別碼。 您也 IoT 中心，您可以[Azure 入口網站]中找到需要的連接字串。

1. 建立一個空的資料夾，稱為**sendcloudtodevicemessage**。 在 [ **sendcloudtodevicemessage** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**sendcloudtodevicemessage**資料夾中，執行下列命令以安裝**azure iothub**套件︰

    ```
    npm install azure-iothub --save
    ```

3. 使用文字編輯器， **sendcloudtodevicemessage**資料夾中建立新的**SendCloudToDeviceMessage.js**檔案。

4. 新增下列`require` **SendCloudToDeviceMessage.js**檔案的開頭的陳述式︰

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. **SendCloudToDeviceMessage.js**檔案中加入下列程式碼。 取代 IoT 中樞[快速入門 IoT 中心]教學課程中所建立的連線字串中的連接字串版面配置區的值。 取代您[開始使用 IoT 中心]教學課程中新增裝置的裝置識別碼目標裝置版面配置區︰

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 新增下列函數列印主控台作業結果︰

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 新增下列函數列印主控台傳送意見反應郵件︰

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 新增下列程式碼，將訊息傳送給您的裝置和裝置確認雲端-裝置訊息時，處理意見反應 」 訊息︰

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. 儲存並關閉**SendCloudToDeviceMessage.js**檔案。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元**simulateddevice**資料夾中，執行下列命令遙測傳送到 IoT 集線器以及接聽雲端-裝置訊息︰

    ```
    node SimulatedDevice.js 
    ```

    ![執行模擬的裝置應用程式][img-simulated-device]

2. 在命令提示字元**sendcloudtodevicemessage**資料夾中，執行下列命令以傳送雲端-裝置郵件，並等待認可意見反應︰

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![執行應用程式傳送 c2d] 命令][img-send-command]

    > [AZURE.NOTE] 為了簡單起見本教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您學到如何傳送和接收雲端-裝置訊息。 

若要完成使用 IoT 中樞的端對端解決方案的範例，請參閱[Azure IoT 套件]。

若要進一步瞭解開發 IoT 中樞的方案，請參閱[IoT 中心開發人員指南]。

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[快速入門 IoT 中心]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[IoT 中心開發人員指南]: iot-hub-devguide.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[暫時錯誤處理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 入口網站]: https://portal.azure.com
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/