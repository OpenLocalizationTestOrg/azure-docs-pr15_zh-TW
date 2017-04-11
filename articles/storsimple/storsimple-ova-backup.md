<properties 
   pageTitle="StorSimple 虛擬陣列備份教學課程 |Microsoft Azure"
   description="說明如何將備份 StorSimple 虛擬陣列與區。"
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
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>備份您的 StorSimple 虛擬陣列

## <a name="overview"></a>概觀 

本教學課程適用於 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置） 執行 2016 年 3 月上市 (GA) 版本或更新版本。

StorSimple 虛擬陣列是混合式雲端儲存空間內部部署虛擬裝置，您可以設定為在檔案伺服器或 iSCSI 伺服器。 可建立備份、 還原的備份，並執行裝置容錯移轉，如果需要損毀修復。 為檔案伺服器設定，您也可以復原項目層級。 本教學課程說明如何使用 Azure 傳統入口網站或 StorSimple 網頁 UI 來建立您 StorSimple 虛擬陣列的排程與手動備份。


## <a name="back-up-shares-and-volumes"></a>備份與區

備份會提供時間點保護、 改善修復能力，及 [最小化的共用及區還原時間。 您可以共用或備份大量 StorSimple 裝置兩種方法︰**排程**或**手動**。 下列各節討論的每一個方法。

> [AZURE.NOTE] 在這個版本中，依預設的原則執行每日在指定的時間，並在裝置上備份所有共用或磁碟區會建立排定的備份。 無法建立自訂的原則排定的備份這一次。

## <a name="change-the-backup-schedule"></a>變更備份排程

StorSimple 虛擬裝置有預設的備份原則，開始一天 (22:30) 指定的時間，並會備份所有共用或磁碟區在裝置上一天。 您可以變更哪些備份啟動，但是頻率和保留 （其指定備份要保留的數量） 無法變更的時間。 在這些備份，整個虛擬裝置備份。因此，建議您排定峰這些備份。

若要變更預設的備份工作的開始時間[Azure 傳統入口網站](https://manage.windowsazure.com/)中，執行下列步驟。

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>若要變更預設的備份原則的開始時間

1. 瀏覽至 [裝置**設定**] 索引標籤。

2. 在 [**備份**] 區段中，指定每日備份的開始時間。

3. 按一下 [**儲存**]。

### <a name="take-a-manual-backup"></a>需要手動備份

除了排定的備份，您可能需要在任何時間的備份手動 （視需要）。

#### <a name="to-create-a-manual-on-demand-backup"></a>若要建立手動 （視需要） 備份

1. 瀏覽至 [**共用**] 索引標籤或 [**區**] 索引標籤。

2. 在頁面底部，按一下 [**備份所有**]。 系統會提示您以驗證您想要立即備份。 按一下核取圖示![核取圖示](./media/storsimple-ova-backup/image3.png)繼續進行備份。

    ![備份確認](./media/storsimple-ova-backup/image4.png)

    備份工作已開始您會收到通知。

    ![啟動的備份](./media/storsimple-ova-backup/image5.png)

    您將會收到工作已順利建立。

    ![建立備份工作](./media/storsimple-ova-backup/image7.png)

3. 若要追蹤的工作進度，請按一下 [**檢視工作**]。

4. 備份工作完成之後，請前往 [**備份目錄**] 索引標籤。 您應該會看到您已完成的備份。

    ![已完成的備份](./media/storsimple-ova-backup/image8.png)

5. 將篩選的選取範圍至適當的裝置、 備份原則，以及時間範圍，然後按一下 [檢查圖示 ![檢查圖示](./media/storsimple-ova-backup/image3.png).

    備份應該會出現在目錄中顯示的備份集清單中。

## <a name="view-existing-backups"></a>檢視現有的備份

若要檢視現有的備份 Azure 傳統入口網站中，執行下列步驟。

#### <a name="to-view-existing-backups"></a>若要檢視現有的備份

1. 在 StorSimple 管理員服務頁面上，按一下 [**備份目錄**] 索引標籤。

2. 選取備份設定，如下所示︰

    1. 選取的裝置。

    2. 在下拉式清單中，選擇 [共用] 或 [大量您想要選取備份]。

    3. 指定的時間範圍。

    4. 按一下核取圖示![](./media/storsimple-ova-backup/image3.png)執行此查詢。

    大量應該會出現在清單或備份聯所選取的共用。

![video_icon](./media/storsimple-ova-backup/video_icon.png) **可用的視訊**

觀看影片，請參閱如何建立共用、 備份共用與還原 StorSimple 虛擬陣列上的資料。

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>後續步驟

深入瞭解[管理 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
