<properties
    pageTitle="管理 Azure 流量管理員設定檔 |Microsoft Azure"
    description="本文可協助您建立、 停用、 啟用、 刪除及檢視 Azure 流量管理員設定檔的歷程記錄。"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>管理 Azure 流量管理員設定檔

流量管理員設定檔使用控制流量到雲端服務或網站端點的傳輸路由的方法。 本文說明如何建立和管理這些設定檔。

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>建立使用快速建立流量管理員設定檔

您可以使用 [Azure 傳統入口網站中的 [快速建立，快速建立流量管理員設定檔。 快速建立可讓您建立基本設定的設定設定檔。 不過，您無法使用快速建立的設定，例如結束點 （雲端服務與網站）、 容錯移轉流量路由方法，或監視設定的容錯移轉順序的設定。 建立您的設定檔之後, 您可以在 Azure 傳統入口網站設定這些設定。 流量管理員支援每個設定檔的最多 200 結束點。 不過，大部分的使用狀況分析藍本需要少數結束點。

### <a name="to-create-a-traffic-manager-profile"></a>若要建立流量管理員設定檔

1. **生產環境中部署您的雲端服務及網站。** 如需有關雲端服務的詳細資訊，請參閱[雲端服務](http://go.microsoft.com/fwlink/p/?LinkId=314074)。 如需有關網站的詳細資訊，請參閱[網站](http://go.microsoft.com/fwlink/p/?LinkId=393327)。

2. **Azure 傳統入口網站登入。** 按一下 [**新增**]，在左下方的入口網站上，按一下 [**網路服務 > 流量管理員**，然後按一下 [開始設定您的設定檔的 [**快速建立**。
3. **設定 DNS 前置字元。** 授與您的資料傳輸管理員設定檔唯一 DNS 前置詞名稱。 您可以指定只流量管理員網域名稱的前置字元。
4. **選取 [訂閱]。** 選取適當的 Azure 訂閱]。 每個設定檔是單一訂閱相關聯。 如果您只有一個訂閱，就不會出現這個選項。
5. **選取的資料傳輸路由的方法。** 選取流量路由方式中的**傳輸路由原則**。 如需有關流量路由方式的詳細資訊，請參閱[關於流量管理員流量路由方式](traffic-manager-routing-methods.md)。
6. **按一下 「 建立 「 若要建立設定檔**。 設定檔設定完成後，您可以找到您的設定檔 Azure 傳統入口網站中的 [流量管理員] 窗格中。
7. **設定 Azure 傳統入口網站中的端點、 監控及其他設定。** 使用快速建立只有設定基本的設定。 就必須進行其他設定，例如結束點 」 和 「 結束點容錯移轉順序的清單。


## <a name="disable-enable-or-delete-a-profile"></a>停用、 啟用或刪除設定檔

您可以停用現有的設定檔，讓該流量管理員不會參考使用者要求設定的結束點。 當您停用流量管理員設定檔時，設定檔的設定檔中所包含的資訊會保持不變，並可以編輯在流量管理員介面。  當您重新啟動設定檔，就會繼續轉介。 當您在 Azure 傳統入口網站中建立流量管理員設定檔時，它會自動啟用。 如果您決定不是必要的設定檔，您可以將其刪除。

### <a name="to-disable-a-profile"></a>若要停用的設定檔

1. 如果您使用自訂網域名稱，變更網際網路 DNS 伺服器上的 CNAME 記錄，使其不再指向您的資料傳輸管理員設定檔。
2. 流量停駐點被導向到流量管理員設定檔設定的端點。
3. 選取您想要停用的設定檔。 在流量管理員] 頁面上，按一下以醒目提示設定檔的設定檔名稱] 旁的資料行。 記事中，按一下 [設定檔或名稱旁的箭號的名稱會開啟 [設定] 頁面的設定檔。
4. 選取後的設定檔，請按一下 [在頁面底部的 [**停用**]。

### <a name="to-enable-a-profile"></a>若要啟用設定檔

1. 選取您想要停用的設定檔。 在流量管理員] 頁面上，按一下以醒目提示設定檔的設定檔名稱] 旁的資料行。 記事中，按一下 [設定檔或名稱旁的箭號的名稱會開啟 [設定] 頁面的設定檔。
2. 選取後的設定檔，請按一下 [在頁面底部的 [**啟用**]。
3. 如果您使用自訂網域名稱，請在您的網際網路 DNS 伺服器，指向您的資料傳輸管理員設定檔的網域名稱建立 CNAME 資源記錄。
4. 流量重新導向至結束點。

### <a name="to-delete-a-profile"></a>若要刪除的設定檔

1. 確定您的網際網路 DNS 伺服器上的 DNS 資源記錄不再使用 CNAME 資源記錄指向您的資料傳輸管理員設定檔的網域名稱。
2. 選取您想要停用的設定檔。 在流量管理員] 頁面上，按一下以醒目提示設定檔的設定檔名稱] 旁的資料行。 記事中，按一下 [設定檔或名稱旁的箭號的名稱會開啟 [設定] 頁面的設定檔。
3. 選取後的設定檔，請按一下 [在頁面底部的 [**刪除**]。

## <a name="view-traffic-manager-profile-change-history"></a>檢視流量管理員設定檔變更歷程記錄

您可以針對流量管理員設定檔中管理服務的 Azure 傳統入口網站檢視變更歷程記錄。

### <a name="to-view-your-traffic-manager-change-history"></a>若要檢視您的資料傳輸經理變更歷程記錄

1. 在 Azure 傳統入口網站的左窗格中，按一下 [**管理服務**]。
2. 在 [管理服務] 頁面上按一下 [**作業的記錄**。
3. 在 [作業記錄] 頁面中，您可以篩選檢視您的資料傳輸管理員設定檔的變更歷程記錄。 選取後您篩選的選項，請按一下 [檢視結果的核取記號]。

   - 若要檢視您的設定檔的變更，選取您的訂閱和時間範圍，然後選取**流量管理員**從 [**類型**] 快顯功能表。
   - 若要篩選的設定檔名稱，[**服務名稱**] 欄位中輸入設定檔的名稱，或從快顯功能表中選取它。
   - 若要檢視的每個個別變更的詳細資訊，請選取具有您想要檢視，請變更的列，然後按一下頁面底部的**詳細資料**。 在 [**作業詳細資料**] 視窗中，您可以檢視 API 物件建立或更新作業的一部分的 XML 表示。

## <a name="next-steps"></a>後續步驟

- [新增端點](traffic-manager-endpoints.md)
- [設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)
- [設定循環路由方法](traffic-manager-configure-round-robin-routing-method.md)
- [設定效能路由方法](traffic-manager-configure-performance-routing-method.md)
- [公司網際網路網域指向流量管理員網域名稱](traffic-manager-point-internet-domain.md)
- [疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)