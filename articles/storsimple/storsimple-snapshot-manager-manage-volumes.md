<properties 
   pageTitle="StorSimple 快照管理員和區 |Microsoft Azure"
   description="說明如何使用 StorSimple 快照管理員 MMC 嵌入式管理單元中，若要檢視及管理區，並設定備份。"
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

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>若要檢視及管理區使用 StorSimple 快照管理員

## <a name="overview"></a>概觀

您可以使用 StorSimple 快照管理員**區**節點 （在 [**範圍**] 窗格中），選取區，並檢視其相關資訊。 區會顯示為主機裝載磁碟機對應。 本機磁碟區與 StorSimple，包括使發現 iSCSI 和裝置所支援的大量類型，則會顯示**區**節點。 

支援的磁碟區的相關詳細資訊，請移至[多個大量類型的支援](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)。

![在 [結果] 窗格中的 [音量] 清單](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**區**節點也可讓您重新掃描或刪除區之後 StorSimple 快照管理員找到它們。 

本教學課程說明如何裝載、 初始化，並格式化區，然後使用 StorSimple 快照管理員︰

- 查看區的相關資訊 
- 刪除區
- 掃描區 
- 設定基本的音量，並將其備份
- 設定動態的左右對稱的音量，並將其備份

>[AZURE.NOTE] 所有的**大量**節點動作也可在 [**動作**] 窗格。
 
## <a name="mount-volumes"></a>裝載區

您可以使用下列程序來裝載、 初始化以及格式化 StorSimple 區。 此程序使用磁碟管理]，[系統管理公用程式硬碟、 對應區或磁碟分割區。 如需磁碟管理的詳細資訊，請移至[磁碟管理](https://technet.microsoft.com/library/cc770943.aspx)Microsoft TechNet 網站上。

#### <a name="to-mount-volumes"></a>裝載區

1. Host （主機） 在電腦上，啟動 Microsoft iSCSI 啟動器。

2. 提供的其中一個介面為目標入口網站的 IP 位址或探索 IP 位址，並連接至裝置。 裝置連接之後，區就能存取您的 Windows 系統。 如需有關如何使用 Microsoft iSCSI 啟動器的詳細資訊，請移至區段] 連線到 iSCSI 目標裝置 」 中[安裝及設定 Microsoft iSCSI 啟動器][1]。

3. 您可以使用以下任何選項來啟動磁碟管理︰

    - 在 [**執行**] 方塊中輸入 Diskmgmt.msc。

    - 啟動 [伺服器管理員，展開 [**儲存**] 節點，然後選取**磁碟管理**。

    - 啟動 [**系統管理工具**，展開 [**電腦管理**節點，，然後選取**磁碟管理**。 

    >[AZURE.NOTE] 您必須使用系統管理員權限來執行磁碟管理。
 
4. 線上採取區︰

   1. 磁碟管理] 中以滑鼠右鍵按一下任何大量標示為**離線**。

   2. 按一下 [**重新啟動磁碟**]。 磁碟應該標示為 [**線上**後重新啟動磁碟。

5. 初始化區︰

   1. 以滑鼠右鍵按一下發現的區。

   2. 在功能表中，選取 [**初始化磁碟**。

   3. 在 [**初始化磁碟**] 對話方塊中，選取您想要初始化的磁碟，然後按一下**[確定]**。

6. 設定格式的簡單磁碟區︰

   1. 以滑鼠右鍵按一下您想要格式化大量。

   2. 在功能表中，選取**新的簡單音量**。

   3. 若要格式化大量使用新的簡單大量精靈︰

      - 指定大量的大小。
      - 提供磁碟機字母。
      - 選取 [NTFS 檔案系統]。
      - 指定 64 KB 配置單位大小。
      - 執行快速格式化。

7. 設定多重磁碟分割區的格式。 如需相關指示，移至區段中，「 磁碟分割區及區 」[實作磁碟](https://msdn.microsoft.com/library/dd163556.aspx)管理。

## <a name="view-information-about-your-volumes"></a>查看區的相關資訊

您可以使用下列程序來檢視本機和 Azure StorSimple 區的相關資訊。

#### <a name="to-view-volume-information"></a>若要檢視大量資訊

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，按一下 [**區**節點。 本機和裝載區，包括所有的 Azure StorSimple 區清單會出現在 [**結果**] 窗格中。 在 [**結果**] 窗格中的資料行進行設定。 （以滑鼠右鍵按一下**區**節點，選取 [**檢視**]，然後選取**新增或移除資料行**。）

    ![設定資料行](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    結果資料行 | 描述 
    :--------------|:-------------
    名稱           | [**名稱**] 欄位包含分派給每個發現大量的磁碟機字母。
    裝置         | [**裝置**] 欄位包含裝置連接至主機電腦的 IP 位址。
    裝置音量名稱 | **裝置音量名稱**] 欄中包含裝置音量選取的音量所屬的名稱。 這是在 Azure 傳統入口網站中的特定區所定義的區名稱。
    存取路徑   | **存取路徑**欄位會顯示 access 路徑音量。 這是用以音量存取主機電腦上的磁碟機代號或連接點。
 
## <a name="delete-a-volume"></a>刪除區

若要刪除大量從 StorSimple 快照管理員使用下列程序。

>[AZURE.NOTE] 如果是任何區群組中的一部分，您無法刪除音量。 （[刪除] 選項不適用於大量群組的成員區。）您必須先刪除整個大量群組，若要刪除音量。


#### <a name="to-delete-a-volume"></a>若要刪除的音量

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，按一下 [**區**節點。 

3. 在 [**結果**] 窗格中，以滑鼠右鍵按一下您想要刪除的音量。

4. 在功能表中，按一下 [**刪除**。 

    ![刪除區](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. [**刪除的音量**] 對話方塊隨即出現。 **確認**方塊中輸入文字，然後再按一下**[確定]**。

6. 根據預設，StorSimple 快照管理員備份大量之前先將它刪除。 此預防措施可防止資料遺失如果不慎刪除動作。 StorSimple 快照管理員顯示**自動快照**進度訊息，並將其備份音量。 

    ![自動快照訊息](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>掃描區

若要重新連線到 StorSimple 快照管理員區掃描使用下列程序。

#### <a name="to-rescan-the-volumes"></a>若要重新掃描區

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，**區**，以滑鼠右鍵按一下，然後按一下**掃描區**。

    ![掃描區](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    此程序同步處理大量清單與 StorSimple 快照管理員。 新的區或已刪除的區，例如的任何變更都會反映在結果。

## <a name="configure-and-back-up-a-basic-volume"></a>設定和備份基本的音量

您可以使用下列程序來設定備份基本音量]，然後立即開始備份或建立的原則，排定的備份。

### <a name="prerequisites"></a>必要條件

之前︰

- 請確定已正確設定 StorSimple 裝置和主機電腦。 如需詳細資訊，請前往[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough-u2.md)。

- 安裝和設定 StorSimple 快照管理員。 如需詳細資訊，請前往[部署 StorSimple 快照管理員](storsimple-snapshot-manager-deployment.md)。

#### <a name="to-configure-backup-of-a-basic-volume"></a>若要設定的基本的大量的備份

1. 建立基本的大量 StorSimple 裝置上。

2. 裝載、 初始化，及格式化大量[裝載區](#mount-volumes)所述。 

3. 按一下在桌面上的 StorSimple 快照管理員圖示。 [StorSimple 快照集管理員] 視窗隨即出現。 

4. 在 [**範圍**] 窗格中，[**區**] 節點，以滑鼠右鍵按一下，然後選取**掃描區**。 掃描完成後，使清單應該會出現在 [**結果**] 窗格中。 

5. 在 [**結果**] 窗格中，以滑鼠右鍵按一下 [音量]，並選取**建立大量群組**。 

    ![建立區群組](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. 在 [**建立大量群組**] 對話方塊中，輸入 [音量] 群組的名稱，指派給它，使，然後按一下**[確定]**。

7. 在 [**範圍**] 窗格中，展開**大量群組**節點。 新的 [音量] 群組應該會出現 [**大量群組**節點下。 

8. 以滑鼠右鍵按一下音量群組名稱。

    - 若要開始互動式 （視需要） 備份工作，按一下 [**備份記錄**]。 

    - 若要安排的自動備份，按一下 [**建立備份原則**]。 在 [**一般**] 頁面上，從清單中選取 [音量] 群組。 在 [**排程**] 頁面中，輸入排程詳細資料。 當您完成時，按一下**[確定]**。 

9. 若要確認備份工作已開始，展開**工作**窗格中的節點**範圍**，，然後按一下 [**執行**] 節點。 在 [**結果**] 窗格中，會出現目前執行的工作清單。 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>設定和備份動態的左右對稱音量

完成下列步驟，以設定備份的動態的左右對稱音量︰

- 步驟 1︰ 使用磁碟管理] 以建立動態的左右對稱的音量。 

- 步驟 2︰ 使用 StorSimple 快照管理員設定的備份。

### <a name="prerequisites"></a>必要條件

之前︰

- 請確定已正確設定 StorSimple 裝置和主機電腦。 如需詳細資訊，請前往[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough-u2.md)。

- 安裝和設定 StorSimple 快照管理員。 如需詳細資訊，請前往[部署 StorSimple 快照管理員](storsimple-snapshot-manager-deployment.md)。

- 設定兩個磁碟區 StorSimple 裝置上。 （在範例中，使用區是**磁碟 1**和**2，磁碟**）。 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>步驟 1︰ 使用磁碟管理] 以建立動態的左右對稱的音量

系統管理公用程式硬碟、 區或其包含的磁碟分割磁碟管理。 如需磁碟管理的詳細資訊，請移至[磁碟管理](https://technet.microsoft.com/library/cc770943.aspx)Microsoft TechNet 網站上。

#### <a name="to-create-a-dynamic-mirrored-volume"></a>若要建立動態的左右對稱的音量

1. 您可以使用以下任何選項來啟動磁碟管理︰ 

   - 開啟 [**執行**] 方塊中，輸入**Diskmgmt.msc**，然後按 enter 鍵。

   - 啟動 [伺服器管理員，展開 [**儲存**] 節點，然後選取**磁碟管理**。 

   - 啟動 [**系統管理工具**，展開 [**電腦管理**節點，，然後選取**磁碟管理**。 

2. 請確定您已使用的兩個區 StorSimple 裝置上。 （在此範例中，使用區是**磁碟 1**和**2，磁碟**）。 

3. 磁碟管理] 視窗的左下窗格中，右欄中以滑鼠右鍵按一下**磁碟 1** ，並選取**新的鏡像音量**。 

    ![新的左右對稱的音量](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. 在 [**新產生鏡像大量**精靈] 頁面中，按一下 [**下一步**]。

5. 在 [**選取磁碟**] 頁面選取**磁碟 2**在**選取**窗格] 中按一下 [**新增**]，然後按 [**下一步**。 

6. **指定磁碟機代號或路徑**在頁面上，接受預設值]，然後再按 [**下一步**。 

7. **格式大量**在頁面上，在 [**資源分派單位大小**] 方塊中，選取**64k**。 選取 [**執行快速格式化**核取方塊，然後再按 [**下一步**。 

8. 在**完成新產生鏡像音量**頁面上，檢閱您的設定，然後按一下 [**完成]**。 

9. 出現訊息，指出基本磁碟，都會轉換成動態磁碟。 按一下**[是]**。

    ![動態磁碟轉換訊息](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. 在 [磁碟管理]，確認磁碟 1 和 2，磁碟會顯示為動態左右對稱的區。 （**動態**應該會出現在 [狀態] 欄中，然後容量長條色彩應該變更為紅色，指出左右對稱的大量）。 

    ![磁碟管理產生鏡像動態磁碟](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>步驟 2︰ 使用 StorSimple 快照管理員來設定備份

您可以使用下列程序來設定動態左右對稱的音量]，然後立即開始備份或建立的原則，排定的備份。

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>若要設定的動態的左右對稱大量的備份

1. 按一下在桌面上的 StorSimple 快照管理員圖示。 [StorSimple 快照集管理員] 視窗隨即出現。 

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下**區**節點並選取**掃描區**。 掃描完成後，使清單應該會出現在 [**結果**] 窗格中。 動態左右對稱的音量會列為單一的音量。 

3. 在 [**結果**] 窗格中，動態左右對稱的音量]，以滑鼠右鍵按一下，然後按一下**建立大量群組**。 

4. 在**建立大量群組**] 對話方塊中，輸入 [音量] 群組的名稱，將動態左右對稱的音量指派給此群組中，，然後按一下**[確定]**。 

5. 在 [**範圍**] 窗格中，展開**大量群組**節點。 新的 [音量] 群組應該會出現 [**大量群組**節點下。 

6. 以滑鼠右鍵按一下音量群組名稱。 

    - 若要開始互動式 （視需要） 備份工作，按一下 [**備份記錄**]。 

    - 若要安排的自動備份，按一下 [**建立備份原則**]。 在 [**一般**] 頁面上，從清單中選取 [音量] 群組。 在 [**排程**] 頁面中，輸入排程詳細資料。 當您完成時，按一下**[確定]**。 

7. 執行時，您可以監視備份工作。 在 [**範圍**] 窗格中，展開 [**工作**] 節點，然後按一下 [**執行**，顯示在 [**結果**] 窗格中的工作詳細資料。 備份工作完成時，就會將詳細資料傳送到**最後一個 24**小時的工作清單。 

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 瞭解如何[使用 StorSimple 快照管理員來建立和管理大量群組](storsimple-snapshot-manager-manage-volume-groups.md)。

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
