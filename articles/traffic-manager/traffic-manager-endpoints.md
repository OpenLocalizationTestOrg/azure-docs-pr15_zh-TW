<properties
   pageTitle="管理 Azure 流量管理員中的端點 |Microsoft Azure"
   description="本文可協助您新增、 移除、 啟用及停用結束點從 Azure 流量管理員。"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="add-disable-enable-or-delete-endpoints"></a>新增、 停用、 啟用或刪除端點

Azure 應用程式服務的 Web 應用程式功能已提供錯誤移轉及網站內的資料中心，無論網站模式的循環流量路由功能。 Azure 流量管理員可讓您指定錯誤移轉及循環流量路由網站和雲端中的服務不同的資料中心。 提供該功能所需的第一步是新增雲端服務或網站端點到流量管理員。

>[AZURE.NOTE] 您無法將外部的位置，或是流量管理員設定檔新增為使用 Azure 傳統入口網站的結束點。 您必須使用 Windows PowerShell[新增 AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774)的 REST API[建立的定義](http://go.microsoft.com/fwlink/p/?LinkId=400772)。

您也可以停用個別屬於流量管理員設定檔的結束點。 結束點包含雲端服務與網站。 停用端點保留為 [組件的設定檔，但設定檔會當做端點不會包含在它。 這個動作會十分有用暫時移除端點所進行的維修作業模式或重新部署中的項目。 一旦端點再次執行時，它可以啟用。

>[AZURE.NOTE] 停用端點具處理 Azure 中的部署狀態。 設定，並且可以接收流量，即使在流量管理員已停用，仍會維持健全的結束點。 此外，停用一個設定檔中的端點並不會影響其在另一個設定檔中的狀態。

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>若要新增的雲端服務或網站的端點


1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
2. 在頁面頂端，按一下 [**結束點**，檢視已在您設定的結束點]。
3. 在頁面底部，按一下 [**新增**]，以存取 [**新增服務結束點**] 頁面。 根據預設，頁面會列出**服務端點**雲端服務。
4. 雲端服務，請啟用這些為這個設定檔的結束點] 清單中選取雲端服務。 清除雲端服務名稱，從清單中移除的端點。
5. 網站，按一下 [**服務類型**] 下拉式清單中，然後按一下**Web 應用程式**。
6. 將其新增至這個設定檔的結束點] 清單中選取 [網站]。 清除網站名稱會從端點的清單。 請注意，您可以只選取單一網站每 Azure 資料中心 （也稱為區域）。 如果您選取的資料中心，當您選取的第一個網站裝載、 多個網站的網站，在相同的資料中心他人無法針對選取範圍。 另請注意列出僅標準的網站。
7. 您選取這個設定檔的結束點之後，請按一下 [儲存變更右下的核取記號。

>[AZURE.NOTE] 如果您使用*容錯移轉*流量路由方法，在您新增或移除端點後，請務必以調整 [容錯移轉優先順序] 清單設定] 頁面上以反映您想要設定的容錯移轉順序。 如需詳細資訊，請參閱[設定容錯移轉路由流量](traffic-manager-configure-failover-routing-method.md)。

## <a name="to-disable-an-endpoint"></a>若要停用端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
2. 在頁面頂端，按一下 [檢視您的設定中所包含的結束點的**結束點**]。
3. 按一下您想要停用的結束點，然後按一下 [在頁面底部的 [**停用**。
4. 流量會停止傳送至根據 DNS 時間存留 (TTL) 的流量管理員網域名稱設定的端點。 您可以變更 TTL 流量管理員設定檔的 [設定] 頁面。

## <a name="to-enable-an-endpoint"></a>若要啟用端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
2. 在頁面頂端，按一下 [檢視您的設定中所包含的結束點的**結束點**]。
3. 按一下您想要啟用的端點，，然後按一下 [在頁面底部的 [**啟用**。
4. 流量就會開始傳送一次為聽寫設定檔服務。

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>若要刪除的雲端服務或網站的端點


1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔，然後按一下 [設定檔名稱右邊的箭號。 這會開啟 [設定] 頁面的設定檔。
2. 在頁面頂端，按一下 [**結束點**，檢視已在您設定的結束點]。
3. 在結束點] 頁面上，按一下您想要從設定檔中刪除端點的名稱。
4. 在頁面底部，按一下 [**刪除**]。

>[AZURE.NOTE] 您無法刪除外部的位置，或是流量管理員設定檔，為使用傳統 Azure 入口網站的端點。 您必須使用 Windows PowerShell。 如需詳細資訊，請參閱[移除 AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx)。

## <a name="next-steps"></a>後續步驟

- [設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)
- [設定循環路由方法](traffic-manager-configure-round-robin-routing-method.md)
- [設定效能路由方法](traffic-manager-configure-performance-routing-method.md)
- [疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)
- [作業上流量管理員 （REST API 參照）](http://go.microsoft.com/fwlink/p/?LinkID=313584)
