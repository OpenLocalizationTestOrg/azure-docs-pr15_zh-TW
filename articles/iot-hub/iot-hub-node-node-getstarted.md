<properties
    pageTitle="快速入門 Node.js azure IoT 中樞 |Microsoft Azure"
    description="Azure IoT 中心與 Node.js 快速開始教學課程。 使用 Azure IoT 中樞和 Node.js Microsoft Azure IoT Sdk 實作網際網路的項目解決方案。"
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>快速入門 Azure IoT 中樞的 Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

在本教學課程結尾，您可以有三個 Node.js 主控台應用程式︰

* **CreateDeviceIdentity.js**，建立裝置身分識別與相關聯的安全性鍵將模擬的裝置連線。
* **ReadDeviceToCloudMessages.js**，其中顯示由您模擬的裝置傳送遙測。
* **SimulatedDevice.js**，先前建立的裝置身分識別與連線到您 IoT 集線器和遙測傳送郵件給的每個第二個使用 AMQP 通訊協定。

> [AZURE.NOTE] 文章[IoT 中心 Sdk] [lnk-hub-sdks]提供各種 Sdk，您可以用來建立兩個裝置與您的方案後端上執行應用程式的相關資訊。

若要完成此教學課程中，您需要下列項目︰

+ Node.js 版本 0.10.x 或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

現在您已經建立 IoT 中心。 您有 IoT 中心主機名稱以及 IoT 中心連接字串，您必須完成本教學課程的其餘部分。

## <a name="create-a-device-identity"></a>建立裝置的身分識別

在此區段中，您可以建立的 Node.js 主控台應用程式中的身分識別您 IoT 中心建立裝置身分識別。 裝置無法連線到 IoT 集線器，除非裝置身分識別登錄中有一個項目。 請參閱[IoT 中心開發人員指南]**裝置身分識別登錄**章節[lnk-devguide-identity]如需詳細資訊。 當您執行此主控台應用程式時，則會產生的唯一裝置識別碼和您的裝置可用來找出本身裝置至雲端郵件傳送到 IoT 中樞的金鑰。

