<properties 
   pageTitle="管理您的 StorSimple 備份目錄 |Microsoft Azure"
   description="說明如何使用 StorSimple 管理員服務備份型錄頁面] 清單中，選取，並刪除備份集區。"
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>使用 StorSimple 管理員服務來管理您的備份目錄

## <a name="overview"></a>概觀

[StorSimple 管理員服務**備份類別目錄**] 頁面會顯示所有備份集進行手動或排程備份時所建立的。 您可以使用此頁面] 清單中的備份的原則] 或 [音量]，選取的所有備份或刪除備份，或使用備份還原或複製區。

本教學課程會說明如何在清單中，選取 [，，刪除備份集。 若要瞭解如何從備份還原您的裝置，移至[還原從備份您的裝置](storsimple-restore-from-backup-set.md)。 若要瞭解如何複製區，請移至[複製 StorSimple 音量](storsimple-clone-volume.md)。

![備份的目錄](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

**備份型錄**頁面提供查詢來縮小備份設定選取範圍。 您可以篩選的備份組擷取，根據下列參數︰

- **裝置**– 在其中建立備份設定的裝置。

- **備份原則或大量**– 備份原則] 或 [此備份組相關聯的音量。

- **的**– 建立備份設定時的日期和時間範圍。

篩選備份集是然後即形成表格根據下列屬性︰

- **名稱**– 備份原則或大量備份設定相關聯的名稱。

- **大小**– 備份設定的實際大小。

- **建立在**備份時所建立的日期和時間。 

- **輸入**– 備份集可以本機快照或雲端快照集。 本機快照是備份的所有儲存在本機上裝置，大量資料，而雲端快照指的是位於雲端中的大量資料的備份。 本機的快照集可以提供更快速的存取權，而雲端快照為所選資料恢復。

- **發起的租用戶由**– 由使用者由排程或手動備份也可以自動啟動。 您可以使用備份原則排程備份。 或者，您可以使用 [**採取備份**選項才會手動備份。

## <a name="list-backup-sets-for-a-volume"></a>清單備份設定來調整音量
 
完成下列步驟，以清單的大量的所有備份。

#### <a name="to-list-backup-sets"></a>清單備份組

1. 在 StorSimple 管理員服務頁面上，按一下 [**備份目錄**] 索引標籤。

2. 篩選選項如下所示︰

    1. 選取適當的裝置。

    2. 在下拉式清單中，選擇 [音量以檢視對應備份]。

    3. 指定的時間範圍。

    4. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 若要執行此查詢。
 
    選取大量相關聯的備份應該出現在清單中。

## <a name="select-a-backup-set"></a>選取 [備份的設定

完成下列步驟，以選取備份磁碟區或備份原則設定。

#### <a name="to-select-a-backup-set"></a>若要選取備份的設定

1. 在 StorSimple 管理員服務頁面上，按一下 [**備份目錄**] 索引標籤。

2. 篩選選項如下所示︰

    1. 選取適當的裝置。

    2. 在下拉式清單中，選擇您想要選取備份的音量或備份原則。

    3. 指定的時間範圍。

    4. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 若要執行此查詢。

    備份原則應該會出現在清單或備份聯選取音量。

3. 選取並展開備份的設定。 **還原**及**刪除**選項會顯示在頁面底部。 您可以在您選取備份設定來執行這些動作。

## <a name="delete-a-backup-set"></a>刪除備份集

當您不再想要保留的資料與其相關聯時，請刪除備份。 執行下列步驟，以刪除備份集。

#### <a name="to-delete-a-backup-set"></a>若要刪除備份的設定

1. 在 [StorSimple 管理員服務] 頁面中，按一下 [**備份目錄] 索引標籤**。

2. 篩選選項如下所示︰

    1. 選取適當的裝置。

    2. 在下拉式清單中，選擇您想要選取備份的音量或備份原則。

    3. 指定的時間範圍。

    4. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 若要執行此查詢。

    備份原則應該會出現在清單或備份聯選取音量。

3. 選取並展開備份的設定。 **還原**及**刪除**選項會顯示在頁面底部。 按一下 [**刪除**]。

4. 刪除位於及時已順利完成的進度時，您會收到通知。 刪除完畢後，重新整理此頁面的查詢。 已刪除的備份設定將不會再出現在下列清單中。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用備份還原您的裝置，從備份目錄](storsimple-restore-from-backup-set.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
