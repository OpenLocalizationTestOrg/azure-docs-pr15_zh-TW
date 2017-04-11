<properties 
   pageTitle="停用，並刪除 StorSimple 裝置 |Microsoft Azure"
   description="說明如何移除服務中的 StorSimple 裝置，第一次停用它，然後再將其刪除。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>停用，並刪除 StorSimple 裝置

## <a name="overview"></a>概觀

您可能會想要採取 StorSimple 裝置登出服務 （例如，如果您要取代或升級您的裝置，或您不再使用的 StorSimple）。 如果這種情況，您必須停用的裝置，您可以將其刪除之前。 停用伺服器裝置與相對應的 StorSimple 管理員服務之間的連線。 本教學課程說明如何移除服務中的 StorSimple 裝置，第一個停用它，然後再將其刪除。 

當您停用的裝置時，就不會出現可存取儲存在本機裝置的任何資料。 復原只會儲存在雲端的裝置與相關聯的資料。  

>[AZURE.WARNING] 停用是永久性的操作，而且無法復原。 停用的裝置無法註冊 StorSimple 管理員服務，除非先重設工廠。 
>
>工廠重設程序會刪除裝置已儲存在本機的所有資料。 因此，務必先停用的裝置，使其雲端快照的所有資料。 這可讓您可以在稍後的階段復原的所有資料。

本教學課程說明如何︰

- 停用的裝置，然後刪除資料
- 停用的裝置，並保留資料

同時也會說明如何停用，並刪除 StorSimple 虛擬裝置上的運作。

>[AZURE.NOTE] 當您停用 StorSimple 實體或虛擬裝置之前，務必以停止或刪除用戶端和主機相依於該裝置。

## <a name="deactivate-and-delete-data"></a>停用和刪除資料

如果您想要完全刪除裝置並不想要保留的裝置上的資料，然後完成下列步驟。

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>若要停用的裝置，然後刪除資料  

1. 之前停用的裝置，您必須先刪除所有音量容器 （和區） 與裝置相關聯。 只有在您已刪除相關聯的備份後，您可以刪除大量容器。

2. 以下列方式停用的裝置︰

    1. 在 [StorSimple 管理員服務**裝置**] 頁面中，選取您想要停用，並在頁面底部，按一下 [**停用**的裝置。

    2. 會顯示確認訊息。 按一下 [ **]**以繼續進行。 停用程序會啟動，並需要幾分鐘才能完成。

3. 停用之後，您也可以完全刪除裝置。 刪除裝置會從裝置連線至服務的清單。 服務然後不再管理已刪除的裝置。 若要刪除裝置使用下列步驟︰

    1. StorSimple 管理員服務**裝置**在頁面上，選取您想要刪除的停用的裝置。

    2. 按一下 [在頁面底部的 [**刪除**]。

    3. 系統會提示您確認。 按一下 [ **]**以繼續進行。

    可能需要幾分鐘的時間刪除裝置。

## <a name="deactivate-and-retain-data"></a>停用，並保留資料

如果您有興趣刪除裝置，但想要保留的資料，然後完成下列步驟。

####<a name="to-deactivate-a-device-and-retain-the-data"></a>若要停用的裝置，並保留資料 

1. 停用的裝置。 仍會維持所有大量容器和裝置的快照。

    1. 在 [StorSimple 管理員服務**裝置**] 頁面中，選取您想要停用，並在頁面底部，按一下 [**停用**的裝置。

    2. 會顯示確認訊息。 按一下 [ **]**以繼續進行。 停用程序會啟動，並需要幾分鐘才能完成。

2. 現在，您可能會失敗大量容器和相關聯的快照。 程序，請移至[錯誤移轉及損毀復原 StorSimple 裝置](storsimple-device-failover-disaster-recovery.md)。

3. 停用，容錯移轉，您也可以完全刪除裝置。 刪除裝置會從裝置連線至服務的清單。 服務然後不再管理已刪除的裝置。 完成下列步驟，以刪除此裝置︰
 
    1. StorSimple 管理員服務**裝置**在頁面上，選取您想要刪除的停用的裝置。

    2. 按一下 [在頁面底部的 [**刪除**]。

    3. 系統會提示您確認。 按一下 [ **]**以繼續進行。

    可能需要幾分鐘的時間刪除裝置。

## <a name="deactivate-and-delete-a-virtual-device"></a>停用，並刪除虛擬裝置

StorSimple 虛擬裝置，停用解除配置虛擬機器。 您可以刪除虛擬機器和其佈時所建立的資源。 虛擬的裝置已停用 」 之後，就無法還原成先前的狀態中。 

停用結果下列動作︰

- 移除 StorSimple 虛擬裝置。

- 移除 OSDisk 與 StorSimple 虛擬裝置所建立的資料磁碟。

- 保留代管服務和虛擬期間佈建所建立的網路。 如果您不使用這些項目，您必須手動刪除。

- 仍會保留 StorSimple 虛擬裝置所建立的雲端快照。

## <a name="next-steps"></a>後續步驟
- 若要還原為原廠預設值的停用的裝置，移至[重設為原廠預設設定的裝置](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。

- 技術的協助，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

- 若要進一步瞭解如何使用 StorSimple 管理員服務，請移至[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。 
