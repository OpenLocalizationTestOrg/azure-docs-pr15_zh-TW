<properties 
   pageTitle="從備份還原 StorSimple 大量 |Microsoft Azure"
   description="說明如何使用 StorSimple 管理員服務備份型錄頁面從備份還原 StorSimple 音量。"
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>還原備份的設定 (更新 2) StorSimple 音量

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>概觀

**備份型錄**頁面會顯示所有備份集進行手動或自動備份時所建立的。 您可以使用此頁面] 清單中的備份的原則] 或 [音量]，選取的所有備份或刪除備份，或使用備份還原或複製區。

 ![備份型錄頁面](./media/storsimple-restore-from-backup-set-u2/restore.png)

本教學課程說明如何使用**備份型錄**頁面從備份還原您的裝置。

您可以還原從本機磁碟區或雲端備份。 在任一情況下，還原作業便會顯示線上的大量資料在背景下載時，立即。 

啟動還原作業之前，您應該注意下列任一動作︰

- **您必須採取音量離線**– 取回音量離線主機和之前的裝置上啟動還原作業。 雖然還原作業自動顯示在裝置上的音量線上，但您必須以手動方式讓線上使用的裝置，主機上。 音量線上在裝置上時，您就可以將大量線上主機上。 （您不需要還原作業完成等到。）程序，請移至[需要大量離線](storsimple-manage-volumes-u2.md#take-a-volume-offline)。

- **大量類型之後還原**– 根據快照; 輸入還原已刪除區也就是本機釘選的區會還原為固定在本機磁碟，並已層的區會還原為分層區。

    現有區，目前使用類型的音量會覆寫快照中所儲存的類型。 例如，如果您是從時大量類型層和大量類型本機現在固定 （因為執行轉換作業） 擷取的快照集還原區，然後音量會還原本機固定的音量。 同樣地，如果現有的本機固定的音量已展開，就會還原從較舊的快照集，當音量已較小，還原的音量仍會保留目前的展開的大小。

    您無法轉換大量至本機固定的大量分層音量或本機固定至分層大量區時音量正在進行還原。 請等候鍵，直到還原作業完成後，然後您可以將音量轉換為其他類型。 轉換大量的相關資訊，請移至[變更大量類型](storsimple-manage-volumes-u2.md#change-the-volume-type)。 

- **大量大小都會反映在還原音量**– 這是很重要的考量，如果您要還原已刪除 （因為固定在本機磁碟完全佈建） 本機固定的大量。 請確定您有足夠的空間，在您嘗試還原先前已遭刪除本機固定的音量。 

- **您無法展開時正在進行還原大量**– 等待等到還原作業完成後，在您嘗試展開音量。 展開大量的相關資訊，請移至[修改音量](storsimple-manage-volumes-u2.md#modify-a-volume)。

- **您可以執行備份時您要還原的本機磁碟區**– 程序移至[使用 StorSimple 管理員服務，管理備份原則](storsimple-manage-backup-policies.md)。

- **您可以取消還原作業**– 如果您要取消還原工作，然後音量會復原之前啟動還原作業的狀態。 程序，請移至[取消工作](storsimple-manage-jobs-u2.md#cancel-a-job)。

## <a name="how-to-use-the-backup-catalog"></a>如何使用備份的目錄

**備份型錄**頁面提供的查詢，可協助您縮小備份設定選取範圍。 您可以篩選的備份組擷取根據下列參數︰

- **裝置**– 在其中建立備份設定的裝置。
- **備份原則**或**大量**– 備份原則] 或 [此備份組相關聯的音量。
- **從**]，然後**以**– 建立備份設定時的日期和時間範圍。

篩選備份集是然後即形成表格根據下列屬性︰

- **名稱**– 備份原則或大量備份設定相關聯的名稱。
- **大小**– 備份設定的實際大小。
- **建立日期]** – 當建立備份的日期和時間。 
- **輸入**– 備份集可以本機快照或雲端快照集。 本機快照是備份的所有儲存在本機上裝置，大量資料，而雲端快照指的是位於雲端中的大量資料的備份。 本機的快照集可以提供更快速的存取權，而雲端快照為所選資料恢復。
- **以起始**– 由使用者的排程或手動備份也可以自動啟動。 （您可以使用備份原則排程備份。 或者，您可以使用 [**採取備份**選項才能互動式的備份。）

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>從備份檔案還原您 StorSimple 的音量

若要從特定備份檔案還原您 StorSimple 音量，您可以使用**備份型錄**頁面。 請記住，不過，還原大量會還原備份時的狀態音量。 備份作業之後所加入的任何資料將會遺失。

> [AZURE.WARNING] 從備份檔案還原，將會取代現有的磁碟區，從備份。 這可能會造成遺失的備份後所撰寫的任何資料。

### <a name="to-restore-your-volume"></a>若要還原您的音量

1. 在 StorSimple 管理員服務頁面上，按一下 [**備份目錄**] 索引標籤。

    ![備份的目錄](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. 選取備份設定，如下所示︰
  1. 選取適當的裝置。
  2. 在下拉式清單中，選擇您想要選取備份的音量或備份原則。
  3. 指定的時間範圍。
  4. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) 若要執行此查詢。
 
    備份原則應該會出現在清單或備份聯選取音量。

3. 展開備份設定檢視的相關聯的區。 這些磁碟必須離線主機和裝置上之前，您可以將其還原。 存取**大量容器**在頁面上，區，然後依照 [[取得大量離線](storsimple-manage-volumes-u2.md#take-a-volume-offline)，讓離線中的步驟。

    > [AZURE.IMPORTANT] 請確定已採取區離線主機上的第一次，才您裝置上會使離線。 如果您不執行主機上花費區離線，它可能會導致資料損毀。

4. 瀏覽回 [**備份類別目錄**] 索引標籤，然後選取備份的設定。

5. 按一下 [在頁面底部的 [**還原**]。

6. 系統會提示您確認。 檢閱還原資訊，然後按一下 [確認] 核取方塊。

    ![確認頁面](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. 按一下核取圖示![核取圖示](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png)。 這會啟動還原工作，您可以檢視存取 [**工作**] 頁面。 

8. 還原完畢之後，您可以驗證區的內容由備份的區。

![使用視訊](./media/storsimple-restore-from-backup-set-u2/Video_icon.png)**可用的視訊**

若要觀看影片示範如何使用複製及還原 StorSimple 復原已刪除的檔案中的功能，請按一下[這裡](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

## <a name="if-the-restore-fails"></a>如果無法還原

如果還原作業失敗基於任何原因，您會收到通知。 如果這種情況，請重新整理備份的清單，以確認備份仍然有效。 如果備份有效，而且您要還原雲端，然後連線問題可能會造成問題。 

完成還原作業、 離線音量主機上並再試一次還原作業。 請注意到還原程序時所執行的大量資料的任何變更將會遺失。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[管理 StorSimple 區](storsimple-manage-volumes-u2.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
