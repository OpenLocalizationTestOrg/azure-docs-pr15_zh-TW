<properties 
   pageTitle="StorSimple 管理員服務儀表板-虛擬陣列 |Microsoft Azure"
   description="說明 StorSimple 管理員服務儀表板，並說明如何使用它來監控您 StorSimple 虛擬陣列的狀況。"
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>使用 StorSimple 虛擬陣列 StorSimple 管理員服務的儀表板

## <a name="overview"></a>概觀

StorSimple 管理員服務儀表板頁面提供 [摘要] 檢視的 StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置或虛擬裝置） 連線到 StorSimple 管理員服務，醒目提示需要系統管理員的注意。 本教學課程介紹儀表板頁面、 說明的儀表板內容和函數，並說明您可以在本頁面上執行的工作。

![服務儀表板](./media/storsimple-ova-service-dashboard/dashboard1.png)

StorSimple 管理員服務的儀表板中會顯示下列資訊︰

- 在頁面頂端的 [**圖表**] 區域，您可以看到相關的指標虛擬裝置。 您可以檢視主要跨所有的虛擬裝置的儲存空間，以及在一段時間虛擬裝置使用雲端儲存空間。 使用圖表右上角控制項，若要指定相對或絕對的使用方式，以查看 1 週、 1 個月、 3 個月或 1 年的時間刻度。 使用 [重新整理] 控制項![重新整理控制項](./media/storsimple-ova-service-dashboard/refresh-control.png)重新整理圖表。

- **使用概觀**顯示主要佈建後，相對於總的儲存空間的所有虛擬裝置上的虛擬裝置使用儲存空間。 **Provisioned**參照的準備及配置使用的儲存空間量、**使用**會參照到共用或區來連線、 虛擬裝置至啟動器檢視使用情況，**最大容量**會顯示所有的虛擬裝置的最大的容量。

- **通知**區域提供所有的虛擬裝置，分組警示嚴重性作用中的所有提醒的快照。 按一下重要性層級會開啟 [**通知**] 頁面，顯示這些通知的範圍。 在 [**提醒**] 頁面中，您可以按一下個別通知若要檢視其他詳細資料的提醒，包括任何建議的動作。 如果問題已解決，您也可以清除提醒。

- [**工作**] 區域上的虛擬裝置連接到您的服務，提供最近的工作的快照。 有可供您查看工作的目前進度以及成功或失敗在過去 24 小時的連結。 

- 在右側的頁面上的 [**快速檢視**] 區域提供有用的資訊，例如 [服務狀態] 總數虛擬裝置連線到服務、 服務的位置，並與服務相關聯的訂閱的詳細資料。 此外，還有作業記錄檔的連結。 按一下連結以查看所有已完成的 StorSimple 管理員服務作業的清單。 

您可以使用 [StorSimple 管理員服務] 儀表板頁面進行下列工作︰

- 取得服務登錄機碼。
- 檢視作業的記錄檔。

## <a name="get-the-service-registration-key"></a>取得服務登錄機碼

服務登錄機碼用於登錄 StorSimple 虛擬裝置的 StorSimple 管理員服務，以便進一步管理動作的 Azure 傳統入口網站中出現的裝置。 索引鍵，第一個虛擬裝置上建立並與剩餘的虛擬裝置共用。 

按一下 [**登錄機碼**（在頁面底端），隨即會開啟 [**服務登錄機碼**] 對話方塊，您可以將目前的服務登錄機碼複製到剪貼簿或重新產生服務登錄機碼。

![登錄機碼](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

重新產生金鑰並不會影響先前已註冊的虛擬裝置︰ 它會影響只有虛擬裝置註冊的服務後重新產生金鑰。

如需取得服務登錄機碼的詳細資訊，移至 [[取得服務登錄機碼。](storsimple-ova-manage-service.md#get-the-service-registration-key)

## <a name="view-the-operations-logs"></a>檢視作業的記錄檔

您可以按一下儀表板的**快速檢視**窗格中可用的作業記錄連結來檢視作業記錄。 這會將您帶往 [管理服務] 頁面，您可以在此篩選，並查看您 StorSimple 管理員服務的特定記錄。

![作業的記錄](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>後續步驟

瞭解如何[使用本機網站來管理您的 StorSimple 虛擬陣列的使用者介面](storsimple-ova-web-ui-admin.md)。