<properties
 pageTitle="開始使用裝置管理 |Microsoft Azure"
 description="本教學課程教您如何開始使用裝置管理上 Azure IoT 中心"
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

# <a name="tutorial-get-started-with-device-management-preview"></a>教學課程︰ 開始使用裝置管理 （預覽版本）

## <a name="introduction"></a>簡介
IoT 雲端應用程式可以使用基本項目中 Azure IoT] 中心內，也就是裝置雙生和直接的方法，從遠端啟動，並監控裝置管理行動裝置上。  本文提供指引與程式碼的裝置和 IoT 雲端應用程式如何運作在一起，以啟動，並監控使用 IoT 中心遠端裝置重新啟動電腦。

若要從遠端開始，並監控裝置管理動作，從雲端架構的後端的應用程式在裝置上，使用 [Azure IoT 中心基本項目，例如[裝置雙生][lnk-devtwin]和[雲端的裝置 (C2D) 方法][lnk-c2dmethod]。 本教學課程教您如何在後端應用程式與裝置搭配運作，讓您啟動，並監控遠端裝置重開機，從 IoT 中心。

您可以使用 C2D 方法來啟動裝置管理動作 （例如重開機，工廠重設]，然後韌體更新） 的雲端中的後端應用程式。 裝置負責︰

- 處理從 IoT] 中心傳送的方法要求。
- 開始在裝置上對應的裝置特定動作。
- 提供透過裝置雙生狀態更新到 IoT 集線器報告內容。

您可以使用雲端中的後端應用程式執行裝置雙生查詢報告您的裝置管理的進度。

本教學課程教您如何以︰

- 使用 [Azure 入口網站建立 IoT 中心，並在您 IoT 中心建立裝置的身分識別。
- 建立模擬的裝置已直接的方法可讓重新開機，其可由雲端呼叫。
- 建立模擬 IoT 中心透過裝置呼叫重開機直接方法主控台應用程式。

在本教學課程結尾，您有兩個 Node.js 主控台應用程式︰

**dmpatterns_getstarted_device.js**，連線到您 IoT 集線器與先前建立的裝置身分識別，會收到重新啟動直接方法、 模擬實體重新啟動電腦，和報表上次重新開機的時間。

**dmpatterns_getstarted_service.js**，呼叫模擬的裝置上的直接的方法，顯示回應，並顯示更新的裝置雙生報告的內容。

若要完成此教學課程中，您需要下列項目︰

Node.js 版本 0.12.x 或更新版本、 <br/>  [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Node.js 本教學課程。

使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此區段中，您建立 Node.js 主控台應用程式回應直接方法稱為雲端，觸發模擬的裝置重新啟動電腦，並使用裝置雙生報告的內容啟用裝置雙生查詢以識別裝置和時，一次啟動。

1. 建立新的空白資料夾稱為**manageddevice**。  在 [ **manageddevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。  接受所有的預設值︰

    ```
    npm init
    ```
    
2. 在您命令提示字元**manageddevice**資料夾中，執行下列命令以安裝**azure-iot-device@dtpreview**裝置 SDK 套件及**azure-iot-device-mqtt@dtpreview**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **manageddevice**資料夾中建立新的**dmpatterns_getstarted_device.js**檔案。

4. 新增下列 「 需要 」 開頭的**dmpatterns_getstarted_device.js**檔案的陳述式︰

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. 新增**連接字串**變數，並使用它來建立裝置用戶端。  取代您的裝置連接字串中的連接字串。  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 新增要在裝置上實作直接方法下列函數

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. 開啟您 IoT 中樞的連線，並開始直接方法接聽程式︰

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. 儲存並關閉**dmpatterns_getstarted_device.js**檔案。

 [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>觸發程序遠端重新啟動電腦上使用直接的方法 

在此區段中，您可以建立的初始遠端使用直接的方法在裝置上重新啟動電腦，並使用裝置雙生查詢來尋找該裝置的最後一個重新啟動時間 Node.js 主控台應用程式。

1. 建立新的空白資料夾稱為**triggerrebootondevice**。  在 [ **triggerrebootondevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。  接受所有的預設值︰

    ```
    npm init
    ```
    
2. 在您命令提示字元**triggerrebootondevice**資料夾中，執行下列命令以安裝**azure-iothub@dtpreview**裝置 SDK 套件及**azure-iot-device-mqtt@dtpreview**套件︰

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. 使用文字編輯器， **triggerrebootondevice**資料夾中建立新的**dmpatterns_getstarted_service.js**檔案。

4. 新增下列 「 需要 」 開頭的**dmpatterns_getstarted_service.js**檔案的陳述式︰

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. 新增下列變數宣告及取代的版面配置區︰

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. 新增下列函數叫用的裝置方法若要重新啟動目標裝置︰

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. 新增查詢的裝置，並取得重開機上次下列函數︰

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. 新增下列程式碼，通話會觸發重開機直接方法和查詢的最後一個重新開機時間的功能︰

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. 儲存並關閉**dmpatterns_getstarted_service.js**檔案。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元**manageddevice**資料夾中，執行下列命令以開始接聽重開機直接方法。

    ```
    node dmpatterns_getstarted_device.js
    ```

2. 在命令提示字元**triggerrebootondevice**資料夾中，執行下列命令以觸發程序的遠端重新開機及查詢的裝置雙生，若要尋找上次重新啟動時間。

    ```
    node dmpatterns_getstarted_service.js
    ```

3. 您會看到直接方法反應來列印郵件

## <a name="customize-and-extend-the-device-management-actions"></a>自訂及擴充裝置管理動作

IoT 方案可以展開已定義之的設定的裝置管理模式，或啟用裝置雙生和 C2D 方法基本自訂圖樣。 其他的裝置管理動作包括工廠重設、 韌體更新、 軟體更新、 電源管理、 網路和連線管理及資料加密。

## <a name="device-maintenance-windows"></a>在 windows 裝置進行的維修作業

一般而言，您可以設定裝置執行的動作會干擾和停機時間的時間。  裝置進行的維修作業視窗是常用的模式，以定義裝置何時更新其設定的時間。 後端方案可以定義，並啟動原則，可進行的維修作業] 視窗的裝置上使用的裝置雙生所需的屬性。 當裝置收到進行的維修作業] 視窗原則時，其可用裝置雙生 [報告] 的屬性來報告原則的狀態。 然後後端應用程式可以使用裝置雙生查詢足以裝置與每個原則的規範。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以使用直接的方法觸發遠端用的裝置上重新啟動裝置雙生報告的內容至報告裝置的最後一個重新啟動時間和裝置雙生探索重開機上次雲端裝置的查詢。

若要繼續與快速入門 IoT 中心裝置管理模式，例如遠端透過 air 韌體更新，請參閱︰

[教學課程︰ 如何韌體更新][lnk-fwupdate]

若要瞭解如何延長您 IoT 多個裝置的通話方案、 並安排的方法，請參閱[排程和廣播的工作][lnk-tutorial-jobs]教學課程。

若要繼續快速入門 IoT 集線器，請參閱[快速入門 IoT 閘道器 SDK][lnk-gateway-SDK]。


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx