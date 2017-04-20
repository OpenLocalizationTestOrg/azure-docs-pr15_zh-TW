> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)

[模擬的裝置雲端上傳範例]的這個逐步解說示範如何使用[Azure IoT 閘道 SDK] [lnk-sdk]將傳送至 IoT Hub 的裝置-雲端遙測從模擬的裝置。

此逐步解說涵蓋：

1. **架構**： 模擬裝置雲端上傳範例架構重要資訊。

2. **建置和執行**︰ 建置和執行範例所需的步驟。

## <a name="architecture"></a>架構

模擬的裝置雲端上傳範例示範如何使用 SDK 來建立其將遙測從模擬裝置傳送至 IoT hub 的閘道。 模擬的裝置無法直接連接到 IoT 集線器因為：

- 裝置不使用瞭解 IoT Hub 的通訊協定。
- 裝置不足夠的智慧記住由 IoT 集線器指派給他們的身分識別。

閘道可透過下列方式解決這些問題模擬裝置：

- 閘道了解模擬的裝置，所使用的通訊協定會裝置-雲端遙測接收從裝置，並將那些郵件轉寄給 IoT Hub 使用瞭解 hub 的通訊協定。
- 閘道代表模擬裝置儲存 IoT Hub 身分識別與模擬的裝置將郵件傳送至 IoT Hub 時作為 proxy 代。

下圖顯示此範例包含閘道模組的主要元件：

![][1]


> [AZURE.NOTE] 模組沒有傳遞任何郵件直接給對方。 模組會將郵件發佈至將郵件傳遞至其他使用訂閱機制下圖所示的模組內部代理人。 如需詳細資訊，請參閱[IoT 閘道 SDK 快速入門][lnk-gw-getstarted]。

### <a name="protocol-ingestion-module"></a>通訊協定擷取模組

這個模組是透過閘道和到雲端，從裝置取得資料的起點。 在範例中，模組會執行四個工作：

1.  它會建立模擬的溫度資料。
    
    注意： 如果您已使用實際的裝置，模組會讀取資料從這些實體的裝置。

2.  它將郵件的內容放置於模擬的溫度資料。

3.  它包含模擬的溫度資料訊息新增假的 MAC 位址屬性。

4.  它提供郵件至下一個模組鏈結中。

> [AZURE.NOTE] 圖中稱為**通訊協定 X 擷取**模組稱為**模擬裝置**中的原始程式碼。

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt; - &gt; IoT Hub 識別碼模組

本單元掃描的郵件包含含有 MAC 位址，模擬裝置 」 的 「 通訊協定擷取模組所新增的屬性。 如果模組發現這類屬性，它將另一個屬性與 IoT Hub 裝置機碼新增至訊息，並使郵件可用的下一個模組鏈結中。 這是如何樣本關聯至 IoT Hub 裝置身分識別模擬的裝置。 開發人員會設定 MAC 位址與對應 IoT Hub 身分識別手動為模組組態的一部分。 

> [AZURE.NOTE]  這個範例使用 MAC 位址為唯一的裝置識別碼並將它與 IoT Hub 裝置 identity 相互關聯。 不過，您可以撰寫您自己使用不同的唯一識別碼的模組。 例如，您可能必須具有唯一的序號或都有唯一裝置名稱內嵌在其可以使用以決定 IoT 中樞裝置 identity 的遙測資料的裝置。

### <a name="iot-hub-communication-module"></a>IoT 中樞通訊模組

這個模組 IoT 中樞與裝置 identity 指派先前模組所採用的郵件和 IoT Hub 使用 HTTP 來傳送的郵件內容。 HTTP 是一種瞭解 IoT Hub 的三個通訊協定。

而不是開啟的連線 IoT Hub 的每個模擬的裝置，這個單元中從閘道到 IoT hub 開啟單一的 HTTP 連線，並透過該連線 multiplexes 模擬的所有裝置的連線。 這可讓單一閘道連線許多多個裝置，模擬或否則比就是可能如果它開啟每個裝置的唯一連線。

![][2]


<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[模擬的裝置雲端上傳範例]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md