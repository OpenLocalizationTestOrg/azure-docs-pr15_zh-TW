<properties
 pageTitle="開發人員指南-IoT 中心端點 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-IoT 中心結束點的參考資訊"
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

# <a name="reference---iot-hub-endpoints"></a>參照-IoT 中心端點

## <a name="list-of-iot-hub-endpoints"></a>IoT 中心端點清單

Azure IoT 中心是多租用戶服務其功能公開至各種不同的動作項目。 下圖顯示不同的端點 IoT 中心公開。

![IoT 中心端點][img-endpoints]

結束點的說明如下︰

* **資源提供者**。 IoT 中心資源提供者公開[Azure 資源管理員][lnk-arm]介面，可讓 Azure 訂閱的擁有者可以建立及刪除 IoT 集線器與更新 IoT 中心屬性。 IoT 中心屬性用來管理[中樞層級安全性原則][lnk-accesscontrol]，而不是裝置層級的存取控制項，以及雲端-裝置和裝置至雲端訊息功能的選項。 IoT 中心資源提供者也可讓您要[匯出的裝置身分識別][lnk-importexport]。
* **裝置身分識別管理**。 每個 IoT 中心公開 HTTP 其餘端點，以管理裝置身分識別的一組 （建立、 擷取、 更新及刪除）。 [裝置身分識別][lnk-device-identities]使用的驗證及存取控制的裝置。
* **裝置雙生管理**。 每個 IoT 中心公開服務具 HTTP 其餘端點查詢並更新[裝置盡相同]的一組[lnk-twins]（要更新標記與內容）。
* **工作管理**。 每個 IoT 中心公開面對服務的 HTTP 其餘端點查詢及管理[工作]的一組[lnk-jobs]。
* **裝置結束點**。 佈建在裝置的身分識別登錄機碼中每個裝置 IoT 中心公開端點裝置可以用來傳送及接收郵件的一組︰
    - *傳送裝置至雲端的郵件*。 使用此端點傳送[裝置至雲端郵件][lnk-d2c]。
    - *接收雲端-裝置訊息*。 裝置接收目標的[雲端-裝置訊息]中使用此端點[lnk-c2d]。
    - *啟動檔案上傳*。 裝置上傳[檔案]，從 IoT 中心接收 Azure 儲存體 SA URI 使用此端點[lnk-upload]。
    - *擷取及更新雙生內容*。 裝置存取其[裝置雙生]使用此端點[lnk-twins]的屬性。
    - *接收直接方法要求*。 裝置使用此端點若要聆聽[直接方法][lnk-methods]的要求。

    這些端點公開使用[MQTT v3.1.1][lnk-mqtt]，HTTP 1.1 （英文） 及[AMQP 1.0] [lnk-amqp]通訊協定。 請注意，AMQP 也可以透過[WebSockets] [lnk-websockets]連接埠 443。
    
    盡相同的和方法的 endpoins 可僅使用[MQTT v3.1.1][lnk-mqtt]。

* **服務端點**。 每個 IoT 中心公開應用程式後端可以用來與裝置通訊的端點的一組。 這些端點是目前僅公開使用[AMQP] [lnk-amqp]通訊協定，除了方法引動端點公開透過 HTTP 1.1 （英文）。
    - *接收裝置至雲端訊息*。 這個端點是相容於[Azure 事件集線器][lnk-event-hubs]。 後端服務可以用它來讀取所有[裝置至雲端郵件][lnk-d2c]傳送您的裝置。
    - *傳送雲端-裝置郵件並接收傳送通知*。 這些端點啟用傳送可靠的[雲端-裝置郵件]應用程式後端[lnk-c2d]，以及接收的相對應的傳遞 」 或 「 到期的通知。
    - *接收檔案的通知*。 此訊息的端點可讓您收到的通知，當您的裝置已成功上傳檔案。 
    - *直接方法叫用*。 此端點允許後端服務叫用[直接方法][lnk-methods]裝置上。

[IoT 中心 Api 和 Sdk] [lnk-sdks]本文說明存取這些端點的各種方式。

最後，請務必請注意，所有 IoT 中心端點都使用[TLS] [lnk-tls]通訊協定以及任何端點曾公開在未加密/不安全通道。

## <a name="field-gateways"></a>功能變數閘道器

在 IoT 解決方案中，*欄位閘道器*位於您的裝置與 IoT 中心端點之間。 它通常位於關閉您的裝置。 使用支援的裝置通訊協定，直接與欄位閘道器通訊您的裝置。 功能變數閘道器連線到使用支援的 IoT 中樞的通訊協定 IoT 中心端點。 高度專業的硬體或低 power 電腦執行完成閘道器適用的端對端案例的軟體，可以是欄位的閘道器。

您可以使用[Azure IoT 閘道器 SDK] [lnk-gateway-sdk]實作欄位的閘道器。 此 SDK 提供特定功能，例如振 IoT 中心來自多個裝置相同的連接到通訊的能力。

## <a name="next-steps"></a>後續步驟

在此 IoT 中心開發人員指南其他參考主題包括︰

- [查詢語言盡相同、 方法及工作][lnk-devguide-query]
- [配額和節流][lnk-devguide-quotas]
- [IoT 中心 MQTT 支援][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md