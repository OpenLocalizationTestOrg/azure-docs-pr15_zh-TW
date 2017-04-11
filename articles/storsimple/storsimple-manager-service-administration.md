<properties 
   pageTitle="StorSimple 管理員服務管理 |Microsoft Azure"
   description="瞭解如何使用 StorSimple 管理員服務 Azure 傳統入口網站中管理您的 StorSimple 裝置。"
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>使用 StorSimple 管理員服務來管理您的 StorSimple 裝置

## <a name="overview"></a>概觀

本文將說明 StorSimple 管理員服務介面，包括如何連線至、 使用各種不同的選項和出透過這個 UI，才能執行的特定工作流程的連結。 本指南可套用至兩者。StorSimple 實體和虛擬裝置。

閱讀本文之後，您將學習如何︰

- 連線至 StorSimple 管理員服務
- 瀏覽 StorSimple 管理員使用者介面
- 管理您的 StorSimple 裝置，透過 StorSimple 管理員服務


## <a name="connect-to-storsimple-manager-service"></a>連線至 StorSimple 管理員服務

StorSimple 管理員服務 Microsoft Azure 中執行，並且連線到多個 StorSimple 裝置。 您管理這些裝置使用管理中心的 Microsoft Azure 傳統入口網站，並在瀏覽器中執行。 若要連接的 StorSimple 管理員服務，執行下列動作。

#### <a name="to-connect-to-the-service"></a>若要連線至服務

