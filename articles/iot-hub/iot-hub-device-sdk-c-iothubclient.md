<properties
    pageTitle="Azure IoT 裝置 SDK C-IoTHubClient |Microsoft Azure"
    description="進一步瞭解使用 IoTHubClient 文件庫中的 Azure IoT 裝置 SDK 的 C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Microsoft Azure IoT 裝置 SDK C – 更多關於 IoTHubClient 資訊

本系列的[第一篇文章](iot-hub-device-sdk-c-intro.md)推出**Microsoft Azure IoT 裝置 SDK C**。該文章說明 SDK 中有兩個架構圖層。 基底是直接管理與 IoT 中樞的通訊**IoTHubClient**文件庫。 此外，還有建置上面提供序列化服務**序列化**文件庫。 在本文中，我們將提供其他詳細資料**IoTHubClient**文件庫。

前一篇文章會說明如何使用事件 IoT 中心來收發郵件**IoTHubClient**文件庫。 本文會說明如何更精確地管理*當*您傳送和接收資料至**較低層級 Api**介紹您擴充的討論。 我們也會說明如何事件中附加屬性 （和從郵件擷取） 使用屬性處理**IoTHubClient**文件庫中的功能。 最後，我們將提供其他說明的不同方式處理從 IoT 中心收到郵件。

本文結束的幾個其他的主題，包括更多關於裝置的認證，以及如何變更行為的**IoTHubClient**透過設定選項。

我們將使用**IoTHubClient** SDK 範例說明這些主題。 如果您想要邊，請參閱**iothub\_用戶端\_範例\_http**和**iothub\_用戶端\_範例\_amqp**如下列範例示範 c 所有項目下列各節所述的 Azure IoT 裝置 SDK 中所包含的應用程式。

您可以將[Microsoft Azure IoT Sdk](https://github.com/Azure/azure-iot-sdks) GitHub 存放庫內尋找**Azure IoT 裝置 SDK C** ，並檢視[C API 參照](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html)中的 API 的詳細資料。

## <a name="the-lower-level-apis"></a>較低層級 Api

前一篇文章會描述**IotHubClient**的內容中的基本作業**iothub\_用戶端\_範例\_amqp**應用程式。 例如，它會說明如何初始化使用此程式碼的文件庫。

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

它也會說明如何傳送使用此函數呼叫的事件。

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

本文也說明如何註冊的回撥函數接收郵件。

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

本文也說明如何免費使用下列程式碼資源。

```
IoTHubClient_Destroy(iotHubClientHandle);
```

但是有這些 Api 的每個小幫手函數︰

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_損毀


所有這些函數的 API 名稱中包含 「 LL 」。 以外，每個這些函數的參數是相同，其非 LL 有何差異。 不過，這些函數的行為是不同的其中一個重要的方法。

當您呼叫**IoTHubClient\_CreateFromConnectionString**，基礎的文件庫中建立新的執行緒的背景中執行。 此執行緒事件，由傳送和接收郵件，IoT 中心。 使用 「 LL 」 Api 時，會建立沒有這類執行緒。 背景執行緒建立是開發人員方便。 您不必擔心明確事件傳送和接收郵件從 IoT 中心，它會自動在背景中。 相反地，「 LL 」 Api 讓您明確控制 IoT] 中心內，與通訊如果您需要它。

若要進一步瞭解此，讓我們來看看範例︰

當您呼叫**IoTHubClient\_SendEventAsync**，您實際上的動作會置於事件緩衝。 當您撥打時所建立的背景執行緒**IoTHubClient\_CreateFromConnectionString**持續監視此緩衝，並將其包含的任何資料傳送到 IoT 中心。 發生這種情況在背景中同時主執行緒正在執行其他工作。

同樣地，當您註冊使用郵件回撥函數**IoTHubClient\_SetMessageCallback**，您有一則訊息時收到的主要執行緒獨立叫用回撥函數的背景執行緒 SDK 的指示。

