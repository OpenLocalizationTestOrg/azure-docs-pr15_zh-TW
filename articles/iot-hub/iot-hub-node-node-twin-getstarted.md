<properties
    pageTitle="快速入門盡相同 |Microsoft Azure"
    description="本教學課程教您如何使用盡相同"
    services="iot-hub"
    documentationCenter="node"
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

# <a name="tutorial-get-started-with-device-twins-preview"></a>教學課程︰ 開始使用裝置盡相同 （預覽版本）

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結尾，您會有兩個 Node.js 主控台應用程式︰

* **AddTagsAndQuery.js**，從 [新增標籤及查詢裝置盡相同的後端執行 Node.js 應用程式。
* **TwinSimulatedDevice.js**，模擬連線到您 IoT 集線器與先前建立的裝置身分識別的裝置和分析其連線條件 Node.js 應用程式。

> [AZURE.NOTE] 文章[IoT 中心 Sdk] [lnk-hub-sdks]提供各種 Sdk 的相關資訊，您可以使用建置裝置和後端的應用程式。

若要完成此教學課程您需要下列項目︰

+ Node.js 版本 0.10.x 或更新版本。

+ 使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在此區段中，您可以建立位置的中繼資料加入雙生**myDeviceId**相關聯的 Node.js 主控台應用程式。 然後儲存在選取裝置中心盡相同位於美國，然後按一下 [項目報告行動數據連線的查詢。

1. 建立新的空白資料夾稱為**addtagsandqueryapp**。 在 [ **addtagsandqueryapp** ] 資料夾中，建立新 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**addtagsandqueryapp**資料夾中，執行下列命令以安裝**azure iothub**套件︰

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. 使用文字編輯器， **addtagsandqueryapp**資料夾中建立新的**AddTagsAndQuery.js**檔案。

4. 下列程式碼新增至**AddTagsAndQuery.js**檔案，和替代**{服務連線字串}**版面配置區與您建立中心所複製連線字串︰

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    **登錄**物件公開互動裝置盡相同，從服務所需的所有方法。 先前的程式碼第一次初始化**登錄**物件，然後擷取的**myDeviceId**，雙生及最後的想要的位置資訊更新其標籤。

    更新標記後，通話**queryTwins**函數。

7. 新增下列程式碼結尾的**AddTagsAndQuery.js**實作**queryTwins**函數︰

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    先前的程式碼會執行兩個查詢︰ 第一個選取只的裝置位於**Redmond43**植物，裝置盡相同，並在第二個修改查詢，以選取也連線到行動電話網路的裝置。

    請注意的上一個的程式碼時，它會建立**查詢**的物件，指定所傳回的文件的最大數目。 **查詢**物件會包含可用來擷取所有結果多次呼叫**nextAsTwin**方法**hasMoreResults**布林值屬性。 稱為 [**下一步**的方法是可用的結果不是裝置盡相同，例如的彙總查詢的結果。

8. 執行應用程式︰

        node AddTagsAndQuery.js

    您應該會看到位於**Redmond43** ，而不使用行動電話網路的裝置限制搜尋結果的查詢的所有裝置都要求的查詢結果中裝置。

    ![][1]

[下一步] 區段中，您會建立報表的連線資訊，並變更的前一節中的查詢結果的裝置應用程式。

## <a name="create-the-device-app"></a>建立裝置應用程式

在此區段中，您可以建立 Node.js 主控台應用程式連線到**myDeviceId**，為中心，然後更新其雙生報告的內容，包含已連線使用行動電話網路的資訊。

> [AZURE.NOTE] 此時，裝置盡相同只能從來存取連線到使用 MQTT 通訊協定 IoT 中樞的裝置。 請參閱[MQTT 支援][lnk-devguide-mqtt]本文指示轉換現有的裝置應用程式使用 MQTT。

1. 建立新的空白資料夾稱為**reportconnectivity**。 在 [ **reportconnectivity** ] 資料夾中，建立新 package.json 檔案使用下列命令，在命令提示字元 」。 接受所有的預設值︰

    ```
    npm init
    ```

2. 在您命令提示字元**reportconnectivity**資料夾中，執行下列命令以安裝**azure iot 裝置**和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **reportconnectivity**資料夾中建立新的**ReportConnectivity.js**檔案。

4. 下列程式碼新增至**ReportConnectivity.js**檔案，和替代**{裝置連線字串}**版面配置區以建立**myDeviceId**裝置身分識別所複製連線字串︰

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    **用戶端**物件公開您需要進行互動與裝置盡相同，從裝置的所有方法。 程式碼，它會初始化**用戶端**物件之後, 擷取的**myDeviceId**雙生，並更新連線資訊其報告的屬性。

5. 執行裝置應用程式

        node ReportConnectivity.js

    您應該會看到訊息`twin state reported`。

6. 現在，裝置報告其連線資訊，它應該會出現在兩個查詢。 返回 [ **addtagsandqueryapp** ] 資料夾，並再次執行查詢︰

        node AddTagsAndQuery.js

    此時間**myDeviceId**應該出現在兩個查詢的結果。

    ![][3]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您 Azure 入口網站中，在設定新的 IoT 中心，然後建立中樞的身分識別登錄中的 [裝置身分識別。 您新增為標記的裝置的中繼資料，從後端應用程式，並寫模擬的裝置應用程式的裝置雙生報表裝置連線資訊。 您也可以瞭解如何查詢使用 IoT 中心類似 SQL 查詢語言這項資訊。

使用下列資源，瞭解如何︰

- 從裝置的[快速入門 IoT] 中心]傳送遙測[lnk-iothub-getstarted]教學課程中，
- 設定裝置使用雙生的所需的內容的[使用所需的屬性設定裝置][lnk-twin-how-to-configure]教學課程中，
- 使用[使用直接方法]控制裝置互動的方式 （例如開啟風扇從使用者控制的應用程式），[lnk-methods-tutorial]教學課程。

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md