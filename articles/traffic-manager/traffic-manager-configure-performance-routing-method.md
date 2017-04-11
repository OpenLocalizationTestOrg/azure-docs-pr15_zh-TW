<properties
   pageTitle="設定效能流量路由方法 |Microsoft Azure"
   description="本文可協助您設定效能流量路由方法在流量管理員"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-performance-traffic-routing-method"></a>設定效能流量路由方法

若要將流量路由傳送雲端服務和網站 （端點） 位於不同的資料中心全球 （也稱為區域），您可以直接連入流量端點的最低的延遲時間從要求的用戶端。 通常，最低延遲資料中心會對應到最接近的地理距離。 效能流量路由方法可讓您散佈根據最低的延遲時間，，但不能使用帳戶即時變更網路設定] 或 [載入至。 如需有關 Azure 流量管理員提供不同的流量路由方式的詳細資訊，請參閱[關於流量管理員流量路由方法](traffic-manager-routing-methods.md)。

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>路由流量端點的一組根據最低延遲︰

1. 在 [Azure 傳統入口網站的左窗格中，按一下 [**流量管理員**圖示以開啟 [流量管理員] 窗格。 如果您尚未建立您的資料傳輸管理員設定檔，請參閱[管理傳輸管理員設定檔](traffic-manager-manage-profiles.md)的步驟建立基本的流量管理員設定檔。
2. Azure 的傳統入口網站，在流量管理員] 窗格中，找出包含您要修改設定的流量管理員設定檔，然後按一下的設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
3. 在您的設定檔頁面上，按一下 [**結束點**] 頁面的頂端，並確認您想要設定中所包含的服務端點簡報。 若要新增或移除您的設定檔中的端點的步驟，請參閱[管理端點流量管理員] 中](traffic-manager-endpoints.md)。
4. 在您的設定檔頁面上，按一下 [**設定**] 以開啟 [設定] 頁面頂端。
5. **傳輸路由的方法設定**，請確認流量路由方法**效能*。如果沒有出現，按一下**效能** 下拉式清單中。
6. 確認**監控設定**的設定正確。 監控，可確保離線的結束點，不會傳送流量。 監視結束點，您必須指定路徑和檔名。 請注意，轉寄斜線 「 / 」 的相對路徑有效項目，以及表示檔案的根目錄中 （預設）。 如需關於監控的詳細資訊，請參閱[關於監控流量管理員](traffic-manager-monitoring.md)。
7. 完成您的設定變更之後，請按一下 [在頁面底部的 [**儲存**]。
8. 在您設定測試所做的變更。 如需詳細資訊，請參閱[測試流量管理員設定](traffic-manager-testing-settings.md)。
9. 一旦您流量管理員設定檔設定，並使用，編輯您公司的網域名稱指向流量管理員網域名稱授權 DNS 伺服器上的 DNS 記錄。 如需有關如何進行此動作的詳細資訊，請參閱[點公司網際網路網域流量管理員網域](traffic-manager-point-internet-domain.md)。

## <a name="next-steps"></a>後續步驟


[公司網際網路網域指向流量管理員網域](traffic-manager-point-internet-domain.md)

[路由流量管理員的方式](traffic-manager-routing-methods.md)

[設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)

[設定循環路由方法](traffic-manager-configure-round-robin-routing-method.md)

[疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)

[流量管理員-停用，啟用或刪除設定檔](disable-enable-or-delete-a-profile.md)

[流量管理員-停用或啟用端點](disable-or-enable-an-endpoint.md)

