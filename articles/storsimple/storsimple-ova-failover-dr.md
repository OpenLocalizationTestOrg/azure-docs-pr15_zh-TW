<properties
   pageTitle="在 StorSimple 虛擬陣列損毀復原和裝置容錯移轉"
   description="進一步瞭解如何容錯移轉 StorSimple 虛擬陣列。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>在 StorSimple 虛擬陣列損毀復原和裝置容錯移轉


## <a name="overview"></a>概觀

本文將說明在 Microsoft Azure StorSimple 虛擬陣列 （也稱為 StorSimple 內部部署虛擬裝置） 損毀修復包括必要容錯移轉至另一個虛擬裝置損毀事件的詳細的步驟。 容錯移轉可讓您將資料從*來源*裝置中的資料中心移轉到另一個位於相同或不同的地理位置的*目標*裝置。 裝置容錯移轉適用於整個裝置。 在容錯移轉，雲端資料來源裝置會變更至的目標裝置的擁有權。

裝置容錯移轉協調損毀修復 (DR) 功能，並從 [**裝置**] 頁面。 此頁面 tabulates 連線到您 StorSimple 管理員服務的所有 StorSimple 裝置。 針對每個裝置時，會顯示好記的名稱、 狀態、 能夠與最大的容量、 類型及模型。

![](./media/storsimple-ova-failover-dr/image15.png)

本文只適用於 StorSimple 虛擬陣列。 若要容錯 8000 數列裝置，移至[容錯移轉和 StorSimple 裝置的損毀修復](storsimple-device-failover-disaster-recovery.md)。


## <a name="what-is-disaster-recovery"></a>什麼是損毀修復？

在損毀修復 (DR) 案例中，主要裝置會停止運作。 在此情況下，您可以移動到另一個裝置失敗的裝置與相關聯，做為*來源*使用的主要裝置，並指定為*目標*的另一個裝置的雲端資料。 此程序被指*容錯移轉*。 期間容錯移轉時，所有磁碟區或從來源裝置共用變更擁有權，並會傳送到目標裝置。 允許沒有篩選的資料。

DR 都使用熱地圖基礎 tiering 與追蹤完整裝置還原為建立模型。 熱力圖所定義之指派熱力圖值的資料在讀取和寫入模式。 此熱力圖對應然後層最低的熱力圖資料區塊至雲端第一次同時高熱力圖 （常用） 資料區塊中的本機層。 期間的 DR 熱力圖用來還原和 rehydrate 雲端的資料。 裝置擷取所有區/共用中的最後一個新的備份 （如下圖所決定內部），然後執行還原的備份。 整個 DR 程序 」 是由裝置協調。


## <a name="prerequisites-for-device-failover"></a>裝置容錯移轉的先決條件


### <a name="prerequisites"></a>必要條件

針對任何裝置容錯移轉時，應該滿足下列先決條件︰

- 來源裝置必須**停用**狀態。

- 目標裝置必須顯示為**作用中**Azure 傳統入口網站中。 您會需要佈建目標虛擬裝置的相同或更高的容量。 然後，您就應該使用本機網頁 UI 來設定和成功註冊虛擬裝置。

    > [AZURE.IMPORTANT] 請勿設定服務已註冊的虛擬裝置即可**完成裝置設定**。 您應該不執行任何裝置設定服務。

- 來源和目標的裝置必須相同類型。 您可以透過虛擬裝置設定為到另一個檔案伺服器檔案伺服器只會失敗。 相同的為 true 的 iSCSI 伺服器。

- 為檔案伺服器 DR，我們建議您加入至相同的網域的來源的目標裝置，讓的共用權限會自動解決。 在相同的網域中的目標裝置容錯移轉支援此版本。

### <a name="other-considerations"></a>其他考量

- 我們建議您先採取離線來源裝置上所有磁碟區或共用。

- 如果這是計劃的容錯移轉時，建議您需要裝置的備份，然後繼續容錯移轉至最小化資料遺失。 如果這是意外的容錯移轉時，最新的備份會用來將裝置還原。

- 可用的目標裝置的 DR 具有相同或更大容量比較來源裝置的裝置。 連線到您的服務，但卻不符合準則的空間不足的裝置無法為目標裝置。

### <a name="dr-prechecks"></a>DR prechecks

DR 在開始之前，prechecks 在裝置上執行。 這些檢查有助於確保 DR 資料時，會發生任何錯誤。 Prechecks 包括︰

- 驗證的儲存空間帳戶

- 檢查 Azure 的雲端連線

- 檢查目標裝置上的可用空間

- 檢查如果 iSCSI 伺服器來源裝置有有效的 ACR 名稱，請 IQN （不超過 220 字元的長度） 和 CHAP 密碼 （12 和 16 個字元的長度） 相關聯區

