<properties
 pageTitle="開發人員指南-直接方法 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-使用直接叫用您的裝置上的程式碼的方法"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>叫用的裝置 （預覽版本） 直接方法

## <a name="overview"></a>概觀

IoT 中心可讓您能夠叫用在雲端的裝置上的方法。 方法表示要求回覆互動與裝置 HTTP 呼叫相似的他們成功或失敗 （緊接使用者指定的逾時），讓使用者知道通話的狀態。 這是很適合用於案例立即採取的處理方式在哪裡裝置是否能夠回應，例如傳送簡訊喚醒到裝置上，如果裝置是離線簡訊正在價格比方法呼叫而有所不同。

您可以將方法為直接遠端程序呼叫裝置。 只在裝置已實作的方法可能會被稱為雲端。 如果雲端嘗試呼叫沒有定義的方法在裝置上的方法，此方法呼叫將會失敗。

每個裝置方法目標單一的裝置。 [工作][lnk-devguide-jobs]提供叫用在多個裝置上的方法和佇列方法引動中斷連線的裝置。

**服務連線**IoT 中樞的權限的任何人都可以叫用在裝置上的方法。

### <a name="when-to-use"></a>使用時機

裝置的方法類似[雲端-裝置]郵件[lnk-devguide-messages]的同時啟用雲端後端傳遞到裝置、 的資訊，但它們之間的差異基本的方式。 概念，方法是同步和無法長期，而雲端-裝置郵件是非同步與 48 小時的持續性。

遵循要求回應模式並不長期方法。 您命令裝置時，持續性缺乏會提供兩個即時的優點︰

- **方法執行立即回應**表示不需要為您管理要求與回應之間的關聯性。
- **提升處理量**表示可以執行的作業速度更快因為 IoT 中心不提供任何持續性。 IoT 中心 」 可讓更多方法來電每單位雲端-裝置郵件。

雲端-裝置訊息但不是需要命令至裝置，而是代表傳遞資訊的一些元，用來在其仍然，挑選裝置和裝置可能或是可能沒有回應雲端服務。 雲端-裝置訊息有較長逾時時間 （最多 48 小時） 方法時更快速地過期。

立即命令引動過程工作排程叫用的裝置上的命令和的裝置上使用的裝置的方法。

## <a name="method-lifecycle"></a>方法生命週期

方法在裝置上實作，且可能需要零或多個輸入正確產生方法內容中。 您叫用透過服務具 URI 直接方法 (`{iot hub}/twins/{device id}/methods/`)。 裝置收到直接的方法，透過裝置的特定 MQTT 主題 (`$iothub/methods/POST/{method name}/`)。 我們可能會在未來支援方法在其他裝置端網路通訊協定。

> [AZURE.NOTE] 當您叫用的裝置上的直接方法時，屬性名稱和值只能包含美國 ASCII 重點英數字元，除了任何在下列設定︰ ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``。

方法都是同步，請成功或失敗之後的逾 (預設︰ 30 秒數，可設定為 3600 秒設定)。 互動式的情況下您想要採取動作只有裝置是線上和接收] 命令，例如開啟 light 從電話裝置的方法。 在下列情況下，您要看到立即成功或失敗，因此雲端服務可以越快越做的結果。 裝置可能會傳回當做方法，某些郵件本文，但不需要執行此作業的方法。 沒有順序不保證或方法電話任何並行語意。

裝置方法呼叫是 HTTP 僅在雲端側邊，從和 MQTT 專用裝置端。

## <a name="reference-topics"></a>參考主題︰

下列主題提供給您使用直接方法的相關資訊。

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>叫用直接方法從後端應用程式

### <a name="method-invocation"></a>方法引動

在裝置上的直接方法引動是包含 HTTP 來電︰

- *URI*特定裝置 (`{iot hub}/twins/{device id}/methods/`)
- 文章*方法*
- *標題*包含授權、 要求識別碼、 內容類型及內容編碼方式
- 透明 JSON*本文*中的下列格式︰

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  逾時為秒數。 如果沒有設定逾時，其預設為 30 秒。
  
### <a name="response"></a>回應

後端會收到包含 「 回覆︰

- *HTTP 狀態碼*，用於來自 IoT] 中心內，包括裝置目前並未連線，將 404 錯誤的錯誤
- *標題*包含 etag，要求識別碼、 內容類型及內容編碼方式
- JSON*本文*以下列格式︰

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   同時`status`和`body`是提供的裝置，用來回應與裝置自己的狀態碼及/或描述。

## <a name="handle-a-direct-method-on-a-devcie"></a>控點上 devcie 的直接方法

### <a name="method-invocation"></a>方法引動

裝置收到直接方法要求 MQTT 主題︰`$iothub/methods/POST/{method name}/?$rid={request id}`

本文會裝置接收是以下列格式︰

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

方法要求是 QoS 0。

### <a name="response"></a>回應

裝置傳送回應`$iothub/methods/res/{status}/?$rid={request id}`，其中︰

 - `status`屬性是方法執行裝置提供的狀態。
 - `$rid`屬性是從自 IoT 中樞的方法引動要求 ID。

本文設定的裝置，而且可以是任何狀態。

## <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已經學會如何使用直接的方法，您可能會感興趣的主題中的下列開發人員指南︰

- [多個裝置上的排程工作][lnk-devguide-jobs]

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [使用雲端至裝置的方法][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
