<properties
 pageTitle="裝置中遠端監視解決方案的資訊中繼資料 |Microsoft Azure"
 description="Azure IoT 描述預先設定的解決方案遠端監控和其架構。"
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
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>裝置中遠端監視預先設定的解決方案的資訊中繼資料

遠端監視預先設定的解決方案 Azure IoT 套件示範管理裝置中繼資料的方法。 本文概述此方案所需的時間，讓您瞭解的方法︰

- 哪些裝置中繼資料儲存方案。
- 解決方案管理裝置中繼資料的方式。

## <a name="context"></a>內容

遠端監視預先設定的解決方案使用[Azure IoT 中心][lnk-iot-hub]若要啟用您的裝置傳送資料至雲端。 IoT 中心包含[裝置身分識別登錄][lnk-identity-registry]控制存取權 IoT 中心。 遠端監控儲存裝置資訊中繼資料的解決方案的特定*裝置登錄*的是 IoT 中心裝置身分識別登錄機碼。 遠端監控方案使用[DocumentDB] [lnk-docdb]實作其裝置登錄儲存裝置資訊中繼資料的資料庫。 [Microsoft Azure IoT 參考架構][lnk-ref-arch]說明裝置登錄一般 IoT 方案中的角色。

> [AZURE.NOTE] 遠端監視預先設定的解決方案保持裝置身分識別登錄裝置登錄同步。 同時，可唯一識別每個裝置連接到您 IoT 集線器使用相同的裝置識別碼。

[IoT 中心裝置管理預覽][ lnk-dm-preview] IoT 集線器本文所述的裝置資訊管理功能類似的新增功能。 目前，遠端監視解決方案只會在 IoT 中心會使用推出 (GA) 功能。

## <a name="device-information-metadata"></a>裝置資訊中繼資料

裝置資訊的中繼資料 JSON 文件儲存在裝置登錄 DocumentDB 資料庫具有下列結構︰

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**︰ 裝置本身將這些屬性，並將裝置是此資料的授權。 其他範例裝置屬性包括製造商、 型號、 和序列值。 
- **DeviceID**︰ 唯一裝置識別碼。 此值會 IoT 中心裝置身分識別登錄中相同。
- **HubEnabledState**︰ 在 IoT 中心裝置的狀態。 此值一開始會設定為**null** ，直到第一次連接裝置。 在 [方案] 入口網站中**null**值表示為裝置正在 「 註冊，但是不存在。 」
- **CreatedTime**︰ 建立裝置的時間。
- **DeviceState**︰ 狀態報告的裝置。
- **UpdatedTime**︰ 透過解決方案入口網站上次更新裝置的時間。
- **SystemProperties**︰ 解決方案入口網站將系統內容，並將裝置並不會知道這些屬性。 範例系統屬性是**ICCID**如果解決方案的權限與，連線到服務管理 SIM 功能的裝置。
- **命令**︰ 的命令清單裝置支援。 裝置提供這項資訊至的方案。
- **CommandHistory**︰ 傳送至裝置的和狀態的命令遠端監視解決方案的命令清單。
- **IsSimulatedDevice**︰ 識別為模擬裝置在裝置的標幟。
- **識別碼**︰ DocumentDB 的唯一識別碼此裝置文件。

> [AZURE.NOTE] 裝置資訊也可以包含描述裝置傳送到 IoT 集線器遙測的中繼資料。 遠端監控方案可使用此遙測中繼資料自訂儀表板的[動態遙測]的顯示方式[lnk-dynamic-telemetry]。

## <a name="lifecycle"></a>生命週期

當您第一次解決方案入口網站中建立裝置時，方案會建立一個項目在其裝置登錄先前所示。 其中許多的資訊最初截短及**HubEnabledState**設定為**null**。 此時，方案也會建立一個項目裝置在裝置的身分識別登錄中，會產生裝置用來驗證 IoT 中樞的按鍵。

當裝置第一次連線至的方案時，它會傳送裝置資訊訊息。 此裝置資訊訊息包含裝置的內容，例如裝置製造商、 型號、 和序列值。 裝置資訊訊息也包含裝置支援包括任何命令參數的相關資訊的命令清單。 當方案收到這則訊息時，它就會更新裝置登錄中的裝置資訊中繼資料。

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>檢視及編輯裝置解決方案入口網站中的資訊

