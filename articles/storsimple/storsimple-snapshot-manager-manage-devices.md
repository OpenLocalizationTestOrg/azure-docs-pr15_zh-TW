<properties 
   pageTitle="管理裝置使用 StorSimple 快照管理員 |Microsoft Azure"
   description="說明如何使用 StorSimple 快照管理員 MMC 嵌入式管理單元連線並管理 StorSimple 裝置。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>使用連線並管理 StorSimple 裝置 StorSimple 快照管理員

## <a name="overview"></a>概觀

您可以使用節點 StorSimple 快照管理員**範圍**] 窗格中，以確認匯入的 StorSimple 裝置資料，並重新整理連線的存放裝置。 此外，當您按一下 [**裝置**] 節點，您可以看到連線的裝置和 [**結果**] 窗格中的相對應的狀態資訊的清單。

![連線的裝置](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**圖 1: StorSimple 快照管理員連接的裝置** 

根據您**檢視**的選項，[**結果**] 窗格會顯示每個裝置的下列資訊。 （如需設定檢視的詳細資訊，請移至[檢視] 功能表](storsimple-use-snapshot-manager.md#view-menu)。


| 結果資料行  |描述          |
|:----------------|:--------------------| 
| 名稱            | 裝置設定 Azure 傳統入口網站中的名稱|
| 模型           | 裝置的模型數目|
| 版本         | 在裝置上安裝軟體的版本 |
| 狀態          | 無論是可用的裝置 |
| 上次同步處理     | 日期及時間時裝置上次同步處理 |
| 序列 [否]。      | 序列值的裝置 |
 
如果您以滑鼠右鍵按一下 [**裝置**] 節點，在 [**範圍**] 窗格中的，您可以選取下列動作︰

- 新增或取代裝置 
- 連線到的裝置，並確認匯入 
- 重新整理連線的裝置 

如果您按一下 [**裝置**] 節點，然後以滑鼠右鍵按一下裝置名稱，在 [**結果**] 窗格中的，您可以選取下列動作︰

- 驗證裝置 
- 檢視裝置詳細資料 
- 重新整理裝置 
- 刪除裝置設定 
- 變更裝置密碼

>[AZURE.NOTE] 所有這些動作也可在 [**動作**] 窗格。
 
本教學課程說明如何使用 StorSimple 快照管理員連線和管理裝置並執行下列工作︰

- 新增或取代裝置 
- 連線到的裝置，並確認匯入 
- 重新整理連線的裝置 
- 驗證裝置 
- 檢視裝置詳細資料 
- 重新整理在個別的裝置 
- 刪除裝置設定 
- 變更密碼過期的裝置
- 取代失敗的裝置

>[AZURE.NOTE] 如需使用 StorSimple 快照管理員介面一般資訊，請移至[StorSimple 快照管理員使用者介面](storsimple-use-snapshot-manager.md)。


## <a name="add-or-replace-a-device"></a>新增或取代裝置

若要新增或取代 StorSimple 裝置使用下列程序。

#### <a name="to-add-or-replace-a-device"></a>若要新增或取代裝置

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下 [**裝置**] 節點，然後按一下**設定裝置**。 **在裝置設定**] 對話方塊隨即出現。

    ![設定 StorSimple 裝置](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. 在 [**裝置**] 下拉式方塊中，選取裝置或虛擬裝置的 IP 位址。 

4. 在 [**密碼**] 方塊中，輸入您為裝置 Azure 傳統入口網站中建立 StorSimple 快照管理員密碼。 按一下**[確定]**。 StorSimple 快照管理員會搜尋您識別出的裝置。 

    - 如果使用的裝置，StorSimple 快照管理員新增連線。 

    - 如果裝置無法基於任何原因，StorSimple 快照管理員會傳回錯誤訊息。 按一下**[確定]**以關閉錯誤訊息，然後再按一下 [**取消**] 關閉**裝置設定**] 對話方塊。

## <a name="connect-a-device-and-verify-imports"></a>連線到的裝置，並確認匯入

若要將 StorSimple 裝置連線，並確認有相關聯的備份的所有現有大量群組會匯入使用下列程序。

#### <a name="to-connect-a-device-and-verify-imports"></a>若要將裝置連接，並確認匯入

