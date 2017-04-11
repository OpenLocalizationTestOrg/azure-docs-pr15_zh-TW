<properties
    pageTitle="疑難排解在傳統的部署中刪除 Azure 儲存體帳戶、 容器或 Vhd |Microsoft Azure"
    description="刪除 Azure 儲存體帳戶、 容器或 Vhd 傳統部署中的疑難排解"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>刪除 Azure 儲存體帳戶、 容器或 Vhd 傳統部署中的疑難排解

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

當您嘗試刪除 Azure 儲存體帳戶、 容器或 VHD [Azure 入口網站](https://portal.azure.com/)或[Azure 傳統入口網站](https://manage.windowsazure.com/)中的，您可能會收到錯誤。 在下列情況下可能造成問題︰

-   當您刪除 VM 時，磁碟和 VHD 不會自動刪除。 可能會在儲存帳戶刪除失敗的原因。 我們不刪除磁碟，讓您可以使用 [磁碟裝載另一個 VM。

-   仍在磁碟或磁碟與相關聯的 blob 租用。

如果本文並未解決您 Azure 的問題，請造訪 Azure 論壇，在[MSDN 和堆疊溢位](https://azure.microsoft.com/support/forums/)。 您可以將您的問題張貼在這些論壇，或者@AzureSupportTwitter 上。 此外，您可以選取[Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [**取得支援**的檔案 Azure 支援要求。

## <a name="symptoms"></a>發生上述任何症狀

下一節列出當您嘗試刪除 Azure 儲存體帳戶、 容器或 Vhd 時，您可能會收到的常見錯誤。

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>情況 1︰ 無法刪除儲存的帳戶

當您瀏覽至[Azure 入口網站](https://portal.azure.com/)或[Azure 傳統入口網站](https://manage.windowsazure.com/)，並**刪除**選取的儲存空間帳戶時，您可能會看到下列錯誤訊息︰

*儲存帳戶 StorageAccountName 包含 VM 圖像。請確定這些 VM 圖像會移除再刪除此儲存帳戶。*

您也可能會看到此錯誤︰

**Azure 入口網站**︰

*無法刪除儲存帳戶 < vm-儲存的帳戶名稱 >。無法刪除儲存帳戶 < vm-儲存的帳戶名稱 >: ' 儲存帳戶 < vm-儲存的帳戶名稱 > 有一些作用中的影像及/或磁碟。確認已刪除此儲存帳戶之前先移除這些影像及/或磁碟。 」。*

**Azure 傳統入口網站**︰

*儲存帳戶 < vm-儲存的帳戶名稱 > 有一些作用中的影像及/或磁碟，例如 xxxxxxxxx-xxxxxxxxx-O-209490240936090599。請確定這些影像及/或磁碟將會移除再刪除此儲存帳戶。*

或

**Azure 入口網站**︰

*儲存帳戶 < vm-儲存的帳戶名稱 > 有 1 容器具有作用中的圖像及/或磁碟的成品。確保這些成品移除了圖像存放庫刪除此儲存帳戶之前*。

**Azure 傳統入口網站**︰

*提交無法儲存帳戶 < vm-儲存的帳戶名稱 > 有 1 容器具有作用中的圖像及/或磁碟的成品。請確定這些成品移除了圖像存放庫刪除此儲存帳戶之前。當您嘗試刪除儲存帳戶並有與其相關聯的磁碟仍在使用中時，您會看到訊息，告訴您有要刪除的作用中磁碟*。

### <a name="scenario-2-unable-to-delete-a-container"></a>案例 2︰ 無法刪除容器

當您嘗試刪除存放容器時，您可能會看到下列錯誤︰

無法刪除存放容器*<container name>。錯誤: 「 目前租用容器，而且沒有租用識別碼指定在邀請中*。

### <a name="scenario-3-unable-to-delete-a-vhd"></a>情況 3︰ 無法刪除 VHD

刪除 VM 後，當您嘗試刪除 blob 相關聯的 vhd，您可能會收到下列訊息︰

*無法刪除 blob ' 路徑/XXXXXX XXXXXX-os-1447379084699.vhd 」。錯誤: 「 目前租用上 blob，而且沒有租用識別碼指定在邀請中。*

## <a name="solution"></a>解決方案
若要解決最常見的問題，請嘗試下列方法︰

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>步驟 1︰ 刪除，無法刪除儲存帳戶、 容器或 VHD 任何 OS 磁碟

1. 切換至[Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取**虛擬機器** > **磁碟**。

    ![Azure 傳統入口網站上的虛擬機器上的圖像。](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. 找出儲存帳戶、 容器或您想要刪除的 VHD 相關聯的磁碟。 當您核取的磁碟位置時，您會發現相關聯的儲存帳戶、 容器或 VHD。

    ![Azure 傳統入口網站顯示的磁碟位置資訊的圖像](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. 確認沒有 VM 會列在 [**附加至**] 欄位的磁碟，然後再刪除 [磁碟。

    > [AZURE.NOTE] 如果磁碟附加到 VM 時，您無法將其刪除。 磁碟會中斷刪除 VM 非同步。 可能需要幾分鐘 VM 刪除該欄位，以清除之後。

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>步驟 2︰ 刪除，無法刪除的儲存空間帳戶或容器的任何 VM 圖像

1. 切換至[Azure 傳統入口網站](https://manage.windowsazure.com/)。
2. 選取**虛擬機器** > **圖像**，然後刪除儲存帳戶、 容器或 VHD 與相關聯的圖像。

    之後，再次嘗試刪除儲存帳戶、 容器或 VHD。

> [AZURE.WARNING] 請務必備份任何您想要刪除帳戶之前儲存的項目。 無法還原已刪除的儲存空間帳戶或擷取任何它包含刪除之前的內容。 這也適用於任何資源在帳戶︰ 當您刪除 VHD、 blob、 表格、 佇列中或檔案，將會永久刪除。 確定資源無法使用。

## <a name="about-the-stopped-deallocated-status"></a>停止 （解除配置） 的狀態相關

建立傳統的部署模型中並沒有保留的 Vm 會有[Azure 入口網站](https://portal.azure.com/)或[Azure 傳統入口網站](https://manage.windowsazure.com/)上的**已停止 （解除配置）**狀態。

**Azure 傳統入口網站**︰

![停止 Azure 入口網站上 Vm (Deallocated) 狀態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Azure 入口網站**︰

![已停止 （解除配置） 狀態 Vm Azure 傳統入口網站上。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

狀態為 「 已停止 （解除配置） 「 釋放電腦的資源，例如 CPU、 記憶體和網路。 磁碟，不過，仍保留，因此您可以快速重新建立 VM 必要。 這些磁碟會建立備份 Azure 儲存的 Vhd 的上方。 儲存帳戶有這些 Vhd，而且磁碟上這些 Vhd 有租用。

## <a name="next-steps"></a>後續步驟

- [刪除儲存帳戶](storage-create-storage-account.md#delete-a-storage-account)
- [如何在 Microsoft Azure (PowerShell) 鎖定的租用的 blob 儲存體](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