如果上述 prechecks 的任何失敗，您無法繼續 DR。 您需要解決這些問題並再試一次 DR。

DR 已順利完成後，會將雲端裝置上的資料來源的擁有權轉移到目標裝置。 來源裝置就不會再入口網站中可以使用。 封鎖所有區/共用來源裝置上存取和目標裝置會變成作用中。

> [AZURE.IMPORTANT]
> 
> 裝置已不再提供，但您佈建主機系統虛擬機器仍正在使用的資源。 DR 順利完成後，您可以刪除這個虛擬機器從主機系統。

## <a name="fail-over-to-a-virtual-array"></a>無法透過虛擬的陣列

我們建議您有另一個 StorSimple 虛擬陣列佈建後，透過本機網頁的 UI，註冊 StorSimple 管理員服務之前執行此程序。


> [AZURE.IMPORTANT]
> 
> - 不允許您無法透過從 StorSimple 8000 數列裝置 1200年虛擬裝置。
> - 您可以從部署政府版入口網站，以在傳統入口網站中 Azure 虛擬裝置在美國聯邦資訊處理標準 (FIPS) 啟用虛擬裝置容錯移轉。 反向也是如此。

執行下列步驟，以將裝置還原至目標 StorSimple 虛擬裝置。

1. 需要離線磁碟區/共用 」 主機上。 請參閱 」 主機上的特定作業系統指示，離線工作區/共用。 如果不已離線，您會需要採取在裝置上的所有區/共用離線，移至**裝置 > 共用**(或**裝置 > 區**)。 選取 [共用/區，然後按一下 [頁面底端的 [**離線**]。 當畫面提示您確認，按一下 [**是**]。 所有共用/磁碟區在裝置上都重複此程序。

2. 在 [**裝置**] 頁面上選取的容錯移轉的來源裝置，然後按一下 [**停用**。 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. 系統會提示您確認。 裝置停用是無法復原的永久程序。 您也會收到採取主機上的 [共用/區離線。

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. 根據確認，就會開始停用。 停用已順利完成之後，您會收到通知。

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. 在 [**裝置**] 頁面上**停用**現在會變更裝置狀態。

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. 選取 [停用的裝置，然後在頁面底部，按一下 [**容錯移轉**]。

6. 在開啟的 [確認容錯移轉精靈] 執行下列動作︰

    1. 從可用的裝置下拉式清單中，選擇 [**目標裝置。** 僅有足夠的容量裝置會顯示在下拉式清單中。

    2. 檢閱來源裝置，例如裝置名稱與容量總數，將無法在共用的名稱與相關聯的詳細資料。

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. 核取 [**我同意容錯移轉是永久性的操作，並容錯移轉已順利完成之後, 會刪除來源裝置**。

8. 按一下核取圖示![](./media/storsimple-ova-failover-dr/image1.png)。


9. 將啟動容錯移轉工作，您將會收到通知。 按一下 [**檢視工作**監控容錯移轉]。

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. 在 [**工作**] 頁面中，您會看到建立的來源裝置的容錯移轉工作。 此工作執行 DR prechecks。

    ![](./media/storsimple-ova-failover-dr/image23.png)

    DR prechecks 成功之後，容錯移轉工作會產生還原的每個共用/大量存在於來源裝置上的工作。

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. 容錯移轉完成後，請移至 [**裝置**] 頁面。

    。 選取用來做容錯移轉程序的目標裝置的 StorSimple 虛擬裝置。

    b。 移至 [**共用**] 頁面 (或**區**如果 iSCSI 伺服器)。 現在應該會列出所有共用 （磁碟區） 從舊的裝置。
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**可用的視訊**

這段影片示範如何您可能會失敗，移到另一個虛擬裝置的 StorSimple 內部部署虛擬裝置。

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>業務連續性損毀修復 (BCDR)

業務連續性損毀復原 (BCDR) 情況整個 Azure 資料中心停止運作。 這會影響您 StorSimple 管理員服務及相關聯的 StorSimple 裝置。

如果有 StorSimple 裝置註冊之前損毀發生，可能需要刪除這些 StorSimple 裝置。 之後，您可以重新建立並設定的裝置。

## <a name="errors-during-dr"></a>DR 時發生錯誤

**雲端連線中斷 DR 期間**

如果雲端連線中斷 DR 開始後才裝置還原完成之前，DR 會失敗，並會通知您。 目標裝置用於 DR 然後標示為*無法使用。* 相同的目標裝置無法將用於未來 DRs。

**不相容的目標裝置**

如果可用的目標裝置沒有足夠的空間，您會看到錯誤沒有相容的目標裝置的效果。

**Precheck 失敗**

如果沒有符合其中一個 prechecks，您會看到 precheck 失敗。

## <a name="next-steps"></a>後續步驟

進一步瞭解如何[管理使用本機網頁 UI StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。
