<properties
 pageTitle="如何排程工作 |Microsoft Azure"
 description="本教學課程教您如何排程工作"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>教學課程︰ 排程與廣播工作 （預覽版本）

## <a name="introduction"></a>簡介
Azure IoT 中心是完全受管理的服務，讓應用程式的後端建立及追蹤排程及更新數百萬個裝置的工作。  工作可執行下列動作︰

- 更新裝置雙生所需內容
- 更新裝置雙生標籤
- 叫用雲端至裝置的方法

概念，工作包裝其中一個動作，並追蹤執行針對一組裝置，雙生查詢所定義的進度。  例如，使用工作應用程式後端可以叫用重開機方法 10000 在裝置上，指定雙生查詢，以及排程在未來的時間。  該應用程式在每個裝置接收，並執行重開機方法，就可以追蹤進度。

深入瞭解每個這些文件中的下列功能︰

- 裝置雙生與屬性︰[開始使用雙生][lnk-get-started-twin]和[教學課程︰ 如何使用雙生屬性][lnk-twin-props]
- 雲端-裝置方法︰[開發人員指南-直接方法][lnk-dev-methods]和[教學課程︰ C2D 方法][lnk-c2d-methods]

本教學課程教您如何以︰

- 建立具有的直接方法可讓 lockDoor 可回到應用程式稱為結束的模擬的裝置。
- 建立主控台應用程式，使用工作的模擬裝置上呼叫 lockDoor 直接方法，並更新使用裝置工作所需的雙生屬性。

在本教學課程結尾，您有兩個 Node.js 主控台應用程式︰

**simDevice.js**，連線到與裝置識別您 IoT 中心，並會收到 lockDoor 直接的方法。

**scheduleJobService.js**，呼叫模擬的裝置上的直接的方法，並更新雙生 disired 屬性使用工作。

若要完成此教學課程中，您需要下列項目︰

Node.js 版本 0.12.x 或更新版本、 <br/>  [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Node.js 本教學課程。

使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您建立 Node.js 主控台應用程式回應直接方法稱為雲端，觸發模擬的裝置重新啟動電腦，並使用裝置雙生報告的內容啟用裝置雙生查詢以識別裝置和時，一次啟動。

1. 建立新的空白資料夾稱為**simDevice**。  在 [ **simDevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。  接受所有的預設值︰

    ```
    npm init
    ```
    
2. 在您命令提示字元在**simDevice**資料夾中，執行下列命令以安裝**azure iot 裝置**裝置 SDK 套件和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **simDevice**資料夾中建立新的**simDevice.js**檔案。

4. 新增下列 「 需要 」 開頭的**simDevice.js**檔案的陳述式︰

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. 新增**連接字串**變數，並使用它來建立裝置用戶端。  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 新增下列函數來處理 lockDoor 方法。

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. 新增下列程式碼，註冊 lockDoor 方式處理常式。

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. 儲存並關閉**simDevice.js**檔案。

> [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>排程工作呼叫直接方法和更新雙生的屬性

在此區段中，您可以建立起始使用直接的方法在裝置上的遠端 lockDoor Node.js 主控台應用程式，並更新雙生的屬性。

1. 建立新的空白資料夾稱為**scheduleJobService**。  在 [ **scheduleJobService** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。  接受所有的預設值︰

    ```
    npm init
    ```
    
2. 在您命令提示字元**scheduleJobService**資料夾中，執行下列命令以安裝**azure iothub**裝置 SDK 套件和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. 使用文字編輯器， **scheduleJobService**資料夾中建立新的**scheduleJobService.js**檔案。

4. 新增下列 「 需要 」 開頭的**dmpatterns_gscheduleJobServiceetstarted_service.js**檔案的陳述式︰

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. 新增下列變數宣告及取代的版面配置區︰

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. 新增下列函數，將會用來監視執行作業的︰

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. 新增下列程式碼，排程的工作，呼叫裝置的方法︰

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. 新增下列排程更新雙生工作︰

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. 儲存並關閉**scheduleJobService.js**檔案。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元**simDevice**資料夾中，執行下列命令以開始接聽重開機直接方法。

    ```
    node simDevice.js
    ```

2. 在命令提示字元**scheduleJobService**資料夾中，執行下列命令以觸發程序的遠端重新開機及查詢的裝置雙生，若要尋找上次重新啟動時間。

    ```
    node scheduleJobService.js
    ```

3. 您會看到的輸出裝置和後端應用程式。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以使用 [工作排程直接方法裝置和裝置雙生的屬性的更新。

若要繼續與快速入門 IoT 中心裝置管理模式，例如遠端透過 air 韌體更新，請參閱︰

[教學課程︰ 如何韌體更新][lnk-fwupdate]

若要繼續快速入門 IoT 集線器，請參閱[快速入門 IoT 閘道器 SDK][lnk-gateway-SDK]。

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx