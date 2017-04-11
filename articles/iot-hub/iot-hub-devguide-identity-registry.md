<properties
 pageTitle="開發人員指南-裝置身分識別登錄 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-裝置身分識別登錄機碼，以及如何用它來管理您的裝置的描述"
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

# <a name="manage-device-identities-in-iot-hub"></a>管理裝置在 IoT 中心的身分識別

## <a name="overview"></a>概觀

每個 IoT 中心有裝置身分識別登錄儲存裝置允許連線至中樞的相關資訊。 裝置可以連線到集線器之前，必須要有該裝置中樞的裝置身分識別登錄中的項目。 在裝置也必須驗證與中樞根據裝置身分識別登錄中儲存的認證。

在高的層級，裝置身分識別登錄是裝置身分識別資源可其餘的集合。 當您在登錄新增項目時，IoT 中心會建立一組的每個裝置資源的服務，例如佇列中包含雲端-裝置訊息中。

### <a name="when-to-use"></a>使用時機

當您需要提供的連線到您的 IoT 集線器，當您需要控制每個裝置的存取權的裝置具結束點，在您的中心裝置時，請使用裝置身分識別登錄機碼。

> [AZURE.NOTE] 裝置身分識別登錄機碼並不會包含任何特定應用程式的中繼資料。

## <a name="device-identity-registry-operations"></a>裝置身分識別登錄作業

IoT 中心裝置身分識別登錄公開下列作業︰

* 建立裝置的身分識別
* 更新裝置身分識別
* 擷取裝置識別碼的身分識別
* 刪除裝置身分識別
* 清單超過 1000 個身分識別
* 將所有的身分識別匯出至 Azure blob 儲存體
* 從 Azure blob 儲存體匯入身分識別

所有這些作業可以使用樂觀，以指定[RFC7232][lnk-rfc7232]。