「 LL 」 Api 沒有建立背景執行緒。 不過，必須呼叫新的 API 明確傳送和接收 IoT 中心中的資料。 下列範例所示。

**Iothub\_用戶端\_範例\_http** SDK 中所包含的應用程式會示範較低層級 Api。 在這個範例中，我們傳送事件到 IoT 集線器程式碼，如下所示︰

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

前三個線條建立郵件，並在最後一行傳送事件。 不過，如先前所述，「 傳送 「 事件表示的資料僅位於緩衝。 不在網路上時傳輸我們稱為 「 **IoTHubClient\_LL\_SendEventAsync**。 實際輸入資料以 IoT 中樞的順序，您必須撥打**IoTHubClient\_LL\_DoWork**，在此範例中︰

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

將此程式碼 (從**iothub\_用戶端\_範例\_http**應用程式) 重複呼叫**IoTHubClient\_LL\_DoWork**。 每次**IoTHubClient\_LL\_DoWork**是稱為，會從緩衝傳送某些事件到 IoT 集線器和擷取佇列的訊息傳送至裝置。 後者表示如果我們註冊回撥函數的訊息，然後回撥叫用 （假設任何郵件佇列）。 我們會使用下列程式碼註冊回撥函數︰

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

原因的**IoTHubClient\_LL\_DoWork**通常稱為循環是稱為每次、 其傳送給 IoT 中樞的*一些*緩衝事件和擷取*下一步*佇列裝置。 每個呼叫不確定要傳送所有緩衝的事件或來擷取所有佇列中的郵件。 如果您想要傳送的所有事件緩衝，然後繼續與其他處理此循環播放取代下列程式碼︰

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

將此程式碼通話**IoTHubClient\_LL\_DoWork**直到緩衝中的所有事件已都傳送至 IoT 中心。 請注意，這不也表示已接收所有佇列的訊息。 部分是原因的，檢查 「 所有 」 的訊息不為確定動作。 如果擷取 「 所有 」 的訊息，但然後另一個傳送到裝置時，會發生什麼情況緊接？ 更好的方式處理所使用程式化的逾時。 例如，郵件回撥函數無法重設計時器，每次叫用它。 然後，您可以撰寫進行處理，如果沒有郵件，例如收到最後一個*X*秒數的邏輯。

當您完成的 ingressing 事件而且接收訊息，請務必 call 清除資源相對應的函數。

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

基本上是只有一組 Api 傳送及接收背景執行緒與另一組所執行的動作而背景執行緒的 Api 的資料。 開發人員許多可能會想非-LL Api，但在較低層級 Api 實用開發人員想明確控制網路傳輸。 例如，某些裝置資料收集段時間，並只輸入事件在指定的時間間隔 （例如，每小時一次或一天）。 較低層級 Api 可讓您將其他控制項時傳送和接收 IoT 中心中的資料。 其他人只會偏好較低層級 Api 提供的簡單。 所有項目，這時主執行緒，而不是在背景中的某些工作發生。

您選擇，不論模型請確定您使用的 Api 一致。 如果您啟動，則可電話**IoTHubClient\_LL\_CreateFromConnectionString**，請確定您只使用任何待處理工作的相對應的較低層級 Api:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_損毀

-   IoTHubClient\_LL\_DoWork

相反也是如此。 如果您以開始**IoTHubClient\_CreateFromConnectionString**，然後使用任何其他處理非 LL 的 Api。

Azure IoT 裝置 SDK 的 C，請參閱**iothub\_用戶端\_範例\_http**應用程式的較低層級 Api 完成完整的範例。 **Iothub\_用戶端\_範例\_amqp**可以參考的完整的範例的非-LL Api 的應用程式。

## <a name="property-handling"></a>內容處理

目前我們已所傳送的資料，我們已參照至郵件本文。 例如，請考慮將此程式碼︰

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

