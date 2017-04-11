<properties
    pageTitle="流量管理員結束點類型 |Microsoft Azure"
    description="本文說明的不同類型的端點，可供使用 Azure 流量管理員"
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

# <a name="traffic-manager-endpoints"></a>流量管理員結束點

Microsoft Azure 流量管理員可讓您控制如何在不同的資料中心中執行的應用程式部署分配網路流量。 您在流量管理員設定為 「 結束點 」 的每個應用程式部署。 流量管理員收到 DNS 要求時，選擇要在 DNS 回應中傳回可用端點。 流量管理員根據選擇目前的端點狀態以及的傳輸路由的方法。 如需詳細資訊，請參閱[流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)。

有三種類型的流量管理員支援的結束點︰

- **Azure 端點**用來裝載於 Azure 服務。
- **外部端點**會用於存放外 Azure 任一內部部署或不同的主機服務提供者的服務。
- **巢狀端點**用來合併流量管理員設定檔，若要建立更有彈性傳輸路由的配置，以支援更大、 更複雜的部署的需求。

有不同類型的結束點組合單一的流量管理員設定檔中的方式沒有限制。 每個設定檔可以包含任何混合的結束點類型。

下列各節說明每個更深入的結束點類型。

## <a name="azure-endpoints"></a>Azure 端點

Azure 服務管理員] 中的流量會使用 azure 結束點。 支援下列 Azure 的資源類型︰

- 傳統' IaaS Vm 和 PaaS 雲端服務。
- Web 應用程式
- PublicIPAddress 資源 (可將其連線至 Vm 直接或透過 Azure 負載平衡器)。 PublicIpAddress 必須使用流量管理員設定檔中的 DNS 名稱。

