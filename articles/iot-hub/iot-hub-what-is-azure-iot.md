<properties
 pageTitle="Azure 方案的網際網路的項目 |Microsoft Azure"
 description="IoT 上 Azure 包括範例解決方案架構，以及如何將其與 Azure IoT 中心與裝置 Sdk 預先設定的解決方案的概觀"
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
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>後續步驟

Azure IoT 中心啟用安全 Azure 服務，而您的應用程式之間可靠的雙向通訊回復結束和數百萬種裝置。 它可以讓應用程式後的端至︰

- 收到遙測在從您的裝置。
- 從您的裝置傳送資料至資料流事件處理器。
- 從裝置中接收檔案上傳。
- 傳送特定裝置的雲端-裝置命令。

您可以使用 IoT 中心實作您自己的解決方案後端。 此外，IoT 中心包含用來佈建裝置、 其安全性認證及他們的權限，以連線至中樞裝置身分識別登錄。 若要進一步瞭解 IoT 集線器，請參閱[IoT 中心是什麼？][lnk-iot-hub].

若要瞭解如何 Azure IoT 中心可以讓您從遠端管理、 設定和更新您的裝置標準 IoT 裝置管理，請參閱[Azure IoT 中心概觀裝置管理][lnk-device-management]。

若要在各種裝置硬體平台與作業系統上執行用戶端應用程式，您可以使用 IoT 裝置 Sdk。 IoT 裝置 Sdk 包含順利傳送遙測 IoT 中心和接收雲端-裝置命令的文件庫。 當您使用 Sdk 時，您可以從多個網路通訊協定選擇與 IoT 中樞通訊。 若要深入瞭解，請參閱[裝置 Sdk 的相關資訊][lnk-device-sdks]。

若要開始撰寫的部分程式碼，並執行的一些範例，請參閱[開始使用 IoT 中心][lnk-getstarted]教學課程。

您也可能有興趣的[Azure IoT 套件][lnk-iot-suite]，這是預先設定的解決方案的集合。 IoT 套件可讓您快速入門並不按比例縮放 IoT 專案以處理一般 IoT 案例-例如遠端監控、 資產管理及預測進行的維修作業。

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md