1. 瀏覽至[https://manage.windowsazure.com/](https://manage.windowsazure.com/)。

1. 使用您的 Microsoft 帳戶認證，登入 Microsoft Azure 傳統入口網站 （位於右上方的窗格）。

1. 向下捲動左側的導覽窗格存取 StorSimple 管理員服務。


## <a name="navigate-storsimple-manager-service-ui"></a>瀏覽 StorSimple 管理員服務使用者介面

瀏覽階層 StorSimple 管理員服務的使用者介面會顯示下列表格中。

- **StorSimple 管理員**登陸頁面會帶您到服務中的所有裝置適用的 UI 服務層級頁面。

- **裝置**頁面會帶您到特定裝置適用的裝置 – 層級 UI 頁面。

- **大量容器**頁面會顯示所有裝置相關聯的磁碟區大量頁面。


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple 管理員服務導覽階層

|登陸頁面|服務等級頁面|裝置層頁面|裝置層頁面|
|---|---|---|---|
|StorSimple 管理員服務|服務儀表板|裝置儀表板||
||裝置 →|監視器|
||備份的目錄|大量 containers→|區|
||設定 （服務）|備份原則||
||工作|設定 （裝置）|
||通知|進行的維修作業|

![使用視訊](./media/storsimple-manager-service-administration/Video_icon.png)**可用的視訊**

若要觀看逐步引導您進行 StorSimple 管理員服務使用者介面的視訊，請按一下[這裡](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/)。

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>管理 StorSimple 裝置使用 StorSimple 管理員服務

下表顯示的所有管理的一般工作和複雜的工作流程可以執行 StorSimple 管理員服務使用者介面中的摘要。 這些工作組織根據要在其它們發起的租用戶的使用者介面頁面。

如需有關每個工作流程的詳細資訊，請按一下表格中適當的程序。

#### <a name="storsimple-manager-workflows"></a>StorSimple 管理員工作流程

|如果您想要執行此動作...|前往 UI 本頁...|使用此程序。|
|---|---|---|
|建立服務</br>刪除服務</br>取得服務登錄機碼</br>每個服務登錄機碼|StorSimple 管理員服務|[部署 StorSimple 管理員服務](storsimple-manage-service.md)
|變更服務資料加密金鑰</br>檢視作業的記錄檔|StorSimple 管理員服務 → 儀表板|[使用 [StorSimple 管理員服務儀表板](storsimple-service-dashboard.md)|
|停用的裝置</br>刪除裝置|StorSimple 管理員服務 → 裝置|[停用或刪除裝置](storsimple-deactivate-and-delete-device.md)|
|深入了解損毀修復與裝置容錯移轉</br>容錯移轉到實體裝置</br>容錯移轉至虛擬裝置</br>業務連續性損毀修復 (BCDR)|StorSimple 管理員服務 → 裝置|[錯誤移轉及損毀復原 StorSimple 裝置](storsimple-device-failover-disaster-recovery.md)|
|清單備份來調整音量</br>選取 [備份的設定</br>刪除備份集|StorSimple 管理員服務 → 備份目錄|[管理備份](storsimple-manage-backup-catalog.md)|
|複製區|StorSimple 管理員服務 → 備份目錄|[複製區](storsimple-clone-volume.md)|
|還原備份的設定|StorSimple 管理員服務 → 備份目錄|[還原備份的設定](storsimple-restore-from-backup-set.md)|
|關於儲存帳戶</br>新增儲存空間帳戶</br>編輯儲存帳戶</br>刪除儲存帳戶</br>儲存帳戶的重要的旋轉角度|StorSimple 管理員服務 → 設定|[管理儲存空間帳戶](storsimple-manage-storage-accounts.md)|
|關於頻寬範本</br>新增頻寬範本</br>編輯頻寬範本</br>刪除頻寬範本</br>使用預設頻寬範本</br>建立全天頻寬範本開始在指定的時間|StorSimple 管理員服務 → 設定|[管理頻寬範本](storsimple-manage-bandwidth-templates.md)|
|瞭解存取控制記錄</br>建立 access 控制項記錄</br>編輯存取控制記錄</br>刪除存取控制記錄|StorSimple 管理員服務 → 設定|[管理存取控制記錄](storsimple-manage-acrs.md)|
|檢視工作詳細資料</br>取消工作|StorSimple 管理員服務 → 工作|[管理工作](storsimple-manage-jobs.md)
|收到通知的通知</br>管理提醒</br>檢閱通知|StorSimple 管理員服務 → 通知|[檢視及管理 StorSimple 通知](storsimple-manage-alerts.md)
|檢視連線的啟動器</br>尋找裝置序列值</br>尋找目標 IQN|裝置 StorSimple 管理員服務 → → 儀表板|[使用 [StorSimple 裝置儀表板](storsimple-device-dashboard.md)|
|建立監控圖表|裝置 StorSimple 管理員服務 → → 監控|[監控 StorSimple 裝置](storsimple-monitor-device.md)|
|新增大量容器</br>修改大量容器</br>刪除大量容器|StorSimple 管理員服務 → 裝置 → 大量容器|[管理大量容器](storsimple-manage-volume-containers.md)|
|新增大量</br>修改音量</br>離線工作區</br>刪除區</br>監控音量|StorSimple 管理員服務 → 裝置 → 大量容器 → 區|[管理大量內容](storsimple-manage-volumes.md)|
|修改裝置設定</br>修改時間設定</br>修改 DNS.md 設定</br>設定網路介面|裝置 StorSimple 管理員服務 → → 設定|[修改 StorSimple 裝置的裝置設定](storsimple-modify-device-config.md)|
|檢視 web proxy 設定|裝置 StorSimple 管理員服務 → → 設定|[設定您的裝置的網頁 proxy](storsimple-configure-web-proxy.md)|
|修改 [裝置管理員密碼</br>修改 StorSimple 快照管理員密碼|裝置 StorSimple 管理員服務 → → 設定|[變更 StorSimple 密碼](storsimple-change-passwords.md)|
|設定 [遠端管理|裝置 StorSimple 管理員服務 → → 設定|[從遠端連線到您的 StorSimple 裝置](storsimple-remote-connect.md)|
|設定提醒設定|裝置 StorSimple 管理員服務 → → 設定|[檢視及管理 StorSimple 通知](storsimple-manage-alerts.md)|
|設定 CHAP StorSimple 裝置|裝置 StorSimple 管理員服務 → → 設定|[設定 CHAP StorSimple 裝置](storsimple-configure-chap.md)|
|新增備份的原則</br>新增或修改排程</br>刪除備份原則</br>需要手動備份</br>建立自訂的備份原則與多個區排程|StorSimple 管理員服務 → 裝置 → 備份原則|[管理備份原則](storsimple-manage-backup-policies.md)|
|停止裝置控制器</br>重新啟動裝置控制站</br>關閉 [裝置控制站</br>重設您的裝置工廠預設值</br>（僅限內部部署裝置上方是）|裝置 StorSimple 管理員服務 → → 進行的維修作業|[管理 StorSimple 裝置控制器](storsimple-manage-device-controller.md)|
|深入了解 StorSimple 硬體元件</br>監視器硬體狀態</br>（僅限內部部署裝置上方是）|裝置 StorSimple 管理員服務 → → 進行的維修作業|[監視器硬體元件](storsimple-monitor-hardware-status.md)|
|建立支援套件|裝置 StorSimple 管理員服務 → → 進行的維修作業|[建立及管理支援套件](storsimple-create-manage-support-package.md)|
|安裝軟體更新|裝置 StorSimple 管理員服務 → → 進行的維修作業|[更新您的裝置](storsimple-update-device.md)|


##<a name="next-steps"></a>後續步驟
如果您遇到任何問題 StorSimple 裝置的每日作業或硬體元件，請參閱︰

- [疑難排解操作的裝置](storsimple-troubleshoot-operational-device.md)
- [使用 StorSimple 監控指標 Led](storsimple-monitoring-indicators.md)

如果您無法解決問題，您需要建立服務要求，請參閱[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。
