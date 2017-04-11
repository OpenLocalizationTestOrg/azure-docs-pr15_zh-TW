<properties 
   pageTitle="部署 StorSimple 快照管理員 |Microsoft Azure"
   description="瞭解如何下載並安裝 StorSimple 快照的管理員，MMC 嵌入式管理單元管理 StorSimple 資料保護與備份功能。"
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>部署 StorSimple 快照管理員 MMC

## <a name="overview"></a>概觀

StorSimple 快照管理員是 Microsoft 管理主控台 (MMC) 嵌入式管理單元，可簡化資料保護與 Microsoft Azure StorSimple 環境中的備份管理。 使用 StorSimple 快照管理員中，您可以管理 Microsoft Azure StorSimple 內部部署和雲端儲存空間，就好像完全整合的存放系統，因此簡化備份與還原程序，並降低成本。 

本教學課程說明設定需求，以及您在安裝、 移除及升級 StorSimple 快照管理員的程序。

>[AZURE.NOTE] 
>
>- 您無法使用 StorSimple 快照管理員來管理 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署的虛擬裝置）。
>
>- 如果您打算 StorSimple 裝置上安裝 StorSimple 更新 2，請務必下載 StorSimple 快照管理員的最新版本，並將其安裝，**才能安裝 StorSimple 更新 2**。 為回溯相容 StorSimple 快照管理員的最新版本，以及搭配 Microsoft Azure StorSimple 所有發行版本。 如果您使用的舊版的 StorSimple 快照管理員，您必須更新 （您不需要解除安裝舊版本，才能安裝新版本）。

## <a name="storsimple-snapshot-manager-installation"></a>StorSimple 快照管理員安裝

StorSimple 快照管理員可以執行的 Windows Server 2008 R2 SP1、 Windows Server 2012 或 Windows Server 2012 R2 作業系統的電腦上安裝。 在伺服器上執行 Windows 2008 R2，您也必須安裝 Windows Server 2008 SP1 和 Windows 管理 Framework 3.0。 

安裝或升級 StorSimple 快照管理員嵌入式管理單元 Microsoft 管理主控台 (MMC) 之前，請確定已正確設定 Microsoft Azure StorSimple 裝置和主機伺服器。 

## <a name="configure-prerequisites"></a>設定的先決條件

