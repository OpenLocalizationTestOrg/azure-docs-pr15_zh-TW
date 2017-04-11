<properties
   pageTitle="停用、 啟用或刪除流量管理員設定檔 |Microsoft Azure"
   description="本文可協助您處理您的資料傳輸管理員設定檔。"
   services="traffic-manager"
   documentationCenter="na"
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

# <a name="disable-enable-or-delete-a-profile"></a>停用、 啟用或刪除設定檔


您可以停用現有的資料傳輸管理員設定檔，使其不會參照使用者要求其設定的結束點。 當您停用流量管理員設定檔、 本身的設定檔及設定檔中所包含的資訊仍會維持不變，可以編輯在流量管理員介面。 當您想要重新啟用設定檔時，您可以輕鬆地執行 Azure 中入口網站和轉介會繼續。 當您在 Azure 入口網站中建立流量管理員設定檔時，它會自動啟用。

## <a name="to-disable-a-profile"></a>若要停用的設定檔

1. 修改 DNS 資源記錄在網際網路 DNS 伺服器中使用網際網路上的適當的記錄類型和指標至另一個名稱或特定位置的 IP 位址。 換句話說，變更您的網際網路 DNS 伺服器上的 DNS 資源記錄，使其不再使用 CNAME 資源記錄指向您的資料傳輸管理員設定檔的網域名稱。
1. 流量會停止被導向到流量管理員設定檔設定的端點。
1. 選取您想要停用的設定檔。 若要選取設定檔，流量管理員在頁面上，按一下以醒目提示設定檔的設定檔名稱] 旁的資料行。 這將您移至 [設定] 頁面的設定檔時請不要按設定檔或名稱旁的箭號的名稱。
1. 選取後的設定檔，請按一下 [停用底部的頁面。

## <a name="to-enable-a-profile"></a>若要啟用設定檔

1. 選取您想要啟用的設定檔。 若要選取設定檔，流量管理員在頁面上，按一下以醒目提示設定檔的設定檔名稱] 旁的資料行。 這將您移至 [設定] 頁面的設定檔時請不要按設定檔或名稱旁的箭號的名稱。
1. 選取後的設定檔，請按一下 [啟用底部的頁面。
1. 修改 DNS 資源記錄在網際網路 DNS 伺服器中使用 [CNAME 記錄類型，將您公司的網域名稱對應到您的資料傳輸管理員設定檔的網域名稱。 如需詳細資訊，請參閱[點公司網際網路網域流量管理員網域](traffic-manager-point-internet-domain.md)。
1. 流量就會開始重新導向的端點。

## <a name="delete-a-profile"></a>刪除設定檔


1. 確定您的網際網路 DNS 伺服器上的 DNS 資源記錄不再使用 CNAME 資源記錄指向您的資料傳輸管理員設定檔的網域名稱。
1. 選取您想要刪除的設定檔。 若要選取流量管理員] 頁面上的設定檔，醒目提示的設定檔
1. 按一下 [設定檔] 旁的資料行。 這將您移至 [設定] 頁面的設定檔時請不要按設定檔或名稱旁的箭號的名稱。
1. 選取後的設定檔，請按一下 [刪除底部的頁面。

## <a name="next-steps"></a>後續步驟

[流量管理員-停用或啟用端點](disable-or-enable-an-endpoint.md)

[設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)

[設定循環路由方法](traffic-manager-configure-round-robin-routing-method.md)

[設定效能路由方法](traffic-manager-configure-performance-routing-method.md)

[疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)