1. 建立新的空白資料夾稱為**createdeviceidentity**。 在 [ **createdeviceidentity** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**createdeviceidentity**資料夾中，執行下列命令以安裝**azure iothub**服務 SDK 套件︰

    ```
    npm install azure-iothub --save
    ```

3. 使用文字編輯器， **CreateDeviceIdentity.js**檔案的資料夾中建立**createdeviceidentity** 。

4. 新增下列`require` **CreateDeviceIdentity.js**檔案的開頭的陳述式︰

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. 將下列程式碼新增至**CreateDeviceIdentity.js**檔案，並將版面配置區的值更換 IoT 中樞您在前一節中建立的連線字串︰ 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 新增下列程式碼中的裝置身分識別您 IoT 中心建立裝置定義。 將此程式碼會建立裝置，如果在登錄中不存在的裝置識別碼，否則會傳回現有的裝置的索引鍵︰

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. 儲存並關閉**CreateDeviceIdentity.js**檔案。

8. 若要執行**createdeviceidentity**應用程式，執行下列命令在命令提示字元中 createdeviceidentity 資料夾︰

    ```
    node CreateDeviceIdentity.js 
    ```

9. 請記下的**裝置識別碼**與**裝置金鑰**。 您需要這些值稍後當您建立的連線到裝置 IoT 中樞的應用程式。

> [AZURE.NOTE] IoT 中心身分識別登錄機碼只會儲存裝置啟用安全存取中樞的身分識別。 它會儲存裝置識別碼與索引鍵作為安全性憑證和啟用或停用旗標，您可以使用來停用 [在個別的裝置存取。 如果您的應用程式需要將其他裝置的特定中繼資料儲存，它應該使用特定應用程式存放區。 請參閱[IoT 中心開發人員指南][lnk-devguide-identity]如需詳細資訊。

## <a name="receive-device-to-cloud-messages"></a>接收裝置至雲端的郵件

在此區段中，您可以建立裝置至雲端郵件讀取 IoT 中樞的 Node.js 主控台應用程式。 IoT 中心公開[事件集線器][lnk-event-hubs-overview]-相容的端點，讓您讀取裝置至雲端的郵件。 若要簡潔，本教學課程中建立基本的讀者不適合高處理量部署。 [處理裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程教您如何小數位數的裝置至雲端訊息。 [開始使用事件集線器][lnk-eventhubs-tutorial]教學課程提供進一步資訊如何處理郵件從事件集線器與適用於 IoT 中心事件中心相容結束點。

> [AZURE.NOTE] 事件中心相容的端點讀取裝置至雲端的郵件一律會使用 AMQP 通訊協定。

1. 建立新的空白資料夾稱為**readdevicetocloudmessages**。 在 [ **readdevicetocloudmessages** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元在**readdevicetocloudmessages**資料夾中，執行下列命令以安裝**azure-事件-集線器**套件︰

    ```
    npm install azure-event-hubs --save
    ```

3. 使用文字編輯器， **ReadDeviceToCloudMessages.js**檔案的資料夾中建立**readdevicetocloudmessages** 。

4. 新增下列`require` **ReadDeviceToCloudMessages.js**檔案的開頭的陳述式︰

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. 新增下列變數宣告，並將版面配置區的值更換 IoT 中心的連線字串︰

    ```
    var connectionString = '{iothub connection string}';
    ```

6. 新增列印輸出到主控台下列兩個功能︰

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. 新增下列**EventHubClient**、 開啟連線到您 IoT] 中心上，建立及每個資料分割收件者。 建立收件者，讓收件者僅限讀取接收者開始執行之後，IoT 集線器傳送郵件時，此應用程式會使用篩選。 這個篩選器中很有用測試環境，讓您看到只是目前設定的郵件。 在生產環境中，您的程式碼應該確認它處理的所有郵件-瞭解[如何處理 IoT 中心裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程，如需詳細資訊︰

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. 儲存並關閉**ReadDeviceToCloudMessages.js**檔案。

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您可以建立模擬會裝置至雲端郵件傳送給 IoT 中樞的裝置在 Node.js 主控台應用程式。

1. 建立新的空白資料夾稱為**simulateddevice**。 在 [ **simulateddevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元在**simulateddevice**資料夾中，執行下列命令以安裝**azure iot 裝置**裝置 SDK 套件和**azure iot-裝置 amqp**套件︰

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. 使用文字編輯器， **simulateddevice**資料夾中建立新的**SimulatedDevice.js**檔案。

4. 新增下列`require` **SimulatedDevice.js**檔案的開頭的陳述式︰

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. 新增**連接字串**變數，並使用它來建立裝置用戶端。 取代**{youriothostname}** IoT 中樞的名稱，您建立*建立 IoT 中心*一節。 **{Yourdevicekey}**取代您在*建立裝置身分識別*] 區段中所產生的裝置關鍵值︰

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. 新增下列函數顯示的應用程式的輸出︰

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 建立回撥，並使用新的郵件傳送給您 IoT 中心每秒的**setInterval**函數︰

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. 開啟您 IoT 中樞的連線，並開始傳送的郵件︰

    ```
    client.open(connectCallback);
    ```

9. 儲存並關閉**SimulatedDevice.js**檔案。

> [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。


## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元**readdevicetocloudmessages**資料夾中，執行下列命令以開始監視 IoT 中心︰

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Node.js IoT 中心服務用戶端應用程式監控裝置至雲端的郵件][7]

2. 在命令提示字元**simulateddevice**資料夾中，執行下列命令以開始遙測資料傳送到您 IoT 中心︰

    ```
    node SimulatedDevice.js
    ```

    ![Node.js IoT 中心裝置用戶端應用程式傳送裝置至雲端的郵件][8]

3. [Azure 入口網站]中的 [**使用狀況**] 磚[lnk-portal]的中樞的郵件數︰

    ![Azure 入口網站使用方式] 方塊顯示數字寄給 IoT 中心][43]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您 Azure 入口網站中，在設定新的 IoT 中心，然後建立中樞的身分識別登錄中的 [裝置身分識別。 您可以使用此裝置身分識別來啟用裝置至雲端郵件傳送到中樞模擬的裝置應用程式。 您也會建立顯示中樞收到的郵件應用程式。 

若要繼續快速入門 IoT 中心，並瀏覽其他 IoT 案例，請參閱︰

- [連接您的裝置][lnk-connect-device]
- [開始使用裝置管理][lnk-device-management]
- [快速入門 IoT 閘道器 SDK][lnk-gateway-SDK]

若要瞭解如何將延伸 IoT 方案和小數位數的裝置至雲端訊息，請參閱[處理裝置至雲端訊息][lnk-process-d2c-tutorial]教學課程。

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
