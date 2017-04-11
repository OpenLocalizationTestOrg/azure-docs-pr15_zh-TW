<properties
 pageTitle="啟用 [受管理的裝置前 IoT 閘道 |Microsoft Azure"
 description="使用 IoT 閘道器的指導方針主題建立使用 IoT 閘道器 SDK，以及管理 IoT 中樞的裝置。"
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# <a name="enable-managed-devices-behind-an-iot-gateway"></a>啟用 [受管理的裝置前 IoT 閘道器

## <a name="basic-device-isolation"></a>基本的裝置隔離

組織經常使用 IoT 閘道器，若要增加 IoT 解決方案的整體安全性。 某些裝置需要傳送資料至雲端，但不能在網際網路上威脅保護自己。 您可以讓這些裝置從外部執行緒是讓其透過閘道外部世界與通訊。

閘道位於在安全的環境及開啟網際網路之間的框線。 閘道器與裝置和閘道器會沿著郵件傳遞到正確的雲端目的地。 閘道器強化針對外部對話、 封鎖未經授權的要求，允許經過授權的繫結流量，而轉寄繫結流量到正確的裝置。

![][1]

您也可以將保護自己前額外的安全性的閘道器的裝置。 在這個案例中您只需要保留 OS 修補最新的漏洞，而非更新 OS 每個裝置上的閘道器。

## <a name="isolation-plus-intelligence"></a>隔離加上智慧

強化的路由器是足夠的閘道器，只要隔離裝置。 不過，IoT 方案通常需要閘道器提供更多智慧比只隔離裝置。 例如，您可能要雲端管理您的裝置。 您會無法使用[LWM2M](https://github.com/OpenMobileAlliance/OMA_LwM2M_for_Developers/wiki)，標準裝置管理的通訊協定，雲端管理解決方案的組件。 不過，使用非 TCP/IP 遙測啟用通訊協定裝置所傳送。 此外，裝置產生大量的資料，您僅要上傳的遙測篩選的子集。 您可以建置解決方案，其中包含資料的兩個不同的資料流與可以處理 IoT 閘道器。 閘道器應該︰

-   了解**遙測**、 篩選，然後上傳至雲端透過閘道器。 閘道器已不再簡單的路由器，只轉寄裝置與雲端之間的資料。

-   只要交換裝置與雲端之間**LWM2M 裝置管理資料**。 閘道器便不需要以了解即將，將資料，而且只需確定裝置與雲端之間來回傳遞資料。

下圖說明這種情況︰

![][2]

## <a name="the-solution-azure-iot-device-management-and-the-iot-gateway-sdk"></a>解決方案︰ Azure IoT 裝置管理與 IoT 閘道器 SDK 

公用預覽版本的[Azure IoT 裝置管理][lnk-device-management]和[Azure IoT 閘道器 SDK]的 beta 版啟用這種情況。 閘道器會處理資料的每個資料流，如下所示︰

-   **遙測**︰ 您可以使用 IoT 閘道器 SDK 建立了解、 篩選、 及遙測資料傳送至雲端的管線。 IoT 閘道器 SDK 提供實作開發人員代表此管線部分程式碼。 您可以尋找[IoT 閘道器 SDK-快速入門]SDK 的詳細資訊架構[lnk-gateway-get-started]教學課程。

-   **裝置管理**︰ Azure 裝置管理提供在裝置時的雲端裝置發出管理命令介面執行的 LWM2M 用戶端。
    
    您不需要任何特殊的邏輯的閘道器，因為它不需要處理裝置與您 IoT 中心之間交換 LWM2M 資料。 您可以啟用網際網路連線共用、 許多現代作業系統上，若要啟用 LWM2M 資料交換閘道器上的功能。 您可以選擇適合的作業系統，這種情況下，因為 IoT 閘道器 SDK 支援各種不同的作業系統。 以下是作業系統的啟用共用在[Windows 10]和[Ubuntu]，其中有許多支援上的網際網路連線的相關指示。

下圖顯示用來啟用這個案例中使用[Azure IoT 裝置管理]高層級架構[lnk-device-management]及[Azure IoT 閘道器 SDK]。

![][3]

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 IoT 閘道器 SDK，請參閱下列教學課程︰

- [IoT 閘道器 SDK-使用 Linux 快速入門][lnk-gateway-get-started]
- [IoT 閘道器 SDK – 使用 Linux 的模擬裝置裝置至雲端傳送郵件][lnk-gateway-simulated]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway-simulated]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT 閘道器 SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[在 Windows 10]: http://windows.microsoft.com/en-us/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-devguide]: iot-hub-devguide.md