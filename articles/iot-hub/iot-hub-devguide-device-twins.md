<properties
 pageTitle="開發人員指南-瞭解裝置盡相同 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-使用裝置盡相同，同步處理狀態] 和 [組態 IoT 中心與您的裝置之間的資料"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>了解裝置盡相同-預覽

## <a name="overview"></a>概觀

*裝置盡相同*的儲存裝置陳述式資訊 （中繼資料、 設定和條件） 的 JSON 文件。 IoT 中心保留裝置雙生針對每個您連線至 IoT 中樞的裝置。 本文將說明︰

* 裝置雙生的結構︰*標記*，*想要*與*報告的內容*，和
* 裝置應用程式，並返回結束可以執行裝置盡相同的作業。

> [AZURE.NOTE] 此時，裝置盡相同只能從來存取連線到使用 MQTT 通訊協定 IoT 中樞的裝置。 請參閱[MQTT 支援][lnk-devguide-mqtt]本文指示轉換現有的裝置應用程式使用 MQTT。

### <a name="when-to-use"></a>使用時機

若要使用的裝置盡相同︰

* 將裝置特定的中繼資料儲存在雲端，例如部署的販賣的位置。
* 報告目前狀態資訊，例如可用的功能與您的裝置應用程式，例如透過行動電話或 wifi 連線在裝置的條件。
* 同步處理之間裝置應用程式並返回結尾，例如後端指定新的韌體版本，才能安裝此項目，以及報告更新程序的不同階段的裝置應用程式的較長時間執行工作流程的狀態。
* 查詢您的裝置的中繼資料、 設定或狀態。

使用[裝置至雲端訊息][lnk-d2c]附上時間的事件，例如感應器資料或鬧鐘時間數列的順序。 使用[雲端-裝置方法][lnk-methods]互動式控制項的裝置，例如風扇開啟。

## <a name="device-twins"></a>裝置盡相同

裝置盡相同儲存裝置的相關資訊的︰

- 裝置和後端可用來同步處理裝置條件及設定。
- 應用程式後端可用來查詢和目標長時間執行作業。

裝置雙生的生命週期已連結至對應的[裝置身分識別][lnk-identity]。 隱含地建立和刪除新的裝置身分識別建立或刪除在 IoT 中心時盡相同。

裝置雙生是 JSON 文件中包含︰