1. 若要將裝置連接到 StorSimple 快照管理員，依照新增或取代裝置。 連接到裝置之後，StorSimple 快照管理員回應，如下所示︰

    - 如果裝置無法基於任何原因，StorSimple 快照管理員會傳回錯誤訊息。 

   - 如果使用的裝置，StorSimple 快照管理員新增連線。 選取的裝置，它會出現在 [**結果**] 窗格中，[狀態] 欄位會指出的裝置時，**可使用**。 所提供的大量群組有相關聯的備份，StorSimple 快照管理員匯入設定的裝置，大量群組。 備份原則不會匯入。 沒有相關聯的備份的大量群組不會匯入。

2. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

3. 以滑鼠右鍵按一下 [**範圍**] 窗格頂端節點，然後按一下 [**切換匯入顯示**。

    ![選取切換匯入顯示](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. [**切換匯入顯示**] 對話方塊隨即出現，並顯示匯入的大量群組和備份的狀態。 按一下**[確定]**。 

大量群組和備份已順利匯入之後，您可以使用 [StorSimple 快照管理員來管理，就像您想要管理大量群組和備份您建立及設定使用 StorSimple 快照管理員也可以。 

## <a name="refresh-connected-devices"></a>重新整理連線的裝置

您可以使用下列程序，同步處理連線的 StorSimple 裝置使用 StorSimple 快照管理員。

####<a name="to-refresh-connected-devices"></a>若要重新整理連線的裝置

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，**裝置**，以滑鼠右鍵按一下，然後再按一下 [**重新整理的裝置**。 這會同步處理連線的裝置使用 StorSimple 快照管理員，讓您可以檢視大量群組和備份，包括任何新的版本資訊。 

    ![重新整理 StorSimple 裝置](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
**重新整理裝置**動作會從連線的裝置擷取任何新的大量群組與任何相關聯的備份。 **掃描區**可用的動作**區**節點，與**重新整理裝置**無法還原備份登錄機碼。

## <a name="authenticate-a-device"></a>驗證裝置

您可以使用下列程序來驗證 StorSimple 裝置使用 StorSimple 快照管理員。

#### <a name="to-authenticate-a-device"></a>若要驗證的裝置

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，按一下 [**裝置**]。

3. 在 [**結果**] 窗格中，以滑鼠右鍵按一下該裝置的名稱，然後按一下**驗證**。

4. [**驗證**] 對話方塊隨即出現。 輸入裝置密碼，然後再按一下**[確定]**。

    ![驗證] 對話方塊](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>檢視裝置詳細資料

您可以使用下列程序來檢視 StorSimple 裝置的詳細資訊，如有必要，重新同步處理的裝置與 StorSimple 快照管理員。

#### <a name="to-view-and-resynchronize-device-details"></a>若要檢視及重新同步處理裝置詳細資料

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，按一下 [**裝置**]。

3. 在 [**結果**] 窗格中，以滑鼠右鍵按一下該裝置的名稱，然後按一下**詳細資料**。 

4.**裝置詳細資料**] 對話方塊隨即出現。 此方塊可顯示名稱、 模型、 版本、 序列值、 狀態、 目標 iSCSI 完整名稱 (IQN) 和同步處理的日期和時間。 

   - 按一下 [同步處理裝置的 [**重新同步處理**]。

   - 按一下**[確定]**或**[取消]** ，關閉對話方塊。

    ![裝置詳細資料](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>重新整理在個別的裝置

您可以使用下列程序，重新同步處理個別 StorSimple 裝置使用 StorSimple 快照管理員。

#### <a name="to-refresh-a-device"></a>若要重新整理裝置

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，按一下 [**裝置**]。 

3. 在 [**結果**] 窗格中，以滑鼠右鍵按一下該裝置的名稱，然後按一下**重新整理的裝置**。 此同步處理裝置使用 StorSimple 快照管理員。 

## <a name="delete-a-device-configuration"></a>刪除裝置設定

若要刪除個別的 StorSimple 裝置設定從 StorSimple 快照管理員使用下列程序。

#### <a name="to-delete-a-device-configuration"></a>若要刪除裝置設定

1. 按一下 [桌面] 圖示可啟動 StorSimple 快照集管理員]。 

2. 在 [**範圍**] 窗格中，按一下 [**裝置**]。 

3. 在 [**結果**] 窗格中，以滑鼠右鍵按一下該裝置的名稱，然後按一下**刪除**。 

4. 出現下列訊息。 按一下 [ **]**若要刪除的設定，或按一下 [**無**]，若要取消刪除動作。

    ![刪除裝置設定](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>變更密碼過期的裝置

您必須輸入密碼以驗證 StorSimple 裝置使用 StorSimple 快照管理員。 當您使用 Windows PowerShell 介面設定裝置時，您可以設定此密碼。 不過，都可以終止密碼。 如果這種情況，您可以使用 Azure 傳統入口網站，來變更密碼。 然後，因為裝置已設定 StorSimple 快照管理員中的密碼到期之前，您必須重新驗證裝置 StorSimple 快照管理員中的商務連絡人。 

#### <a name="to-change-the-expired-password"></a>若要變更密碼過期

1. 在 Azure 傳統入口網站，開始 StorSimple 管理員服務。

2. 按一下 [**裝置** > **設定**裝置。

3. 向下 StorSimple 快照集管理員] 區段中捲動。 輸入密碼 14 15 個字元。 請確認密碼包含混合的大寫、 小寫字母、 數字及特殊字元。

4. 重新輸入密碼以進行確認。

5. 按一下 [在頁面底部的 [**儲存**]。

#### <a name="to-re-authenticate-the-device"></a>重新驗證裝置

1. 啟動 StorSimple 快照集管理員]。

2. 在 [**範圍**] 窗格中，按一下 [**裝置**]。 設定裝置的清單會出現在 [**結果**] 窗格中。 

3. 選取裝置，按一下滑鼠右鍵，然後再按一下 [**驗證**。

4. 在 [**驗證**] 視窗中，輸入新密碼。 

5. 選取裝置、 按一下滑鼠右鍵，然後選取 [**重新整理裝置**。 此同步處理裝置使用 StorSimple 快照管理員。 

## <a name="replace-a-failed-device"></a>取代失敗的裝置

如果 StorSimple 裝置失敗，且為替代待命 （容錯移轉） 裝置，使用下列步驟連線至新裝置，並檢視相關聯的備份。

#### <a name="to-connect-to-a-new-device-after-failover"></a>若要連接至新裝置後移轉後

1. 重新設定 iSCSI 連線至新裝置。 如需相關指示，請移至 」 步驟 7︰ 裝載、 初始化，與格式大量 」 在[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough-u2.md)。 

>[AZURE.NOTE] 如果新 StorSimple 裝置有相同的 IP 位址和舊，您可能無法連線舊的設定。 

2. 停止 Microsoft StorSimple 管理服務︰

    1. 啟動伺服器管理員]。

    2. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。 

    3. 在 [**服務**] 視窗中，選取 [ **Microsoft StorSimple 管理服務**。 

    4. 在右窗格中，在 [ **Microsoft StorSimple 管理服務**] 底下，按一下 [**停止服務**。 

3. 移除舊的裝置與相關的設定資訊︰ 

    1. 在檔案總管] 中，瀏覽至 C:\ProgramData\Microsoft\StorSimple\BACatalog。 

    2. 刪除 BACatalog 資料夾中的檔案。 

4. 重新啟動 Microsoft StorSimple 管理服務︰ 

    1. 在 [伺服器管理員儀表板的 [**工具**] 功能表中，選取 [**服務**]。 

    2. 在 [**服務**] 視窗中，選取 [ **Microsoft StorSimple 管理服務**。 

    3. 在右窗格中，在**Microsoft StorSimple 管理服務**] 底下，按一下 [**重新啟動服務**。 

5. 啟動 StorSimple 快照集管理員]。 

6. 若要設定新的 StorSimple 裝置，完成 [步驟 2 中的步驟︰ 在[部署 StorSimple 快照管理員](storsimple-snapshot-manager-deployment.md)StorSimple 裝置連線。 

7. 以滑鼠右鍵按一下最上層的節點 （StorSimple 快照管理員範例，） 上的 [**範圍**] 窗格中，然後按一下 [**切換匯入顯示**。 

8. 匯入的大量群組和備份會顯示在 StorSimple 快照管理員時，就會出現的訊息。 按一下**[確定]**。 

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 StorSimple 快照管理員來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 瞭解如何[使用 StorSimple 快照管理員來檢視及管理區](storsimple-snapshot-manager-manage-volumes.md)。

