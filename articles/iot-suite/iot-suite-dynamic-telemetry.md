<properties
    pageTitle="使用動態遙測 |Microsoft Azure"
    description="請遵循此教學課程以瞭解如何使用動態遙測遠端監控預先設定的解決方案。"
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>使用動態遙測遠端監控預先設定的解決方案

## <a name="introduction"></a>簡介

動態遙測可讓您以視覺化方式呈現任何遙測傳送至遠端監視預先設定的解決方案。 使用預先設定的解決方案部署模擬的裝置傳送溫度和濕度遙測，您可以在儀表板呈現。 如果您要自訂現有的模擬的裝置、 建立新的模擬的裝置，或實體的裝置連接到預先設定的方案，您可以傳送外部溫度、 轉速或 windspeed 等其他遙測值。 您可以再以視覺化方式呈現此其他遙測儀表板上。

本教學課程中會使用簡單的 Node.js 模擬的裝置，您可以輕鬆修改下嘗試使用 [動態遙測。

若要完成此教學課程中，您需要︰

- 作用中的 Azure 訂閱。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版][lnk_free_trial]。
- [Node.js] [lnk-node]版本 0.12.x 或更新版本。

您可以自行完成此教學課程中的任何作業系統，例如 Windows 或 Linux，您可以在此安裝 Node.js。

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>設定 Node.js 模擬的裝置。

1. 遠端監控儀表板上按一下 [ **+ 新增裝置**，然後新增自訂的裝置。 記下 IoT 中心主機名稱與裝置識別碼裝置鍵。 需要在本教學課程稍後當您準備好 remote_monitoring.js 裝置用戶端應用程式。

2. 請確定該 Node.js 版本 0.12.x 或稍後在您的部署電腦上安裝。 執行`node --version`在命令提示字元或命令介面，來檢查版本。 使用封裝管理員安裝 Node.js linux 的相關資訊，請參閱[透過封裝管理員的安裝 Node.js][node-linux]。

3. 當您安裝了 Node.js 時，請複製最新版[azure-iot-sdk] [lnk-github-repo]存放庫到您的部署電腦。 一律使用**母片**的分支最新版本的文件庫和範例。

4. 從[azure-iot-sdk]的本機複本[lnk-github-repo]存放庫，複製以下兩個節點/裝置範例資料夾從空白資料夾開發電腦檔案︰

  - packages.json
  - remote_monitoring.js

5. 開啟 remote_monitoring.js 檔案，並查看下列變數定義︰

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. 取代您的裝置連接字串中的**[IoT 中心裝置連線字串]** 。 IoT 中心主機名稱、 裝置識別碼，和您在步驟 1 所做的筆記的裝置機碼中使用的值。 裝置連線字串具有下列格式︰

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    如果您 IoT 中心 hostname **contoso** ，而您的裝置識別碼會**mydevice**，您的連接字串看起來如下所示︰

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. 儲存檔案。 在命令介面或包含這些檔案來安裝必要的封裝，然後執行 [範例應用程式的資料夾中的命令提示字元中，執行下列命令︰

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>觀察動態遙測的實際操作

儀表板顯示溫度和濕度遙測，從現有的模擬裝置︰

![預設儀表板][image1]

如果您選取您在前一節中執行的 Node.js 模擬的裝置時，您會看到溫度、 濕度和外部的溫度遙測︰

![將外部溫度新增至儀表板][image2]

遠端監視解決方案自動偵測到其他外部溫度遙測類型，並將其加入儀表板上的圖表。

## <a name="add-a-telemetry-type"></a>新增遙測類型

下一個步驟是將新的一組值 Node.js 模擬裝置所產生的遙測︰

1. 停止 Node.js 模擬的裝置在命令提示字元或命令介面輸入**Ctrl + C** 。

2. 在 remote_monitoring.js 的檔案，您可以看到現有的溫度、 濕度和外部的溫度遙測的基本資料值。 新增**rpm**的基本資料值如下所示︰

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js 模擬的裝置 remote_monitoring.js 檔案中使用**generateRandomIncrement**函數，將隨機遞增值基本資料值。 隨機**轉速**值設定藉一行程式碼之後現有 randomizations，如下所示︰

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. 將新的 rpm 值新增至 [裝置傳送到 IoT 集線器 JSON 內容︰

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. 執行 Node.js 模擬的裝置使用下列命令︰

    ```
    node remote_monitoring.js
    ```

6. 觀察會顯示在儀表板中的圖表的新轉速遙測類型︰

![將轉速新增至儀表板][image3]

> [AZURE.NOTE] 您可能需要停用，並啟用 Node.js 裝置以查看的變更會立即儀表板中的 [**裝置**] 頁面上。

## <a name="customize-the-dashboard-display"></a>自訂儀表板顯示

**裝置資訊**訊息可以包含裝置可以傳送到 IoT 集線器遙測的中繼資料。 此中繼資料，可以指定遙測類型的裝置傳送。 修改**deviceMetaData** remote_monitoring.js 檔案包含下列**命令**定義的**遙測**定義中的值。 下列程式碼片段顯示**命令**定義 (請務必將`,`**命令**定義之後):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] 遠端監視解決方案使用不區分大小寫須相符，來比較中繼資料定義遙測資料流中的資料。

新增**遙測**定義，如下圖所示，在前面的程式碼片段不會變更儀表板的行為。 不過，中繼資料也可以包含來自訂儀表板中的顯示的**DisplayName**屬性。 更新**遙測**中繼資料定義下列程式碼片段所示︰

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

以下螢幕擷取畫面顯示此變更如何修改圖表圖例的儀表板上︰

![自訂圖表圖例][image4]

> [AZURE.NOTE] 您可能需要停用，並啟用 Node.js 裝置以查看的變更會立即儀表板中的 [**裝置**] 頁面上。

## <a name="filter-the-telemetry-types"></a>篩選遙測類型

根據預設，在儀表板上的圖表會顯示每個資料數列，遙測資料流中。 您可以使用的**裝置資訊**中繼資料的圖表上的特定遙測類型顯示。 

若要讓圖表顯示只溫度和濕度遙測，省略**ExternalTemperature**從**裝置資訊****遙測**中繼資料，如下所示︰

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**戶外溫度**不會顯示在圖表上︰

![篩選遙測儀表板上][image5]

這項變更只會影響圖表顯示。 **ExternalTemperature**資料值仍會儲存，並提供進行任何的後端處理。

> [AZURE.NOTE] 您可能需要停用，並啟用 Node.js 裝置以查看的變更會立即儀表板中的 [**裝置**] 頁面上。

## <a name="handle-errors"></a>處理錯誤

若要在圖表上顯示資料流，其**裝置資訊**中繼資料中的**輸入**必須符合遙測值的資料類型。 例如，如果中繼資料指定濕度資料**類型**是**int** **雙引號**中找不到遙測資料流然後濕度遙測不會顯示在圖表上。 不過，**濕度**值仍儲存並提供進行任何的後端處理。

## <a name="next-steps"></a>後續步驟

現在，您已經看到如何使用動態遙測，您可以進一步瞭解預先設定的解決方案使用裝置資訊的方式︰[裝置中遠端的資訊中繼資料監視預先設定的解決方案][lnk-devinfo]。

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
