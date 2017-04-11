<properties
 pageTitle="如何韌體更新 |Microsoft Azure"
 description="本教學課程教您如何韌體更新"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>教學課程︰ 如何韌體更新 （預覽版本）

## <a name="introduction"></a>簡介
在 [[開始使用裝置管理][lnk-dm-getstarted]教學課程，您會看到如何使用[裝置雙生][lnk-devtwin]和[雲端的裝置 (C2D) 方法][lnk-c2dmethod]遠端重新啟動裝置的基本項目。 本教學課程中使用相同的 IoT 中心基本提供的指引，並說明如何執行的端對端模擬的韌體更新。  這個模式是韌體更新實作中用於 Intel Edison 裝置範例。

本教學課程教您如何以︰

- 建立模擬的裝置，透過 IoT 中心通話 firmwareUpdate 直接方法主控台應用程式。
- 建立實作 firmwareUpdate 直接方法進入透過多階段程序會下載韌體圖像，請下載韌體圖像，而且最後適用於第韌體圖像的模擬的裝置。  在執行每個階段雙生報告的內容] 以更新的裝置使用進度。

在本教學課程結尾，您有兩個 Node.js 主控台應用程式︰

**dmpatterns_fwupdate_service.js**，會在模擬的裝置上呼叫的直接的方法，顯示回應，並會定期 (每個 500) 顯示更新的裝置雙生報告的內容。

**dmpatterns_fwupdate_device.js**，連線到您 IoT 集線器與先前建立的裝置身分識別，會收到 firmwareUpdate 直接方法、 執行多重狀態的程序，以模擬韌體更新，包括︰ 等待圖像下載、 下載新的圖像，以及最後套用圖像。


若要完成此教學課程中，您需要下列項目︰

Node.js 版本 0.12.x 或更新版本、 <br/>  [準備您的開發環境][lnk-dev-setup]說明如何安裝 Windows 或 Linux Node.js 本教學課程。

使用中的 Azure 帳戶。 (如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘的時間。)

請遵循[開始使用裝置管理](iot-hub-device-management-get-started.md)的文件，以建立 IoT 中心並取得連線字串。

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬的裝置應用程式

在此節]，建立回應呼叫雲端，觸發模擬的裝置韌體更新直接方法 Node.js 主控台應用程式，使用裝置雙生報告的內容啟用裝置雙生查詢以識別裝置和時，一次啟動。

1. 建立新的空白資料夾稱為**manageddevice**。  在 [ **manageddevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。  接受所有的預設值︰

    ```
    npm init
    ```
    
2. 在您命令提示字元**manageddevice**資料夾中，執行下列命令以安裝**azure-iot-device@dtpreview**裝置 SDK 套件及**azure-iot-device-mqtt@dtpreview**套件︰

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 使用文字編輯器， **manageddevice**資料夾中建立新的**dmpatterns_fwupdate_device.js**檔案。

4. 新增下列 「 需要 」 開頭的**dmpatterns_fwupdate_device.js**檔案的陳述式︰

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. 新增**連接字串**變數，並使用它來建立裝置用戶端。  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 新增下列函數，將會使用更新雙生報告的內容

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. 新增下列函數將模擬下載並套用韌體圖像。

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. 新增下列函數也會更新到雙生韌體更新狀態報告給等待下載的屬性。  一般而言，裝置會另行知會 avaiable 更新，且系統管理員定義的原則的原因開始下載並套用更新的裝置。  這是會啟用該原則邏輯。  為求，我們要延遲 4 秒鐘，並繼續下載韌體圖像。 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. 新增下列函數也會更新到雙生韌體更新狀態報告給下載韌體圖像的屬性。  它後續模擬韌體下載並最後更新韌體更新狀態下載成功或失敗的通知。

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. 新增下列函數也會更新到雙生韌體更新狀態報告給套用韌體圖像的屬性。  它後續模擬套用韌體圖像，並最後更新韌體更新狀態]，套用成功或失敗的通知。

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. 新增下列 functoin 處理 firmwareUpdate 方法，並啟動多階段韌體更新程序。

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. 最後，新增下列連線到為裝置，IoT 中樞的程式碼 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] 若要保留項目簡單，此教學課程並未實作任何重試原則。 在實際執行程式碼，您應該實作重試原則 （例如指數輪詢），建議在[暫時性錯誤處理]的 MSDN 文章[lnk-transient-faults]。

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>觸發遠端韌體更新裝置使用直接的方法 

在此區段中，您可以建立的初始遠端韌體更新使用直接的方法在裝置上，並使用裝置雙生查詢定期取得該裝置上的 [作用中的韌體更新的狀態 Node.js 主控台應用程式。


1. 建立新的空白資料夾稱為**triggerfwupdateondevice**。  在 [ **triggerfwupdateondevice** ] 資料夾中，建立 package.json 檔案使用下列命令，在命令提示字元 」。  接受所有的預設值︰

    ```
    npm init
    ```
    
2. 在您命令提示字元**triggerfwupdateondevice**資料夾中，執行下列命令以安裝**azure iothub**裝置 SDK 套件和**azure iot-裝置 mqtt**套件︰

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. 使用文字編輯器， **triggerfwupdateondevice**資料夾中建立新的**dmpatterns_getstarted_service.js**檔案。

4. 新增下列 「 需要 」 開頭的**dmpatterns_getstarted_service.js**檔案的陳述式︰

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. 新增下列變數宣告及取代的版面配置區︰

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. 新增下列函數尋找，並顯示值的 firmwareUpdate 報告的屬性。

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. 新增下列函數叫用 firmwareUpdate 方法若要重新啟動目標裝置︰

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. 最後，新增下列程式碼，以開始韌體函式更新順序，並開始定期顯示雙生報告的內容︰

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. 儲存並關閉**dmpatterns_fwupdate_service.js**檔案。

## <a name="run-the-applications"></a>執行應用程式

您已準備好執行應用程式。

1. 在命令提示字元**manageddevice**資料夾中，執行下列命令以開始接聽重開機直接方法。

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. 在命令提示字元**triggerfwupdateondevice**資料夾中，執行下列命令以觸發程序的遠端重新開機及查詢的裝置雙生，若要尋找上次重新啟動時間。

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. 您會看到直接方法反應來列印郵件

## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以使用直接的方法觸發裝置上的遠端韌體更新，且定期使用雙生報告的內容若要瞭解的韌體進度更新程序。  

若要瞭解如何延長您 IoT 多個裝置的通話方案、 並安排的方法，請參閱[排程和廣播的工作][lnk-tutorial-jobs]教學課程。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
