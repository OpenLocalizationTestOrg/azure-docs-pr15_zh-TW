<properties 
   pageTitle="StorSimple 管理員虛擬陣列管理 |Microsoft Azure"
   description="瞭解如何使用 StorSimple 管理員服務 Azure 傳統入口網站中管理 StorSimple 內部部署的虛擬陣列。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>使用 StorSimple 管理員服務來管理您的 StorSimple 虛擬陣列

![設定程序的流程](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>概觀

本文說明 StorSimple 管理員服務介面，包括如何連線至且可用的各種選項，並提供的連結，可以透過這個 UI 執行的特定工作流程。 

閱讀本文之後，您就會知道如何︰

- 連線至 StorSimple 管理員服務
- 瀏覽 StorSimple 管理員使用者介面
- 管理您 StorSimple 虛擬陣列，透過 StorSimple 管理員服務

> [AZURE.NOTE] 若要檢視可供 StorSimple 8000 數列裝置的 [管理] 選項，請參閱[管理您的 StorSimple 裝置 StorSimple 管理員服務](storsimple-manager-service-administration.md)。

## <a name="connect-to-the-storsimple-manager-service"></a>連線至 StorSimple 管理員服務

StorSimple 管理員服務 Microsoft Azure 中執行，並且連線到多個 StorSimple 虛擬陣列。 您管理這些裝置使用管理中心的 Microsoft Azure 傳統入口網站，並在瀏覽器中執行。 若要連接的 StorSimple 管理員服務，執行下列動作。

#### <a name="to-connect-to-the-service"></a>若要連線至服務

1. 移至[https://manage.windowsazure.com/](https://manage.windowsazure.com/)。

2. 使用您的 Microsoft 帳戶認證，登入 Microsoft Azure 傳統入口網站 （位於右上方的窗格）。

3. 向下捲動左側的導覽窗格存取 StorSimple 管理員服務。

    ![向下捲動至 [服務](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>瀏覽 StorSimple 管理員服務使用者介面

瀏覽階層 StorSimple 管理員服務的使用者介面會顯示下列表格中。

- **StorSimple 管理員**登陸頁面會帶您到適用於服務中的所有虛擬陣列的使用者介面服務層級頁面。

- [**裝置**] 頁面會帶您到裝置 – 層級 UI 頁面適用於特定的虛擬陣列。

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple 管理員服務導覽階層

|登陸頁面|服務等級頁面|裝置層頁面|
|---|---|---|
|StorSimple 管理員服務|儀表板 （服務）|儀表板 （裝置）|
||裝置 →|監視器|
||備份的目錄|共用 （檔案伺服器） 或 </br>區 （iSCSI 伺服器）|
||設定 （服務）|設定 （裝置）|
||工作|進行的維修作業|
||通知|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>使用 StorSimple 管理員服務來執行管理工作

下表顯示的所有管理的一般工作和複雜的工作流程可以執行 StorSimple 管理員服務使用者介面中的摘要。 這些工作組織根據要在其它們發起的租用戶的使用者介面頁面。

如需有關每個工作流程的詳細資訊，請按一下表格中適當的程序。

#### <a name="storsimple-manager-workflows"></a>StorSimple 管理員工作流程

|如果您想要執行此動作...|前往 UI 本頁...|使用此程序|
|---|---|---|
|建立服務</br>刪除服務</br>取得服務登錄機碼</br>重新產生服務登錄機碼|StorSimple 管理員服務|[部署 StorSimple 管理員服務](storsimple-ova-manage-service.md)|
|變更服務資料加密金鑰</br>檢視作業的記錄檔|StorSimple 管理員服務 → 儀表板|[使用 [StorSimple 服務儀表板](storsimple-ova-service-dashboard.md)|
|停用的虛擬陣列</br>刪除的虛擬陣列|StorSimple 管理員服務 → 裝置|[停用或刪除虛擬的陣列](storsimple-ova-deactivate-and-delete-device.md)|
|損毀復原和裝置容錯移轉</br>容錯移轉的先決條件</br>容錯移轉至虛擬裝置</br>業務連續性損毀修復 (BCDR)</br>期間損毀修復的錯誤|StorSimple 管理員服務 → 裝置|[在 StorSimple 虛擬陣列損毀復原和裝置容錯移轉](storsimple-ova-failover-dr.md)|
|備份與區</br>需要手動備份</br>變更備份排程</br>檢視現有的備份|StorSimple 管理員服務 → 備份目錄|[備份您的 StorSimple 虛擬陣列](storsimple-ova-backup.md)|
|從備份還原共用</br>從備份還原區</br>項目層級復原 （僅限檔案伺服器）|StorSimple 管理員服務 → 備份目錄|[從您 StorSimple 虛擬陣列備份還原](storsimple-ova-restore.md)|
|關於儲存帳戶</br>新增儲存空間帳戶</br>編輯儲存帳戶</br>刪除儲存帳戶|StorSimple 管理員服務 → 設定|[管理 StorSimple 虛擬陣列的儲存空間帳戶](storsimple-ova-manage-storage-accounts.md)|
|瞭解存取控制記錄</br>新增或修改 access 控制項記錄 </br>刪除存取控制記錄|StorSimple 管理員服務 → 設定|[管理存取控制記錄 StorSimple 虛擬陣列](storsimple-ova-manage-acrs.md)|
|檢視工作詳細資料|StorSimple 管理員服務 → 工作| [管理 StorSimple 虛擬陣列工作](storsimple-ova-manage-jobs.md)|
|設定提醒設定</br>收到通知的通知</br>管理提醒</br>檢閱通知|StorSimple 管理員服務 → 通知|[檢視及管理通知 StorSimple 虛擬陣列](storsimple-ova-manage-alerts.md)|
|修改裝置系統管理員的密碼|裝置 StorSimple 管理員服務 → → 設定|[變更 StorSimple 虛擬陣列裝置系統管理員的密碼](storsimple-ova-change-device-admin-password.md)|
|安裝軟體更新|裝置 StorSimple 管理員服務 → → 進行的維修作業|[更新您的虛擬陣列](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] 您必須使用[本機網頁 UI](storsimple-ova-web-ui-admin.md)如下列工作︰
>
>- [擷取服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [建立支援套件](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [停止然後再重新虛擬的陣列](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>後續步驟
網頁 UI 的相關資訊，以及如何使用它，請移至[使用 StorSimple 網路使用者介面，來管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
