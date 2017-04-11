<properties
 pageTitle="讀取郵件保存在 Azure 儲存體 |Microsoft Azure"
 description="監視您 Azure 資料表儲存體寫入裝置至雲端的郵件。"
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

# <a name="33-read-messages-persisted-in-azure-storage"></a>3.3 讀取的郵件保存在 Azure 儲存體

## <a name="331-what-will-you-do"></a>3.3.1 您會執行什麼動作

監控裝置至雲端郵件當您 Azure 資料表儲存體寫入郵件從您覆盆子 Pi 3 傳送給您 IoT 中樞的。 如果您符合任何問題，請搜尋中的[疑難排解頁面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)的解決方案。

## <a name="332-what-will-you-learn"></a>3.3.2 您將學習什麼

- 如何讀取郵件使用大讀取郵件工作保留在您 Azure 資料表儲存體。

## <a name="333-what-do-you-need"></a>3.3.3 您需要什麼

- 您必須已順利[執行 Azure 閃爍範例應用程式，在您覆盆子 Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)前一節。

## <a name="334-read-new-messages-from-your-storage-account"></a>3.3.4 閱讀您儲存的帳戶的新郵件

前一節中，您會在您 Pi 執行範例應用程式。 Azure IoT 中心範例應用程式傳送郵件。 傳送至您 IoT 中樞的訊息會儲存到您的 Azure 資料表儲存體透過 Azure 函數應用程式。 您需要從您 Azure 資料表儲存體讀取郵件的 Azure 儲存體連接字串。

若要閱讀儲存在您 Azure 資料表儲存體中的郵件，請遵循下列步驟︰

1. 執行下列命令以取得連線字串︰

    ```bash
    az storage account list -g iot-sample --query [].name
    az storage account show-connection-string -g iot-sample -n {storage name}
    ```

    第一個命令擷取`storage name`第二個命令中用來取得連線字串。 `iot-sample`做為預設值的`{resource group name}`如果您沒有變更課程 2 中的值。

2. 開啟設定檔`config-raspberrypi.json`Visual Studio 程式碼中的檔案來執行下列命令︰

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. 取代`[Azure Storage connection string]`與您在步驟 1 中取得連線字串。
4. 儲存`config-raspberrypi.json`檔案。
5. 重新傳送郵件，然後他們讀取您 Azure 資料表儲存體藉由執行下列命令︰

    ```bash
    gulp run --read-storage
    ```

    從 Azure 資料表儲存體閱讀邏輯位於`azure-table.js`檔案。

    ![大執行-讀取儲存空間](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="335-summary"></a>3.3.5 摘要

您已成功連接您 Pi 到您 IoT 集線器在雲端，閃爍範例應用程式傳送裝置至雲端的郵件。 您也可以用於 Azure 函數應用程式內送 IoT 中心郵件儲存至您的 Azure 資料表儲存體。 您可以移至下一個課程如何從 IoT 中心的雲端-裝置訊息傳送至您 Pi。

## <a name="next-steps"></a>後續步驟

[課程 4︰ 傳送雲端-裝置郵件](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)
