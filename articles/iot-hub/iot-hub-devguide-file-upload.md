<properties
 pageTitle="開發人員指南-檔案上傳 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-上傳到 IoT 集線器從裝置的檔案"
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

# <a name="upload-files-from-a-device"></a>將檔案從裝置上傳

## <a name="overview"></a>概觀

[IoT 中心結束點]中的說明[lnk-endpoints]文章裝置可以啟動檔案上傳傳送通知透過裝置具端點 (**/devices/ {deviceId} / 檔案**)。  當裝置通知的完成上傳的 IoT 中樞時，IoT 中心會產生您為郵件接收到的服務具端點 (**/messages/servicebound/filenotifications**) 的檔案上傳通知。

而不是透過 IoT 集線器本身的仲介郵件，IoT 中心改為做為發送器相關聯的 Azure 儲存體帳戶。 裝置要求的儲存空間權杖從 IoT 中心的特定裝置想要上傳的檔案。 裝置上傳檔案儲存空間，以使用 SA URI 和裝置上傳完成時傳送到 IoT 集線器完成的通知。 IoT 中樞驗證的檔案上傳的時間，然後將檔案上傳通知加入至新的服務具檔案簡訊通知結束點。

您將檔案上傳到 IoT 集線器從裝置，您必須先設定您的中心關聯[Azure 儲存體][lnk-associate-storage]至該帳戶。

您的裝置可以[初始化傳][ lnk-initialize] ，然後[通知 IoT 中心][lnk-notify]完成上傳。 您也可以在裝置通知 IoT 中心上傳完畢時，當服務可以產生[通知訊息][lnk-service-notification]。

### <a name="when-to-use"></a>使用時機

當您需要上傳您的後端服務，而非傳送的郵件，透過 IoT 集線器從裝置檔案時，請使用此 IoT 中心功能。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure 儲存體帳戶關聯 IoT 中心

若要使用的檔案上傳功能，您必須先 IoT 集線器連結 Azure 儲存體帳戶。 您可以透過[Azure 入口網站][lnk-management-portal]，或以程式設計方式透過[IoT 中心資源提供者 REST Api][lnk-resource-provider-apis]。 一旦您有相關聯 IoT 中心 Azure 儲存體帳戶，服務會傳回 SA URI 裝置時裝置的檔案上傳要求。

> [AZURE.NOTE] [Azure IoT 中心 Sdk] [lnk-sdks]自動處理擷取 SA URI、 上傳檔案，和通知的完成上傳的 IoT 中樞。

## <a name="initialize-a-file-upload"></a>初始化檔案上傳

IoT 中心有特別要求 SA URI 儲存空間將檔案上傳至裝置的端點。 裝置在 [IoT 中心傳送文章起始檔案上傳程序`{iot hub}.azure-devices.net/devices/{deviceId}/files`下列 JSON 本文︰

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT 中心會傳回下列裝置用來上傳檔案︰

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>已遭取代︰ 初始化以取得檔案上傳

> [AZURE.NOTE] 本節將說明如何從 IoT 中心接收 SA URI 遭取代之的功能。 請使用上述方法文章。

IoT 中心有支援的檔案上傳]，以取得 SA URI 儲存空間，而另一個通知的完成上傳 IoT 中樞的其中一個的兩個其餘端點。 裝置取得傳送到 IoT 中樞在起始檔案上傳程序`{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`。 中心傳回 SA URI 特定檔案上傳，並用於完成上傳的相互關聯識別碼。

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>通知的 [已完成的檔案上傳的 IoT 中樞

裝置負責上傳的檔案，以使用 Azure 儲存體 Sdk 的儲存空間。 裝置一旦完成上傳，傳送至在 [IoT 中心文章`{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications`下列 JSON 本文︰

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

值的`isSuccess`是布林值代表是否已成功上載檔案。 狀態碼`statusCode`是要儲存的檔案上傳的狀態和`statusDescription`對應到`statusCode`。

## <a name="reference-topics"></a>參考主題︰

下列主題提供給您上傳檔案從裝置的詳細資訊。

## <a name="file-upload-notifications"></a>檔案上傳通知

在裝置上傳的檔案，並會通知 IoT 中心的上傳完成，服務選擇性地產生通知訊息包含名稱和儲存檔案的位置。

[結束點]所述[lnk-endpoints]，IoT 中心提供透過服務具端點 (**/messages/servicebound/fileuploadnotifications**) 與郵件的檔案上傳通知。 檔案上傳通知的接收語意雲端-裝置郵件一樣，而且有相同的[郵件生命週期][lnk-lifecycle]。 從檔案上傳通知端點擷取每一封郵件是 JSON 記錄具有下列內容︰

| 屬性 | 描述 |
| -------- | ----------- |
| EnqueuedTimeUtc | 指出時建立通知的時間戳記。 |
| DeviceId | **DeviceId**的裝置上傳的檔案。 |
| BlobUri | URI 上傳的檔案。 |
| BlobName | 上傳的檔案名稱。 |
| LastUpdatedTime | 表示檔案上次更新時間戳記。 |
| BlobSizeInBytes | 上傳檔案的大小。 |

**範例**。 這是檔案上傳通知訊息範例本文。

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>檔案上傳通知設定選項

每個 IoT 中心公開檔案上傳通知的下列設定選項︰

| 屬性 | 描述 | 範圍及預設值 |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | 控制檔案上傳通知會寫入檔案通知結束點。 | Bool。 預設︰ True。 |
| **fileNotifications.ttlAsIso8601** | 檔案上傳通知預設 TTL。 | ISO_8601 間隔最 48 H （最小值 1 分鐘）。 預設︰ 1 小時。 |
| **fileNotifications.lockDuration** | 鎖定檔案上傳通知佇列中的工期。 | 5 到 300 秒數 （最小的 5 秒）。 預設︰ 60 秒數。 |
| **fileNotifications.maxDeliveryCount** | 最大值傳遞計數檔案上傳通知佇列。 | 1 到 100。 預設︰ 100。 |

## <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已經學會如何上傳檔案從使用 IoT 中樞的裝置，您可能會感興趣的開發人員指南下列主題︰

- [管理裝置在 IoT 中心的身分識別][lnk-devguide-identities]
- [控制存取權 IoT 中心][lnk-devguide-security]
- [使用同步處理狀態和設定裝置盡相同][lnk-devguide-device-twins]
- [叫用的裝置上直接方法][lnk-devguide-directmethods]
- [多個裝置上的排程工作][lnk-devguide-jobs]

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [如何上傳檔案從裝置 IoT 中樞的雲端][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
