<properties
 pageTitle="開發人員指南-詞彙 |Microsoft Azure"
 description="相關 IoT 中心常見的詞彙的詞彙"
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

# <a name="glossary-of-iot-hub-terms"></a>IoT 中心字詞的詞彙

本文將列出一些常見的詞彙 IoT 中心相關聯。

## <a name="advanced-message-queueing-protocol-amqp"></a>進階的訊息佇列通訊協定 (AMQP)

[AMQP](https://www.amqp.org/)是一種 IoT 中心支援與裝置通訊的訊息通訊協定。 如需支援 IoT 中樞的訊息通訊協定的詳細資訊，請參閱[傳送及接收郵件 IoT 中心](iot-hub-devguide-messaging.md)。

## <a name="cloud-to-device-c2d"></a>雲端裝置 (C2D)

通常用來指從 IoT 中樞傳送至連線的裝置的訊息。 通常，這些訊息是指示採取行動裝置的命令。 如需詳細資訊，請參閱[傳送及接收郵件 IoT 中心](iot-hub-devguide-messaging.md)。

## <a name="condition"></a>條件

報告裝置應用程式，請指向裝置狀態資訊，例如目前使用中的 [連線] 方法。 裝置也可以回報其功能。 您可以查詢條件，並使用裝置雙生的功能。

## <a name="data-point-message"></a>資料點的郵件

資料點訊息是包含遙測資料，例如風速或色溫雲端-裝置訊息。

## <a name="desired-properties"></a>想要的內容

在裝置盡相同的內容，所要的內容使用配合*報告的內容*進行同步處理裝置設定] 或 [條件。 想要的屬性僅可設定應用程式回結束，觀察裝置應用程式。 

## <a name="device-to-cloud-d2c"></a>裝置至雲端 (D2C)

通常用來參照到 IoT 集線器從連線的裝置傳送的郵件。 這些訊息可能[資料點](#data-point-message)或[互動](#interactive-message)的郵件。 如需詳細資訊，請參閱[傳送及接收郵件 IoT 中心](iot-hub-devguide-messaging.md)。

## <a name="device-identity-registry"></a>裝置身分識別登錄

[裝置身分識別登錄](iot-hub-devguide-identity-registry.md)是儲存允許連線至中樞的個別裝置的相關資訊的 IoT] 中心內建元件。

## <a name="device"></a>裝置

在裝置在 IoT 內容，通常是小型，獨立計算裝置可能會收集資料，或控制其他裝置。 例如裝置可能，環境監控裝置或澆水和通風系統中 greenhouse 控制站。

## <a name="device-twin"></a>裝置雙生

[裝置雙生](iot-hub-devguide-device-twins.md)是在 IoT 中心的條件和組態設定的實體裝置的複本。 您可以使用的裝置雙生管理實體裝置的設定。

## <a name="direct-method"></a>直接方法

[直接方法](iot-hub-devguide-direct-methods.md)可供您觸發方法叫用的 API IoT 中心上執行的裝置上的方式。

## <a name="event-hub-compatible-endpoint"></a>事件相容於中樞的端點

若要讀取裝置至雲端訊息傳送給您 IoT 中心，您可以連線到端點中心上，然後使用的任何事件中心相容方法讀取的郵件。 事件中心相容方法包括使用事件集線器 Sdk 和 Azure 資料流分析。

## <a name="field-gateway"></a>功能變數閘道器

功能變數的閘道器可讓您的裝置無法直接連接到 IoT 集線器，通常部署本機裝置的連線。 如需詳細資訊，請參閱[什麼是 Azure IoT 中心？](iot-hub-what-is-iot-hub.md)。

## <a name="job"></a>工作

您的方案後端可以使用工作排程與裝置註冊您 IoT 中樞的一組追蹤活動。 活動包含更新裝置雙生標籤，並且叫用直接的方法，更新裝置雙生所需內容。

## <a name="protocol-gateway"></a>通訊協定閘道器

通訊協定的閘道器通常部署在雲端，並提供通訊協定的裝置連線到 IoT 中樞的翻譯服務。 如需詳細資訊，請參閱[什麼是 Azure IoT 中心？](iot-hub-what-is-iot-hub.md)。

## <a name="interactive-message"></a>互動式的訊息

互動式的郵件是雲端-裝置訊息的觸發程序中的應用程式後端立即的動作。 比方說，裝置可能會傳送有關應該自動登入 CRM 系統失敗警示。

## <a name="iot-hub"></a>IoT 中心

IoT 中心是可讓可靠和安全的雙向通訊數百萬種 IoT 裝置和解決方案的後端之間的完整的管理 Azure 服務。 如需詳細資訊，請參閱[什麼是 Azure IoT 中心？](iot-hub-what-is-iot-hub.md)。

## <a name="iot-suite"></a>IoT 套件

Azure IoT 套件封裝一起使用預先設定的解決方案的多個 Azure 服務。 這些預先設定的解決方案可讓您快速開始使用的常見 IoT 案例的端對端實作。 如需詳細資訊，請參閱[什麼是 Azure IoT 套件？](../iot-suite/iot-suite-overview.md)。

## <a name="job"></a>工作

IoT 中心中的[工作](iot-hub-devguide-jobs.md)可讓您執行的作業，例如韌體跨多個裝置連接到您集線器升級。

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/)是一種 IoT 中心支援與裝置通訊的訊息通訊協定。 如需支援 IoT 中樞的訊息通訊協定的詳細資訊，請參閱[傳送及接收郵件 IoT 中心](iot-hub-devguide-messaging.md)。

## <a name="reported-properties"></a>報告的內容

在裝置盡相同的內容，報告的內容是搭配*需的屬性*用來同步處理裝置設定] 或 [條件。 報告的內容僅可設定的裝置應用程式，並可以讀取及查詢的應用程式後端。

## <a name="tags"></a>標記

在 devcie 盡相同的內容，標籤是裝置中繼資料儲存並擷取的應用程式後端 JSON 文件的格式。 在裝置上的應用程式看不到 [標籤]。

## <a name="system-properties"></a>系統內容

在裝置盡相同的內容，系統內容會為唯讀，且包含有關裝置使用，例如最後的活動時間和連線狀態的資訊。