<properties
 pageTitle="IoT 中心裝置管理概觀 |Microsoft Azure"
 description="本文提供的 Azure IoT 中心中的裝置管理概觀︰ 企業裝置生命週期、 重開機，工廠重設、 韌體更新、 設定、 裝置盡相同、 查詢、 工作"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Azure IoT 中心裝置管理 （預覽版本） 的概觀

## <a name="introduction"></a>簡介

Azure IoT 中心提供的功能和擴充模型，讓裝置和建置強大 IoT 裝置管理解決方案的後端開發人員。 IoT 裝置範圍限制感應器和單一目的微到強大的閘道器路由通訊群組的裝置。  此外，使用案例與 IoT 運算子需求而有所不同大幅產業。  變化，還是 Azure IoT 中心裝置管理提供各種不同的裝置和使用者集跟著功能、 模式和程式碼文件庫。

建立成功的企業 IoT 解決方案，以提供策略運算子如何處理持續管理的裝置其集合的重要部分。 IoT 運算子需要簡單及可靠的工具和啟用這些專注於工作的更重要層面的應用程式。 本文提供︰

- Azure IoT 中心裝置管理方法簡短的概觀。
- 常見的裝置管理原則的描述。
- 在裝置生命週期的描述。
- 常見的裝置管理模式概觀。

## <a name="iot-device-management-principles"></a>IoT 裝置管理原則

IoT 提供一組唯一的裝置管理的挑戰，每個企業級解決方案必須提供下列原則︰

![Azure IoT 中心裝置管理原則圖形][img-dm_principles]

- **縮放比例與自動化**︰ IoT 方案需要簡單的工具，可以自動化例行工作，並啟用較小的作業教職員管理數百萬種裝置。 每日，運算子預期會從遠端處理裝置作業、 大量，並只會收到發生問題時需要其直接的注意。

- **開放性與相容性**︰ IoT 裝置生態是杯不同。 必須量身打造管理工具，以納入其間的裝置類別、 平台和通訊協定。 運算子必須能夠支援多種類型的裝置，從最有限的內嵌單一程序晶片、 強大和完整功能的電腦。

- **內容的線上狀態**︰ IoT 環境而動態也能變更。 服務可靠性是最重要的。 裝置管理作業必須納入 SLA 進行的維修作業 windows、 網路和 power 狀態、 使用中的條件，以及裝置地理位置，以確保該進行的維修作業停機時間不會影響要徑營運或建立危險的條件。

- **服務許多角色**︰ 支援唯一的工作流程和 IoT 操作角色的程序很重要。 操作人員必須 harmoniously 使用指定的限制式的內部的 IT 部門。  他們也必須尋找持續方法曲面即時裝置作業的資訊監督員及其他商務管理角色。

## <a name="iot-device-lifecycle"></a>IoT 裝置生命週期

有一組通用的所有企業 IoT 專案的一般裝置管理階段。 Azure IoT 有 IoT 裝置生命週期內的五個階段︰

![五個 Azure IoT 裝置生命週期的時期︰ 規劃、 佈建、 設定、 監控、 淘汰][img-device_lifecycle]

在每個這些五個階段，有幾應該完成提供完整的解決方案的裝置運算子需求︰

- **計劃**︰ 啟用運算子來建立裝置中繼資料配置可輕鬆正確地查詢，以及一群大量管理作業的裝置。 您可以使用的裝置雙生標記與內容的形式儲存此裝置中繼資料。

    *深入閱讀*︰[開始使用裝置盡相同][lnk-twins-getstarted]，[瞭解裝置盡相同][lnk-twins-devguide]，[如何使用雙生屬性][lnk-twin-properties]

- **佈建**︰ 安全地佈建 IoT 中樞的新裝置，並啟用運算子立即探索裝置的功能。  使用 IoT 中心裝置登錄以建立彈性的裝置身分識別與認證，然後大量執行這項作業，使用工作。 建立裝置回報他們的功能和透過裝置雙生裝置屬性的條件。

    *深入閱讀*︰[管理裝置身分識別][lnk-identity-registry]，[大量裝置身分識別管理][lnk-bulk-identity]，[如何使用雙生屬性][lnk-twin-properties]

- **設定**︰ 維持健康狀況和安全性促進大量設定變更，以及韌體更新至 [裝置]。 使用所要的內容，或直接的方法與廣播的工作，請執行這些裝置管理作業大量。

    *深入閱讀*︰[使用直接方法][lnk-c2d-methods]，[叫用的裝置上的直接方法][lnk-methods-devguide]，[如何使用雙生屬性][lnk-twin-properties]，[排程和廣播的工作][lnk-jobs]，[多個裝置上的排程工作][lnk-jobs-devguide]

- **監視器**︰ 監控整體裝置集合狀況，進行中的作業的狀態，通知運算子，可能需要其注意的問題。  套用裝置雙生，若要讓報表即時作業條件的和狀態更新作業的裝置。 使用裝置雙生查詢中呈現最直接的問題的建立功能強大的儀表板報表。

    *深入閱讀*︰[如何使用雙生屬性][lnk-twin-properties]，[盡相同及工作的查詢語言][lnk-query-language]

- **淘汰**︰ 取代或解除委任失敗之後的裝置，升級循環圖，或服務存留時間的結尾。  若要維持裝置資訊的實體裝置如果是使用裝置雙生被取代，或若正在已停用封存。 使用 IoT 中心裝置登錄安全地撤銷裝置身分識別與認證。

    *深入閱讀*︰[如何使用雙生屬性][lnk-twin-properties]，[管理裝置身分識別][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>IoT 中心裝置管理圖樣

IoT 中心啟用下列設定裝置管理模式。  [裝置管理教學課程][lnk-get-started]顯示您的更多詳細資料，如何延伸這些模式以符合您的確切需求及設計這些核心範本為基礎的新圖樣。

- **重新啟動**-後端應用程式通知裝置透過直接方法它已經啟動重新啟動電腦。  裝置使用裝置雙生報告的內容] 以更新重新啟動狀態的裝置。

    ![Azure IoT 中心裝置管理重新啟動模式圖形][img-reboot_pattern]

- **工廠重設**為後端應用程式通知裝置透過直接方法它已經啟動工廠重設。  裝置使用裝置雙生報告的內容，更新工廠重設狀態的裝置。

    ![Azure IoT 中心裝置管理工廠重設模式圖形][img-facreset_pattern]

- **設定**後端應用程式會使用裝置雙生所需內容來設定裝置上執行軟體。  裝置使用裝置雙生報告的內容] 以更新裝置的設定狀態。

    ![Azure IoT 中心裝置管理設定模式圖形][img-config_pattern]

- **更新韌體**-後端應用程式通知裝置透過直接方法它已經啟動韌體更新。  裝置起始下載韌體套件，套用韌體套件，最後重新連線至 IoT 中心服務的多個步驟的程序。  在多重步驟的程序，整個裝置使用裝置雙生報告進度和裝置的狀態更新的內容。

    ![Azure IoT 中心裝置管理韌體更新模式圖形][img-fwupdate_pattern]

- **報告進度和狀態**-應用程式後端的裝置，報告的狀態和進度的裝置上執行的動作的一組執行裝置雙生查詢。

    ![Azure IoT 中心裝置管理報告進度和狀態模式圖形][img-report_progress_pattern]

## <a name="next-steps"></a>後續步驟

您可以使用功能、 模式和 Azure IoT 中心裝置管理提供，建立 IoT 滿足企業 IoT 運算子需求中的每個裝置生命週期階段中的應用程式的程式碼文件庫。

若要繼續學習如何 Azure IoT 中心裝置管理功能，請參閱[開始使用 Azure IoT 中心裝置管理][lnk-get-started]教學課程。

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md