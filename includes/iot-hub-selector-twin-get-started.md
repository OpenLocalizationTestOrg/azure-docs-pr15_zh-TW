> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>簡介

裝置盡相同的儲存裝置陳述式資訊 （中繼資料、 設定和條件） 的 JSON 文件。 IoT 中心保留您連線到 IoT 中樞的每個裝置的裝置雙生。

若要使用的裝置盡相同︰

* 存放裝置中繼資料的後端。
* 報告您的裝置應用程式的目前狀態資訊，例如可用的功能與條件 （例如，用於連線方法）。
* 裝置應用程式與後端之間，同步處理狀態的較長時間執行工作流程 （例如韌體及設定更新）。
* 查詢您的裝置的中繼資料、 設定或狀態。

> [AZURE.NOTE] 裝置盡相同的設計的同步處理和裝置設定與條件的查詢。 使用[裝置至雲端訊息][lnk-d2c]附上時間事件 （例如遙測資料流的時間為基礎的感應器） 和[雲端-裝置方法]的順序[lnk-methods]互動式控制項的裝置，例如風扇從使用者控制的應用程式開啟。

IoT 中心中的儲存裝置盡相同，並包含︰

* *標籤*裝置中繼資料只能由後端; 存取
* *所要的內容*，可修改的後端和裝置應用程式; 機制 JSON 物件與
* *報告的內容*，JSON 物件裝置應用程式可修改和讀取後結束。 標記與屬性不能包含陣列，但可以巢狀物件。

![][img-twin]

此外，應用程式後端可以查詢的所有上述資料的裝置盡相同。
請參閱[瞭解裝置盡相同][lnk-twins]如需關於裝置盡相同，並[IoT 中心查詢語言][lnk-query]參考查詢。

> [AZURE.NOTE] 此時，裝置盡相同只能從來存取連線到使用 MQTT 通訊協定 IoT 中樞的裝置。 請參閱[MQTT 支援][lnk-devguide-mqtt]本文指示轉換現有的裝置應用程式使用 MQTT。

本教學課程教您如何以︰

- 建立裝置雙生，並為*報告屬性*報表裝置雙生其連線頻道的模擬的裝置中加入*標記*的後端應用程式。
- 查詢您的後端應用程式，使用標記與先前建立的內容篩選的裝置。


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md