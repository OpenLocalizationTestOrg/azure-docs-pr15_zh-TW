<properties
   pageTitle="使用 Node.js 裝置連接到 |Microsoft Azure"
   description="說明如何將裝置連接到預先設定的 Azure IoT 套件遠端監視解決方案使用 Node.js 撰寫的應用程式。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>將您的裝置連線到遠端監視的預先設定解決方案 (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>建立並執行 node.js 範例方案

1. 若要複製*Microsoft Azure IoT Sdk* GitHub 存放庫，並將*Microsoft Azure IoT 裝置 Node.js SDK*安裝在您的桌面環境，請依照下列[準備您的開發環境][lnk-github-prepare]指示。

2. 從[azure-iot-sdk]的本機複本[lnk-github-repo]存放庫，複製以下兩個檔案從節點/裝置範例資料夾的資料夾在您的裝置上︰

  - packages.json
  - remote_monitoring.js

3. 開啟 remote_monitoring.js 檔案，並尋找下列變數︰

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. 取代您的裝置連接字串中的**[IoT 中心裝置連線字串]** 。 您可以找到值 IoT 中心 hostname 裝置識別碼與裝置鍵遠端監控的解決方案儀表板中。 裝置連線字串具有下列格式︰

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    如果您 IoT 中心 hostname **contoso** ，而您的裝置識別碼會**mydevice**，您的連接字串看起來像︰

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. 儲存檔案。 在命令提示字元中包含這些檔案來安裝必要的封裝，然後執行 [範例應用程式的資料夾，請執行下列命令︰

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md