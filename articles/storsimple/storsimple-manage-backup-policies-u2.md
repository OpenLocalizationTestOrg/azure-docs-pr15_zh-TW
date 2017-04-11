<properties 
   pageTitle="管理您的 StorSimple 備份原則 |Microsoft Azure"
   description="說明如何使用 StorSimple 管理員服務來建立和管理手動備份、 備份的排程，並備份保留。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>使用 StorSimple 管理員服務來管理備份原則 (更新 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>概觀

本教學課程說明如何使用 [StorSimple 管理員服務**備份原則**] 頁面來控制備份的程序和備份保留您 StorSimple 區。 它也會說明如何完成手動備份。

當您備份區時，您可以選擇建立本機快照或雲端快照。 如果您要備份本機固定的音量，我們建議您指定雲端快照。 進行大量的本機固定區之後，有許多變換資料集的本機快照集可能造成的情況下，您可以快速執行本機的空間。 如果您選擇本機的快照，我們建議您要備份的最新狀態，保留它們，一天的較少的每日快照，然後將它們刪除。

當本機固定的大量的雲端快照，您可以複製變更的資料至雲端，它是 deduplicated 及壓縮的位置。 

## <a name="the-backup-policies-page"></a>[備份原則] 頁面

[**備份原則**] 頁面可讓您管理備份的原則和排程本機雲端快照集。 （備份原則會用來設定備份的排程和備份保留集合的區）。備份原則可讓您同時擷取的多個區快照。 這表示備份原則的備份，將會當機一致的複本。 **備份原則**] 頁面上列出的備份原則、 其類型、 相關聯的區、 備份保留，數和啟用這些原則的選項。

[**備份原則**] 頁面也可讓您篩選的現有的備份原則的一或多個下列欄位︰

- **原則名稱**– 相關聯原則的名稱。 不同類型的原則包括︰

   - 排程的原則，明確建立的使用者。
   - 自動原則，這個選項，大量的預設備份已啟用時的大量建立時所建立。 這些原則會命名為*/p*_Default 其中*/p*指的是由 Azure 傳統入口網站中的使用者設定 StorSimple 大量的名稱。 每日雲端快照開頭 22:30 裝置次產生的自動原則。
   - 匯入的原則，原本建立 StorSimple 快照管理員中。 這些具有描述所匯入原則 StorSimple 快照管理員主機的標籤。

- **區**– 相關聯原則的區。 備份原則相關聯的所有區都群組在一起時建立備份。

- **最後一次成功備份**– 的日期和時間的最後一個成功的備份與此原則的。

- **下一步備份**– 的日期與時間會啟動此原則的下一個排程備份。

- **排程**– 排程備份原則相關聯的數目。

您可以執行此頁面的常用的作業是︰

- 新增備份的原則 
- 新增或修改排程 
- 刪除備份原則 
- 需要手動備份 
- 建立自訂的備份原則與多個區排程 

## <a name="add-a-backup-policy"></a>新增備份的原則

新增自動排程備份備份原則。 若要新增 StorSimple 裝置的備份原則 Azure 傳統入口網站中，執行下列步驟。 新增原則之後，您可以定義排程 (請參閱[新增或修改排程](#add-or-modify-a-schedule))。

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![使用視訊](./media/storsimple-manage-backup-policies-u2/Video_icon.png)**可用的視訊**

若要觀看影片示範如何建立本機或雲端備份原則，請按一下[這裡](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)。


## <a name="add-or-modify-a-schedule"></a>新增或修改排程

您可以新增或修改附加至現有的備份原則 StorSimple 裝置上的排程。 若要新增或修改排程 Azure 傳統入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>刪除備份原則

若要刪除 StorSimple 裝置上的備份原則 Azure 傳統入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>需要手動備份

若要建立單一區指定 （手動） 備份 Azure 傳統入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>建立自訂的備份原則與多個區排程

若要建立自訂的備份原則有多個區及排程 Azure 傳統入口網站中，執行下列步驟。

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## <a name="next-steps"></a>後續步驟

瞭解更多關於[使用 StorSimple 管理員服務來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
