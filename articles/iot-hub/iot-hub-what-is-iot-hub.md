<properties
 pageTitle="Azure IoT 中心概觀 |Microsoft Azure"
 description="Azure IoT 中心服務概觀︰ 什麼是 iot 中心裝置連線、 網際網路的項目通訊模式，以及服務協助通訊模式"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>什麼是 Azure IoT 中心？

歡迎使用 Azure IoT 中心。 本文提供 Azure IoT 中樞的概觀，並說明您為何使用這項服務實作網際網路的項目 (IoT) 解決方案。 Azure IoT 中心是完全受管理的服務，讓數百萬 IoT 裝置和解決方案的後端之間可靠和安全的雙向通訊。 Azure IoT 中心︰

- 提供可靠是 「 裝置-雲端和雲端-裝置在訊息。
- 啟用安全通訊使用每個裝置安全性認證與存取控制。
- 提供更多監視裝置連線與裝置身分識別管理事件。
- 包含最常用的語言與平台裝置文件庫。

文章[IoT 中心比較和事件集線器][lnk-compare]說明這兩個服務之間的主要差異，並醒目提示 IoT 方案中使用 IoT 中樞的優點。

![為項目方案的網際網路雲端閘道器的 azure IoT 中心][img-architecture]

> [AZURE.NOTE] 在您採取進階的討論區 IoT 架構，請參閱[Microsoft Azure IoT 參考架構][lnk-refarch]。

## <a name="iot-device-connectivity-challenges"></a>IoT 裝置連線挑戰

IoT 中心和裝置文件庫，可協助您符合如何可靠且安全地裝置連線至的方案後端的挑戰。 IoT 裝置︰

- 通常都沒有人運算子內嵌的系統。
- 可以是在遠端位置，耗費的實體存取位於何處。
- 僅能透過方案後端。
- 可能會有限制和處理資源。
- 可能會有斷斷續續、 變得很慢，或昂貴的網路連線。
- 可能需要使用專屬、 自訂或產業的專用的應用程式的通訊協定。
- 可以建立使用大量的常用的硬體與軟體平台。

除了上方的需求，小數位數、 安全性和可靠性，也必須傳遞任何 IoT 解決方案。 設定連線需求的結果是困難且費時實作當您使用傳統技術，例如網頁容器和訊息經紀人。

## <a name="why-use-azure-iot-hub"></a>為什麼要使用 Azure IoT 中心

Azure IoT 中心位址裝置連線挑戰以下列方式︰

-   **每個裝置驗證及安全的連線**。 您可以提供每個裝置使用自己[的安全性金鑰][lnk-devguide-security]啟用以連接至 IoT 中心。 [IoT 中心身分識別登錄][lnk-devguide-identityregistry]方案中儲存裝置身分識別與索引鍵。 解決方案的後端可以新增個別的裝置，若要允許或拒絕清單來啟用裝置存取完整的控制權。

