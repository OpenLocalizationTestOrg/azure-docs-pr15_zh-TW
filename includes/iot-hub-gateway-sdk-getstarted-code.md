## <a name="typical-output"></a>一般輸出

下面是由 Hello World 範例寫入記錄檔輸出範例。 已新增兼具和 Tab 字元的可讀性：

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>程式碼片段 （英文)

本章節將討論 Hello World 範例中的程式碼的一些重要部分。

### <a name="gateway-creation"></a>閘道建立

開發人員必須撰寫*閘道程序*。 此程式會建立內部的基礎結構 （代理人）、 載入模組，並將每個項目設定以正常運作。 Sdk （英文） 提供之**Gateway_Create_From_JSON**函數可讓您啟動載入 JSON 檔案的閘道。 若要使用您必須將其傳路徑至指定的模組載入 JSON 檔案**Gateway_Create_From_JSON**函數。 

您可以找出閘道的程序中 Hello World 樣本中[main.c]代碼[lnk-main-c]檔案。 可讀性，如下列程式碼片段會顯示閘道程序程式碼簡短的版本。 此程式建立閘道和則等候使用者按下**ENTER**鍵之前它畢閘道。 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

JSON 設定檔包含模組載入的清單。 每個模組必須指定 a：

- **module_name**︰ 模組的唯一名稱。
- **module_path**： 路徑包含模組的文件庫。 針對 Linux 這是.so 檔案，這是在 windows.dll 檔案。
- **args**︰ 模組需要的任何組態資訊。

JSON 檔案也包含會傳遞至代理人的模組之間的連結。 連結有兩個屬性：
- **來源**： 從模組名稱`modules`] 區段中，或"\*"。
- **接收**： 從模組名稱`modules`區段

每個連結會定義郵件路由及方向。 郵件從模組`source`會傳遞至模組`sink`。 `source`可能會設為"\*"，指出將會由收到郵件從任何模組`sink`。

下列範例會顯示用來設定 Linux 上的 Hello World 樣本的 JSON 設定檔案。 每封郵件所產生的模組`hello_world`將模組所耗用`logger`。 是否模組需要引數而定模組的設計。 在本例中為記錄器模組會採用引數是輸出檔案的路徑和 [Hello World] 模組不採用任何引數：

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>[Hello World 模組郵件發佈

您可以找到"hello world"模組用來將郵件發佈['hello_world.c']中的程式碼[lnk-helloworld-c]檔案。 下列程式碼片段顯示修改過的版本及其他註解和一些錯誤處理程式碼的可讀性移除：

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>[Hello World 模組郵件處理

[Hello World] 模組永遠不需要處理任何其他模組來 broker 發佈的訊息。 這會使實作訊息回呼的 Hello World 模組中的任何作業函數。

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>發佈] 和 [處理記錄器模組訊息

記錄器模組從 broker 收到的郵件和其寫入檔案。 永遠不會發佈任何訊息。 因此，記錄器模組的程式碼永遠不會呼叫**Broker_Publish**函數。

在[logger.c] **Logger_Recieve**函數[lnk-logger-c]檔案是將郵件傳送至記錄器模組叫用 broker 回撥。 下列程式碼片段顯示修改過的版本及其他註解和一些錯誤處理程式碼的可讀性移除：

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>後續步驟

若要了解如何使用 IoT 閘道 sdk （英文），請參閱下列各項：

- [IoT 閘道 sdk （英文） – 傳送裝置-雲端訊息與模擬裝置使用 Linux][lnk-gateway-simulated]。
- [Azure IoT 閘道 SDK] [ lnk-gateway-sdk] GitHub 上。

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md