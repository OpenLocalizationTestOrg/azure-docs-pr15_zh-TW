<properties
 pageTitle="執行範例應用程式將接收訊息雲端-裝置 |Microsoft Azure"
 description="在教學單元 4 範例應用程式會在您 Pi 上執行，和監視從您 IoT] 中心內送郵件。 大工作會從您的 IoT 中心以閃爍 LED 傳送給您 Pi 的郵件。"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="41-run-the-sample-application-to-receive-cloud-to-device-messages"></a>4.1 執行範例應用程式將接收訊息雲端-裝置

在此區段中，您會在您覆盆子 Pi 3 部署範例應用程式。 範例應用程式監視從您 IoT] 中心內送郵件。 您也會傳送郵件給您 Pi，從您 IoT 中心您電腦上執行大工作。 在收到的郵件時，範例應用程式會閃爍 LED。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="411-what-you-will-do"></a>4.1.1 您將會執行的動作

- 連線到您 IoT 集線器範例應用程式。
- 部署，並執行範例應用程式。
- 從您 IoT] 中心傳送郵件，以您的 Pi 閃爍 LED。

## <a name="412-what-you-will-learn"></a>4.1.2 您將學習什麼

- 如何監控從您 IoT] 中心內送郵件。
- 如何從 IoT 中心的雲端-裝置訊息傳送至您 Pi。 

## <a name="413-what-do-you-need"></a>4.1.3 您需要什麼

- 設定用於覆盆子 Pi 3。 若要瞭解如何將您 Pi 設定，請參閱[教學單元 1︰ 開始使用您的覆盆子 Pi 3 裝置](iot-hub-raspberry-pi-kit-node-get-started.md)
- 會建立在 Azure 訂閱 IoT 中心。 若要瞭解如何建立 Azure IoT 集線器，請參閱[教學單元 2︰ 建立您的 Azure IoT 集散地](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="414-connect-the-sample-application-to-your-iot-hub"></a>4.1.4 連線到您 IoT 集線器範例應用程式

1. 請確定您是在 repo 資料夾`iot-hub-node-raspberrypi-getting-started`。 開啟 Visual Studio 程式碼中的範例應用程式，執行下列命令︰

    ```bash
    cd Lesson4
    code .
    ```

    請注意`app.js`檔案中`app`子資料夾。 `app.js`檔案是包含監控從 IoT] 中心內送郵件的程式碼之索引鍵的來源檔案。 `blinkLED`函數閃爍 LED。

    ![Repo 結構](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)

2. 初始化設定檔，並輸入下列命令︰

    ```bash
    npm install
    gulp init
    ```

    如果您已完成教學單元 3 這台電腦上，因此您可以直接跳到步驟 4.1.5 繼承所有的設定。 如果您在不同的電腦上完成教學單元 3，您需要取代中的預留位置`config-raspberrypi.json`檔案。 `config-raspberrypi.json`檔案是您常用的資料夾的子資料夾中。

    ![設定](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

- 您 Pi 的 IP 位址或主機名稱，讓執行命令取代**[裝置主機名稱或 IP 位址]**`devdisco list --eth`
- 取代您收到執行命令的裝置連接字串中的**[IoT 裝置連線字串]** `az iot hub show-connection-string --name {my hub name} --resource-group {resource group name}`。
- **[IoT 中心連線字串]**取代為您快速執行命令 IoT 中心連線字串`az iot device show-connection-string --hub {my hub name} --device-id {device id} --resource-group {resource group name}`。

## <a name="415-deploy-and-run-the-sample-application"></a>4.1.5 部署及執行範例應用程式

部署，並在您 Pi 上執行範例應用程式，執行下列命令︰
  
```
gulp
```

[大] 命令會先執行安裝工具工作。 然後將其部署您 Pi 範例應用程式。 最後，您 Pi 和主機電腦上的個別工作，從您 IoT 中心，傳送給您 Pi 的 20 閃爍郵件上執行應用程式。

一旦範例應用程式執行時，它開始接聽來自您 IoT 中樞的郵件。 同時，大工作會傳送 「 閃爍 」 訊息的數個從您 IoT 中心給您 Pi。 針對每個接收到的閃爍訊息，範例應用程式的通話閃爍 LED blinkLED 函數。

您應該會看到閃爍每兩秒，如大工作傳送從您 IoT 中心 20 訊息給您 Pi LED。 最後一個項目會出現 「 停止 」 的訊息，指出應用程式停止執行。

![大](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="416-summary"></a>4.1.6 摘要

您已送郵件從您 IoT 中心到您的 Pi 閃爍 LED。 下一節會說明如何變更開啟和關閉的 LED 行為的選擇性區段。

## <a name="next-steps"></a>後續步驟

[選擇性區段︰ 開啟和關閉的 LED 行為變更](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)