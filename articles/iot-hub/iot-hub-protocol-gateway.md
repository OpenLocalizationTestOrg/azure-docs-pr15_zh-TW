<properties
   pageTitle="Azure IoT 通訊協定閘道器 |Microsoft Azure"
   description="說明如何使用 Azure IoT 通訊協定閘道來擴充功能和 Azure IoT 中樞的通訊協定支援。"
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>支援 IoT 中心額外的通訊協定

Azure IoT 中心原本即支援 MQTT AMQP，與 HTTP 通訊協定通訊。 在某些情況下，裝置或欄位閘道可能無法使用這些標準的通訊協定，並且需要通訊協定配接。 在這種情況下，您可以使用自訂的閘道器。 自訂的閘道器可以啟用 IoT 中心端點的通訊協定配接橋與 IoT 中樞的流量。 若要啟用通訊協定配接 IoT 集線器，您可以自訂的閘道器為使用[Azure IoT 通訊協定閘道器](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)。

## <a name="azure-iot-protocol-gateway"></a>Azure IoT 通訊協定閘道器

Azure IoT 通訊協定閘道器專為最高-縮放比例與 IoT 中樞的雙向裝置通訊的通訊協定配接架構。 通訊協定閘道器在特定的通訊協定接受裝置連線傳遞元件。 它在 AMQP 1.0 等 IoT 中樞的流量。 Azure IoT 通訊協定閘道器是開啟來源軟體的專案提供彈性新增支援各種不同的通訊協定和通訊協定版本。

您可以使用 Azure 雲端服務工作者角色彈性的方式部署 Azure 的通訊協定閘道器。 此外，可以部署通訊協定閘道器在內部部署環境中，例如欄位閘道器。

Azure IoT 通訊協定閘道器包含可讓您自訂 MQTT 通訊協定行為，如有必要 MQTT 通訊協定介面卡。 由於 IoT 中心提供 MQTT v3.1.1 通訊協定的內建的支援，只有您應該考慮使用 MQTT 通訊協定介面卡，如果您有通訊協定自訂需要或特定需求的其他功能。

MQTT 介面卡也會示範建立其他的通訊協定的通訊協定介面卡的程式設計模型。 此外，Azure IoT 通訊協定閘道器的程式設計模型可讓您以插入自訂元件的特殊的處理，例如自訂驗證、 訊息轉換、 壓縮/解壓縮或加密/解密的裝置與 IoT 中心之間的流量。

彈性的通訊協定閘道器及 MQTT 實作都提供開啟來源軟體專案中。 這個選項可讓您依需求自訂實作。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解 Azure IoT 通訊協定閘道器，以及如何使用，並將其部署為 IoT 方案的一部分，請參閱︰

* [Azure IoT 通訊協定閘道器存放庫 GitHub 上](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT 通訊協定閘道器開發人員指南](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

若要進一步瞭解規劃 IoT 中心部署，請參閱︰

- [事件集線器與比較][lnk-compare]
- [縮放、 HA 和 DR][lnk-scaling]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
