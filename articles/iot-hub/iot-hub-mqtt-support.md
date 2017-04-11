<properties
 pageTitle="IoT 中心 MQTT 支援 |Microsoft Azure"
 description="描述 MQTT 支援 IoT 中心層級"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>IoT 中心 MQTT 支援

IoT 中心啟用裝置通訊與使用[MQTT v3.1.1] IoT 中心裝置端點[lnk-mqtt-org]連接埠 8883 或 MQTT v3.1.1 WebSocket 通訊協定連接埠 443 的通訊協定。 IoT 中心需要使用 SSL/SSL 受到保護的所有裝置通訊 （因此，IoT 中心不支援不安全連線連接埠 1883年）。

## <a name="connecting-to-iot-hub"></a>連線到 IoT 中心

裝置可以連線到 IoT 集線器，請使用[Microsoft Azure IoT Sdk]文件庫使用 MQTT 通訊協定[lnk-device-sdks]或使用 MQTT 通訊協定直接。

## <a name="using-the-device-client-sdks"></a>使用裝置用戶端 Sdk

[裝置用戶端 Sdk] [lnk-device-sdks]支援 MQTT 通訊協定可供 Java、 Node.js、 C、 C# 和 Python。 裝置用戶端 Sdk 會使用標準 IoT 中心連接字串建立 IoT 中樞的連線。 若要使用的 MQTT 通訊協定，用戶端通訊協定參數必須設為**MQTT**。 根據預設，Sdk 連接到 IoT 集線器**CleanSession**與裝置用戶端會加上標幟設定為**0** ，並使用與 IoT 中樞的郵件交換**QoS 1** 。

當裝置連接到 IoT 集線器時，裝置用戶端 Sdk 會提供方法，讓郵件從傳送和接收郵件 IoT 中樞的裝置。

下表包含每個支援語言的程式碼範例的連結，並指定要使用來連線到 IoT 中心使用 MQTT 通訊協定的參數。

| 語言                   | 通訊協定參數        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure iot-裝置 mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>從 AMQP 升級裝置應用程式，以 MQTT
如果您使用的[裝置用戶端 Sdk][lnk-device-sdks]，從使用 AMQP 至 MQTT 切換需要變更在用戶端初始化通訊協定參數，如上述。

當這麼做，請確認核取下列項目︰

* AMQP 會傳回錯誤狀況，而 MQTT 終止連線。 如此一來您處理邏輯的例外狀況可能需要一些變更。
* MQTT 不支援*拒絕*作業時收到[C2D 訊息][lnk-messaging]。 如果您的後端需要從裝置應用程式收到的回應，請考慮使用[直接方法][lnk-methods]。

## <a name="using-the-mqtt-protocol-directly"></a>直接使用 MQTT 通訊協定

如果裝置無法使用的裝置用戶端 Sdk，它仍可以連線至公用裝置端點使用 MQTT 通訊協定。 在 [**連線**封包裝置應該使用下列的值︰

- 如需**ClientId** ] 欄位中，使用**deviceId**。 
- [**使用者名稱**] 欄位中，使用`{iothubhostname}/{device_id}`，其中 {iothubhostname} IoT 中樞的完整 CName。

    例如， **contoso.azure devices.net** IoT 中心名稱時，如果您的裝置名稱**MyDevice01**應該包含完整的**Username**功能變數`contoso.azure-devices.net/MyDevice01`。

- [**密碼**] 欄位中，使用 SA 權杖。 SA 權杖的格式是與 HTTP 和 AMQP 通訊協定相同︰<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    如需有關如何產生 SA 權杖的詳細資訊，請參閱 [裝置] 區段的[使用 IoT 中心安全性權杖][lnk-sas-tokens]。
    
    測試時，您也可以使用[裝置總管][lnk-device-explorer]工具，以快速產生 SA 權杖，您可以複製並貼到您自己的程式碼︰
    
    1. 移至裝置的檔案總管中的 [**管理**] 索引標籤。
    2. 按一下 [ **SA Token** （右上角）。
    3. 在**SASTokenForm**，選取下拉式清單中**DeviceID**您的裝置上。 設定您的**[TTL]**。
    4. 按一下 [**產生**]，建立您的憑證。
    
    所產生的 SA token 此結構︰   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`。

    使用 MQTT 連線的 [**密碼**] 欄位會使用此 token 加的部分︰   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`。

MQTT 的連線中斷連線封包，IoT 中心可協助您疑難排解連線問題的其他資訊及問題**作業監控**頻道上的事件。

### <a name="sending-messages-to-iot-hub"></a>傳送訊息給 IoT 中心

成功連接之後，在裝置可以傳送郵件給 IoT 中心使用`devices/{device_id}/messages/events/`或`devices/{device_id}/messages/events/{property_bag}`為**主題的名稱**。 `{property_bag}`元素可讓裝置 url 編碼格式傳送郵件的其他屬性。 例如︰

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] 此`{property_bag}`項目使用相同的編碼方式與在 HTTP 通訊協定的查詢字串。