* [**標籤**]。 JSON 文件讀取及寫入的後端。 裝置應用程式看不到 [標籤]。
* **Desired 屬性**]。 同步處理裝置設定] 或 [條件中報告的內容搭配使用。 想要的屬性僅可設定應用程式回結束，並可以讀取裝置應用程式。 裝置應用程式也會變更所要的屬性的即時收到通知。
* **Reported 屬性**]。 您可以使用配合所要的內容進行同步處理裝置設定] 或 [條件。 報告的內容僅可設定的裝置應用程式，並可以讀取及查詢的應用程式後端。

此外，裝置雙生根目錄包含唯讀屬性的相對應的身分識別，為包含[裝置身分識別登錄]中從[lnk-identity]。

![][img-twin]

以下是裝置雙生 JSON 文件的範例︰

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

在根物件，是 [系統內容]，然後容器物件的`tags`，兩者均`reported`和`desired properties`。 `properties`容器包含一些唯讀的項目 (`$metadata`， `$etag`，及`$version`) 的所述分別[雙生中繼資料][lnk-twin-metadata]和[樂觀][lnk-concurrency]節。

### <a name="reported-property-example"></a>報告的內容範例

在上述範例中，包含裝置雙生`batteryLevel`報告裝置應用程式的屬性。 此屬性可讓查詢，並根據最後一個報告的電池層級的裝置上運作。 有另一個範例的裝置應用程式報表裝置功能或連線選項。

請注意如何報告的內容簡化的案例的後端興趣最後的屬性值的位置。 使用[裝置至雲端訊息][lnk-d2c]如果需要處理裝置遙測形式的附上時間事件，例如時間序列順序的後端。

### <a name="desired-configuration-example"></a>所需的設定範例

在上述範例中，`telemetryConfig`想要與報告的內容會使用後結束和裝置應用程式同步處理此裝置的遙測設定。 例如︰

1. 應用程式後端設定所需的設定值所需的屬性。 以下是想要的屬性與文件的部分︰

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. 變更立即如果連線，或第一份重新連線時，會收到通知的裝置應用程式。 裝置應用程式，然後報告更新的設定 (或錯誤狀況使用`status`屬性)。 以下是部分報告的內容︰

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. 可以將應用程式後端跨多個裝置，追蹤設定作業的結果，藉由[查詢][lnk-query]盡相同。

> [AZURE.NOTE] 上述的程式碼片段是方式的範例，針對可讀性，可能編碼裝置設定和其狀態最佳化。 IoT 中心不會強制特定的結構描述所需和報告中的屬性裝置盡相同。

在許多情況下盡相同用來同步處理更新韌體等長時間執行作業。 請參閱[使用所需的屬性設定裝置][lnk-twin-properties]如需有關如何使用內容同步處理，並追蹤時間有多長，請在裝置上執行作業。

## <a name="back-end-operations"></a>後端作業

使用下列原子作業，透過 HTTP 公開雙生後端操作︰

1. **擷取雙生識別碼**。 這項作業會傳回雙生的文件，包括標記和有需要，請內容報告和系統內容。
2. **部分更新雙生**。 這項作業，可讓後端部分更新雙生標記或所要的內容。 部分更新以表示 JSON 文件，新增或更新任何提及的屬性。 若要設定屬性`null`將會移除。 例如，下列會建立一個新的所需的屬性值`{"newProperty": "newValue"}`，會覆寫現有的值`existingProperty`與`"otherNewValue"`，並完全移除`otherOldProperty`。 任何變更不改變其他現有的所需的內容] 或 [標籤︰

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **取代所要的內容**。 這項作業，可讓完全覆寫所有現有所要的內容，然後替換新 JSON 文件的後端`properties/desired`。
4. **取代標籤**。 Analogously 若要取代所要的內容，這項作業 」 可讓完全覆寫所有現有的標籤，然後替換新 JSON 文件的後端`tags`。

上述所有作業都支援[樂觀][ lnk-concurrency] ，並要求**ServiceConnect**權限，定義[安全性][lnk-security]文章。

除了這些作業後端可以查詢使用類似 SQL[查詢語言]盡相同[lnk-query]，並執行大量使用[工作]盡相同的作業[lnk-jobs]。

## <a name="device-operations"></a>裝置作業

使用下列原子作業雙生操作裝置應用程式︰

1. **擷取雙生**。 這項作業會傳回雙生的文件的內容 (包括標記並想要、 報告和系統內容) 目前連接的裝置。
2. **更新部分報告內容**。 這項作業，可讓部分更新報告內容的目前連線的裝置。 這會使用相同的 JSON 更新格式為後端對部分更新所需的屬性。
3. **Observe 所需的屬性**。 目前連線的裝置，可以選擇他們發生時收到的更新所需屬性。 裝置接收到的後端所執行的相同形式的更新 （部分或完整取代）。

上述所有作業都需要**DeviceConnect**權限，定義[安全性][lnk-security]文章。

[Azure IoT 裝置 Sdk] [lnk-sdks]輕鬆使用多種語言與平台的上述作業。 想要的內容同步處理 IoT 中樞的基本功能的詳細資料的詳細資訊可以找到[裝置重新連線流程]中[lnk-reconnection]。

> [AZURE.NOTE] 目前，裝置盡相同只能從來存取連線到 IoT 中心使用 MQTT 通訊協定的裝置。

## <a name="reference-topics"></a>參考主題︰

下列主題提供關於控制存取權 IoT 中心的詳細資訊。

## <a name="tags-and-properties-format"></a>標記與內容的格式

[標籤]，您要與報告的內容是使用下列限制 JSON 物件︰

* JSON 物件中的所有機碼會區分大小寫的是 128 char UNICODE 字串。 允許的字元會排除 UNICODE 控制字元 （區段 C0 和 C1），及`'.'`， `' '`，及`'$'`。
* JSON 物件中所有的值可為下列 JSON 類型︰ 布林值、 數字、 字串、 物件。 不允許陣列。

## <a name="twin-size"></a>雙生大小

IoT 中心會強制執行 8 KB 大小限制的值`tags`， `properties/desired`，及`properties/reported`，排除唯讀的項目。
會計算大小，來計算排除 UNICODE 的所有字元都控制字元 （區段 C0 和 C1） 及空格`' '`出現以外的字串常數。
想要放大的上方限制這些文件的所有作業時，會發生錯誤都拒絕 IoT 中心。

## <a name="twin-metadata"></a>雙生中繼資料

IoT 中心保持在想要與報告的內容中的每個 JSON 物件的上次更新時間戳記。 時間戳記，以 utc 表示而[ISO8601]格式編碼`YYYY-MM-DDTHH:MM:SS.mmmZ`。
例如︰

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

若要保留移除物件索引鍵的更新，這項資訊會保留在每個層級 （而不只是 JSON 結構的分葉）。

## <a name="optimistic-concurrency"></a>樂觀

標記，想要與報告內容所有支援樂觀。
標記有 etag，依照[RFC7232]，代表該標籤的 JSON 表示。 您可以從後端條件更新作業中使用此一致性。

想要與報告的內容沒有 etag，但有`$version`保證遞增的值。 Analogously etag，以版本可用於，例如 （報告屬性裝置應用程式）] 或 [後端所要的屬性更新的廠商強制一致性的更新。

版本也很有用 observing 代理程式 （例如，觀察所需的屬性裝置的應用程式） 時都重新同步化競爭之間擷取作業的結果，並更新通知。 區段[裝置重新連線流程][lnk-reconnection]提供的更多資訊。

## <a name="device-reconnection-flow"></a>裝置重新連線流程

IoT 中心不會保留已中斷連線的裝置所要的屬性更新通知。 看來連線的裝置必須擷取完整需的屬性文件中，除了訂閱更新通知。 指定競爭更新通知和完整擷取之間的機率，必須確定下列流程︰

1. 裝置應用程式連線至 IoT 中心。
2. 如需屬性訂閱的裝置應用程式更新通知。
3. 裝置應用程式中擷取完整的文件的所需的屬性。

裝置應用程式可以略過所有通知與`$version`小於或等於完整擷取文件的版本。 這可能是因為 IoT 中心保證版本永遠遞增。

> [AZURE.NOTE] 此邏輯已經實作[Azure IoT 裝置 Sdk]中[lnk-sdks]。 裝置應用程式無法使用 Azure IoT 裝置 Sdk，而且必須直接程式 MQTT 介面時，才有幫助此描述。

## <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已經瞭解裝置盡相同，您可能會感興趣的開發人員指南下列主題︰

- [叫用的裝置上直接方法][lnk-methods]
- [多個裝置上的排程工作][lnk-jobs]

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [如何使用裝置雙生][lnk-twin-tutorial]
- [如何使用雙生屬性][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png