下列步驟可提供，您必須先完成您安裝 StorSimple 快照管理員設定工作的整體概觀。 完成的 Microsoft Azure StorSimple 設定] 及 [安裝資訊，包括系統需求與逐步指示，請參閱[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。

>[AZURE.IMPORTANT]在開始之前，請檢閱[部署設定檢查清單](storsimple-deployment-walkthrough.md#deployment-configuration-checklist)並[部署先決條件](storsimple-deployment-walkthrough.md#deployment-prerequisites)在[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>安裝 StorSimple 快照管理員之前

1. 解壓縮、 裝載，並連接的 Microsoft Azure StorSimple 裝置[您 StorSimple 8100 裝置上安裝](storsimple-8100-hardware-installation.md)或[安裝 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md)中所述。

2. 請確定電腦主機正在執行以下作業系統其中一項︰

    - Windows Server 2008 R2 （執行 Windows 2008 R2 的伺服器上的其他資訊，您也必須安裝 Windows Server 2008 SP1 和 Windows 管理 Framework 3.0）
    - Windows Server 2012
    - Windows Server 2012 R2
 
    為 StorSimple 虛擬裝置，主機必須是 Microsoft Azure 虛擬機器。 

3. 請確定您符合所有 Microsoft Azure StorSimple 設定需求。 如需詳細資訊，請前往[部署的先決條件](storsimple-deployment-walkthrough.md#deployment-prerequisites)。

4. 將裝置連接到主應用程式，並執行初始設定。 如需詳細資訊，請前往[部署步驟](storsimple-deployment-walkthrough.md#deployment-steps)。

5. 在裝置上建立區、 將他們指派給主應用程式，並確認主機可以連接，並使用它們。 StorSimple 快照管理員支援下列類型的區︰ 

    - 基本區
    - 簡單的區
    - 動態區
    - 左右對稱動態區 (RAID 1)
    - 叢集共用區
 
    StorSimple 裝置或 StorSimple 虛擬裝置上建立區的相關資訊，請移至[步驟 6︰ 建立大量](storsimple-deployment-walkthrough.md#step-6-create-a-volume)，在[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。

## <a name="install-a-new-storsimple-snapshot-manager"></a>安裝新的 StorSimple 快照管理員

在安裝之前 StorSimple 快照管理員，請確認區您建立 StorSimple 裝置或 StorSimple 虛擬裝置裝載、 初始化，及格式[設定的先決條件](#configure-prerequisites)所述。

>[AZURE.IMPORTANT]
>
>- 為 StorSimple 虛擬裝置，主機必須是 Microsoft Azure 虛擬機器。 
>
>- Windows 2008 R2、 Windows Server 2012 或 Windows Server 2012 R2，則必須執行主機。 如果您的伺服器正在執行 Windows Server 2008 R2，您也必須安裝 Windows Server 2008 SP1 和 Windows 管理 Framework 3.0。
>
>- 您可以連接裝置到 StorSimple 快照管理員之前，您必須設定 StorSimple 裝置主機 iSCSI 連線。

請遵循下列步驟以完成全新安裝的 StorSimple 快照管理員。 如果您要安裝升級，請移至[升級或重新安裝 StorSimple 快照管理員](#upgrade-or-reinstall-storsimple-snapshot-manager)。

- 步驟 1︰ 安裝 StorSimple 快照管理員 
- 步驟 2︰ 連線至裝置的 StorSimple 快照管理員 
- 步驟 3︰ 確認裝置連線 

###<a name="step-1-install-storsimple-snapshot-manager"></a>步驟 1︰ 安裝 StorSimple 快照管理員

您可以使用下列步驟來安裝 StorSimple 快照管理員。

#### <a name="to-install-storsimple-snapshot-manager"></a>若要安裝 StorSimple 快照管理員

1. 下載 StorSimple 快照管理員軟體 （前往 Microsoft 下載中心[StorSimple 快照管理員](https://www.microsoft.com/download/details.aspx?id=44220)） 並將其儲存至本機主機上。

2. 在 [檔案總管壓縮的資料夾，以滑鼠右鍵按一下，然後按一下**擷取所有**。

3. 在 [**擷取壓縮 (Zipped) 的資料夾**] 視窗中，在 [**選取目的及擷取檔案**] 方塊中輸入或瀏覽至您想要擷取檔案的路徑。 

       >[AZURE.IMPORTANT] 您必須安裝 StorSimple 快照管理員 c 磁碟機上。
 
4. 選取**顯示解壓縮的檔案，完成時**] 核取方塊，然後按一下 [**解壓縮**]。

    ![擷取檔案] 對話方塊](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. 擷取完成時，會開啟目的地資料夾。 按兩下所顯示的目的地資料夾中的應用程式設定圖示。

5. **設定成功**訊息出現時，請按一下 [**關閉**]。 在桌面上，您應該會看到 [StorSimple 快照管理員] 圖示。

    ![桌面圖示](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>步驟 2︰ 連線至裝置的 StorSimple 快照管理員

您可以使用下列步驟，StorSimple 裝置連線 StorSimple 快照管理員。

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>連線至裝置的 StorSimple 快照管理員

1. 按一下在桌面上的 StorSimple 快照管理員圖示。 [StorSimple 快照集管理員] 視窗隨即出現。 在視窗包含**範圍**窗格與 [**結果**] 窗格中，[**動作**] 窗格。 

    ![StorSimple 快照管理員使用者介面](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - [**範圍**] 窗格 （左窗格） 包含組織樹狀結構中的節點的清單。 您可以展開部分節點，選取 [檢視] 或 [相關的節點的特定資料。 按一下箭號圖示，即可展開或摺疊節點。 以滑鼠右鍵按一下以查看可用的動作項目清單的 [**範圍**] 窗格中的項目。 

    - **結果**窗格 （中間窗格） 包含節點、 檢視或您在 [**範圍**] 窗格中選取的資料的詳細資訊。

    - [**動作**] 窗格會列出節點、 檢視]，或您在 [**範圍**] 窗格中選取的資料，您可以執行的作業。

    如 StorSimple 快照管理員使用者介面的完整描述，請參閱[StorSimple 快照管理員使用者介面](storsimple-use-snapshot-manager.md)。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下 [**裝置**] 節點，然後按一下**設定裝置**。 **在裝置設定**] 對話方塊隨即出現。

    ![設定裝置](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. 在 [**裝置**] 清單方塊中，選取的 Microsoft Azure StorSimple 裝置或虛擬裝置的 IP 位址。 在 [**密碼**] 方塊中，輸入您為裝置 Azure 傳統入口網站中建立 StorSimple 快照管理員密碼。 按一下**[確定]**。

4. StorSimple 快照管理員會搜尋您識別出的裝置。 如果使用的裝置，StorSimple 快照管理員新增連線。 您可以 [[確認裝置連線](#to-verify-the-connection)] 以確認已成功新增連線。

    如果裝置無法基於任何原因，StorSimple 快照管理員會傳回錯誤訊息。 按一下**[確定]**以關閉錯誤訊息，然後再按一下 [**取消**] 關閉**裝置設定**] 對話方塊。

5. 連接到裝置之後，StorSimple 快照管理員匯入設定的裝置，每個大量群組，提供大量群組相關聯的備份。 沒有相關聯的備份的大量群組不會匯入。 此外，不會匯入備份大量群組所建立的原則。 若要查看匯入的群組，請以滑鼠右鍵按一下 [**範圍**] 窗格中，最上層的**大量群組**節點，然後按一下 [**切換匯入的群組**。

### <a name="step-3-verify-the-connection-to-the-device"></a>步驟 3︰ 確認裝置連線

您可以使用下列步驟，驗證 StorSimple 快照管理員已連線至 StorSimple 裝置。

#### <a name="to-verify-the-connection"></a>若要確認連線

1. 在 [**範圍**] 窗格中，按一下 [**裝置**] 節點。

    ![StorSimple 快照管理員裝置狀態](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. 核取 [**結果**] 窗格中︰ 

   - 綠色的指標出現在 [裝置] 圖示**可使用**出現在 [**狀態**] 欄中，如果裝置被連接。 

   - 如果紅色指示器會出現在 [裝置] 圖示，無法使用會出現在 [**狀態**] 欄，然後使用的裝置未連線。 

   - 如果**更新**出現在 [**狀態**] 欄中，然後 StorSimple 快照管理員要擷取大量群組和連線裝置的相關聯的備份。

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>升級或重新安裝 StorSimple 快照管理員

您必須解除安裝 StorSimple 快照管理員完全升級或重新安裝軟體。 

再重新安裝 StorSimple 快照管理員，備份主機電腦上現有的 StorSimple 快照 Manager 資料庫。 這會儲存備份的原則和設定資訊，以便從備份還原這項資料。

如果您要升級或重新安裝 StorSimple 快照管理員，請遵循下列步驟︰

- 步驟 1︰ 解除安裝 StorSimple 快照管理員 
- 步驟 2︰ 備份 StorSimple 快照 Manager 資料庫 
- 步驟 3︰ 重新安裝 StorSimple 快照管理員，並還原資料庫 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>步驟 1︰ 解除安裝 StorSimple 快照管理員

若要解除安裝 StorSimple 快照 Manager 中使用下列步驟。

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>若要解除安裝 StorSimple 快照管理員

1. Host （主機） 電腦上，開啟**[控制台**]，按一下 [**程式**]，然後按一下**[程式和功能**。

2. 在左窗格中，按一下 [**解除安裝或變更程式**]。

3. 以滑鼠右鍵按一下**StorSimple 快照集管理員**]，然後按一下 [**解除安裝**。

4. 此舉會啟動 StorSimple 快照 Manager 安裝程式。 按一下 [**修改設定**]，然後按一下 [**解除安裝**。

    >[AZURE.NOTE] 如果有任何 MMC 處理程序，在背景執行，例如 StorSimple 快照管理員或磁碟管理，將無法解除安裝，而且您會收到您嘗試解除安裝程式之前，請關閉所有執行個體 MMC 郵件。 選取**會自動關閉應用程式，並嘗試重新啟動安裝完成後**，，然後再按一下**[確定**]。
 
5. 解除安裝程序完成時，便會出現的**成功設定**的訊息。 按一下 [**關閉**]。

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>步驟 2︰ 備份 StorSimple 快照 Manager 資料庫

使用下列步驟建立並儲存 StorSimple 快照 Manager 資料庫的複本。

#### <a name="to-back-up-the-database"></a>若要備份的資料庫

1. 停止 Microsoft StorSimple 管理服務︰

   1. 啟動伺服器管理員]。

   2. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。

   3. 在 [**服務**] 頁面中，選取 [ **Microsoft StorSimple 管理服務**。

   4. 在右窗格中，在 [ **Microsoft StorSimple 管理服務**] 底下，按一下 [**停止服務**。

        ![停止 StorSimple 管理員服務](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. 瀏覽至 C:\ProgramData\Microsoft\StorSimple\BACatalog。 

    >[AZURE.NOTE] ProgramData 是隱藏的資料夾。

3. 找出目錄 XML 檔案，複製檔案]，並的複本存放在安全的位置，或在雲端。

    ![StorSimple 備份目錄檔案](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. 重新啟動 Microsoft StorSimple 管理服務︰ 

    1. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。

    2. 在 [**服務**] 頁面中，選取 [ **Microsoft StorSimple 管理服務**e]。

    3. 在右窗格中，在**Microsoft StorSimple 管理服務**] 底下，按一下 [**重新啟動服務**。 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>步驟 3︰ 重新安裝 StorSimple 快照管理員，並還原資料庫

若要重新安裝 StorSimple 快照管理員，請依照下列[安裝新的 StorSimple 快照管理員](#install-a-new-storsimple-snapshot-manager)中的步驟。 還原 StorSimple 快照 Manager 資料庫，然後使用下列程序。

#### <a name="to-restore-the-database"></a>若要還原資料庫

1. 停止 Microsoft StorSimple 管理服務︰

    1. 啟動伺服器管理員]。

    2. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。

    3. 在 [**服務**] 頁面中，選取 [ **Microsoft StorSimple 管理服務**。

    4. 在右窗格中，在 [ **Microsoft StorSimple 管理服務**] 底下，按一下 [**停止服務**。

2. 瀏覽至 C:\ProgramData\Microsoft\StorSimple\BACatalog。 

     >[AZURE.NOTE] ProgramData 是隱藏的資料夾。

3. 刪除目錄的 XML 檔案，然後替換成您先前儲存的版本。

4. 重新啟動 Microsoft StorSimple 管理服務︰ 

    1. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。

    2. 在 [**服務**] 頁面中，選取 [ **Microsoft StorSimple 管理服務**。

    3. 在右窗格中，在**Microsoft StorSimple 管理服務**] 底下，按一下 [**重新啟動服務**。

## <a name="next-steps"></a>後續步驟

- 若要瞭解更多關於 StorSimple 快照管理員，請移至[什麼是 StorSimple 快照管理員？](storsimple-what-is-snapshot-manager.md)。

- 若要進一步瞭解 StorSimple 快照管理員使用者介面，移至[StorSimple 快照管理員使用者介面](storsimple-use-snapshot-manager.md)。

- 若要進一步瞭解使用 StorSimple 快照管理員，請移至[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