裝置用戶端應用程式也可以使用`devices/{device_id}/messages/events/{property_bag}`為**將主題名稱**以定義*將郵件*轉寄為遙測訊息。

IoT 中心不支援 QoS 2 的郵件。 如果裝置用戶端發佈**QoS 2**的訊息，IoT 中心關閉的網路連線。
IoT 中心已不存在保留郵件。 如果裝置會傳送一封郵件**保留**旗標設定為 1，IoT 中心新增**x-選擇-保留**的郵件應用程式屬性。 在此情況下，而非保存保留郵件，IoT 中心傳遞至後端應用程式。

### <a name="receiving-messages"></a>接收郵件

若要從 IoT 中心收到的郵件，請在裝置應該訂閱使用`devices/{device_id}/messages/devicebound/#`**主題篩選**。 多層次萬用字元**#**篩選的主題只會用於允許裝置接收主題名稱中的其他屬性。 IoT 中心不允許的使用方式**#**或**？** 萬用字元副主題的篩選。 由於 IoT 中心不一般用途發行 sub 訊息代理人，只支援的說明的主題名稱 」 和 「 主題篩選。

請注意的裝置不會收到任何訊息的 IoT] 中心內，先將其已成功訂閱其裝置的特定結束點，表示`devices/{device_id}/messages/devicebound/#`主題篩選。 成功訂閱已建立之後，會啟動裝置，接收所訂閱的時間後傳送至該雲端-裝置訊息。 如果裝置連接**CleanSession**旗標設為**0**，訂閱會保留在不同的工作階段中。 在此情況下，與**CleanSession 0**連接裝置時，會收到未完成的郵件時，已中斷連線已傳送至該。 如果裝置使用**CleanSession**標幟設為**1** ，但它不會收到任何訊息從 IoT 中心鍵，直到其裝置結束點，而定。

IoT 中心傳遞郵件**主題名稱** `devices/{device_id}/messages/devicebound/`，或`devices/{device_id}/messages/devicebound/{property_bag}`如果有任何郵件內容。 `{property_bag}`包含郵件內容的 url 編碼金鑰/值的組。 屬性包中包含只應用程式內容和使用者可設定系統內容 （例如**訊息**或**相互關聯識別碼**）。 系統屬性名稱有前置詞**$**，應用程式屬性沒有前置字元的使用原始的屬性名稱。

當裝置用戶端與**QoS 2**主題而定時，IoT 中心授與**SUBACK**封包中最大的 QoS 階層 1。 之後，IoT 中心會將郵件傳送到使用 QoS 1 的裝置。

### <a name="additional-considerations"></a>其他考量

為完稿的考量，如果您需要自訂 MQTT 通訊協定行為位於雲端，您應該檢閱[Azure IoT 通訊協定閘道器][ lnk-azure-protocol-gateway] ，可讓您部署高效能自訂通訊協定的閘道器之直接與 IoT 中樞的介面。 Azure IoT 通訊協定閘道器可讓您自訂以容納 brownfield MQTT 部署的裝置通訊協定或其他自訂的通訊協定。 不過，此方法均會需要執行並操作自訂通訊協定的閘道器。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[筆記 MQTT 支援][ lnk-mqtt-devguide] Azure IoT 中心開發人員指南中。

若要進一步瞭解 MQTT 通訊協定，請參閱[MQTT 文件][lnk-mqtt-docs]。

若要進一步瞭解規劃 IoT 中心部署，請參閱︰

- [Azure 已認證 IoT 裝置目錄][lnk-devices]
- [支援額外的通訊協定][lnk-protocols]
- [事件集線器與比較][lnk-compare]
- [縮放、 HA 和 DR][lnk-scaling]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
