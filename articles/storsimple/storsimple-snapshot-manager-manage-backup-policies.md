<properties 
   pageTitle="StorSimple 快照管理員備份原則 |Microsoft Azure"
   description="說明如何使用 StorSimple 快照管理員 MMC 嵌入式管理單元來建立和管理的備份的原則，控制排程的備份。"
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
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>使用 StorSimple 快照管理員 」 建立及管理備份原則

## <a name="overview"></a>概觀

備份原則會建立備份本機或雲端中的大量資料的排程。 當您建立備份的原則時，您也可以指定保留原則。 （您可以保留最多 64 快照集）。如需有關備份原則的詳細資訊，請參閱中的[備份類型](storsimple-what-is-snapshot-manager.md#backup-type) [StorSimple 8000 系列︰ 雲端混合式解決方案](storsimple-overview.md)。

本教學課程說明如何︰

- 建立備份原則 
- 編輯備份的原則 
- 刪除備份原則 

## <a name="create-a-backup-policy"></a>建立備份原則

您可以使用下列程序建立新的備份原則。

#### <a name="to-create-a-backup-policy"></a>若要建立備份原則

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下**備份原則**，然後按一下 [**建立備份原則**。

    ![建立備份原則](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **建立原則**] 對話方塊隨即出現。 

    ![建立原則-一般] 索引標籤](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. 在 [**一般**] 索引標籤上提供下列資訊︰

   1. 在 [**名稱**] 文字方塊中，輸入原則的名稱。

   2. 在 [**音量] 群組**文字] 方塊中，輸入音量群組原則相關聯的名稱。

   3. 選取 [**區域快照**或**雲端快照**。

   4. 選取要保留的快照數。 如果您選取 [**所有**]，將會是 64 快照保留 （最大值）。 

4. 按一下 [**排程**] 索引標籤。

    ![建立原則-排程] 索引標籤](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. 在 [**排程**] 索引標籤上提供下列資訊︰ 

   1. 按一下 [**啟用**] 核取方塊來安排和客戶的下一個備份]。

   2. 在 [**設定**] 底下，選取**一次**，**每日**、**每週**或**月**。 

   3. **開始**在文字方塊中，按一下 [行事曆] 圖示，然後選取 [開始日期]。

   4. 在 [**進階設定**] 中，您可以將選用重複的排程和結束日期。

   5. 按一下**[確定]**。

建立備份原則之後，下列資訊會出現在 [**結果**] 窗格中︰

- **名稱**– 備份原則的名稱。

- **輸入**– 區域快照或雲端快照。

- **大量群組**– 大量相關聯的群組原則。

- **保留**– 快照保留; 數最大值為 64。

- **建立**– 此原則所建立的日期。

- **啟用**– 原則是否目前效果︰ **True**表示它是作用中。**False**表示不在作用中。 

## <a name="edit-a-backup-policy"></a>編輯備份的原則

您可以使用下列程序來編輯現有的備份原則。

#### <a name="to-edit-a-backup-policy"></a>若要編輯的備份的原則

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，按一下 [**備份原則**節點。 所有的備份原則會出現在 [**結果**] 窗格中。 

3. 以滑鼠右鍵按一下您想要編輯的原則，然後按一下 [**編輯**。 

    ![編輯備份的原則](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. **建立原則**視窗出現時，輸入您的變更，然後按一下**[確定]**。 

## <a name="delete-a-backup-policy"></a>刪除備份原則

若要刪除備份原則使用下列程序。

#### <a name="to-delete-a-backup-policy"></a>若要刪除備份的原則

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，按一下 [**備份原則**節點。 所有的備份原則會出現在 [**結果**] 窗格中。 

3. 以滑鼠右鍵按一下您想要刪除的備份原則，然後按一下 [**刪除**。

4. 出現確認訊息，請按一下 [**是**]。

    ![刪除備份原則確認](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 瞭解如何[使用 StorSimple 快照管理員檢視及管理備份工作](storsimple-snapshot-manager-manage-backup-jobs.md)。
