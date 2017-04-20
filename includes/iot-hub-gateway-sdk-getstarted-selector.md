> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

本文提供的[Hello World 範例程式碼]的詳細逐步解說[lnk-helloworld-sample]為了說明[Azure IoT 閘道 sdk （英文)]的基本元件[lnk-gateway-sdk]架構。 該範例會使用 IoT Hub 閘道 SDK 來建立簡單的閘道的記錄檔每隔五秒鐘"hello world"訊息。

此逐步解說涵蓋：

- **概念**： 撰寫任何閘道使用 IoT 閘道 sdk （英文） 所建立的元件的概念性概觀。  
- **Hello World 架構的範例**： 說明的概念適用於 Hello World 樣本的方式和元件相互。
- **How to 建置範例**： 建立範例所需的步驟。
- **如何執行範例**： 範例執行時所需的步驟。 
- **典型的輸出**： 輸出至預期當您執行此範例的範例。
- **程式碼片段**： 要顯示的 Hello World 範例如何實作關鍵閘道元件的程式碼片段的集合。

## <a name="azure-iot-gateway-sdk-concepts"></a>Azure IoT 閘道 SDK 概念

檢查的範例程式碼或建立使用 IoT 閘道 SDK 您自己欄位閘道之前，您應先瞭解 underpin sdk 的架構的重要概念。

### <a name="modules"></a>模組

您所建立及以組合*模組*建置使用 Azure IoT 閘道 SDK 的閘道。 模組使用*的郵件*交換彼此的資料。 模組會收到一則訊息、 在其上執行某些動作、 （選擇性） 轉換成新的郵件及再將其處理其他模組發行。 某些模組可能只會產生新的郵件和永遠不會處理內送郵件。 模組鏈結會建立一個點的管線資料上執行轉換各模組資料處理管線。

![其中的閘道建置的 Azure IoT 閘道 SDK 中的核心模組][1]
 
SDK 包含下列項目：

- 預先書寫的模組以執行常見的閘道功能。
- 開發人員可用來撰寫自訂模組介面。
- 部署及執行一組的模組所需的基礎結構。

Sdk （英文） 提供可讓您建立各種作業系統和平台上執行的閘道抽象層。

![Azure IoT 閘道 SDK 抽象層][2]

### <a name="messages"></a>郵件

雖然思考傳遞訊息給對方的模組是方便的方式來概念如何閘道函數，則不會未正確反映會發生什麼事。 模組使用 broker 與彼此通訊、 發佈訊息來 broker （匯流排、 pubsub 或任何其他通訊模式），且讓路由郵件傳送至連線至該模組代理人。

模組使用**Broker_Publish**函數來 broker 發行一則訊息。 代理人將郵件傳遞至模組呼叫回呼函數。 郵件是由索引鍵/值屬性和內容傳遞為記憶體區塊的一組所組成。

![Broker Azure IoT 閘道 SDK 中的角色][3]

### <a name="message-routing-and-filtering"></a>郵件路由和篩選

有兩種導向正確的模組的訊息。 一組連結可將讓 broker 知道來源和接收的每個模組，或模組可以當做篩選依據的郵件內容傳遞來 broker。 如果郵件用於在模組應僅採取動作後一則訊息。 連結和訊息篩選是什麼有效率地建立訊息管線。

## <a name="hello-world-sample-architecture"></a>[Hello World 範例架構

Hello World 範例說明前一節中說明的概念。 Hello World 範例實作有兩個模組所組成管線的閘道：

-   [ *Hello world* ] 模組會每隔五秒鐘建立訊息並傳遞至記錄器模組。
-   *記錄器*模組會將它收到的訊息寫入檔案。

![使用 Azure IoT 閘道 SDK 內建的 Hello World 範例的架構][4]

前一節所述，[Hello World] 模組不會傳遞郵件直接以記錄器模組每隔五秒鐘。 而是它會發佈一則訊息來 broker 每隔五秒鐘。

記錄器模組收到來自 broker 訊息和動作顯示時，郵件的內容寫入檔案。

記錄器模組只能使用提供從代理人的郵件，則永遠不會發佈新訊息來 broker]。

![如何 broker 路由 Azure IoT 閘道 SDK 中的模組之間的郵件][5]

如上圖顯示 Hello World 範例和來源檔案[儲存機制]中實作不同的某些部分的範例的相對路徑的架構[lnk-gateway-sdk]。 探索在自己的程式碼或使用下列程式碼片段作為指引。

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk