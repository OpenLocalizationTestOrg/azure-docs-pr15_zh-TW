<properties 
   pageTitle="從備份還原 StorSimple 大量 |Microsoft Azure"
   description="說明如何使用 StorSimple 管理員服務備份型錄頁面從備份還原 StorSimple 音量。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>從備份還原 StorSimple 音量

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>概觀

**備份型錄**頁面會顯示所有備份集進行手動或自動備份時所建立的。 您可以使用此頁面] 清單中的備份的原則] 或 [音量]，選取的所有備份或刪除備份，或使用備份還原或複製區。

 ![備份型錄頁面](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

本教學課程說明如何使用**備份型錄**頁面從備份還原您的裝置上區。

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

若要從特定備份檔案還原您 StorSimple 音量，您可以使用**備份型錄**頁面。 

> [AZURE.WARNING] 從備份檔案還原，將會取代現有的磁碟區，從備份。 這可能會造成遺失的備份後所撰寫的任何資料。

啟動區還原之前，請確定音量已離線。 您必須先主機上的大量離線，然後使用的裝置。 請遵循[採取離線區](storsimple-manage-volumes.md#take-a-volume-offline)中的步驟進行。 執行下列步驟，以從備份還原音量。

### <a name="to-restore-from-a-backup-set"></a>若要從備份還原

1. 在 StorSimple 管理員服務頁面上，按一下 [**備份目錄**] 索引標籤。

    ![備份的目錄](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. 選取備份設定，如下所示︰
  1. 選取適當的裝置。
  2. 在下拉式清單中，選擇您想要選取備份的音量或備份原則。
  3. 指定的時間範圍。
  4. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) 若要執行此查詢。
 
    備份原則應該會出現在清單或備份聯選取音量。

3. 展開備份設定檢視的相關聯的區。 這些磁碟必須離線主機和裝置上之前，您可以將其還原。 請遵循[採取離線區](storsimple-manage-volumes.md#take-a-volume-offline)中的步驟進行。

    >  [AZURE.IMPORTANT] 請確定已採取區離線主機上的第一次，才您裝置上會使離線。 如果您不執行主機上花費區離線，它可能會導致資料損毀。

4. 選取備份的設定。 按一下 [在頁面底部的 [**還原**]。

6. 系統會提示您確認。 

    ![確認頁面](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. 檢閱還原資訊，然後按一下核取圖示![核取圖示](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png)。 這會啟動還原工作，您可以檢視存取 [**工作**] 頁面。 

8. 還原完畢之後，您可以驗證區的內容由備份的區。

![使用視訊](./media/storsimple-restore-from-backup-set/Video_icon.png)**可用的視訊**

若要觀看影片示範如何使用複製及還原 StorSimple 復原已刪除的檔案中的功能，請按一下[這裡](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[管理 StorSimple 區](storsimple-manage-volumes.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