PublicIPAddress 資源是 Azure 資源管理員資源。 在傳統的部署模型中不存在。 因此他們的年齡只支援在流量主管的 Azure 資源管理員體驗。 透過資源管理員] 及 [傳統部署模型支援其他的結束點類型。

使用時 Azure 端點，流量管理員會偵測時停止和開始 「 傳統' IaaS VM、 雲端服務或在 Web 應用程式。 此狀態會反應在端點狀態。 如需詳細資訊，請參閱[流量管理員端點監控](traffic-manager-monitoring.md#endpoint-and-profile-status)。 停止基礎服務時，結束點狀況檢查或直接流量的點，並不會執行流量管理員。 沒有流量管理員帳單事件發生停止執行個體。 重新啟動服務時，您有合格接收流量帳單履歷表和端點。 此偵測並不適用於 PublicIpAddress 結束點。

## <a name="external-endpoints"></a>外部端點

外部 Azure 服務會使用外部的結束點。 服務，例如裝載於內部部署或不同的提供者。 外部的結束點可以個別使用或合併與 Azure 端點相同的資料傳輸管理員設定檔中。 合併與外部的端點 Azure 結束點，可讓各種情況︰

- 作用中作用中] 或 [主動被動容錯移轉模型] 中，使用 Azure 提供更高的多餘的現有的內部部署的應用程式。
- 若要降低世界各地的使用者的應用程式延遲，延伸至其他的地理位置 Azure 中現有的內部部署應用程式。 如需詳細資訊，請參閱[流量管理員 '效能' 路由流量](traffic-manager-routing-methods.md#performance-traffic-routing-method)。
- 使用 Azure 提供更大的現有的內部部署的應用程式，持續或做為 「 突發-雲端 」 方案，以符合需求的特殊圖文集。

在某些情況下，是很有用使用外部端點參考 Azure 服務 （如需範例，請參閱[常見問題集](#faq)）。 在此情況下，狀況檢查被付費速率 Azure 端點，不外部端點工資率。 不過，不同於 Azure 端點，如果您停止或刪除的基礎的服務健康狀況核取計費繼續，直到您停用或刪除流量管理員中的端點。

## <a name="nested-endpoints"></a>巢狀的端點

巢狀的端點合併多個流量管理員設定檔，以建立彈性的傳輸路由配置及支援]，較大的複雜的部署的需求。 使用巢狀端點 '子系' 設定檔會新增為端點至 「 上層 」 的設定檔。 將 [子] 與 [父項的設定檔可以包含其他任何類型，包括其他巢狀的設定檔的端點。 如需詳細資訊，請參閱[巢狀的流量管理員設定檔](traffic-manager-nested-profiles.md)。

## <a name="web-apps-as-endpoints"></a>Web 應用程式為端點

設定 Web 應用程式為端點流量管理員中時，就會套用其他事項︰

1. '標準' SKU 或上方的 Web 應用程式只是用於與流量 Manager 符合資格。 嘗試新增較低的 SKU 的 Web 應用程式會失敗。 降級的現有的 Web 應用程式的 SKU 結果流量管理員 」 中不會再傳送給該 Web 應用程式的 [流量。

2. 端點收到 HTTP 要求時，會使用邀請中的 「 主機 」 標頭來判斷哪一個 Web App 應該服務要求。 主機標頭包含用來啟動要求，例如 「 contosoapp.azurewebsites.net' DNS 名稱。 若要使用不同的 DNS 名稱與您的 Web 應用程式，必須應用程式的自訂網域名稱註冊 DNS 名稱。 新增時的 Web 應用程式端點為 Azure 端點，請流量管理員設定檔 DNS 名稱會自動註冊的應用程式。 刪除端點時，會自動移除此登錄。

3. 每個流量管理員設定檔可能有最一個 Web App 端點每個 Azure 的區域。 若要解決此限制式，您可以設定 Web 應用程式與外部的端點。 如需詳細資訊，請參閱[常見問題集](#faq)。

## <a name="enabling-and-disabling-endpoints"></a>啟用及停用的端點

停用端點流量管理員] 中很有用暫時將流量移除進行的維修作業模式或重新部署中的端點。 當端點一次執行時，可以重新啟用。

啟用和停用透過流量管理員入口網站，PowerShell、 CLI 或 REST API，所有的支援資源管理員] 及 [傳統部署模型的結束點。

>[AZURE.NOTE] 停用 Azure 端點具處理 Azure 中的部署狀態。 Azure 的服務 （例如 VM 或 Web App 會保留執行，並且可以接收流量，即使在流量管理員已停用。 可以傳送流量，直接對服務執行個體，而非透過流量管理員設定檔 DNS 名稱。 如需詳細資訊，請參閱[流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)。

目前的資格的每個端點接收流量取決於下列因素︰

- 設定檔狀態 （啟用/停用）
- 結束點狀態 （啟用/停用）
- 結果的狀況檢查有該端點

如需詳細資訊，請參閱[流量管理員端點監控](traffic-manager-monitoring.md#endpoint-and-profile-status)。

>[AZURE.NOTE] 因為流量管理員會在 DNS 層級，就會無法會影響任何端點現有的連線。 端點無法使用時，流量管理員指導新連線至另一個可用的端點。 不過，停用或不佳的端點背後主機可能會繼續收到流量透過現有的連線，直到這些工作階段終止。 應用程式應該限制工作階段期間，允許清空現有連線] 中的資料傳輸。

如果停用的設定檔中的所有端點，或本身的設定檔] 會停都用，流量管理員就會傳送至新的 DNS 查詢 'NXDOMAIN' 回應。

## <a name="faq"></a>常見問題集

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>可以使用流量管理員與從多個訂閱的結束點嗎？

使用來自多個訂閱的結束點不能使用 Azure Web 應用程式。 Azure Web 應用程式需要單一的訂閱中只會用任何使用 Web 應用程式的自訂網域名稱。 無法從使用相同的網域名稱的多份訂閱使用 Web 應用程式。

若是其他結束點類型] 中，則可能使用流量管理員與多個訂閱的結束點。 您設定流量管理員的方式取決於您是否使用傳統的部署模型或資源管理員體驗。

- 資源管理員] 中，從任何訂閱的結束點可以新增到 [流量 Manager] 中，只要設定流量管理員設定檔的人員具有端點的 「 讀取 」 權限。 這些權限授與使用[Azure 資源管理員角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)。
- 在傳統的部署模型介面，流量管理員會需要雲端服務或設為 Azure 結束點的 Web 應用程式位於同一份訂閱的流量管理員設定檔。 在其他訂閱雲端服務端點可以新增至流量管理員為 「 外部 」 結束點。 這些外部端點被付費 Azure 結束點，而非外部的工資率。

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>可以使用雲端服務 '臨時 」 的位置使用流量管理員嗎？

