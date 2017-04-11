<properties
    pageTitle="效能考量 Azure 流量管理員的 |Microsoft Azure"
    description="了解流量管理員及如何使用流量管理員時，請測試您的網站的效能的效能"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="performance-considerations-for-traffic-manager"></a>效能考量的流量管理員

本頁面說明使用流量管理員的效能考量。 請考慮下列情況︰

您在 WestUS 和 EastAsia 區域內有您網站的執行個體。 其中一個執行個體失敗流量管理員探查的狀況檢查。 應用程式的流量會導向至健全的區域。 此容錯移轉預期但效能可能會根據現在距區域在移動中的資料傳輸的延遲問題。

## <a name="how-traffic-manager-works"></a>流量管理員的運作方式

僅限效能的影響流量管理員可以在您的網站上安裝的是初始 DNS 查閱。 Microsoft DNS 根伺服器主控 trafficmanager.net 區域會處理 DNS 要求流量管理員設定檔的名稱。 流量管理員會填入，並定期更新 Microsoft 的 DNS 根伺服器根據流量管理員原則] 和 [探查結果。 期間初始 DNS 查閱，因此即使沒有 DNS 查詢傳送到流量管理員。

流量管理員組成的幾個元件︰ DNS 名稱伺服器、 API 服務、 儲存空間層級] 及監視服務端點。 如果流量管理員服務元件失敗，則不會影響您的資料傳輸管理員設定檔與相關聯的 DNS 名稱。 Microsoft DNS 伺服器中的記錄維持不變。 不過，端點監控和 DNS 更新不會發生。 因此，流量管理員無法更新 DNS，指向您的容錯移轉網站時您主要的網站當機。

DNS 名稱解析速度，而快取結果。 初始 DNS 查閱的速度取決於用戶端使用名稱解析 DNS 伺服器。 一般而言，在用戶端可以自行完成 DNS 查閱 ~ 50 ms 內。 查閱的結果會快取期間 DNS 時間存留 (TTL)。 預設 TTL，流量管理員是 300 秒。

流量不會流動透過流量管理員。 DNS 查閱完成後，用戶端具有網站的執行個體的 IP 位址。 用戶端直接連接到該地址，並不會將透過流量管理員。 您選擇的流量管理員原則有不影響 DNS 效能。 不過，效能路由方法可以產生負面影響應用程式經驗。 例如，如果您原則會將北美地區裝載於亞洲執行個體的流量重新導向，這些工作階段的網路延遲可能會效能問題。

## <a name="measuring-traffic-manager-performance"></a>測量流量管理員效能

有多個網站，您可以使用了解的效能和行為流量管理員設定檔。 許多這些網站免費提供，但可能會有限制。 某些網站提供增強的監控和付費的報告。

這些網站量值 DNS 延遲和顯示工具解決的 IP 位址的世界各地的用戶端位置。 大部分的這些工具不要快取 DNS 結果。 因此，工具顯示完整 DNS 查閱每次執行測試。 當您測試從您自己的用戶端時，只會體驗完整的 DNS 查閱效能，一次 [TTL] 在這段期間。

## <a name="sample-tools-to-measure-dns-performance"></a>範例工具測量 DNS 效能

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS 提供許多效能工具。 DNS 比較工具可以顯示您所需的時間，您的 DNS 名稱解析，以及瞭解的與其他 DNS 服務提供者。

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    最簡單的工具是 WebSitePulse。 輸入 URL，請參閱 DNS 解析時間、 第一個位元組，最後一個位元組，與其他效能統計資料。 您可以選擇從三個不同的測試位置。 在此範例中，您會看到第一次執行顯示 DNS 查閱採用 0.204 數秒。

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    因為快取結果，第二個檢定的結果相同的資料傳輸管理員端點 DNS 查閱會 0.002 數秒。

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [CA 應用程式模擬螢幕](https://asm.ca.com/en/checkit.php)

    稱為 Watchmouse 核取網站工具，此網站顯示您的 DNS 解析時間從多個地理區域同時。 輸入 URL，請參閱 DNS 解析時間、 連線時間與速度從數個地理位置。 若要查看哪些代管的服務會傳回不同的位置，世界各地的使用這項測試。

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    此工具提供效能統計值為網頁的每個項目。 [頁面分析] 索引標籤會顯示 DNS 查閱所花費的時間的百分比。

- [什麼是我的 DNS？](http://www.whatsmydns.net/)

    此網站沒有 DNS 查閱，從 20 不同的位置，並將結果顯示在地圖上。

- [深入 Web 介面](http://www.digwebinterface.com)

    此網站會顯示更多詳細的 DNS 資訊包括 CNAMEs 和記錄。 請確定您檢查 Colorize 輸出] 和 [狀態] 選項] 底下，選取 「 所有 」 下的名稱伺服器。

## <a name="next-steps"></a>後續步驟

[關於流量管理員流量路由方法](traffic-manager-routing-methods.md)

[測試您的資料傳輸管理員設定](traffic-manager-testing-settings.md)

[作業上流量管理員 （REST API 參照）](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理員 Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=400769)
