<properties
 pageTitle="開發人員指南主題 IoT 中心 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南，其中包含 IoT 中心端點、 安全性、 裝置身分識別登錄、 裝置管理和訊息"
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

# <a name="azure-iot-hub-developer-guide"></a>Azure IoT 中心開發人員指南

Azure IoT 中心是完全受管理的服務，協助啟用可靠和安全數百萬種 IoT 裝置之間的雙向通訊和回應用程式結束。

Azure IoT 中心會提供您︰

* 使用每個裝置安全性憑證安全通訊與存取控制。
* 可靠裝置至雲端和雲端-裝置超比例訊息。
* 最常用的語言與平台裝置文件庫使用方便的裝置連線。

本 IoT 中心開發人員指南包含下列文章︰

- [傳送及接收郵件與 IoT 中心][devguide-messaging]說明 IoT 中心公開訊息功能 （裝置至雲端和雲端-裝置）。 本文也包含郵件格式，例如主題相關的資訊與支援的通訊協定和他們使用連接埠號碼。
- [將檔案從裝置上傳][devguide-upload]說明如何上傳檔案從裝置。 本文也包含 uplaod 程序可以傳送主題，例如通知] 資訊。
- [管理裝置身分識別，在 IoT 中心][devguide-identities]說明資訊會將儲存每個 IoT 中樞的裝置身分識別登錄，及如何存取並加以修改。
- [控制存取權 IoT 中心][devguide-security]描述用於 IoT 中心功能的兩個裝置的存取權授與及雲端元件的安全性模型。 本文包含使用權杖和 X.509 憑證，您可以授與的權限的詳細資料的相關資訊。
- [使用同步處理狀態和設定裝置盡相同][devguide-device-twins]描述*裝置雙生*概念，以及它公開例如其雙生與同步處理裝置的功能。 本文包含裝置雙生中儲存的資料的相關資訊。
- [叫用的裝置上直接方法][devguide-directmethods]描述的生命週期的直接的方法，如何叫用您的後端應用程式在裝置上的方法，處理您的裝置上的直接方法的相關資訊。
- [多個裝置上的工作排程][devguide-jobs]描述您可以在如何排程多個裝置上的工作。 本文說明如何提交執行工作，例如執行直接的方法，更新使用 devcie 雙生 devcie 的工作。 也將說明如何查詢工作狀態。
- [參照-IoT 中心端點][devguide-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。 本文也說明如何使用功能變數的閘道器，讓某些裝置連線到您 IoT 中心結束點。
- [參照-盡相同、 方法及工作的查詢語言][devguide-query]說明該查詢語言可擷取您中心裝置盡相同與工作的相關資訊。
- [參照-配額和節流][devguide-quotas]摘要列出配額設定 IoT 中心服務和節流若要查看您在超出配額時，您可以預期的行為。
- [參照-裝置和服務 Sdk] [devguide-sdks]清單 Sdk，您可以使用開發用的裝置和服務應用程式的互動 IoT 中心。 本文包含線上 API 文件的連結。
- [參照-IoT 中心 MQTT 支援][devguide-mqtt]提供 IoT 中心如何支援 MQTT 通訊協定的詳細的資訊。 本文說明的內建至 Sdk MQTT 通訊協定的支援，並提供直接使用 MQTT 通訊協定的相關資訊。
- [詞彙][devguide-glossary]常見 IoT 中心相關字詞的清單。



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

