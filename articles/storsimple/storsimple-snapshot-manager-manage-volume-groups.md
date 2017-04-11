<properties 
   pageTitle="StorSimple 快照管理員大量群組 |Microsoft Azure"
   description="說明如何使用 StorSimple 快照管理員 MMC 嵌入式管理單元建立及管理大量群組。"
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>使用 StorSimple 快照管理員 」 建立及管理大量群組

## <a name="overview"></a>概觀

您可以使用 [**範圍**] 窗格上的**大量群組**節點大量群組指派區檢視大量群組的相關資訊、 安排備份，並編輯大量群組。 

大量群組是相關的區用於確保備份應用程式一致的資料庫。 如需詳細資訊，請參閱[區和大量群組](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups)和[Windows 區陰影複製服務整合](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)。

>[AZURE.IMPORTANT] 
>
> * [音量] 群組中的所有磁碟區必須來自單一雲端服務提供者。
> 
> * 當您設定大量群組時，請勿混合叢集共用區 (CSVs) 和相同的 [音量] 群組中的非 CSVs。 StorSimple 快照管理員不支援的 CSVs 非 CSVs 中相同的快照。
 
![大量群組節點](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**圖 1: StorSimple 快照管理員大量群組節點** 

本教學課程會說明如何使用 StorSimple 快照管理員︰

- 查看大量群組的相關資訊 
- 建立區群組
- 備份大量群組
- 編輯 [音量] 群組
- 刪除區群組

所有這些動作也可在 [**動作**] 窗格。
 
## <a name="view-volume-groups"></a>檢視區群組

如果您按一下 [**大量群組**節點，[**結果**] 窗格會顯示每個音量] 群組中，根據您的資料行選取範圍的下列資訊。 （在 [**結果**] 窗格中的資料行進行設定。 以滑鼠右鍵按一下**區**節點、 選取**檢視**和 [**新增/移除資料行**。）

結果資料行 | 描述 
:--------------|:------------ 
名稱           | [**名稱**] 欄中包含大量群組的名稱。
應用程式    | [**應用程式**] 欄會顯示 VSS 作者目前已安裝與 Windows 主機上執行的數目。
選取       | **選取**欄數所包含的磁碟區的 [音量] 群組中。 零 (0) 表示沒有任何應用程式與 [音量] 群組中的媒體相關聯。
匯入       | **匯入**資料行的 [匯入的區數。 設為**True**時，此欄會指出大量群組已匯入從 Azure 傳統入口網站，而不建立 StorSimple 快照管理員中的商務連絡人。
 
>[AZURE.NOTE] Azure 傳統入口網站中的**備份原則**] 索引標籤上，也會顯示 StorSimple 快照管理員大量群組。
 
## <a name="create-a-volume-group"></a>建立區群組

若要建立大量群組使用下列程序。

#### <a name="to-create-a-volume-group"></a>若要建立大量群組

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，**大量群組**]，以滑鼠右鍵按一下，然後按一下**建立大量群組**。 

    ![建立區群組](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    [**建立大量的群組]**對話方塊隨即出現。 

    ![建立大量群組] 對話方塊](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  輸入下列資訊︰ 

    1. 在 [**名稱**] 方塊中，輸入新的 [音量] 群組的唯一名稱。 

    2. 在 [**應用程式**] 方塊中，選取您將會新增至 [音量] 群組的區相關聯的應用程式。 

        那些應用程式使用 StorSimple 區，並以 VSS 作者啟用這些**應用程式**] 方塊的清單。 只有當作者並知道的所有磁碟 StorSimple 區都就啟用 VSS 寫入。 [應用程式] 方塊是空值，如果沒有使用 Azure StorSimple 區，並有支援 VSS 作者的應用程式會安裝。 （目前 Azure StorSimple 支援 Microsoft Exchange 和 SQL Server。）如需有關 VSS 作者的詳細資訊，請參閱[Windows 區陰影複製服務整合](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)。

        如果您選取應用程式時，會自動選取所有與其相關聯的區。 相反地，如果您選取特定的應用程式相關聯的區，應用程式中會自動選取 [**應用程式**] 方塊。 

    3. 在 [**區**] 方塊中，選取 StorSimple 區新增到區群組。 

      - 您可以包含單一或多個磁碟分割區。 （多個磁碟分割區可以是動態磁碟或基本的磁碟多磁碟分割）。包含多個磁碟分割區被視為一個單位。 因此，如果您可以新增其中一個資料分割區群組，所有其他的磁碟分割會自動新增至該大量群組一次。 大量群組新增多個的磁碟分割區之後，多個的磁碟分割區繼續被視為一個單位。

      - 您可以建立空白的大量群組不指定任何使它們。 

      - 請勿混合叢集共用區 (CSVs) 和相同的 [音量] 群組中的非 CSVs。 StorSimple 快照管理員不支援的 CSV 磁碟非 CSV 區和中相同的快照。 

4. 按一下**[確定**] 儲存區群組。

## <a name="back-up-a-volume-group"></a>備份大量群組

您可以使用下列程序來備份大量群組。

#### <a name="to-back-up-a-volume-group"></a>若要備份的大量群組

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，展開**大量群組**節點大量群組名稱，以滑鼠右鍵按一下，再按一下**記錄備份**。 

    ![立即備份大量群組](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. 在**採取備份**] 對話方塊中，選取**區域快照**或**雲端快照**，，然後按一下 [**建立**]。 

    ![需要備份] 對話方塊](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. 若要確認正在執行備份，展開 [**工作**] 節點，然後按一下**執行**。 備份應該會列出。

5. 若要檢視已完成的快照，展開**備份目錄**節點展開大量群組名稱，再按一下**區域快照**或**雲端快照**。 順利完成時，便會列出備份。 

## <a name="edit-a-volume-group"></a>編輯 [音量] 群組

您可以使用下列程序來編輯大量群組。

#### <a name="to-edit-a-volume-group"></a>若要編輯 [音量] 群組

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，展開 [**大量群組**節點、 大量群組名稱，以滑鼠右鍵按一下，然後按一下**編輯**。 

3. [**建立大量的群組]**對話方塊隨即出現。 您可以變更的**名稱**與**應用程式**，**使**項目。 

4. 按一下**[確定]**儲存變更]。

## <a name="delete-a-volume-group"></a>刪除區群組

若要刪除大量群組使用下列程序。 

>[AZURE.WARNING] 也會一併刪除大量群組相關聯的所有備份。

#### <a name="to-delete-a-volume-group"></a>若要刪除大量群組

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，展開 [**大量群組**節點、 大量群組名稱，以滑鼠右鍵按一下，然後按一下**刪除**。 

3. **刪除大量群組**] 對話方塊隨即出現。 **確認**方塊中輸入文字，然後再按一下**[確定]**。 

    [已刪除的音量] 群組中消失，從 [**結果**] 窗格中的清單，並從備份目錄刪除所有與該大量群組相關聯的備份。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 瞭解如何[使用 StorSimple 快照管理員來建立和管理備份原則](storsimple-snapshot-manager-manage-backup-policies.md)。