> [AZURE.IMPORTANT] 擷取中樞的身分識別登錄中的所有身分識別的唯一方法是使用 [[匯出][lnk-export]功能。

IoT 中心裝置身分識別登錄︰

- 不含任何應用程式的中繼資料。
- 可存取的字典，例如使用**deviceId**索引鍵。
- 不支援生動的查詢。

IoT 解決方案通常會有不同的方案特定存放區包含特定應用程式的中繼資料。 智慧建置解決方案中的特定解決方案存放區，例如記錄部署溫度計時的聊天室。

> [AZURE.IMPORTANT] 只使用裝置管理與佈建作業裝置身分識別登錄機碼。 在執行階段高處理量作業不應該取決於裝置身分識別登錄中執行的作業。 例如，命令在傳送前檢查裝置的連線狀態不支援的圖樣的線條。 請務必[節流費率][lnk-quotas]裝置身分識別登錄機碼，和[裝置活動訊號][lnk-guidance-heartbeat]圖樣。

## <a name="disable-devices"></a>停用的裝置

您可以更新的身分識別登錄中的 [**狀態**] 屬性，以停用的裝置。 一般而言，您可以使用此屬性在兩個案例︰

- 在提供的協調流程程序。 如需詳細資訊，請參閱[裝置佈建][lnk-guidance-provisioning]。
- 如果您基於任何原因，您可以考慮受到或變得未經授權的裝置。

## <a name="import-and-export-device-identities"></a>匯入及匯出裝置身分識別

您也可以使用 [非同步作業[IoT 中心資源提供者的端點]上從 IoT 中樞的身分識別登錄，匯出裝置，大量的身分識別[lnk-endpoints]。 匯出是使用儲存從身分識別登錄讀取裝置身分識別資料的客戶提供 blob 容器的較長時間執行工作。

您可以使用 [非同步作業[IoT 中心資源提供者的端點]上裝置，大量的身分識別匯入 IoT 中樞的身分識別登錄，[lnk-endpoints]。 匯入是使用客戶提供 blob 容器中的資料來裝置身分識別資料寫入裝置身分識別登錄機碼的較長時間執行工作。

- 匯入及匯出 Api 的詳細資訊，請參閱[IoT 中心資源提供者 REST Api][lnk-resource-provider-apis]。
- 若要進一步瞭解執行匯入及匯出作業，請參閱[大量 IoT 中心裝置身分識別管理][lnk-bulk-identity]。

## <a name="device-provisioning"></a>佈建裝置

指定的 IoT 解決方案儲存裝置資料取決於該方案的特定需求。 不過，解決方案必須至少儲存裝置身分識別與驗證索引鍵。 Azure IoT 中心包含身分識別登錄，可以儲存為每個裝置，例如識別碼、 驗證金鑰和狀態碼的值。 解決方案可以使用 Azure 資料表儲存體、 Azure blob 儲存體或 Azure DocumentDB 其他 Azure 服務來儲存任何其他裝置資料。

*裝置佈建*是將初始裝置資料新增至您的方案中的儲存區的程序。 若要啟用新裝置連線到您的中心，您必須將新的裝置識別碼與索引鍵新增至 IoT 中心身分識別登錄機碼。 提供的程序的一部分，您可能需要初始化在其他方案存放裝置的特定資料。

## <a name="device-heartbeat"></a>裝置活動訊號

IoT 中心身分識別登錄包含名**connectionState**的欄位。 您只能在開發期間使用**connectionState**欄位和偵錯 IoT 解決方案應該不查詢欄位在執行階段 （例如，若要檢查裝置是否已連接才能決定是否要傳送雲端-裝置郵件或簡訊）。

如果 IoT 方案必須知道如果裝置連接 （在執行階段，或非**connectionState**屬性會提供更精確），而您的方案應該實作*活動訊號圖樣*。

活動訊號模式中，使用的裝置傳送裝置至雲端訊息至少一次每個固定的 （例如，至少每小時） 的時間量。 這表示，即使在裝置並沒有任何要傳送的資料，它仍會傳送以空白裝置至雲端訊息 （通常是識別為活動訊號的屬性）。 服務位於方案維護地圖的最後一個活動訊號接收的每一個裝置，並假設有問題的裝置如果未預期的時間內收到活動訊號郵件。

更複雜的實作可能包含的資訊[作業監控][lnk-devguide-opmon]識別嘗試連線或通訊但失敗的裝置。 當您執行的活動訊號模式時，請務必[IoT 中心配額和節流][lnk-quotas]。

> [AZURE.NOTE] 如果 IoT 方案需要裝置連線狀態，只是要決定是否要傳送的郵件雲端-裝置，大量的裝置不廣播訊息是，以圖樣考慮是使用簡短的到期時間。 此達成相同的結果為維護裝置連線狀態登錄使用時更具效率的活動訊號圖樣。 您也可，要求訊息的通知，通知 IoT 中樞的哪些裝置可接收訊息而不是線上或會失敗。

## <a name="reference-topics"></a>參考主題︰

下列主題提供給您深入瞭解 devcie 身分識別登錄機碼。

## <a name="device-identity-properties"></a>裝置識別屬性

使用下列屬性的 JSON 文件以表示裝置身分識別。

| 屬性 | 選項 | 描述 |
| -------- | ------- | ----------- |
| deviceId | 需要的唯讀狀態更新 | ASCII 7 位元英數字元的區分大小寫字串 （最多 128 個字元） + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`。 |
| generationId | 需要的唯讀狀態 | 中樞產生、 區分大小寫的字串超過 128 個字元數。 這用來區分相同**deviceId**，裝置時他們已刪除的郵件，重新建立。 |
| etag | 需要的唯讀狀態 | 字串，表示裝置身分識別，依照[RFC7232]弱式 etag[lnk-rfc7232]。|
| 驗證 | 選擇性 | 包含驗證資訊和安全性教材的複合索引的物件。 |
| auth.symkey | 選擇性 | 複合索引的物件，其中包含的主要與次要鍵，請儲存 base64 格式。 |
| 狀態 | 所需 | Access 標記。 可以**啟用**或**停用**。 如果**啟用**，裝置便可連線。 如果**停用**，此裝置無法存取任何裝置具結束點。 |
| statusReason | 選擇性 | 128 字元長字串存放裝置身分識別狀態的原因。 允許所有 utf-8 字元。 |
| statusUpdateTime | 唯讀狀態 | 暫時指標，顯示的日期和時間的最後一個狀態更新。 |
| connectionState | 唯讀狀態 | 指出連線狀態欄位: [**已連線**或**中斷連線**。 此欄位表示裝置連線狀態 IoT 中心檢視。 **重要事項**︰ 此欄位應該用於僅適用於開發/偵錯之用。 連線狀態會更新僅適用於使用 MQTT 或 AMQP 的裝置。 此外，根據通訊協定層級的 ping （MQTT ping 或 AMQP ping），而且可以有上限只有 5 分鐘的延遲。 基於下列原因，可以有誤判，例如裝置報告為連線，但的實際中斷連線。 |
| connectionStateUpdatedTime | 唯讀狀態 | 暫時指標，顯示的日期和最後一次連線狀態已更新。 |
| lastActivityTime  | 唯讀狀態 | 暫時的指標，顯示的日期和最後一次裝置連線、 接收]，或傳送的郵件。 |

> [AZURE.NOTE] 連線狀態只能代表 IoT 中心檢視的連線狀態。 更新為此狀態可能會延遲，視網路條件和設定而定。

## <a name="additional-reference-material"></a>其他參考資料

在 [開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-endpoints]描述每個 IoT 中心公開執行階段] 和 [管理作業的各種結束點。
- [節流和配額][lnk-quotas]說明適用於 IoT 中心服務及使用服務時的預期節流行為配額。
- [IoT 中心裝置和服務 Sdk] [lnk-sdks]列出各種語言 Sdk 您開發用的裝置和服務應用程式的互動 IoT 中心時使用。
- [查詢語言盡相同、 方法及工作][lnk-query]說明您可以用來擷取資訊 IoT 中心從您的裝置盡相同、 方法及工作查詢語言。
- [IoT 中心 MQTT 支援][ lnk-devguide-mqtt] MQTT 通訊協定提供有關 IoT 中心支援的詳細資訊。

## <a name="next-steps"></a>後續步驟

現在您已經學會如何使用 IoT 中心裝置身分識別登錄機碼，您可能會感興趣的開發人員指南下列主題︰

- [控制存取權 IoT 中心][lnk-devguide-security]
- [使用同步處理狀態和設定裝置盡相同][lnk-devguide-device-twins]
- [叫用的裝置上直接方法][lnk-devguide-directmethods]
- [多個裝置上的排程工作][lnk-devguide-jobs]

如果您想要嘗試的一些本文所述的概念，您可能會感興趣的下列 IoT 中心教學課程︰

- [開始使用 Azure IoT 中心][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md