此範例中傳送訊息給 IoT 中樞的文字"Hello World 」。 不過，IoT 中心也可讓屬性]，附加至每一封郵件。 屬性是可附加至郵件的名稱/值組。 例如，我們可以修改先前的程式碼的郵件中附加屬性︰

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

我們開始，則可電話**IoTHubMessage\_屬性**並將它傳遞訊息的控點。 什麼我們回頭是**地圖\_處理**讓我們開始新增內容的參照。 後者透過呼叫**地圖\_AddOrUpdate**，其會對應的參考\_控點與屬性名稱，屬性值。 使用這個 API 我們可以新增任何數量的屬性，我們要。

從**事件集線器**讀取事件時，收件者可以列舉屬性，並擷取其對應的值。 例如，.net 這會可藉由存取[EventData 物件的 Properties 集合](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx)。

在上一個範例中，我們要附加屬性我們傳送給 IoT 中樞的事件。 也可以從 IoT 中心收到的郵件附加屬性。 如果我們想要從郵件擷取內容，我們可以使用下列程式碼，我們郵件回撥函數中︰

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

通話**IoTHubMessage\_屬性**傳回**地圖\_處理**參照。 我們然後將該參照**地圖\_GetInternals**以取得名稱/值組 （以及屬性的計數） 的陣列的參照。 此時，則簡單的列舉屬性以取得我們想要的值。

您沒有使用應用程式中的屬性。 不過，如果您需要設定事件或從郵件擷取， **IoTHubClient**文件庫可輕鬆。

## <a name="message-handling"></a>郵件處理

當郵件到達時從 IoT 中心之前所述的**IoTHubClient**文件庫的回應叫用已註冊的回撥功能。 沒有傳回值得一些額外的說明此函數的參數。 以下是中回撥函數摘錄**iothub\_用戶端\_範例\_http**範例應用程式︰

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

請注意，則傳回類型**IOTHUBMESSAGE\_處理\_結果**和在此案例中我們傳回**IOTHUBMESSAGE\_接受**。 有變更的**IoTHubClient**文件庫如何反應郵件回撥其他值我們可以從這個函數傳回。 以下是選項]。

-   **IOTHUBMESSAGE\_接受**– 成功處理郵件。 **IoTHubClient**文件庫不會叫用相同的訊息一次的回撥函數。

-   **IOTHUBMESSAGE\_已拒絕**– 未處理郵件，並有不想要在未來執行此作業。 **IoTHubClient**文件庫不應該叫用相同的訊息一次的回撥函數。

-   **IOTHUBMESSAGE\_已放棄**– 不成功，處理郵件，但**IoTHubClient**文件庫應該叫用相同的訊息一次的回撥函數。

前兩個傳回代碼， **IoTHubClient**文件庫傳送訊息給 IoT 中心指示應從裝置佇列中刪除郵件，且未傳送一次。 最後的結果相同 （則會刪除郵件從裝置佇列中），但仍會記錄是否接受或拒絕郵件。  錄製此區別依然相當實用寄件者的郵件可以聆聽的意見反應，並找出在裝置是否接受或拒絕特定的郵件。

最後的大小寫郵件同時送到 IoT] 中心內，但它會指出應該重新傳遞郵件。 通常您會放棄一則訊息，如果您遇到一些錯誤，但想要嘗試再次處理郵件。 相反地，拒絕郵件適合，當您遇到無法復原的錯誤 （或如果您只要決定您不想要處理訊息）。

在任何情況下，請注意不同傳回的程式碼，好讓您可以引發**IoTHubClient**文件庫中您要的行為。

## <a name="alternate-device-credentials"></a>其他裝置的認證

與先前所述，時使用**IoTHubClient**文件庫的第一個項目會以取得**IOTHUB\_用戶端\_處理**呼叫如下所示︰

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

