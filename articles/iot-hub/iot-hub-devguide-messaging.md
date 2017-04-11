<properties
 pageTitle="開發人員指南-訊息 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-裝置至雲端和雲端-裝置訊息"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="send-and-receive-messages-with-iot-hub"></a>傳送及接收郵件與 IoT 中心

## <a name="overview"></a>概觀

IoT 中心會提供下列訊息的基本功能，與裝置通訊︰

- [裝置至雲端][lnk-d2c]從裝置應用程式後端。
- [雲端-裝置][lnk-c2d]從應用程式後 [端 （「*服務*」 或 「*雲端*」）。

核心屬性 IoT 中心訊息功能是可靠性和持續性的郵件。 這些屬性啟用裝置一邊，並且載入特殊圖文集的事件處理位於雲端的連線不穩定恢復功能。 IoT 中心實作*至少一次*傳遞保證的雲端-裝置和裝置至雲端通訊功能。

IoT 中心支援多個[裝置具通訊協定][ lnk-protocols] （例如 MQTT、 AMQP 和 HTTP）。 若要跨通訊協定支援順暢的互通性，IoT 中心定義[常見的郵件格式][lnk-message-format]的所有裝置具通訊協定都支援。

IoT 中心公開[事件中心相容端點][lnk-compatible-endpoint]啟用閱讀收到中樞的裝置至雲端郵件的後端應用程式。

### <a name="when-to-use"></a>使用時機

訊息是 IoT 中樞的核心功能。 每當您需要從您的裝置傳送郵件，您的後端，或從您的後端的郵件傳送至裝置時，請使用它。

比較 IoT 中心和事件集線器服務，請參閱[IoT 中心比較和事件集線器][lnk-compare]。

## <a name="device-to-cloud-messages"></a>裝置至雲端的郵件

您的裝置至雲端透過傳送郵件的裝置具端點 （**/devices/ {deviceId} / 郵件/事件**）。 您的後端服務收到透過服務具端點 (**/messages/events**) 與[事件集線器]相容的裝置至雲端郵件[lnk-event-hubs]。 因此，您可以使用標準[事件集線器整合和 Sdk] [lnk-compatible-endpoint]將接收訊息裝置至雲端。

IoT 中心實作裝置至雲端訊息的方式，類似於[事件集線器][lnk-event-hubs]。 IoT 中心裝置至雲端郵件會超過事件集線器*事件*等[服務匯流排][ lnk-servicebus] *郵件*。

此實作具有下列的含意︰

* 同樣的事件集線器事件裝置至雲端訊息都會長期在 IoT 中心保留七天 (請參閱[裝置至雲端的設定選項][lnk-d2c-configuration])。
* 裝置至雲端郵件在建立時間設定固定組的磁碟分割區的分割 (請參閱[裝置至雲端的設定選項][lnk-d2c-configuration])。
* Analogously 到事件集線器讀取裝置至雲端郵件用戶端必須處理磁碟分割區和檢查點。 請參閱[事件集線器-使用事件][lnk-event-hubs-consuming-events]。
* 事件集線器事件，例如裝置至雲端的郵件可以最 256 KB，而且可以分批最佳化給群組。 批次可以最 256 KB，，最多 500 的郵件。

之間有區別，不過，幾個重要 IoT 中心裝置至雲端訊息及事件集線器︰

* 在[IoT 中心來控制存取權限]所述[lnk-devguide-security]] 區段中，IoT 中心 」 可讓每個裝置的驗證及存取控制項。
* IoT 中心 」 可讓數百萬同時連線的裝置 (請參閱[配額和節流][lnk-quotas])、 事件集線器限制為每個命名空間的 5000 AMQP 連線時。
* 不允許任意分割使用**PartitionKey**IoT 中心。 根據其原始**deviceId**分割裝置至雲端的郵件。
* 縮放比例 IoT 中心是與縮放事件集線器稍有不同。 如需詳細資訊，請參閱[縮放 IoT 中心][lnk-guidance-scale]。

