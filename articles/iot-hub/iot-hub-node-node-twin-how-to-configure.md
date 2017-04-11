<properties
    pageTitle="使用雙生屬性 |Microsoft Azure"
    description="本教學課程教您如何使用雙生屬性"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>教學課程︰ 使用您要的屬性設定裝置 （預覽版本）

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教學課程結尾，您會有兩個 Node.js 主控台應用程式︰

* **SimulateDeviceConfiguration.js**，等待更新的所需的設定，報告模擬的設定更新程序的狀態的模擬的裝置應用程式。
* **SetDesiredConfigurationAndQuery.js**，從後端，哪些裝置上設定所需的設定和查詢的設定更新程序執行 Node.js 應用程式。

> [AZURE.NOTE] 文章[IoT 中心 Sdk] [lnk-hub-sdks]提供各種 Sdk 的相關資訊，您可以使用建置裝置和後端的應用程式。

若要完成此教學課程您需要下列項目︰

+ Node.js 版本 0.10.x 或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

如果您已追蹤的[快速入門裝置盡相同][lnk-twin-tutorial]教學課程中，您已經有裝置啟用管理中樞和稱為**myDeviceId**; 裝置身分識別您可以直接跳到[建立模擬的裝置應用程式]和[lnk-how-to-configure-createapp]一節。

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您可以建立連線到您為**myDeviceId**中心，等待更新的所需的設定，然後報表模擬的設定更新程序中的 [更新 Node.js 主控台應用程式。

1. 建立新的空白資料夾稱為**simulatedeviceconfiguration**。 在 [ **simulatedeviceconfiguration** ] 資料夾中，建立新 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**simulatedeviceconfiguration**資料夾中，執行下列命令以安裝**azure iot 裝置**和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **simulatedeviceconfiguration**資料夾中建立新的**SimulateDeviceConfiguration.js**檔案。

4. 下列程式碼新增至**SimulateDeviceConfiguration.js**檔案，和替代**{裝置連線字串}**版面配置區以建立**myDeviceId**裝置身分識別所複製連線字串︰

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    **用戶端**物件公開互動裝置盡相同，從裝置所需的所有方法。 程式碼，它會初始化**用戶端**物件之後, 擷取的**myDeviceId**，雙生，然後附加想要的屬性更新處理常式。 處理常式驗證是否有實際的設定變更要求時，請比較 configIds，然後叫用開始設定變更的方法。

    請注意，方便，先前的程式碼使用硬式編碼的預設初始設定。 實數的應用程式可能會想要從本機存放區載入該設定。
    
    > [AZURE.IMPORTANT] 在裝置連線永遠發出所要的屬性變更事件一次，請務必檢查有實際所需屬性變更之前執行任何動作。

5. 新增下列方法之前`client.open()`引動︰

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    **InitConfigChange**方法更新本機雙生物件上的報告的屬性設定更新要求狀態設定為 [**擱置中**，然後更新裝置雙生服務。 在成功更新雙生之後，模擬終止執行中的**completeConfigChange**長時間執行程序。 這個方法更新**成功**設定的狀態，及移除**pendingConfig**物件本機的雙生報告的內容。 然後，它就會更新服務雙生。

    請注意，若要儲存的頻寬，報告的內容會更新以指定要修改 （命名的**修補程式**將上述程式碼），而非取代整份文件的內容。

    > [AZURE.NOTE] 本教學課程中不會模擬同時設定更新任何行為。 某些設定更新程序可能可以容納的目標設定變更時執行更新與其他人可能佇列，其他人可以拒絕這些錯誤狀況。 請務必考慮所需的行為的特定設定程序，並新增適當的邏輯之前起始設定變更。

6. 執行裝置應用程式︰

        node SimulateDeviceConfiguration.js

    您應該會看到訊息`retrieved device twin`。 將應用程式執行。

## <a name="create-the-service-app"></a>建立服務應用程式

在此區段中，您將建立的 Node.js 主控台應用程式的更新**myDeviceId**以新的遙測設定物件相關聯雙生*所要的屬性*。 然後查詢儲存在中樞裝置盡相同，並顯示您要和報告設定的裝置之間的差異。

1. 建立新的空白資料夾稱為**setdesiredandqueryapp**。 在 [ **setdesiredandqueryapp** ] 資料夾中，建立新 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**setdesiredandqueryapp**資料夾中，執行下列命令以安裝**azure iothub**套件︰

    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```

3. 使用文字編輯器， **addtagsandqueryapp**資料夾中建立新的**SetDesiredAndQuery.js**檔案。

4. 下列程式碼新增至**SetDesiredAndQuery.js**檔案，和替代**{服務連線字串}**版面配置區與您建立中心所複製連線字串︰

        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
         
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });
            

    **登錄**物件公開互動裝置盡相同，從服務所需的所有方法。 程式碼，它會初始化**登錄**物件之後, 擷取的**myDeviceId**，雙生，並更新新遙測設定物件其所要的內容。 之後，它會呼叫**queryTwins**函數事件 10 秒內。

    > [AZURE.IMPORTANT] 這個應用程式查詢 IoT 中心供說明每 10 秒。 跨多個裝置，產生使用者具報告，而非偵測的變更，請使用查詢。 如果您的方案要求的裝置事件的即時通知使用[的裝置至雲端訊息][lnk-d2c]。

5. 新增下列程式碼右邊之前`registry.getDeviceTwin()`實作**queryTwins**函數的引動︰

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };

    先前的程式碼查詢盡相同儲存在] 中心內，並列印所需和報告遙測設定。 請參閱[IoT 中心查詢語言][lnk-query]若要瞭解如何在所有裝置上產生豐富的報表。


8. **SimulateDeviceConfiguration.js**執行應用程式︰

        node SetDesiredAndQuery.js 5m

    您應該會看到報告設定變更從**成功****擱置****成功**再次使用新的作用中傳送五分鐘，而不是 24 小時的頻率。

    > [AZURE.IMPORTANT] 有一分鐘裝置報表作業與查詢結果之間的延遲。 這是啟用查詢基礎結構，以便在更高的小數位數。 若要擷取的單一雙生一致的檢視會使用**getDeviceTwin**方法**登錄**類別中。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以為*所要的內容*設定所需的設定，從後端應用程式，並寫偵測的變更，並模擬其狀態報告為*報告的內容*，以雙生多步驟更新程序的模擬的裝置應用程式。

使用下列資源，瞭解如何︰

- 從裝置的[快速入門 IoT] 中心]傳送遙測[lnk-iothub-getstarted]教學課程中，
- 排程，或在上執行作業大量的裝置，請參閱[使用工作排程與廣播裝置作業][lnk-schedule-jobs]教學課程。
- 使用[使用直接方法]控制裝置互動的方式 （例如開啟風扇從使用者控制的應用程式），[lnk-methods-tutorial]教學課程。


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
