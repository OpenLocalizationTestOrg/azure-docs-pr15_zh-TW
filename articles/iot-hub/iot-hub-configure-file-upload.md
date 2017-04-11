<properties
     pageTitle="使用 [Azure 入口網站設定檔案上傳 |Microsoft Azure"
     description="概略瞭解如何設定使用 Azure 入口網站的檔案上傳"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>設定檔案上傳使用 Azure 入口網站

## <a name="file-upload"></a>檔案上傳

若要使用的[檔案上傳 IoT 中心中的功能][lnk-upload]，您必須先關聯 Azure 儲存體帳戶中心。 選取的**檔案上傳**的設定，以顯示檔案上傳 IoT 中樞要修改之內容的清單。

![][13]

**儲存容器**︰ 使用 Azure 入口網站中 Azure 儲存體帳戶中您目前的 Azure 訂閱，與您 IoT 中樞選取 blob 容器。 如有需要，您可以建立 Azure 儲存體帳戶**容器**刀**儲存帳戶**刀和 blob 容器上。 IoT 中心會自動產生寫入權其上傳檔案時所使用的裝置此 blob 容器的 SA Uri。

![][14]

**接收訊息的通知上傳的檔案**︰ 啟用或停用透過切換檔案上傳通知。

**SA [TTL]**: 此設定，則時間存留傳回裝置 IoT 中樞的 SA uri。 預設設定為一小時，但可以自訂為使用滑桿其他值。

**設定預設 TTL 檔案通知**︰ 之前已經過期時間存留的檔案上傳通知。 依預設會設定為一天，但可以自訂為使用滑桿其他值。

**檔案通知最大值傳遞計數**︰ 的次數 IoT 中心嘗試傳送檔案上傳通知。 設定為 10，根據預設，但可以自訂為使用滑桿其他值。

![][15]

## <a name="next-steps"></a>後續步驟

IoT 中樞的檔案上傳功能的相關資訊，請參閱[從裝置的檔案上傳][lnk-upload]開發人員指南中。

請遵循這些連結，深入瞭解管理 Azure IoT 中心︰

- [大量管理 IoT 裝置][lnk-bulk]
- [使用指標][lnk-metrics]
- [監控的作業][lnk-monitor]

若要進一步探索 IoT 中樞的功能，請參閱︰

- [開發人員指南][lnk-devguide]
- [模擬 IoT 閘道器 SDK 的裝置][lnk-gateway]
- [設定 secure 從頭 IoT 方案][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md