<properties 
   pageTitle="StorSimple 快照管理員 MMC 動作的功能表 |Microsoft Azure"
   description="說明如何使用標準 Microsoft 管理主控台 (MMC) 動作的功能表 StorSimple 快照管理員中的商務連絡人。"
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
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>在 [StorSimple 快照管理員使用 MMC 動作的功能表

## <a name="overview"></a>概觀

在 StorSimple 快照管理員中，您會看到下列動作列上的所有動作功能表和**動作**] 窗格中的所有變化。 

- 檢視
- 從這裡開始新的視窗 
- 重新整理 
- 匯出清單 
- 說明 

這些動作組件的 Microsoft 管理主控台 (MMC) 而不是特定到 StorSimple 快照管理員。 本教學課程說明這些動作，並說明如何使用每個 StorSimple 快照管理員中的商務連絡人。

## <a name="view"></a>檢視

若要變更 [**結果**] 窗格中檢視及變更主控台視窗檢視，您可以使用 [**檢視**] 選項。 

#### <a name="to-change-the-results-pane-view"></a>若要變更 [結果] 窗格中檢視

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下 [**檢視**] 選項。 

3. 若要新增或移除出現的資料行，在 [**結果**] 窗格中，按一下 [**新增/移除資料行**。 **新增或移除資料行**] 對話方塊隨即出現。

    ![新增或移除結果] 窗格中的資料行](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 

4. 請完成表單，如下所示︰

    - 從 [**可用**欄] 清單中選取的項目，然後按一下 [**新增**]，以將其新增至 [**顯示欄位**] 清單。 

    - 按一下 [**顯示欄位**] 清單中的項目，然後按一下 [**移除**]，以從清單中移除。 

    - 在**顯示**的 [欄] 清單中選取一個項目，然後按一下**[上移**或**下移**在清單中向上或向下移動項目]。 

    - 按一下 [**還原預設值]**會回到預設的**結果**窗格設定]。 

5. 當您完成時的選擇，請按一下**[確定]**。 

#### <a name="to-change-the-console-window-view"></a>若要變更主控台視窗檢視

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，按一下 [**檢視**]，然後按一下**自訂**。 [**自訂**] 對話方塊隨即出現。

    ![自訂主控台視窗](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 

3. 選取或清除核取方塊以顯示或隱藏 [主控台] 視窗中的項目。 當您完成時的選擇，請按一下**[確定]**。

## <a name="new-window-from-here"></a>從這裡開始新的視窗

若要開啟新的主控台視窗，您可以使用 [**從這裡開始新視窗**] 選項。

#### <a name="to-open-a-new-console-window"></a>若要開啟新的主控台視窗

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，然後按一下**新視窗從這裡開始**。 

    在新視窗隨即出現，並顯示您選取 [只範圍。 例如，如果您以滑鼠右鍵按一下**備份原則**節點，新視窗會顯示只有**備份原則**節點**範圍**] 窗格和 [**結果**] 窗格中的已定義之備份原則的清單中。 請參閱下列範例。

    ![從這裡開始新的視窗](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 
 
## <a name="refresh"></a>重新整理

若要更新主控台視窗，您可以使用 [**重新整理**] 動作。

#### <a name="to-update-the-console-window"></a>若要更新主控台視窗

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點或展開節點和以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下 [**重新整理**。 

## <a name="export-list"></a>匯出清單

您可以使用 [**匯出清單**指令儲存逗點分隔值 (CSV) 檔案中的清單。 例如，您可以匯出備份原則] 或 [類別目錄的清單。 您可以匯入的試算表應用程式，以分析 CSV 檔案。

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>若要儲存逗點分隔值 (CSV) 檔案中的清單

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下**匯出清單**。 

3. **匯出清單**] 對話方塊隨即出現。 請完成表單，如下所示︰ 

    1. 在 [**檔案名稱**] 方塊中輸入 CSV 檔案的名稱，或按一下箭號，從下拉式清單中選取。

    2. 在 [**存檔類型**] 方塊中，按一下箭號，然後從下拉式清單中選取的檔案類型。

    3. 若要將選取的項目，請選取的列，，然後按一下 [**儲存僅選取的資料列**] 核取方塊。 若要儲存匯出所有的清單，請清除**儲存僅選取的資料列**] 核取方塊。

    4. 按一下 [**儲存**]。

    ![將清單匯出為逗點分隔值檔案](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 
 
## <a name="help"></a>說明

若要檢視可用的線上說明 StorSimple 快照管理員和 MMC，您可以使用**[說明**] 功能表。

#### <a name="to-view-available-online-help"></a>若要檢視可用的線上說明

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下**協助**。 

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[StorSimple 快照管理員使用者介面](storsimple-use-snapshot-manager.md)。
- 進一步瞭解[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