引數**IoTHubClient\_CreateFromConnectionString**是我們裝置和參數，指出通訊協定我們使用與 IoT 中樞通訊的連接字串。 連接字串具有會顯示，如下所示的格式︰

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

有四個部分這個字串中的資訊︰ IoT 中心名稱、 IoT 中心後置字元、 裝置識別碼及共用的便捷鍵。 當您 Azure 入口網站中建立您的 IoT 中心執行個體，IoT 中樞的取得的完整的網域名稱 (FQDN)，這可讓您 IoT 中心名稱 （FQDN 的第一個部分） 及 IoT 中心後置字元 （的剩餘部分 FQDN）。 當您註冊您的裝置與 IoT 中心 （如下所述[前一篇文章](iot-hub-device-sdk-c-intro.md)） 取得裝置識別碼和共用便捷鍵。

**IoTHubClient\_CreateFromConnectionString**可讓您初始化文件庫的其中一個方法。 如果您想要的話，您可以建立新的**IOTHUB\_用戶端\_處理**使用這些個別的參數，而不是連線字串。 這被達成下列程式碼︰

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

這會花一樣**IoTHubClient\_CreateFromConnectionString**。

很明顯您想要使用**IoTHubClient\_CreateFromConnectionString**而不是更詳細的初始化方法。 請記住，不過，當您在 IoT 中心註冊裝置，您所取得是一個裝置識別碼與裝置金鑰 （不連接字串）。 在[前一篇文章](iot-hub-device-sdk-c-intro.md)**裝置管理員**SDK 工具會使用文件庫中**Azure IoT 服務 SDK**若要從裝置識別碼、 裝置，以及 IoT 中心主機名稱建立連線字串。 因此呼叫**IoTHubClient\_LL\_建立**可能是最好，因為它會將儲存您的步驟，產生的連線字串。 使用方便的方式。

## <a name="configuration-options"></a>設定選項

到目前為止的**IoTHubClient**文件庫的運作的方式的相關說明的所有項目會反映出其預設行為。 然而，有幾個選項可以設定變更文件庫的運作方式。 這透過運用**IoTHubClient\_LL\_高效**API。 請考慮此範例中︰

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

有幾項常用的選項︰

-   **SetBatching**(bool) – 如果批次傳送**，則為 true**，然後傳送到 IoT 中樞的資料。 如果**為 false**，則郵件會傳送個別。 預設為**false**。 請注意， **SetBatching**選項僅適用於 HTTP 通訊協定，而不要到 MQTT 或 AMQP 通訊協定。

-   **逾時**(未簽署的 int) – 此值以毫秒為單位表示。 如果傳送 HTTP 要求或收到回應時間長於這次，然後連接逾時。

請務必批次的選項。 根據預設，文件庫 ingresses 事件個別 (單一事件是您傳遞給觀眾**IoTHubClient\_LL\_SendEventAsync**)。 批次的選項**，則為 true**時，文件庫會收集，可以從 （最多郵件大小上限 IoT 中心就會接受） 緩衝的事件。  事件批次傳送給 IoT 中心單一 HTTP 呼叫 （個別的事件會結合在一起 JSON 陣列中）。 由於您減少網路往返啟用批次處理通常會顯示較大的效能提升。 它也會大幅降低頻寬，因為您要傳送一組與事件批次的 HTTP 標頭，而不一組的標頭，為每個個別的事件。 除非您有特定的理由，否則通常是您需要啟用批次。

## <a name="next-steps"></a>後續步驟

本文將說明的詳細資料的行為**Azure IoT 裝置 SDK C** **IoTHubClient**文件庫。此資訊，您應該瞭解的**IoTHubClient**文件庫的功能。 [下一篇文章](iot-hub-device-sdk-c-serializer.md)提供**序列化**文件庫類似的詳細資料。

若要進一步瞭解開發 IoT 集線器，請參閱[IoT 中心 Sdk][lnk-sdks]。

若要進一步探索 IoT 中樞的功能，請參閱︰

- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
