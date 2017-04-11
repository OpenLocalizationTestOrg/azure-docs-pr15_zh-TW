<properties
    pageTitle="模擬 IoT 閘道器 SDK 的裝置 |Microsoft Azure"
    description="以描繪出從模擬裝置使用 Azure IoT 閘道器 SDK 傳送遙測使用 Windows azure IoT 閘道器 SDK 逐步解說。"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-windows"></a>Azure IoT 閘道器 SDK （beta 版） – 傳送裝置至雲端郵件使用 Windows 的模擬裝置

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>建立和執行範例

在您開始之前，您必須︰

- [設定您的開發環境][lnk-setupdevbox]使用 SDK Windows 上的。
- [建立 IoT 中心][ lnk-create-hub] Azure 訂閱，您需要完成此逐步解說中心的名稱。 如果您沒有帳戶，您可以建立一個[免費的帳戶][lnk-free-trial]在幾分鐘。
- 加入 IoT 中心的兩個裝置，並記下其識別碼與裝置索引鍵。 您可以使用的[裝置總管或 iothub 總管][lnk-explorer-tools]工具新增至您在上一個步驟中建立 [IoT 中心的 [您的裝置，並可擷取其索引鍵。

若要建立範例︰

1. 開啟 [**開發人員 VS2015 的命令提示字元**命令提示字元]。
2. 瀏覽至您的本機複本的**azure iot-閘道器 sdk**存放庫中的根資料夾。
3. 執行**工具\\build.cmd**指令碼。 這個指令碼會建立 Visual Studio 解決方案檔案、 建置方案，並執行測試。 **Azure iot-閘道器 sdk**存放庫的本機複本中，您可以在 [**建立**] 資料夾中找到 Visual Studio 方案。

若要執行的範例︰

在文字編輯器中開啟的檔案**範例\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** **azure iot-閘道器 sdk**存放庫的本機複本中。 此檔案中的範例閘道器設定模組︰

- **IoTHub**模組連線到您的 IoT 集散地。 您必須將它傳送資料至 IoT 中心設定。 具體來說， **IoTHubName**值設為您 IoT 中樞的名稱，而將**IoTHubSuffix**時值設為**azure devices.net**。 將**傳輸**時值設為下列其中一項: 「 HTTP 」、 「 AMQP 」 或 「 MQTT 」。 請注意，目前，只 」 HTTP 」 會共用 TCP 連線的所有裝置郵件。 如果您設定 「 AMQP 」 或 「 MQTT 」 的值，閘道器會維護個別 TCP 連線 IoT 中心針對每個裝置。
- **對應**模組地圖模擬裝置的 MAC 地址至您 IoT 中心裝置識別碼。 請確定**deviceId**值符合您新增至您 IoT] 中心內，兩個裝置的識別碼**deviceKey**值，包含兩個裝置的索引鍵。
- **BLE1**和**BLE2**模組是模擬的裝置。 請注意如何其 MAC 位址符合**對應**模組中。
- **記錄器**模組檔案至記錄您閘道器的活動。
- 以下所示的**模組路徑**值，假設您複製到**c**磁碟機的根 IoT 閘道器 SDK 存放庫。 如果您下載到另一個位置，您需要調整**模組路徑**值。
- 底部的 JSON 檔案的**連結**陣列連線**BLE1**和**BLE2**模組**對應**模組和**對應**模組至**IoTHub**模組。 也可確保所有郵件將由**記錄器**模組都記錄。

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

儲存您的設定檔所做的任何變更。

若要執行的範例︰

1. 在命令提示字元中，瀏覽至您的**azure iot-閘道器 sdk**存放庫本機複本的根資料夾。
2. 執行下列命令︰
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. 您可以使用的[裝置總管或 iothub 總管][lnk-explorer-tools]工具來監控 IoT 中心會接收來自閘道器的郵件。


## <a name="next-steps"></a>後續步驟

如果您想要瞭解更多進階的 IoT 閘道器 SDK 及下嘗試使用 [程式碼範例，請造訪下列開發人員教學課程和資源︰

- [從有 IoT 閘道器 SDK 實數的裝置傳送裝置至雲端的郵件][lnk-physical-device]
- [Azure IoT 閘道器 SDK][lnk-gateway-sdk]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 