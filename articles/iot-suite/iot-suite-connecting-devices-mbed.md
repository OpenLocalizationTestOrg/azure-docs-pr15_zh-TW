<properties
   pageTitle="連接裝置上 mbed 使用 C |Microsoft Azure"
   description="說明如何將裝置連接到預先設定的 Azure IoT 套件遠端監視解決方案使用撰寫 C 執行 mbed 裝置上的應用程式。"
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>將您的裝置連線到遠端監視的預先設定解決方案 (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>建立並執行 C 範例方案

下列的指示說明的步驟連線[Mbed 啟用 Freescale FRDM K64F] [lnk-mbed-home]遠端監視解決方案的裝置。

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>將 mbed 裝置連接到您的網路和桌面的電腦

1. 將 mbed 裝置連接到您的網路使用乙太網路纜線。 此步驟，因為範例應用程式需要存取網際網路。

2. 請參閱[快速入門 mbed] [ lnk-mbed-getstarted] mbed 裝置連線到您的桌面電腦。

3. 如果您的桌面電腦執行的 Windows，請參閱[電腦設定][ lnk-mbed-pcconnect] mbed 裝置序列連接埠存取設定。

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>建立 mbed 專案及匯入的範例程式碼

1. 在您網頁瀏覽器中，移至 mbed.org[開發人員網站](https://developer.mbed.org/)。 如果您尚未註冊，您會看到其中一個選項以建立的帳戶 （它是免費的）。 否則，您帳戶認證登入。 在頁面的右上角，然後按一下**編譯器**。 此動作可讓您*工作區*介面。

2. 請確定您使用的硬體平台會出現在視窗的右上角，或按一下以選取您的硬體平台右上角的圖示。

3. 在 [主要] 功能表上，按一下 [**匯入**。 然後按一下 [匯入旁 mbed 地球標誌的 URL 連結的 [**按一下這裡**。

    ![][6]

4. 在快顯視窗中，輸入連結的範例程式碼 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/，然後按一下 [**匯入**]。

    ![][7]

5. 您可以在 mbed 編譯器視窗中看到的匯入專案也匯入各種不同的文件庫。 一些會提供，以及維護 Azure IoT 小組 （[azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/) [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、 [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、 [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)），而有些協力廠商文件庫可以在目錄中 mbed 文件庫。

    ![][8]

6. 開啟 remote_monitoring\remote_monitoring.c 檔案，並在檔案中找出下列程式碼︰

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. 取代 [裝置識別碼] 和 [裝置鍵]，若要啟用範例程式連線至您 IoT 中心裝置資料。 使用 IoT 中心主機名稱取代 [IoTHub 名稱] 和 [IoTHub 後置字元，亦即的 azure devices.net] 版面配置區。 例如，如果**contoso.azure devices.net**您 IoT 中心主機名稱， **contoso**是**hubName**及所有項目後**hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>逐步瞭解程式碼

如果您有興趣程式的運作方式，本節說明主要的部分範例程式碼。 如果您只是要執行的程式碼，跳到[建立並執行程式](#buildandrun)。

#### <a name="defining-the-model"></a>定義模型

此範例使用[序列化][lnk-serializer]文件庫，以定義指定郵件到 IoT 集線器傳送及接收從 IoT 中心裝置的模型。 在此範例中， **Contoso**命名空間定義指定以及中繼資料，例如裝置識別碼、 裝置內容和裝置回應的命令的**溫度** **ExternalTemperature**，與**濕度**遙測資料的**Thermostat**模型︰

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

相關的模型定義的回應裝置的 [ **SetTemperature**及**SetHumidity** ] 命令的定義︰

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>連線至文件庫的模型

函數**sendMessage**和**IoTHubMessage**會從裝置傳送遙測和連線從 IoT 中心的郵件] 命令處理常式的程式碼。

#### <a name="the-remotemonitoringrun-function"></a>Remote_monitoring_run 函數

若要執行為 IoT 中心裝置用戶端裝置的行為的應用程式啟動時，程式的**主**函數叫用**remote_monitoring_run**函數。 函數的巢狀組大多包含此**remote_monitoring_run**函數︰

- **平台\_初始化**和**平台\_deinit**執行特定初始化和關閉作業。
- **序列化\_初始化**和**序列化\_deinit**初始化和解除初始化序列化文件庫。
- **IoTHubClient\_建立**和**IoTHubClient\_損毀**建立用戶端控點， **iotHubClientHandle**，用的裝置認證連線到您的 IoT 集散地。

在 [ **remote_monitoring_run**函式的 [主要] 區段中，程式會執行下列作業使用**iotHubClientHandle**控點︰

- 建立 Contoso thermostat 模型的執行個體，並設定的郵件回撥提供兩個命令。
- 傳送本身，包括其支援到 IoT 集線器使用序列化文件庫的命令裝置的相關資訊。 當中樞收到這則訊息時，它變成儀表板中的裝置狀態從 [**擱置中****執行**。
- 開始傳送溫度、 外部溫度及濕度值 IoT 集線器每秒**時**循環播放。

參照，以下是範例**DeviceInfo**訊息傳送到 IoT 集線器在啟動時︰

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

參照，以下是範例**遙測**訊息傳送至 IoT 中心︰

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

參照，以下是的範例**命令**自 IoT 中心︰

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>建立並執行程式

1. 按一下 [**編譯**建置程式]。 安全地，您可以略過任何警告，但如果建置產生的錯誤]，修正問題，再繼續執行。

2. 如果建立成功，mbed 編譯器網站產生.bin 檔案與您專案的名稱，並將其下載至您的本機電腦。 .bin 將檔案複製到裝置。 將.bin 檔案儲存至裝置會造成重新啟動和執行程式.bin 檔案中所包含的裝置。 您可以手動重新啟動程式隨時按 mbed 裝置上的 [重設] 按鈕。

3. 連線至使用 SSH 用戶端應用程式，例如 PuTTY 的裝置。 您可以判斷您的裝置，請核取 Windows 裝置管理員使用的循序連接埠。

    ![][11]

4. 在 [PuTTY，**循序**連線類型。 裝置通常會 9600 傳輸連線，請在 [**速度**] 方塊中輸入 9600。 然後，請按一下 [**開啟**]。

5. 程式開始執行。 您可能要重設白板 （按下 CTRL + 符號，或按下區的 [重設] 按鈕） 如果程式不會自動啟動您的連線時。

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