-   **監控的裝置連線作業**。 您會收到關於裝置身分識別管理作業和裝置連線事件的詳細的作業記錄。 此監控功能可讓您 IoT 解決方案，以識別連線發生問題，例如嘗試錯誤的認證與連線的裝置太過頻繁，傳送郵件] 或 [拒絕所有的雲端-裝置郵件。

-   **更多組裝置文件庫**。 [Azure IoT 裝置 Sdk] [lnk-device-sdks]可用且支援各種不同的語言與平台-C 許多 Linux 散佈、 Windows 和即時的作業系統。 Azure IoT 裝置 Sdk 也支援受管理的語言，例如 C#、 Java 和 JavaScript。

-   **IoT 通訊協定和擴充功能**。 如果您的方案無法使用的裝置文件庫，IoT 中心公開公用的通訊協定，可讓原本即使用 MQTT v3.1.1、 HTTP 1.1 （英文） 或 AMQP 1.0 通訊協定的裝置。 您也可以延伸 IoT 中心以提供自訂的通訊協定的支援︰

    - 建立欄位閘道器與[Azure IoT 閘道器 SDK] [lnk-gateway-sdk]您自訂的通訊協定轉換其中三個理解 IoT 中樞的通訊協定。 
    - 自訂[Azure IoT 通訊協定閘道器][protocol-gateway]，在雲端中執行的開啟來源元件。

-   **縮放比例**。 Azure IoT 中心縮放數百萬同時連線的裝置並數百萬秒的事件。

下列優點是一般許多通訊模式。 IoT 中心目前可讓您執行下列特定的通訊模式︰

-   **事件架構裝置至雲端 ingestion。** IoT 中心可靠可從您的裝置中接收數百萬秒的事件。 它可以然後處理這些快速鍵路徑上使用事件處理器引擎。 它也能將它們儲存在低溫分析的路徑。 IoT 中心會保留最多為確保可靠的處理和吸收的載入七天的事件資料。

-   **可靠雲端-裝置訊息 (或*命令*)。 * * 方案後端可以使用 IoT 中心個別的裝置傳送郵件在最少次傳遞保證。 每一封郵件會有個別 live 時間設定，並在後端可以要求送達和到期日信回條。 這些信回條確保完整完善生命週期的雲端-裝置郵件。 然後，您可以實作商務邏輯包含在裝置執行的作業。

-   **上傳至雲端的檔案和快取感應器資料。** 您的裝置可以上傳檔案到使用 SA Uri 由 IoT 中心管理您的 Azure 儲存體。 若要啟用後端處理雲端送達檔案時，IoT 中心可以產生通知。

## <a name="gateways"></a>閘道器

在閘道 IoT 解決方案中的通常是 [[通訊協定閘道器][lnk-gateway]的部署在雲端或[欄位閘道器][lnk-field-gateway]的本機部署與您的裝置。 通訊協定的閘道器會執行通訊協定轉譯，例如以 AMQP MQTT。 欄位的閘道器可以邊緣上執行分析，做出即時減少延遲提供裝置管理服務、 安全性與隱私權的限制式，強制執行，也執行 [通訊協定翻譯]。 這兩種閘道器類型可作為橋樑裝置與您 IoT 中心之間。

從簡單的傳輸路由裝置 （例如網路位址轉譯裝置或防火牆） 的功能變數的閘道器不同，因為它通常會執行作用中的角色中管理您的方案中存取和資訊流程。

解決方案可能包含通訊協定與欄位的閘道器。

## <a name="how-does-iot-hub-work"></a>IoT 中心如何運作？

Azure IoT 中心實作[服務協助通訊][lnk-service-assisted-pattern]橋樑裝置與您的方案後端之間的互動的模式。 服務協助通訊的目標是建立值得信任，控制系統，例如 IoT] 中心內及特殊用途的裝置中部署之間的雙向通訊路徑不受信任的實體空間。 模式會建立下列原則︰

- 安全性優先於其他所有功能。
- 裝置不接受不明的網路資訊。 在裝置，建立所有連線及路由僅輸出的方式。 從後端接收命令的裝置，裝置必須定期啟動的連線，檢查有任何擱置的命令來處理程序。
- 裝置只應該連線至，或建立路由已知他們 peered，例如 IoT 中樞的服務。
- 應用程式通訊協定層的保護裝置和服務或之間裝置和閘道的通訊路徑。
- 系統層級的授權和驗證會根據每個裝置身分識別。 進行存取認證與權限可撤銷幾乎立即之。
- 連線的裝置有時候會因為 power 或連線問題的雙向通訊可協助按住命令和裝置通知，直到裝置連線至會收到通知。 IoT 中心會保留 [傳送] 命令的裝置的特定佇列。
- 透過在特定的服務的閘道器的受保護傳輸分別禁止應用程式內容資料。

行動裝置的產業具有用於服務協助通訊模式在龐大實作推入通知服務，例如， [Windows 推入通知服務][lnk-wns]， [Google 雲端訊息][lnk-google-messaging]，與[Apple 推入通知服務][lnk-apple-push]。

IoT 中心支援 ExpressRoute 的公用等路徑上。

## <a name="next-steps"></a>後續步驟

若要瞭解如何 Azure IoT 中心可以讓您從遠端管理、 設定和更新您的裝置標準 IoT 裝置管理，請參閱[Azure IoT 中心概觀裝置管理][lnk-device-management]。

若要在各種裝置硬體平台與作業系統上執行用戶端應用程式，您可以使用 IoT 裝置 Sdk。 IoT 裝置 Sdk 包含順利傳送遙測 IoT 中心和接收雲端-裝置命令的文件庫。 當您使用 Sdk 時，您可以從各種不同的網路通訊協定選擇與 IoT 中樞通訊。 若要深入瞭解，請參閱[裝置 Sdk 的相關資訊][lnk-device-sdks]。

若要開始撰寫的部分程式碼，並執行的一些範例，請參閱[開始使用 IoT 中心][lnk-get-started]教學課程。

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "服務協助通訊 Clemens Vasters 的部落格文章"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
