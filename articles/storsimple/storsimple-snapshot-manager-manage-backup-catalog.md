<properties 
   pageTitle="StorSimple 快照管理員備份目錄 |Microsoft Azure"
   description="說明如何使用 StorSimple 快照管理員 MMC 嵌入式管理單元檢視及管理類別目錄。"
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

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>使用 StorSimple 快照管理員來管理類別目錄

## <a name="overview"></a>概觀

主要功能 StorSimple 快照管理員的是讓您建立應用程式一致的備份複本，StorSimple 大量內容的形式的快照。 快照集就會列在稱為*備份類別目錄*的 XML 檔案。 備份的目錄會組織依大量群組及本機快照或雲端快照的快照。 

本教學課程會說明如何使用**備份目錄**節點完成下列工作︰

- 還原音量 
- 複製區或大量群組 
- 刪除備份 
- 復原檔案
- 還原 Storsimple 快照 Manager 資料庫

您可以檢視備份的目錄，展開 [**備份目錄**節點的**範圍**窗格中，然後再展開 [音量] 群組。

- 如果您按一下音量群組的名稱，[**結果**] 窗格會顯示本機快照與適用於 [音量] 群組中的雲端快照的數目。 

- 如果您按一下**區域快照**或**雲端快照**，[**結果**] 窗格會顯示每個備份快照 （根據您的**檢視**設定） 的下列資訊︰ 

    - **名稱**– 快照的時間。 

    - **輸入**– 這是本機快照或雲端快照。 

    - **擁有者**– 內容的擁有者。 

    - **可用**– 是否目前可用的快照。 **True**表示快照可用，而且可以還原。**False**表示快照不再提供使用。 

    - **匯入**– 備份是否已匯入。 **True**指示備份已匯入 StorSimple 管理員服務時使用的裝置已設定好 StorSimple 快照管理員中;**False**表示它不匯入，但 StorSimple 快照管理員所建立。 （您可以輕鬆識別匯入的大量群組後置字元加入的識別要從中匯入 [音量] 群組中的裝置，因此。）

    ![備份的目錄](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- 如果您展開**區域快照**或**雲端快照**，然後再按一下 [個別的快照集名稱，[**結果**] 窗格會顯示您所選的快照的下列資訊︰

    - **名稱**– 磁碟機字母，以識別音量。 

    - **本機名稱**– 本機磁碟機 （如果有的話） 的名稱。 

    - **裝置**– 裝置的名稱上音量已存在。 

    - **可用**– 是否目前可用的快照。 **True**表示快照可用，而且可以還原。**False**表示快照不再提供使用。 


## <a name="restore-a-volume"></a>還原音量

從備份還原大量使用下列程序。

#### <a name="prerequisites"></a>必要條件

如果您有不這麼做，建立大量和大量群組，然後刪除 [音量。 根據預設，StorSimple 快照管理員備份大量才允許它刪除。 如果資料需要復原基於任何原因不小心刪除音量或，此預防措施可以防止資料遺失。 

它會建立預防備份時，StorSimple 快照管理員就會顯示下列訊息。

![自動快照訊息](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]您無法刪除的大量群組的一部分。 [刪除] 選項無法使用。 <br>

#### <a name="to-restore-a-volume"></a>若要還原的音量

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，展開 [**備份目錄**節點，展開 [音量] 群組中，，然後按一下**本機快照**或**雲端快照集**。 備份快照的清單會出現在 [**結果**] 窗格中。 

3. 尋找您要還原的備份按一下滑鼠右鍵，然後按一下 [**還原**。 

    ![還原備份的目錄](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. 在 [確認] 頁面上檢閱詳細資料]，輸入**確認**，然後再按一下**[確定]**。 StorSimple 快照管理員會使用備份還原音量。 

    ![還原確認訊息](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. 執行時，您可以監視還原動作。 在 [**範圍**] 窗格中，展開 [**工作**] 節點，然後按一下**執行**。 在 [**結果**] 窗格中，顯示工作詳細資料。 還原工作完成時，就會將工作詳細資料傳送到**最後一個 24 小時**清單。

## <a name="clone-a-volume-or-volume-group"></a>複製區或大量群組

您可以使用下列程序來建立大量或大量] 群組中的重複 （複製）。

#### <a name="to-clone-a-volume-or-volume-group"></a>若要複製區或大量群組

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，展開 [**備份目錄**節點，展開 [音量] 群組中，，然後按一下**雲端快照集**。 備份的清單會出現在 [**結果**] 窗格中。

3. 尋找音量或您想要複製，以滑鼠右鍵按一下音量或大量群組的名稱，然後按一下 [**複製**的 [音量] 群組。 [**複製雲端快照**] 對話方塊隨即出現。

    ![複製雲端快照](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. 請完成**複製雲端快照**] 對話方塊中，如下所示︰ 

    1. 在 [**名稱**] 文字方塊中，輸入複製區的名稱。 這個名稱會出現在**區**節點。 

    2. （選擇性） 選取**磁碟機**，，然後從下拉式清單中選取 [磁碟機字母。 

    3. （選擇性） 選取**資料夾 (NTFS)**，輸入資料夾路徑，或按一下 [瀏覽並選取資料夾的位置。 

    4. 按一下 [**建立**]。

5. 複製的程序完成時，您必須初始化複製的音量。 啟動伺服器管理員]，然後再啟動 [磁碟管理。 如需詳細指示，請參閱[裝載區](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)。 初始化之後，會列在 [**範圍**] 窗格中的**區**節點音量。 如果您沒有看到所列的音量，重新整理磁碟區清單 （[**區**] 節點，以滑鼠右鍵按一下，然後按一下 [**重新整理**）。

## <a name="delete-a-backup"></a>刪除備份

若要刪除類別目錄的快照，使用下列程序。 

>[AZURE.NOTE]刪除快照刪除快照集相關聯的備份的資料。 不過，清除雲端的資料的程序可能需要一些時間。<br>
 
#### <a name="to-delete-a-backup"></a>若要刪除的備份

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，展開 [**備份目錄**節點，展開 [音量] 群組中，，然後按一下**本機快照**或**雲端快照集**。 快照的清單會出現在 [**結果**] 窗格中。 

3. 以滑鼠右鍵按一下您想要刪除的快照，然後按一下 [**刪除**。

4. 出現確認訊息，請按一下**[確定**]。 

## <a name="recover-a-file"></a>復原檔案

如果不小心刪除大量檔案，您可以復原檔案擷取快照的預先日期刪除與使用快照建立複製的 [音量]，然後複製檔案複製音量設為原始音量。

#### <a name="prerequisites"></a>必要條件

在開始之前，請確定您有目前 [音量] 群組中的備份。 然後，刪除儲存在其中一個區群組中的媒體檔案。 最後，使用下列步驟，從您的備份還原已刪除的檔案。 

#### <a name="to-recover-a-deleted-file"></a>若要復原已刪除的檔案

1. 按一下在桌面上的 StorSimple 快照管理員圖示。 StorSimple 快照管理員主控台視窗隨即出現。 

2. 在 [**範圍**] 窗格中，展開 [**備份目錄**節點，並瀏覽至的快照，包含已刪除的檔案。 一般而言，您應該選擇刪除之前所建立的快照。 

3. 尋找您想要複製，大量按一下滑鼠右鍵，然後按一下 [**複製**。 [**複製雲端快照**] 對話方塊隨即出現。

    ![複製雲端快照](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. 請完成**複製雲端快照**] 對話方塊中，如下所示︰ 

   1. 在 [**名稱**] 文字方塊中，輸入複製區的名稱。 這個名稱會出現在**區**節點。 

   2. （選用）選取 [**磁碟機**，，然後從下拉式清單中選取 [磁碟機字母。 

   3. （選用）選取**資料夾 (NTFS)**，然後輸入資料夾路徑，或按一下 [**瀏覽**並選取資料夾的位置。 

   4. 按一下 [**建立**]。 

5. 複製的程序完成時，您必須初始化複製的音量。 啟動伺服器管理員]，然後再啟動 [磁碟管理。 如需詳細指示，請參閱[裝載區](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)。 初始化之後，會列在 [**範圍**] 窗格中的**區**節點音量。 

    如果您沒有看到所列的音量，重新整理磁碟區清單 （[**區**] 節點，以滑鼠右鍵按一下，然後按一下 [**重新整理**）。

6. 開啟包含複製的區的 NTFS 資料夾，展開 [**區**] 節點，然後開啟複製的音量。 找出您要復原的檔案，然後將其複製到主要音量。

7. 還原檔案之後，您可以刪除包含複製的區的 NTFS 資料夾。

## <a name="restore-the-storsimple-snapshot-manager-database"></a>還原 StorSimple 快照 Manager 資料庫

您應該定期備份 StorSimple 快照管理員資料庫主機電腦上。 如果發生損毀，或因任何原因主機電腦失敗，您可以將它然後還原備份。 建立資料庫備份是手動程序。

#### <a name="to-back-up-and-restore-the-database"></a>若要備份及還原資料庫

1. 停止 Microsoft StorSimple 管理服務︰

    1. 啟動伺服器管理員]。

    2. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。

    3. 在 [**服務**] 視窗中，選取 [ **Microsoft StorSimple 管理服務**。

    4. 在右窗格中，在 [ **Microsoft StorSimple 管理服務**] 底下，按一下 [**停止服務**。

2. Host （主機） 電腦上，瀏覽至 C:\ProgramData\Microsoft\StorSimple\BACatalog。 

    >[AZURE.NOTE] ProgramData 是隱藏的資料夾。
 
3. 找出目錄 XML 檔案，複製檔案]，並的複本存放在安全的位置，或在雲端。 如果主機失敗，您可以使用此備份檔案以協助您復原您建立 StorSimple 快照管理員中的備份原則。

    ![Azure StorSimple 備份目錄檔案](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. 重新啟動 Microsoft StorSimple 管理服務︰ 

    1. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。
    
    2. 在 [**服務**] 視窗中，選取 [ **Microsoft StorSimple 管理服務**。

    3. 在右窗格中，在**Microsoft StorSimple 管理服務**] 底下，按一下 [**重新啟動服務**。

5. Host （主機） 電腦上，瀏覽至 C:\ProgramData\Microsoft\StorSimple\BACatalog。 

6. 刪除目錄的 XML 檔案，然後替換成您所建立的備份版本。 

7. 按一下 [桌面 StorSimple 快照管理員圖示以啟動 StorSimple 快照集管理員]。 

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 進一步瞭解[StorSimple 快照管理員工作與工作流程](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows)。
