<properties
   pageTitle="設定流量管理員容錯移轉流量路由方法 |Microsoft Azure"
   description="本文可協助您設定容錯移轉流量路由方法在流量管理員"
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

# <a name="configure-failover-routing-method"></a>設定容錯移轉路由方法

通常，組織想要可靠性提供其服務。 它會提供備份的服務，以免其主要服務中斷。 服務容錯移轉的常見模式是提供一組的相同的服務，並在主要的服務，傳送流量維持設定的一或多個備份的服務清單。 您可以依照下列程序，Azure 雲端服務與網站設定此類型的備份。

請注意 Azure 網站已無論網站模式提供容錯移轉流量路由方法功能的網站中的資料中心 （也稱為區域）。 流量管理員可讓您在不同的資料中心中，指定網站的容錯移轉流量路由方法。

## <a name="to-configure-failover-traffic-routing-method"></a>若要設定容錯移轉流量路由方法︰

1. 在 [Azure 傳統入口網站的左窗格中，按一下 [**流量管理員**圖示以開啟 [流量管理員] 窗格。 如果您尚未建立您的資料傳輸管理員設定檔，請參閱[管理傳輸管理員設定檔](traffic-manager-manage-profiles.md)的步驟建立基本的流量管理員設定檔。
2. 在 Azure 傳統入口網站中的 [流量管理員] 窗格尋找包含您要修改設定的流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
3. 在您的設定檔頁面上，按一下 [**結束點**] 頁面的頂端，並確認兩者雲端服務，展示您想要包含在您設定的網站 （端點）。 若要新增或移除端點的步驟，請參閱[管理端點流量管理員] 中](traffic-manager-endpoints.md)。
4. 在您的設定檔頁面上，按一下 [**設定**] 以開啟 [設定] 頁面頂端。
5. **傳輸路由的方法設定**，請確認流量路由方法**容錯移轉**。 如果不是，請按一下 [從下拉式清單的 [**容錯移轉**]。
6. 針對**容錯移轉 [優先順序] 清單**中，調整端點的容錯移轉順序。 當您選取的**容錯移轉**流量路由方法時，所選的端點的順序重要的資訊。 主要端點會位於頂端。 使用向上和向下箭號，視需要變更順序。 如需如何使用 Windows PowerShell 設定容錯移轉優先順序，請參閱[設定 AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880)資訊。
7. 確認**監控設定**的設定正確。 監控，可確保離線的結束點，不會傳送流量。 監視結束點，您必須指定路徑和檔名。 請注意，轉寄斜線 「 / 」 的相對路徑有效項目，以及表示檔案的根目錄中 （預設）。 如需關於監控的詳細資訊，請參閱[流量管理員監控](traffic-manager-monitoring.md)。
8. 完成您的設定變更之後，請按一下 [在頁面底部的 [**儲存**]。
9. 在您設定測試所做的變更。 如需詳細資訊，請參閱[測試流量管理員設定](traffic-manager-testing-settings.md)。
10. 一旦您流量管理員設定檔設定，並使用，編輯您公司的網域名稱指向流量管理員網域名稱授權 DNS 伺服器上的 DNS 記錄。 如需有關如何進行此動作的詳細資訊，請參閱[點公司網際網路網域流量管理員網域](traffic-manager-point-internet-domain.md)。

## <a name="next-steps"></a>後續步驟

[公司網際網路網域指向流量管理員網域](traffic-manager-point-internet-domain.md)

[路由流量管理員的方式](traffic-manager-routing-methods.md)

[設定循環路由方法](traffic-manager-configure-round-robin-routing-method.md)

[設定效能路由方法](traffic-manager-configure-performance-routing-method.md)

[疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)

[流量管理員-停用，啟用或刪除設定檔](disable-enable-or-delete-a-profile.md)

[流量管理員-停用或啟用端點](disable-or-enable-an-endpoint.md)