> [AZURE.NOTE] 您無法在所有的案例中替代事件集線器 IoT 中心。 例如，在某些事件處理的計算，它可能必須重新分割分析的資料流之前的事件提供解答不同屬性或欄位。 在此案例中，您可以使用事件中心提高資料流處理管道的郵件的兩個部分。 如需詳細資訊，請參閱[Azure 事件集線器概觀]中的*磁碟分割區*[lnk-eventhub-partitions]。

如需詳細瞭解如何使用裝置至雲端訊息，，請參閱[IoT 中心 Api 和 Sdk][lnk-sdks]。

> [AZURE.NOTE] 使用時 HTTP 傳送訊息裝置至雲端，屬性名稱和值只能包含 ASCII 英數字元，以及``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``。

### <a name="non-telemetry-traffic"></a>非遙測流量

通常，除了遙測資料點，裝置也傳送訊息和需要執行和處理從應用程式的商務邏輯層的要求。 例如，要徑警示，必須觸發特定動作後端，或裝置回覆寄件者的後端的命令。

如需有關這類郵件的 [處理程序的最佳方式的詳細資訊，請參閱[教學課程︰ 如何處理 IoT 中心裝置至雲端訊息][lnk-d2c-tutorial]教學課程。

### <a name="device-to-cloud-configuration-options"></a>裝置至雲端設定選項

IoT 中心公開下列屬性，讓您控制裝置至雲端訊息。

* **磁碟分割數目**。 設定此屬性，以定義的磁碟分割區的裝置至雲端事件 ingestion 數的建立。
* **保留時間**。 這個屬性會指定裝置至雲端訊息的保留時間。 預設值是一天，但它可以增加七天。

此外，analogously 到事件集線器 IoT 中心可讓您管理消費者群組至雲端的裝置上接收結束點。

您可以修改所有這些屬性，以程式設計方式透過[IoT 中心資源提供者 REST Api][lnk-resource-provider-apis]，或使用[Azure 入口網站][lnk-management-portal]。

### <a name="anti-spoofing-properties"></a>反詐騙屬性

若要避免裝置中的郵件裝置至雲端，IoT 中心詐騙戳記所有郵件的下列屬性︰

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

前兩個包含**deviceId**和**generationId**原始的裝置，依照[裝置身分識別內容]的[lnk-device-properties]。

**ConnectionAuthMethod**屬性包含 JSON 序列化物件時，下列屬性︰

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>雲端-裝置訊息

您雲端-裝置透過傳送郵件的服務具端點 (**/messages/devicebound**)。 在裝置收到透過裝置的特定端點 (**/devices/ {deviceId} / 郵件/devicebound**)。

在單一裝置被目標每雲端-裝置封郵件，**以**屬性設定為**/devices/ {deviceId} / 郵件/devicebound**。

>[AZURE.IMPORTANT] 每個裝置佇列會保留最多 50 個雲端-裝置訊息。 嘗試更多的郵件傳送至錯誤的同一個裝置結果。

> [AZURE.NOTE] 當您傳送雲端-裝置訊息時，屬性名稱和值只能包含 ASCII 英數字元，以及``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``。

### <a name="message-lifecycle"></a>郵件生命週期

若要確保至少一次郵件傳遞，IoT 中心持續存在，每個裝置佇列中的雲端-裝置訊息。 裝置必須明確認可*完成*IoT 集線器從佇列中移除它們。 這可以確保恢復連線與裝置失敗。

下圖顯示雲端-裝置郵件的生命週期狀態圖。

![雲端-裝置訊息生命週期][img-lifecycle]

當服務傳送郵件時，它會被視為*佇列*。 當裝置想要*接收*訊息，IoT 中心*鎖定*訊息 （若要**隱藏**設定的狀態） 若要開始收到其他郵件同一個裝置上讓其他執行緒。 裝置執行緒完成後處理郵件，請*完成*訊息通知 IoT 中心。

也可以在裝置︰

- 郵件中，會使 IoT 中心，將它**Deadlettered**狀態為 [*拒絕*]。 附註︰ 以 MQTT 連接裝置無法拒絕 C2D 的郵件。
- *放棄*設定**佇列**會使 IoT 中心，將郵件放回佇列中的狀態訊息。

執行緒可能會失敗，而不通知 IoT 中心處理郵件。 在此情況下，郵件會自動轉場從**隱藏**狀態**佇列**狀態之後*可見度 （或鎖定） 會出現逾時*。 此逾時的預設值是一分鐘。

郵件可以切換**佇列**，然後選取 [**隱藏**狀態，最多數目的 IoT 中心的 [**最大值傳遞計數**] 屬性中指定的時間。 之後的轉場效果的數字，IoT 中心會將郵件的狀態設定為**Deadlettered**。 同樣地，IoT 中心設定的狀態訊息**Deadlettered**後到期時間 (請參閱[存留時間][lnk-ttl])。

在雲端至裝置的郵件上的教學課程，請參閱[教學課程︰ 如何傳送郵件 IoT 中心雲端-裝置][lnk-c2d-tutorial]。 在不同的 Api 和 Sdk 參考主題的方式公開雲端-裝置功能，請參閱[IoT 中心 Api 和 Sdk][lnk-sdks]。

> [AZURE.NOTE] 一般而言，雲端-裝置訊息完成時遺失的郵件不會影響應用程式邏輯。 例如，郵件內容成功保存在本機的儲存空間，或已順利執行作業。 郵件可能也攜帶暫時性的詳細資訊，其遺失不會影響應用程式的功能。 有時候，您可以長時間執行工作的雲端-裝置郵件完成後保存在本機存放區中的工作描述。 然後，您可以在不同階段的任務進度通知應用程式後端的一或多個裝置至雲端的郵件。

### <a name="message-expiration-time-to-live"></a>郵件到期 （存留時間）

雲端-裝置中的每封郵件有的到期時間。 這次是設定的服務 （ **ExpiryTimeUtc**屬性），或是 IoT 中心使用預設的*存留時間*指定為 IoT 中心屬性。 請參閱[雲端-裝置設定選項][lnk-c2d-configuration]。

> [AZURE.NOTE] 利用訊息到期並避免常見的方式傳送訊息給中斷連線的裝置，可設定短時間，live 值。 這種方法達到相同的結果為維護裝置連線狀態，又更有效率。 當您要求訊息的通知時，IoT 中心會通知您的裝置可接收訊息，而且哪些裝置沒有在線上或失敗。

### <a name="message-feedback"></a>郵件的意見反應

當您傳送的雲端-裝置訊息時，服務可以要求傳送關於該訊息的最終狀態的每個訊息意見反應。

- 如果您將**Ack**屬性設定為**正數**，IoT 中心，並且在雲端-裝置郵件已經**完成**狀態，就會產生的意見反應 」 訊息。
- 如果您**Ack**將屬性設定為**負數**，IoT 中心會產生意見反應 」 訊息，只有，雲端-裝置郵件到達**Deadlettered**狀態。
- 如果您是要**完整**設定**Ack**屬性，IoT 中心會產生的意見反應 」 訊息中。

> [AZURE.NOTE] 如果**Ack**已**完整**，而且您沒有收到的意見反應 」 訊息，這表示的意見反應 」 訊息，指出過期。 服務無法知道原始郵件有何改變。 在練習中，服務應該確認，可以處理意見反應之前續訂。 最大的到期時間兩天，可讓足夠的時間，以取得服務執行一次發生故障。

[結束點]所述[lnk-endpoints]，IoT 中心郵件以提供意見反應透過服務具端點 (**/messages/servicebound/feedback**)。 接收意見反應的語意雲端-裝置郵件一樣，而且有相同的[郵件生命週期][lnk-lifecycle]。 可能的話，郵件的意見反應是批次，在單一郵件中，以下列格式︰

| 屬性 | 描述 |
| -------- | ----------- |
| EnqueuedTime | 指出建立郵件時的時間戳記。 |
| 使用者識別碼 | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

本文是 JSON 序列化陣列記錄中，每個都有下列屬性︰

| 屬性 | 描述 |
| -------- | ----------- |
| EnqueuedTimeUtc | 指出當訊息的結果有時間戳記。 例如，完成的裝置] 或 [郵件過期。 |
| OriginalMessageId | 這項意見反應資訊與相關的雲端-裝置訊息的**訊息**。 |
| StatusCode | 必要的整數。 用於 IoT 中心所產生的意見反應 」 訊息。 <br/> 0 = 成功 <br/> 1 = 郵件過期 <br/> 2 = 最大值傳遞計數超過上限 <br/> 3 = 拒絕的訊息 |
| 描述 | **StatusCode**的字串值。 |
| DeviceId | 目標裝置的雲端-裝置郵件的相關意見反應 」 這段**DeviceId** 。 |
| DeviceGenerationId | 目標裝置的雲端-裝置郵件的相關意見反應 」 這段**DeviceGenerationId** 。 |


>[AZURE.IMPORTANT] 服務必須指定雲端-裝置訊息都能使用原始郵件關聯的意見反應 」 的**訊息**。

下列範例顯示的意見反應郵件的本文。

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>雲端-裝置設定選項

每個 IoT 中心公開雲端-裝置訊息的下列設定選項。

| 屬性 | 描述 | 範圍及預設值 |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | [TTL] 的預設值雲端-裝置訊息。 | ISO_8601 間隔最 2D （最小值 1 分鐘）。 預設︰ 1 小時。 |
| maxDeliveryCount | 雲端-裝置每個裝置佇列的最大值傳遞計數。 | 1 到 100。 預設︰ 10。 |
| feedback.ttlAsIso8601 | 保留服務繫結意見反應 」 訊息。 | ISO_8601 間隔最 2D （最小值 1 分鐘）。 預設︰ 1 小時。 |
| feedback.maxDeliveryCount | 意見反應佇列中的最大值傳遞計數。 | 1 到 100。 預設︰ 100。 |

如需詳細資訊，請參閱[建立 IoT 集線器][lnk-portal]。

## <a name="read-device-to-cloud-messages"></a>讀取裝置至雲端的郵件

IoT 中心公開讀取收到您中心裝置至雲端訊息後端服務端點。 端點是事件中心相容，可讓您使用任何的機制事件集線器服務支援讀取的郵件。

當您使用[的.NET Azure 服務匯流排 SDK] [lnk-servicebus-sdk]或[事件集線器-事件處理器主機][lnk-eventprocessorhost]，您可以使用任何 IoT 中樞的連接字串的正確權限。 為事件中心名稱，然後使用**郵件/事件**。

當您使用 Sdk （或產品整合） 不知道 IoT 中心，您必須從[Azure 入口網站]上的 IoT 中心設定擷取事件中心相容端點和事件中心相容名稱[lnk-management-portal]:

1. 在 IoT 中心刀中，按一下 [**訊息**]。
2. 您可以在**裝置至雲端設定**] 區段中，找到下列的值︰**事件中心相容端點**、**事件中心相容的名稱**及**分割**。

    ![裝置至雲端設定][img-eventhubcompatible]

> [AZURE.NOTE] 如果 SDK 需要**主機名稱**或**命名空間**值，從移除**事件中心相容結束點**。 例如，如果您的事件中心相容端點是**sb://iothub-ns-myiothub-1234.servicebus.windows.net/**，**主機名稱** **iothub ns-myiothub 1234.servicebus.windows.net**，而**命名空間**應**iothub ns-myiothub 1234**。

然後，您可以使用任何已連線至指定的事件中樞的**ServiceConnect**權限的共用的 access 安全性原則。

如果您需要建立的事件中心連線字串使用先前的資訊，請使用下列模式︰

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

以下是清單 Sdk，您可以使用事件中心相容端點 IoT 中心公開的整合︰

* [Java 事件集線器用戶端](https://github.com/hdinsight/eventhubs-client)
* [Apache 大量 spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md)。 您可以檢視[spout 來源](https://github.com/apache/storm/tree/master/external/storm-eventhubs)GitHub 上。
* [Apache 火花整合](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>參考主題︰

下列主題提供給您交換訊息 IoT 中樞的詳細資訊。

## <a name="message-format"></a>郵件格式

IoT 中樞的郵件組成︰

* *系統內容*一組。 IoT 中心解譯或設定的屬性。 此設定為預先定義的。
* 一組的*應用程式屬性*。 字串應用程式可以定義的屬性與存取權，而不需要還原序列化郵件本文的字典。 IoT 中心不會修改這些屬性。
* 不透明的二進位本文。

如需有關如何在不同的通訊協定編碼郵件的詳細資訊，請參閱[IoT 中心 Api 和 Sdk][lnk-sdks]。

下表列出 IoT 中心郵件中的 [系統內容的設定。

| 屬性 | 描述 |
| -------- | ----------- |
| 訊息 | 郵件中，用於要求回覆圖樣可設定使用者識別碼。 格式︰ 區分大小寫 （128 個字元） ASCII 7 位元英數字元的字串 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| 順序編號 | 數字 （每個裝置佇列唯一） 指派給每個雲端-裝置訊息 IoT 中心。 |
| 若要 | 在[雲端的裝置]中指定的目的地[lnk-c2d]郵件。 |
| ExpiryTimeUtc | 日期及時間的郵件到期。 |
| EnqueuedTime | 日期及時間 IoT 中心收到郵件。 |
| 相互關聯識別碼 | 字串中的屬性回應訊息通常包含邀請，邀請回覆模式中的訊息。 |
| 使用者識別碼 | 用來指定郵件的識別碼。 當 IoT 中心所產生的郵件時，將會設定為`{iot hub name}`。 |
| Ack | 意見反應 」 訊息的產生器。 這個屬性會用於雲端-裝置郵件中要求產生當做郵件的耗用的意見反應 」 訊息的 IoT 中心裝置。 可能的值︰**無**（預設）︰ 會產生不意見反應 」 的訊息，**正數**︰ 收到意見反應 」 訊息，如果郵件已完成，**負數**︰ 收到意見反應 」 訊息，如果郵件過期 （或 [最大值傳遞次數已達） 不完成的裝置，或選取 [**完整**︰ 負值。 如需詳細資訊，請參閱[郵件的意見反應][lnk-feedback]。 |
| ConnectionDeviceId | 識別碼 IoT 中心設定裝置至雲端的郵件]。 它所含的裝置傳送郵件的**deviceId** 。 |
| ConnectionDeviceGenerationId | 識別碼 IoT 中心設定裝置至雲端的郵件]。 包含**generationId** (依照[裝置身分識別內容][lnk-device-properties]) 傳送郵件的裝置。 |
| ConnectionAuthMethod | 驗證方法 IoT 中心設定裝置至雲端的郵件]。 這個屬性會包含用來驗證傳送郵件的裝置的驗證方法的相關資訊。 如需詳細資訊，請參閱[裝置雲端反詐騙][lnk-antispoofing]。|

## <a name="communication-protocols"></a>通訊協定

IoT 中心 」 可讓裝置使用[MQTT][lnk-mqtt]，透過 WebSockets， [AMQP]MQTT[lnk-amqp]，在 WebSockets，並 HTTP AMQP 通訊協定裝置端通訊。 下表提供您選擇的通訊協定的高層級建議︰

| 通訊協定 | 當您應該選擇這個通訊協定 |
| -------- | ------------------------------------ |
| MQTT <br> 透過 WebSocket MQTT     | 不需要的相同的 SSL 連線 （兩者都有它自己的每個裝置認證） 的多個裝置的所有裝置上使用。 |
| AMQP <br> 透過 WebSocket AMQP    | 利用多工在裝置上的連線，請使用上欄位與雲端閘道器。 |
| HTTP    | 使用的裝置，無法支援其他通訊協定。 |

當您選擇您的通訊協定裝置端通訊時，請考慮下列幾點︰

* **雲端-裝置圖樣**。 HTTP 沒有實作伺服器推入有效方法。 因此，當您使用的 HTTP，裝置輪詢 IoT 中心雲端-裝置訊息。 這個方法最裝置和 IoT 中樞的效率。 在目前的 HTTP 指導方針，每個裝置應該投票的郵件，每 25 分鐘以上。 另一方面，MQTT 和 AMQP 支援 server 推入接收雲端-裝置訊息時。 啟用立即的推入郵件從 IoT 中心裝置。 傳遞延遲的問題，MQTT 或 AMQP 要使用的最佳通訊協定。 為所連線的裝置，HTTP 以及運作。
* **功能變數閘道器**。 使用 MQTT 和 HTTP 時，您無法連線 （兩者都有它自己的每個裝置認證） 的多個裝置使用相同的 SSL 連線。 因此，對於[欄位閘道的案例][lnk-azure-gateway-guidance]，這些通訊協定是不理想，因為它們需要 SSL] 之間的連線欄位閘道器以及 IoT 中心針對每個裝置連接到欄位閘道器。
* **低資源裝置**。 MQTT 及 HTTP 文件庫有較小的使用量比 AMQP 文件庫。 因此，如果裝置有限的資源 （例如小於 1 MB 的 RAM），這些通訊協定可能是唯一的通訊協定實作。
* **網路橫越**。 標準 AMQP 通訊協定使用連接埠 5671，MQTT 接聽連接埠 8883，可能會導致非 HTTP 通訊協定關閉時的網路問題。 透過 WebSockets MQTT，在 WebSockets，並 HTTP AMQP 可在此案例中使用。
* **內容的大小**。 MQTT 和 AMQP 會產生更精簡的裝載於 HTTP 的二進位通訊協定。

> [AZURE.NOTE] 使用時 HTTP，每個裝置應該輪詢雲端-裝置訊息每 25 分鐘以上。 不過，在開發期間，則可以獲得每個 25 分鐘以上的常見問題。

## <a name="port-numbers"></a>連接埠號碼

裝置可以與 IoT 中心中使用各種不同的通訊協定的 Azure 通訊。 一般而言的通訊協定選擇受到解決方案的特定需求。 下表列出必須開啟，才能使用特定的通訊協定裝置的相關的輸出連接埠︰

| 通訊協定 | 連接埠 |
| -------- | ------- |
| MQTT     | 8883    |
| 透過 WebSockets MQTT | 443    |
| AMQP     | 5671    |
| 透過 WebSockets AMQP | 443    |
| HTTP     | 443     |
| LWM2M （裝置管理） | 5684 |

一旦您建立 IoT 中心 Azure 區域中，中樞保留的中樞的存留時間相同的 IP 位址。 不過，若要維持服務的品質，如果 Microsoft 移到不同的刻度] 單位的 IoT 中心接著會被指派新的 IP 位址。

## <a name="notes-on-mqtt-support"></a>備忘稿 MQTT 支援

IoT 中心實作 MQTT v3.1.1 通訊協定下列限制與特定的行為︰

  * **不支援 QoS 2**。 當裝置用戶端發佈**QoS 2**的訊息時，IoT 中心會關閉網路連線。 當裝置用戶端與**QoS 2**主題而定時，IoT 中心授與**SUBACK**封包中最大的 QoS 階層 1。
  * **保留郵件不會保存**。 如果裝置用戶端會發佈郵件保留旗標設定為 1，IoT 中心新增**x-選擇-保留**的郵件應用程式屬性。 在此情況下，IoT 中心不會保留保留郵件，但改為傳送到後端應用程式。

如需詳細資訊，請參閱[支援的 IoT 中心 MQTT][lnk-devguide-mqtt]。

為完稿的考量，您應該檢閱[Azure IoT 通訊協定閘道器][ lnk-azure-protocol-gateway] ，可讓您部署高效能自訂通訊協定的閘道器之直接與 IoT 中樞的介面。 Azure IoT 通訊協定閘道器可讓您自訂以容納 brownfield MQTT 部署的裝置通訊協定或其他自訂的通訊協定。 不過，此方法均會需要執行並操作自訂通訊協定的閘道器。

## <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已經學會如何傳送和接收與 IoT 中樞的訊息，您可能會感興趣的下列開發人員指南主題︰

- [將檔案從裝置上傳][lnk-devguide-upload]
- [管理裝置在 IoT 中心的身分識別][lnk-devguide-identities]
- [控制存取權 IoT 中心][lnk-devguide-security]
- [使用同步處理狀態和設定裝置盡相同][lnk-devguide-device-twins]
- [叫用的裝置上直接方法][lnk-devguide-directmethods]
- [多個裝置上的排程工作][lnk-devguide-jobs]

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [開始使用 Azure IoT 中心][lnk-getstarted-tutorial]
- [如何傳送郵件雲端-裝置 IoT 中心][lnk-c2d-tutorial]
- [如何處理 IoT 中心裝置至雲端的訊息][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
