<properties 
   pageTitle="StorSimple 快照管理員備份工作 |Microsoft Azure"
   description="說明如何使用 StorSimple 快照管理員 MMC 嵌入式管理單元中，來檢視和管理排程與目前正在執行，已完成的備份工作。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/26/2016"
   ms.author="v-sharos" />


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>若要檢視及管理備份工作使用 StorSimple 快照管理員

## <a name="overview"></a>概觀

**排程**、**最後一個 24 小時**及**執行**備份的工作您啟動互動的方式或設定原則，則會顯示**工作**窗格中的節點**範圍**。 

本教學課程會說明如何使用 [**工作**] 節點顯示排程與最近目前正在執行備份工作的相關資訊。 （工作相對應的資訊的清單會在 [**結果**] 窗格中）。此外，您可以用滑鼠右鍵按一下所列的工作，並查看內容功能表會列出可用的動作。

## <a name="view-scheduled-jobs"></a>檢視排程的工作

您可以使用下列程序來檢視排程備份工作。

#### <a name="to-view-scheduled-jobs"></a>若要檢視排程的工作

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，展開 [**工作**] 節點，然後按一下**排程**。 在 [**結果**] 窗格中，顯示下列資訊︰

    - **名稱**– 排程快照的名稱

    - **下一步執行**– 的日期和時間的下一個排程快照

    - **最後一個執行**– 的日期和時間的最新的排程快照

    >[AZURE.NOTE] 一次只快照，**執行下一個**和**最後一個執行**位於相同。 
 
    ![排定的備份工作](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. 若要在指定的工作上執行的其他動作，以滑鼠右鍵按一下工作名稱，在 [**結果**] 窗格中，選取] 功能表中。

## <a name="view-recent-jobs"></a>檢視最近的工作

您可以使用下列程序來檢視備份與還原過去 24 小時中已完成的工作。

#### <a name="to-view-recent-jobs"></a>若要檢視最近的工作

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，展開 [**工作**] 節點，然後按一下**最後一個 24 小時**。 [**結果**] 窗格會顯示備份工作的過去 24 小時 （以 64 工作的最大值）。 在 [**結果**] 窗格中，根據您指定的**檢視**選項，會出現下列資訊︰

    - **名稱**– 排程快照的名稱。
 
    - **入門**– 的日期和時間時開始快速查詢。

    - **停止**– 的日期和時間時快照完成或已終止。

    - 時間**經過**–**已啟動**和**停止**之間的時間量。

    - **狀態**-最近完成工作的狀態。 **成功**表示備份已順利建立。 **失敗**指出工作並未順利執行。

    - **資訊**– 失敗的原因。

    - **位元組處理 (MB)** – 從處理 （以 Mb) 的 [音量] 群組的資料量。 

    ![在過去 24 小時中執行的工作](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. 若要在指定的工作上執行的其他動作，以滑鼠右鍵按一下工作名稱，在 [**結果**] 窗格中，選取] 功能表中。

    ![刪除工作](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>檢視目前正在執行的工作

使用下列程序正在執行的檢視工作。

#### <a name="to-view-currently-running-jobs"></a>若要檢視目前正在執行的工作

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，展開 [**工作**] 節點，然後按一下**執行**。 根據您指定的**檢視**選項，在 [**結果**] 窗格中會出現下列資訊︰ 

    - **名稱**– 排程快照的名稱。

    - **入門**– 的日期和時間時開始快速查詢。

    - **重點複習**– 備份的目前的動作。

    - **狀態**-完成的百分比。
    
    - **經過**– 從備份開始後所經過的時間量。 

    - **平均處理量 (MB)** – 的總位元組資料處理全部時間處理 (Mb) 的比率。

    - **位元組處理 (MB)** – 總位元組處理 （以 Mb) 的資料。

    - **位元組撰寫 (MB)** – 總位元組 （以 Mb) 撰寫的資料。 它包含資料，以及中繼資料，因此大於通常位元組處理。

    ![目前正在執行的工作](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. 若要在指定的工作上執行的其他動作，以滑鼠右鍵按一下工作名稱，在 [**結果**] 窗格中，選取] 功能表中。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 瞭解如何[使用 StorSimple 快照管理員來管理類別目錄](storsimple-snapshot-manager-manage-backup-catalog.md)。















            


 

