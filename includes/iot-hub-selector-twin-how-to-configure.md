> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)

## <a name="introduction"></a>簡介

在[開始使用 IoT Hub 雙][lnk-twin-tutorial]、 學會如何使用*標記*報告裝置條件使用*報告的內容*、 裝置應用程式從您的解決方案後端設定裝置的中繼資料和查詢使用的 SQL 類似的語言此資訊。

在本教學課程中，您將了解如何使用雙生*所需的屬性*與*報告的內容*，若要從遠端設定裝置應用程式搭配使用。 更明確本教學課程示範如何雙生的報告及所需的屬性啟用為裝置應用程式設定多個步驟設定並提供所有裝置間的這項作業的狀態解決方案後端看得到。

在高層級中，此教學課程會遵循裝置管理*所需的狀態圖樣*。 此模式的基本概念是有解決方案後端指定所要的受管理的裝置，而不傳送特定命令的狀態。 這會放負責建立達到所需的狀態 （其中特定裝置條件會影響能夠立即執行特定命令的 IoT 案例中十分重要，） 的最佳方式裝置時持續報告給後端的目前狀態和可能的錯誤條件。 所需的狀態模式是狀態的很有幫助管理大型集的裝置，它可讓後端之間的所有裝置有完整的可見性設定程序。
您可以在[Azure IoT 中樞概觀 （英文） 裝置管理]裝置管理中找到有關角色的所需的狀態模式的詳細資訊[lnk-dm-overview]。

> [AZURE.NOTE] 在其中裝置控制其他互動的方式 （從使用者控制 app 風扇開啟） 的情況下，請考慮使用[雲端-裝置方法][lnk-methods]。

在本教學課程中，應用程式後端變更遙測設定的目標裝置和裝置應用程式，因為接在此教學課程會模擬簡單延遲多個步驟程序 （如需要軟體模組重新啟動） 組態更新、 套用）。

後端會以下列方式儲存裝置雙生所需屬性中的設定：

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [AZURE.NOTE] 設定可以複雜的物件，因為它們通常是指派唯一識別碼 (雜湊或[Guid][lnk-guid]) 簡化其比較。

裝置應用程式報告及其鏡像報告的內容中所需的屬性**telemetryConfig**的目前設定：

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

請注意如何報告的**telemetryConfig**具有其他屬性**狀態**，用來報告組態更新程序的狀態。

接收新所需的設定時, 裝置應用程式會報告暫止的組態變更的資訊：

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

然後，在某些稍後裝置應用程式將會報告失敗的這項作業成功由更新上面的屬性。
請注意後端的方式可以，在任何時候，跨所有裝置查詢組態程序的狀態。

此教學課程示範如何以：

- 建立從後端接收組態更新並報告多項更新為*報告的屬性*設定更新程序的模擬的裝置。
- 建立更新的裝置所需的設定，然後會查詢組態更新程序的後端應用程式。

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier