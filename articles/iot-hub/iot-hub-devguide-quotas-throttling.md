<properties
 pageTitle="開發人員指南-配額和節流 |Microsoft Azure"
 description="Azure IoT 中心開發人員指南-適用於 IoT 中心及節流預期的配額的描述"
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

# <a name="reference---quotas-and-throttling"></a>參照-配額和節流

## <a name="quotas-and-throttling"></a>配額和節流

每個 Azure 訂閱可以有最多 10 個 IoT 集線器。

每個 IoT 中心已佈建在特定的 SKU 單位以特定數字 (如需詳細資訊，請參閱[Azure IoT 中心價格][lnk-pricing])。 SKU 和單位數量決定您可以傳送訊息的最大的每日配額。

SKU 也會決定 IoT 中心會強制執行所有作業節流限制。

## <a name="operation-throttles"></a>作業節流

作業節流是中 minute 的範圍，就會套用，為了避免不當使用工資率限制。 IoT 中心嘗試避免傳回錯誤可能的話，但同時也會傳回例外狀況，如果流速違反太久。

以下是強制節流的清單。 參照個別的中樞的值。

| 節流 | 每個中心值 |
| -------- | ------------- |
| 身分識別的登錄作業 （建立、 擷取、 清單、 更新、 刪除） | 5000/min/單位 （S3) <br/> 100/min/單位 （S1 和 S2）。 |
| 裝置連線 | 6000/sec/單位 （S3) 120/sec/單位 （S2) 12/sec/單位 （S1)。 <br/>100 秒的最小值。 <br/> 例如，兩個 S1 單位是 2\*12 = 24/sec，但您必須至少 100/sec 過您的單位。 您可以使用九個 S1 單位，108/sec (9\*12) 透過您的單位。 |
| 傳送裝置至雲端 | 6000/sec/單位 （S3) 120/sec/單位 （S2) 12/sec/單位 （S1)。 <br/>100 秒的最小值。 <br/> 例如，兩個 S1 單位是 2\*12 = 24/sec，但您必須至少 100/sec 過您的單位。 您可以使用九個 S1 單位，108/sec (9\*12) 透過您的單位。 |
| 雲端的裝置傳送 | 5000/min/單位 （S3) 100/min/單位 （S1 和 S2）。 |
| 雲端-裝置收到 | 50000/min/單位 （S3) 1000年/min/單位 （S1 和 S2）。 |
| 檔案上傳作業 | 5000 檔案上傳通知/min/單位 （S3)、 100 檔案上傳通知/min/單位 （適用於 S1 和 S2）。 <br/> 10000 SA Uri 可查看 Azure 儲存體帳戶一次。<br/> 10 SA Uri/裝置可查看一次。 | 

請務必釐清*裝置連線*流速控制的速度，可以建立新的裝置連接 IoT] 中心內，與不同時連線裝置的最大數目。 流速佈建的中樞的單位數量而定。

例如，如果您購買一個 S1 單位，您會收到流速秒 100 連線。 這表示連線 100000 裝置，需要至少 1000 秒 （約 16 分鐘）。 不過，您可以有多同時連線的裝置必須在您裝置的身分識別登錄中註冊裝置。

IoT 中樞的深入討論節流行為] 中，請參閱部落格文章[節流 IoT 中心，而且您][lnk-throttle-blog]。

>[AZURE.NOTE] 在任何時候，就可以增加配額或流速限制增加在 IoT 中心能夠單位數量。

>[AZURE.IMPORTANT] 身分識別的登錄作業被專供裝置管理和佈建案例中執行階段。 讀取或更新大量裝置身分識別的支援透過[匯入及匯出作業][lnk-importexport]。

## <a name="next-steps"></a>後續步驟

在此 IoT 中心開發人員指南其他參考主題包括︰

- [IoT 中心端點][lnk-devguide-endpoints]
- [查詢語言盡相同、 方法及工作][lnk-devguide-query]
- [IoT 中心 MQTT 支援][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md