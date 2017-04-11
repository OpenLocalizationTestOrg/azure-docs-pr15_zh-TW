<properties
   pageTitle="連線到 Windows 上使用 C 的裝置 |Microsoft Azure"
   description="說明如何將裝置連接到預先設定的 Azure IoT 套件遠端監視解決方案使用撰寫 C 在 Windows 上執行的應用程式。"
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>將您的裝置連線到遠端監視的預先設定解決方案 (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>在 Windows 上建立 C 範例解決方案

下列步驟將告訴您如何使用 Visual Studio 建立撰寫 C 與遠端監控預先設定的解決方案進行通訊的用戶端應用程式。

在 Visual Studio 2015 中建立的起始專案，並新增 IoT 中心裝置用戶端 NuGet 套件︰

1. 在 Visual Studio 2015，建立 C 主控台的應用程式，使用 Visual c + + **Win32 主控台應用程式**範本。 專案**RMDevice**的名稱。

2. 在 [ **Win32 應用程式精靈**中的 [**應用程式設定**] 頁面中，確定**主控台應用程式**已選取，然後取消核取 [**先行編譯標頭**，並**檢查安全性開發週期 (SDL)**。

3. 在**方案總管]**中，刪除檔案 stdafx.h targetver.h，與 stdafx.cpp。

4. 在**方案總管]**中，重新命名檔案 RMDevice.cpp RMDevice.c]。

5. 在**方案總管]**中，請以滑鼠右鍵按一下**RMDevice**專案，，然後按一下 [**管理 NuGet 套件**。 按一下 [**瀏覽]**，然後搜尋並安裝下列 NuGet 封裝到專案︰

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

6. 在**方案總管]**中，請以滑鼠右鍵按一下**RMDevice**專案，，然後按一下 [**屬性**]，開啟專案的 [**頁面屬性**] 對話方塊。 如需詳細資訊，請參閱[設定 Visual c + + 專案屬性][lnk-c-project-properties]。 

7. 按一下**連結器**] 資料夾，然後按一下 [**輸入**的 [屬性] 頁面。

8. 新增**其他**相依性**crypt32.lib** 。 若要將專案儲存屬性值再次按一下**[確定]** ，然後**[確定]** 。

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>指定 IoT 中心裝置的行為

IoT 中樞用戶端文件庫會使用模型，來指定 IoT 中心並收到 IoT 中樞的命令的裝置傳送郵件的格式。

1. 在 Visual Studio 中，開啟 RMDevice.c 檔案。 取代現有的`#include`陳述式下列程式碼︰

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. 新增下列變數宣告之後`#include`陳述式。 取代預留位置值 [裝置識別碼] 和 [裝置鍵] 使用您的裝置，從遠端監控的解決方案儀表板的值。 您可以使用從儀表板 IoT 中心主機名稱來取代 [IoTHub 名稱]。 例如，如果您 IoT 中心 Hostname **contoso.azure devices.net**，取代 [IoTHub 名稱] **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. 新增下列程式碼，定義可讓裝置通訊 IoT 中樞的模型。 此模型指定裝置為遙測溫度、 外部溫度、 濕度和裝置識別碼傳送。 裝置也會傳送到 IoT 集線器，包括裝置支援的命令清單的裝置的相關的中繼資料。 此裝置回應**SetTemperature**和**SetHumidity**的命令︰

    ```
    // Define the Model
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

## <a name="implement-the-behavior-of-the-device"></a>實作裝置的行為

現在新增實作模型中定義的行為程式碼。

1. 新增下列裝置收到**SetTemperature**和**SetHumidity**命令從 IoT 中心時所執行的函數︰

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

2. 新增下列函數的傳送訊息給 IoT 中心︰

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. 新增下列連結 sdk 序列化文件庫的函數︰

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. 新增下列函數連線至 IoT 中心、 傳送和接收訊息，並從中心中斷連線。 請注意裝置如何傳送自己的中繼資料，包括其支援 IoT 集線器連線時的命令。 此中繼資料可讓您的方案，若要在儀表板上的 [**執行**更新裝置的狀態︰

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
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

5. 取代叫用**remote_monitoring_run**函數的下列程式碼的**主要**功能︰

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. 按一下 [**建立**]，然後**建立的解決方案**以建立裝置應用程式]。

7. 在**方案總管]**中，以滑鼠右鍵按一下**RMDevice**專案**偵錯**，，然後按一下然後按一下 [**啟動新的執行個體**的範例。 在應用程式會傳送給 IoT 中樞的範例遙測和接收命令，主控台就會顯示郵件。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx