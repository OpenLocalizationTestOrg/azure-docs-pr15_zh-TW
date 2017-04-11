<properties
 pageTitle="IoT 中心 HA 和 DR |Microsoft Azure"
 description="說明可協助建立損毀的高度可用 IoT 解決方案修復功能的功能。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT 中心高可用性和損毀復原

Azure 服務中，為 IoT 中心會提供高可用性 (HA) Azure 區域層級重複使用不含任何額外的工作所需的方案。 此外，Azure 提供功能，協助您建置損毀修復 (DR) 功能或跨地區可用性的方案，如果所需的數字。 您必須設計，並準備您善用這些 DR 功能，如果您想要提供全域管理員、 跨地區可用性裝置或使用者的解決方案。 [Azure 業務連續性技術指南](../resiliency/resiliency-technical-guidance.md)的文章說明業務連續性和 DR Azure 中的內建的功能。 [損毀修復和 Azure 應用程式的可用性][]紙張提供的 Azure 應用程式，以達到 HA 和 DR 策略架構指引。

## <a name="azure-iot-hub-dr"></a>Azure IoT 中心 DR
除了內部地區 HA IoT 中心實作損毀修復需要使用者沒有互動的容錯移轉機制。 IoT 中心 DR 自動啟動，而且有復原時間目標 (RTO) 的 2 26 小時和下列復原點目標 (RPOs)。

| 功能 | RPO |
| ------------- | --- |
| 登錄] 和 [通訊作業的服務可用性 | 可能的 CName 遺失 |
| 裝置身分識別登錄中識別資料 | 0-5 分鐘資料遺失 |
| 裝置至雲端的郵件 | 所有未讀取的郵件都會遺失 |
| 監控郵件的作業 | 所有未讀取的郵件都會遺失 |
| 雲端-裝置訊息 | 0-5 分鐘資料遺失 |
| 雲端至裝置的意見反應佇列中 | 所有未讀取的郵件都會遺失 |

## <a name="regional-failover-with-iot-hub"></a>使用 IoT 中樞的地區容錯移轉

完成的處理方式在 IoT 解決方案部署拓撲的超出範圍的本文中，但為了高可用性及損毀修復，我們會考慮*地區的容錯移轉*部署模型。

在地區的容錯移轉模型中，方案後端正在執行主要是在一個的資料中心位置，但其他 IoT 中心和後端部署在另一個資料中心位置供容錯移轉時，在主要的資料中心的 [IoT] 中心遭遇中斷或因故中斷裝置的網路連線至主要的資料中心。 主要閘道無法連線到裝置使用的第二個服務端點。 使用跨地區容錯移轉功能，可改善解決方案可用性以外的一個區域。

在高的層級，實作地區的容錯移轉模型與 IoT] 中心內，您需要下列動作。

* **次要 IoT 中樞和裝置路由邏輯**︰ 如果您的主要區域服務中斷，裝置必須開始連線到您的第二個區域。 指定狀態注意大部分服務相關的性質，很常見的解決方案系統管理員在區域間容錯移轉程序。 維持控制項的程序，通訊的裝置，新的結束點的最佳方式就是定期檢查目前使用中的端點的*指引對*服務。 指引服務可複製，並保持連線到的簡單的 web 應用程式 （例如，使用[Azure 流量管理員][]） 使用 DNS 重新導向技巧。
* **身分識別登錄複寫**-才可以使用次要 IoT 中心必須包含可以連線至的方案的所有裝置身分識別。 解決方案應該保留地理複寫備份裝置身分識別，以上傳這些檔案至第二個 IoT 中心切換裝置的作用中的端點之前]。 IoT 中樞的裝置身分識別匯出功能是非常實用，此內容中。 如需詳細資訊，請參閱[IoT 中心開發人員指南身分識別登錄][]。
* 所有必須移轉狀態和次要網站中所建立的資料**合併邏輯**的主要區域有空時再返回的主要區域。 這通常與裝置身分識別和應用程式的中繼資料，必須與主要 IoT 中樞的主要區域中的其他特定應用程式儲存合併。 若要簡化此步驟，通常建議您使用冪等作業。 這會最小化其他的影響，而不只是從最終一致的通訊群組的事件，從重複項目或-單傳遞的事件。 此外，應該設計應用程式邏輯容許可能不一致或 「 稍微 」 從日期狀態。 這是因為 「 治療 「 系統的其他時間根據復原點目標 (RPO)。

## <a name="next-steps"></a>後續步驟

請遵循這些連結，深入瞭解 Azure IoT 中心︰

- [快速入門 IoT 集線器 （教學課程）][lnk-get-started]
- [什麼是 Azure IoT 中心？][]

[損毀修復和 Azure 應用程式的可用性]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure 流量管理員]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT 中心開發人員指南身分識別登錄]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[什麼是 Azure IoT 中心？]: iot-hub-what-is-iot-hub.md
