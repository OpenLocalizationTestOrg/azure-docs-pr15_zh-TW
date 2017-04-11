<properties
   pageTitle="停用或啟用流量管理員端點 |Microsoft Azure"
   description="本文將說明停用或啟用流量管理員的設定檔結束點。"
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

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>停用或啟用流量管理員端點

您也可以停用個別屬於流量管理員設定檔的結束點。 結束點包含雲端服務與網站。 停用端點保留為 [組件的設定檔，但設定檔會當做端點不會包含在它。 這個動作會十分有用暫時移除端點所進行的維修作業模式或重新部署中的項目。 一旦端點再次執行時，您可以啟用

>[AZURE.NOTE] **停用端點具處理 Azure 中的部署狀態。設定，並且可以接收流量，即使在流量管理員已停用，仍會維持健全的結束點。此外，停用一個設定檔中的端點並不會影響其在另一個設定檔中的狀態。**

## <a name="to-disable-an-endpoint"></a>若要停用端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
1. 在頁面頂端，按一下 [檢視您的設定中所包含的結束點的**結束點**]。
1. 按一下您想要停用的結束點，然後按一下 [在頁面底部的 [**停用**。
1. 流量會停止傳送至根據 DNS 時間存留 (TTL) 的流量管理員網域名稱設定的端點。 您可以變更 TTL 流量管理員設定檔的 [設定] 頁面。

## <a name="to-enable-an-endpoint"></a>若要啟用端點


1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
1. 在頁面頂端，按一下 [檢視您的設定中所包含的結束點的**結束點**]。
1. 按一下您想要啟用的端點，，然後按一下 [在頁面底部的 [**啟用**。
1. 流量就會開始傳送一次為聽寫設定檔服務。

## <a name="next-steps"></a>後續步驟

[流量管理員-停用，啟用或刪除設定檔](disable-enable-or-delete-a-profile.md)

[疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)

[流量管理員效能考量](traffic-manager-performance-considerations.md)