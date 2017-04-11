<properties
 pageTitle="使用直接方法 |Microsoft Azure"
 description="本教學課程教您如何使用直接的方法"
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
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>教學課程︰ 使用直接的方法

## <a name="introduction"></a>簡介

Azure IoT 中心完全受管理的服務，讓可靠，而安全數百萬 IoT 裝置與應用程式之間的雙向通訊後端。 上一個教學課程 （[快速入門 IoT 中心]和[雲端-裝置以傳送訊息 IoT 中樞]） 說明裝置至雲端和雲端-裝置訊息的基本功能 IoT 中心。 IoT 中心也可讓您能夠叫用在雲端的裝置上的非持續性方法。 方法表示要求回覆互動與裝置 HTTP 呼叫相似的他們成功或失敗 （緊接使用者指定的逾時），讓使用者知道通話的狀態。 [叫用的裝置上直接方法][lnk-devguide-methods]說明中更多詳細資料的方法，並提供使用方法與雲端-裝置訊息的相關指引。

本教學課程教您如何以︰

- 使用 [Azure 入口網站建立 IoT 中心，並在您 IoT 中心建立裝置的身分識別。
- 建立具有直接的方法可以由雲端稱為模擬的裝置。
- 建立主控台應用程式的模擬的裝置，透過 IoT 中心上通話直接的方法。

> [AZURE.NOTE] 此時，直接的方法是只能從連線到使用 MQTT 通訊協定 IoT 中樞的裝置。 請參閱[MQTT 支援][lnk-devguide-mqtt]本文指示轉換現有的裝置應用程式使用 MQTT。

在本教學課程結尾，您有兩個 Node.js 主控台應用程式︰

* **CallMethodOnDevice.js**，呼叫模擬的裝置上的方法，並顯示回應。
* **SimulatedDevice.js**，先前建立的裝置身分識別與連線到您 IoT 集線器和回應的雲端呼叫的方法。

> [AZURE.NOTE] 文章[IoT 中心 Sdk] [lnk-hub-sdks]提供各種 Sdk，您可以用來建立兩個裝置與您的方案後端上執行應用程式的相關資訊。

若要完成此教學課程中，您需要下列項目︰

+ Node.js 版本 0.10.x 或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您可以建立回應呼叫雲端方法 Node.js 主控台應用程式。

1. 建立新的空白資料夾稱為**simulateddevice**。 在 [ **simulateddevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**simulateddevice**資料夾中，執行下列命令以安裝**azure iot 裝置**裝置 SDK 套件和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **simulateddevice**資料夾中建立新的**SimulatedDevice.js**檔案。

4. 新增下列`require` **SimulatedDevice.js**檔案的開頭的陳述式︰

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. 新增**連接字串**變數，並使用它來建立裝置用戶端。 **{裝置連線字串}**取代您在*建立裝置身分識別*] 區段中所產生的連線字串︰

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. 新增下列函數來實作在裝置上的方法︰

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. 開啟您 IoT 中心並開始連線初始化方法接聽程式︰

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. 儲存並關閉**SimulatedDevice.js**檔案。

> [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如連線重試），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。

## <a name="call-a-method-on-a-device"></a>在裝置上呼叫的方法

在此區段中，您可以建立的模擬的裝置上呼叫的方法，並顯示 [回應 Node.js 主控台應用程式。

1. 建立一個稱為**callmethodondevice**新的空白資料夾。 在 [ **callmethodondevice** ] 資料夾中，建立使用下列命令，在您命令提示字元 package.json 檔案。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**callmethodondevice**資料夾中，執行下列命令以安裝**azure iothub**套件︰

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. 使用文字編輯器， **CallMethodOnDevice.js**檔案的資料夾中建立**callmethodondevice** 。

4. 新增下列`require` **CallMethodOnDevice.js**檔案的開頭的陳述式︰

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. 新增下列變數宣告，並將版面配置區的值更換 IoT 中心的連線字串︰

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. 建立用戶端來開啟您 IoT 中樞的連線。

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. 新增下列函數叫用的裝置方法及列印主控台裝置回應︰

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. 儲存並關閉**CallMethodOnDevice.js**檔案。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元**simulateddevice**資料夾中，執行下列命令以開始接聽來電 IoT 中心的方法︰

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. 在命令提示字元**callmethodondevice**資料夾中，執行下列命令以開始監視 IoT 中心︰

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. 您會看到反應方法來列印郵件，並從裝置稱為方法顯示回應的應用程式的裝置︰

    ![][9]
    
## <a name="next-steps"></a>後續步驟

在本教學課程中，您 Azure 入口網站中，在設定新的 IoT 中心，然後建立中樞的身分識別登錄中的 [裝置身分識別。 您可以使用此裝置身分識別來啟用模擬的裝置應用程式來回應雲端叫用的方法。 您也會建立叫用的裝置上的方法，並顯示從裝置回應的應用程式。 

若要繼續快速入門 IoT 中心，並瀏覽其他 IoT 案例，請參閱︰

- [快速入門 IoT 中心]
- [多個裝置上的排程工作][lnk-devguide-jobs]

若要瞭解如何延長您的方案、 並安排方法呼叫多個裝置的 IoT，請參閱[排程和廣播的工作][lnk-tutorial-jobs]教學課程。

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[雲端-裝置以傳送訊息 IoT 中心]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[快速入門 IoT 中心]: iot-hub-node-node-getstarted.md
