<properties
   pageTitle="圓環流量路由方式設定流量管理員 |Microsoft Azure"
   description="本文可協助您設定循環負載平衡流量管理員端點。"
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

# <a name="configure-round-robin-routing-method"></a>設定循環路由方法

一般傳輸路由方法模式是提供一組的相同的端點，其中包含雲端服務與網站，並流量傳送至每個圓形的方式。 下列步驟建立大綱如何設定流量管理員才能執行此類型的流量路由方法。 如需不同的流量路由方式的詳細資訊，請參閱[關於流量管理員流量路由方式](traffic-manager-routing-methods.md)。

>[AZURE.NOTE] Azure 網站已提供循環負載平衡網站內的資料中心 （也稱為區域） 的功能。 流量管理員可讓您在不同的資料中心中，指定網站的循環流量路由方法。

## <a name="routing-traffic-equally-round-robin-across-a-set-of-endpoints"></a>路由流量平均 （圓環） 一組的結束點︰

1. 在 [Azure 傳統入口網站的左窗格中，按一下 [**流量管理員**圖示以開啟 [流量管理員] 窗格。 如果您尚未建立您的資料傳輸管理員設定檔，請參閱[管理傳輸管理員設定檔](traffic-manager-manage-profiles.md)的步驟建立基本的流量管理員設定檔。
2. Azure 的傳統入口網站，在流量管理員] 窗格中，找出包含您要修改設定的流量管理員設定檔，然後按一下的設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
3. 在您的設定檔頁面上，按一下 [**結束點**] 頁面的頂端，並確認您想要設定中所包含的服務端點簡報。 若要新增或移除端點的步驟，請參閱[管理端點流量管理員] 中](traffic-manager-endpoints.md)。
4. 在您的設定檔頁面上，按一下 [**設定**] 以開啟 [設定] 頁面頂端。
5. **傳輸路由的方式設定**，請確認流量路由方法**循環**。 如果不是，請按一下 [下拉式清單中的 [**循環**]。
6. 確認**監控設定**的設定正確。 監控，可確保離線的結束點，不會傳送流量。 監視結束點，您必須指定路徑和檔名。 請注意，轉寄斜線 「 / 」 的相對路徑有效項目，以及表示檔案的根目錄中 （預設）。 如需關於監控的詳細資訊，請參閱[關於監控流量管理員](traffic-manager-monitoring.md)。
7. 完成您的設定變更之後，請按一下 [在頁面底部的 [**儲存**]。
8. 在您設定測試所做的變更。 如需詳細資訊，請參閱[測試流量管理員設定](traffic-manager-testing-settings.md)。
9. 一旦您流量管理員設定檔設定，並使用，編輯您公司的網域名稱指向流量管理員網域名稱授權 DNS 伺服器上的 DNS 記錄。 如需有關如何進行此動作的詳細資訊，請參閱[點公司網際網路網域流量管理員網域](traffic-manager-point-internet-domain.md)。

## <a name="next-steps"></a>後續步驟


[公司網際網路網域指向流量管理員網域](traffic-manager-point-internet-domain.md)

[路由流量管理員的方式](traffic-manager-routing-methods.md)

[設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)

[設定效能路由方法](traffic-manager-configure-performance-routing-method.md)

[疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)

[流量管理員-停用，啟用或刪除設定檔](disable-enable-or-delete-a-profile.md)

[流量管理員-停用或啟用端點](disable-or-enable-an-endpoint.md)