[是]。 執行位置的雲端服務可以設定為外部端點流量管理員] 中。 狀況檢查是仍會 Azure 端點工資率支付薪資。 外部的結束點類型是使用，因為基礎服務的變更會不挑選自動。 與外部的端點，流量管理員無法偵測到雲端服務會停止或刪除。 因此，流量管理員持續狀況檢查的計費，直到端點已停用或刪除的郵件。

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>流量管理員是否支援 IPv6 結束點？

流量管理員目前不提供 IPv6 addressible 的名稱伺服器。 不過，流量管理員仍可讓 IPv6 用戶端連線至 IPv6 結束點。 用戶端不會讓 DNS 要求直接到流量管理員。 不過，用戶端使用遞迴 DNS 服務。 IPv6 專用的用戶端會傳送至透過 IPv6 遞迴 DNS 服務要求。 然後遞迴服務應該無法連絡使用 IPv4 流量管理員名稱伺服器。

流量管理員會以 DNS 名稱端點的回應。 若要支援 IPv6 端點，必須有 DNS AAAA 記錄的 IPv6 位址指向端點 DNS 名稱。 流量管理員狀況檢查僅支援 IPv4 位址。 服務需要相同的 DNS 名稱 IPv4 端點的方式公開。

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>可以在同一個區域中的多個 Web 應用程式使用流量管理員嗎？

一般而言，流量管理員用來將流量導向部署在不同區域中的應用程式。 不過，它也可用的應用程式在同一個區域中有一個以上的部署的位置。 流量管理員 Azure 結束點從都會新增至相同的資料傳輸管理員設定檔的相同 Azure 地區不允許一個以上的 Web 應用程式結束點。

下列步驟會提供這項限制式的解決方法︰

1. 核取您端點位於不同的 web 應用程式 」 刻度] 單位]。 將網域名稱必須對應到指定的小數位數單位中的單一網站。 因此，在相同的 [刻度] 單位的兩個 Web 應用程式無法共用流量管理員設定檔。
2. 每個 Web 應用程式中新增自訂主機名稱為 vanity 網域名稱。 每個 Web 應用程式必須在不同的刻度] 單位。 所有的 Web 應用程式必須屬於相同的訂閱。
3. 新增一個 （和只有一個） 的 Web 應用程式結束點到流量管理員設定檔，Azure 端點。
4. 新增至您的流量管理員設定檔的每個額外的 Web 應用程式端點，與外部的端點。 外部端點才可加入使用資源管理員部署模型。
5. 建立 vanity 網域指向您流量管理員設定檔的 DNS 名稱中的 [DNS CNAME 記錄 (<>...。 trafficmanager.net)。
6. 存取您的網站，透過 vanity 網域名稱，而不是流量管理員設定檔 DNS 名稱。

## <a name="next-steps"></a>後續步驟

- 瞭解[流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)。
- 進一步瞭解流量管理員[端點監控和自動容錯移轉](traffic-manager-monitoring.md)。
- 深入了解流量管理員[流量路由方式](traffic-manager-routing-methods.md)。
