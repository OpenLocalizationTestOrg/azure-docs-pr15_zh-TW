<properties
    pageTitle="管理 Azure 流量管理員中的端點 |Microsoft Azure"
    description="本文可協助您新增、 移除、 啟用及停用結束點從 Azure 流量管理員。"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>新增、 停用、 啟用，或刪除端點

Azure 應用程式服務的 Web 應用程式功能已提供錯誤移轉及網站內的資料中心，無論網站模式的循環流量路由功能。 Azure 流量管理員可讓您指定錯誤移轉及循環流量路由網站和雲端中的服務不同的資料中心。 提供該功能所需的第一步是新增雲端服務或網站端點到流量管理員。

>[AZURE.NOTE]  本文說明如何使用 [傳統] 入口網站。 建立及工作分派的雲端服務 Web 應用程式的端點，只支援 Azure 傳統入口網站。 新的[Azure 入口網站](https://portal.azure.com)是慣用的介面。

您也可以停用個別屬於流量管理員設定檔的結束點。 停用端點保留為 [組件的設定檔，但設定檔會當做端點不會包含在它。 此動作可用於暫時移除進行的維修作業模式或重新部署中的端點。 一旦端點再次執行時，它可以啟用。

>[AZURE.NOTE] 停用端點具處理 Azure 中的部署狀態。 設定，並且可以接收流量，即使在流量管理員已停用，則會保持健全的結束點。 此外，停用一個設定檔中的端點並不會影響其在另一個設定檔中的狀態。

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>若要新增的雲端服務或網站的端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔。 若要開啟 [設定] 頁面，請按一下 [設定檔名稱右邊的箭號。
2. 在頁面頂端，按一下 [**結束點**，檢視已在您設定的結束點]。
3. 在頁面底部，按一下 [**新增**]，以存取 [**新增服務結束點**] 頁面。 根據預設，頁面會列出**服務端點**雲端服務。
4. 如需雲端服務，請將其新增為這個設定檔的結束點清單中選取雲端服務。 清除雲端服務名稱，從清單中移除的端點。
5. 網站，按一下 [**服務類型**] 下拉式清單中，然後按一下**Web 應用程式**。
6. 將其新增至這個設定檔的結束點] 清單中選取 [網站]。 清除網站名稱會從端點的清單。 您可以選取每個 Azure 資料中心 （也稱為區域） 只能有一個網站。 當您選取的第一個網站時，相同的資料中心中的其他網站就會無法使用的選取範圍。 另請注意列出僅標準的網站。
7. 您選取這個設定檔的結束點之後，請按一下 [儲存變更右下的核取記號。

>[AZURE.NOTE] 您新增或移除使用*容錯移轉*流量路由方式的設定檔中的端點之後，[容錯移轉優先順序] 清單可能無法排序他們您想要的方式。 您可以調整 [設定] 頁面上的 [容錯移轉優先順序] 清單的順序。 如需詳細資訊，請參閱[設定容錯移轉路由流量](traffic-manager-configure-failover-routing-method.md)。

## <a name="to-disable-an-endpoint"></a>若要停用端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔。 若要開啟 [設定] 頁面，請按一下 [設定檔名稱右邊的箭號。
2. 在頁面頂端，按一下 [檢視您的設定中所包含的結束點的**結束點**]。
3. 按一下您想要停用的結束點，然後按一下 [在頁面底部的 [**停用**。
4. 用戶端繼續傳送流量到端點，期間時間存留 (TTL)。 您可以變更 [TTL] 的流量管理員設定檔的 [設定] 頁面上。

## <a name="to-enable-an-endpoint"></a>若要啟用端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔。 若要開啟 [設定] 頁面，請按一下 [設定檔名稱右邊的箭號。
2. 在頁面頂端，按一下 [檢視您的設定中所包含的結束點的**結束點**]。
3. 按一下您想要啟用的端點，，然後按一下 [在頁面底部的 [**啟用**。
4. 用戶端的設定檔由導向啟用端點。

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>若要刪除的雲端服務或網站的端點

1. 在 [Azure 傳統入口網站中的 [流量管理員] 窗格中，找出含有您要修改的端點設定流量管理員設定檔。 若要開啟 [設定] 頁面，按一下 [設定檔名稱右邊的箭號。
2. 在頁面頂端，按一下 [**結束點**，檢視已在您設定的結束點]。
3. 在結束點] 頁面上，按一下您想要從設定檔中刪除端點的名稱。
4. 在頁面底部，按一下 [**刪除**]。

## <a name="next-steps"></a>後續步驟

* [管理流量管理員設定檔](traffic-manager-manage-profiles.md)
* [設定路由方式](traffic-manager-configure-routing-method.md)
* [疑難排解流量管理員降低狀態](traffic-manager-troubleshooting-degraded.md)
* [流量管理員效能考量](traffic-manager-performance-considerations.md)
* [作業上流量管理員 （REST API 參照）](http://go.microsoft.com/fwlink/p/?LinkID=313584)
