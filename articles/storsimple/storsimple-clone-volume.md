<properties
   pageTitle="複製您 StorSimple 音量 |Microsoft Azure"
   description="描述不同複製類型，以及何時使用，並說明如何使用備份設定複製個別的音量。"
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

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>若要複製區中使用 StorSimple 管理員服務

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>概觀

[StorSimple 管理員服務**備份類別目錄**] 頁面會顯示所有備份集進行手動或自動備份時所建立的。 您可以使用此頁面] 清單中的備份的原則] 或 [音量]，選取的所有備份或刪除備份，或使用備份還原或複製區。

![備份型錄頁面](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

本教學課程說明如何使用備份設定複製個別的音量。 同時也會說明*暫時性*和*永久*複製之間的差異。 

## <a name="create-a-clone-of-a-volume"></a>建立大量的複本

使用本機或雲端快照，您可以在同一個裝置，另一個裝置或甚至虛擬機器上建立複本。

#### <a name="to-clone-a-volume"></a>若要複製區

1. 在 [StorSimple 管理員服務] 頁面中，按一下 [**備份目錄**] 索引標籤並選取備份的設定。

2. 展開備份設定檢視的相關聯的區。 按一下，然後從備份設定選取區。

     ![複製區](./media/storsimple-clone-volume/HCS_Clone.png) 

3. 按一下 [開始複製選取的大量**複製**]。

4. 在 [複製區精靈] 底下**指定名稱與位置**︰

  1. 識別目標裝置。 這是會建立複本的位置。 您可以選擇同一個裝置，或指定另一個裝置。 如果您選擇其他雲端服務提供者相關聯的大量 (不 Azure) 下拉式清單中的目標裝置只會顯示實際的裝置。 您無法複製區虛擬裝置上的其他雲端服務提供者相關聯。

        >  [AZURE.NOTE] 請確定所需複製容量低於目標裝置上的可用的容量。
  2. 指定您複製的唯一的區名稱。 名稱必須包含 3 與 127 字元之間。
  3. 按一下箭號圖示 ![箭號圖示](./media/storsimple-clone-volume/HCS_ArrowIcon.png) 若要繼續進行下一個頁面。

5. 下**，可以使用此大量的指定主機**︰

  1. 指定複製存取控制記錄 (ACR)。 您可以新增新的 ACR，或從現有清單中選擇。
  2. 按一下 [檢查] 圖示 ![檢查圖示](./media/storsimple-clone-volume/HCS_CheckIcon.png)若要完成作業。

6. 會啟動複製工作，並會通知您已成功建立複製時。 按一下 [監視複製工作，在 [**工作**] 頁面上的**檢視工作**。

7. 複製後完成工作︰

  1. 移至 [**裝置**] 頁面，然後選取 [**大量容器**] 索引標籤。 
  2. 選取您複製來源大量相關聯的大量容器。 在清單中的區，您應該會看到已建立的複製。

>[AZURE.NOTE] 監控和預設備份複製區會自動停用。

複製的建立方式是暫時性的複本。 如需複製類型的詳細資訊，請參閱[暫時性永久複製與](#transient-vs.-permanent-clones)文件。

此複製現在一般音量]，而有可能是在區上的任何作業可供複製。 您必須設定此大量的任何備份。

## <a name="transient-vs-permanent-clones"></a>永久的複製與暫時性

只有在您正在複製到另一個裝置時，會建立暫時性和永久複製。 您可以複製從備份檔案至另一個裝置上設定的特定音量。 建立以這種方式複製是*暫時性*的複本。 暫時性複製必須原始大量的參照，並使用該大量閱讀時本機撰寫。 

您取得雲端快照的暫時性複製之後，請複製結果將會*永久*複本。 永久複製原樣，並沒有任何其複製從原始大量的參照。  

## <a name="scenarios-for-transient-and-permanent-clones"></a>暫時和永久複製的案例

下列各節說明在可以使用暫時性和永久性複製範例情況。

### <a name="item-level-recovery-with-a-transient-clone"></a>使用暫時性複製的項目層級修復

您要復原的項目拐杖的十歲 Microsoft PowerPoint 簡報檔案。 您的 IT 系統管理員指定的時間範圍，從特定的備份，然後篩選音量。 管理員然後複製音量，找出您要找的檔案並將其提供給您。 在這個案例中，用於暫時性的複本。 
 
![使用視訊](./media/storsimple-clone-volume/Video_icon.png)**可用的視訊**

若要觀看影片示範如何使用複製及還原 StorSimple 復原已刪除的檔案中的功能，請按一下[這裡](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>永久的複製與生產環境中測試

您需要驗證生產環境中的測試錯誤。 您建立大量的複本，您可以在生產環境中雲端概覽此複製。 複製的區現在獨立。 在這個案例中，用於永久性的複本。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[還原備份從 StorSimple 音量](storsimple-restore-from-backup-set.md)。

- 瞭解如何[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。

 