解決方案入口網站中的 [裝置] 清單會顯示下列裝置屬性做為資料行︰**狀態**、 **DeviceId**、**製造商**、**型號**、**序列**、**韌體**、**平台**、**處理器**及**安裝的 RAM**。 儀表板上的裝置屬性**緯度**和**經度**磁碟機中 Bing 地圖服務的位置。 

![裝置的清單][img-device-list]

如果您在方案入口網站中的 [**裝置詳細資料**] 窗格中按一下 [**編輯**]，您可以編輯所有這些屬性。 編輯這些屬性更新的記錄，DocumentDB 資料庫中的裝置。 不過，如果在裝置傳送更新的裝置的狀態訊息，它會覆寫任何方案入口網站中所做的變更。 因為只裝置所擁有權限透過這些屬性，您無法編輯**DeviceId**、**主機名稱**、 **HubEnabledState**、 **CreatedTime**、 **DeviceState**及**UpdatedTime**解決方案入口網站中的內容。

![裝置編輯][img-device-edit]

若要移除您的方案的裝置，您可以使用 [解決方案] 入口網站。 當您移除裝置時，的解決方案解決方案裝置登錄機碼會移除裝置資訊中繼資料，並移除 IoT 中心裝置身分識別登錄中的裝置項目。 您可以移除裝置之前，您必須停用。

![裝置移除][img-device-remove]

## <a name="device-information-message-processing"></a>裝置資訊訊息處理

裝置裝置傳送的資訊訊息是有別於遙測郵件。 裝置資訊郵件都包含裝置內容、 裝置可以回覆的命令和任何命令歷程記錄等資訊。 IoT 中心本身並不會知道裝置資訊訊息中所包含的中繼資料，並以相同的方式處理的所有裝置至雲端郵件都處理的訊息。 在遠端監控方案[Azure 資料流分析][ lnk-stream-analytics] (ASA) 工作會從 IoT 中心讀取的郵件。 **DeviceInfo**串流分析工作的郵件包含篩選**」 Save 」: 「 DeviceInfo 」**並轉接至執行中的 web 工作的**EventProcessorHost**主機執行個體。 **EventProcessorHost**執行個體中的邏輯使用的裝置識別碼來尋找特定裝置的 DocumentDB 記錄和更新記錄。 裝置登錄記錄現在包含例如裝置內容、 命令和命令歷程記錄的資訊。

> [AZURE.NOTE] 裝置資訊訊息是標準的裝置至雲端訊息。 解決方案區別裝置資訊訊息及遙測訊息使用 ASA 查詢。

## <a name="example-device-information-records"></a>範例裝置資訊記錄

遠端監視預先設定的解決方案使用兩種類型的裝置資訊記錄︰ 模擬裝置部署解決方案與您連線至的方案自訂裝置的記錄的記錄。

### <a name="simulated-device"></a>模擬的裝置

下列範例會顯示在模擬裝置的 JSON 裝置資訊記錄。 這筆記錄的值為**UpdatedTime**，亦即指出裝置所傳送**DeviceInfo**訊息到 IoT 集線器。 記錄包含一些常見的裝置內容、 定義六命令模擬的裝置支援，具有**IsSimulatedDevice**標幟設為**1**。

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>自訂裝置

下列範例會顯示在自訂裝置的 JSON 裝置資訊記錄，而且有**IsSimulatedDevice**旗標設定為**0**。 您可以看見此自訂裝置支援的兩個命令和解決方案入口網站已傳送至裝置的**SetTemperature**命令︰

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

下列範例顯示 JSON **DeviceInfo**訊息傳送至更新裝置資訊中繼資料的裝置︰

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>後續步驟

現在您已完成的學習方式，您可以自訂的預先設定的解決方案，您可以瀏覽的一些其他功能和預先設定的 IoT 套件解決方案的功能︰

- [預先設定的預測維護解決方案概觀][lnk-predictive-overview]
- [常見問題集 IoT 套件][lnk-faq]
- [從頭 IoT 安全性][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
