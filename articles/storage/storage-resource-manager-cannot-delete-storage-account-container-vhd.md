<properties
    pageTitle="疑難排解錯誤，當您在資源管理員部署中刪除 Azure 儲存體帳戶、 容器或 Vhd |Microsoft Azure"
    description="當您在資源管理員部署中刪除 Azure 儲存體帳戶、 容器或 Vhd 疑難排解錯誤"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="genli"/>

# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>當您在資源管理員部署中刪除 Azure 儲存體帳戶、 容器或 Vhd 疑難排解錯誤

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

當您嘗試刪除 Azure 儲存體帳戶、 容器或虛擬硬碟 (VHD) [Azure 入口網站](https://portal.azure.com)中，您可能會收到錯誤。 本文提供疑難排解指南，協助解決 Azure 資源管理員部署中的問題。

如果本文無法解決您 Azure 的問題，請瀏覽以 Azure 論壇[MSDN 和堆疊溢位](https://azure.microsoft.com/support/forums/)。 您可以將您的問題張貼在這些論壇，或者@AzureSupportTwitter 上。 此外，您可以選取[Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [**取得支援**的檔案 Azure 支援要求。

## <a name="symptoms"></a>發生上述任何症狀

### <a name="scenario-1"></a>情況 1

當您嘗試刪除 VHD 儲存帳戶資源管理員部署中時，您會收到下列錯誤訊息︰

**無法刪除 blob 'vhds/BlobName.vhd'。錯誤︰ 有位於目前租用 blob 且沒有租用識別碼指定在邀請中。**

因為虛擬機器 (VM) 在您嘗試刪除 VHD 有租用，會發生此問題。

### <a name="scenario-2"></a>案例 2

當您嘗試刪除容器儲存帳戶資源管理員部署中時，您會收到下列錯誤訊息︰

**無法刪除存放容器 'vhd 」。錯誤︰ 沒有目前租用容器上，在邀請中指定沒有租用識別碼。**

因為容器具有 VHD 鎖定租用狀態，會發生此問題。

### <a name="scenario-3"></a>情況 3

當您嘗試刪除資源管理員部署中的儲存空間帳戶時，您會收到下列錯誤訊息︰

**無法刪除儲存帳戶 」 StorageAccountName 」。錯誤︰ 無法刪除儲存帳戶，因為其在使用的成品。**

因為儲存帳戶包含租用狀態 VHD，會發生此問題。

## <a name="solution"></a>解決方案

若要解決這些問題，您必須找出造成錯誤 VHD 和相關聯的 VM。 然後，卸離從 （適用於資料磁碟） VM VHD 或刪除 （適用於 OS 磁碟） 使用 VHD VM。 這租用移除 VHD，並允許遭到刪除。

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>步驟 1︰ 找出問題 VHD 和相關聯的 VM


1. [Azure 入口網站](https://portal.azure.com)登入。
2. 在 [**中心**] 功能表中，選取 [**所有資源**]。 移至您想要刪除的儲存空間帳戶，然後選取 [ **Blob** > **vhd**。

    ![儲存帳戶與反白顯示的 「 vhd 」 容器入口網站的螢幕擷取畫面](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

3. 核取每一個 VHD 容器中的屬性。 找出在**Leased**狀態 VHD。 接著，決定使用 VM VHD。 通常，您可以決定 VM 保留 VHD 檢查 VHD 的名稱︰

    - OS 磁碟通常會遵循此命名慣例︰ VMNameYYYYMMDDHHMMSS.vhd
    - 資料磁碟通常會遵循此命名慣例︰ VMName-YYYYMMDD-HHMMSS.vhd

    ![螢幕擷取畫面顯示在入口網站中的容器資訊 VM、 租賃狀態的 「 鎖定 」，以及租用狀態的醒目提示 「 Leased 」 的名稱](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>步驟 2︰ 移除 VHD 租用

若要刪除 （適用於 OS 磁碟） 使用 VHD VM:

1.  [Azure 入口網站](https://portal.azure.com)登入。
2.  在 [**中心**] 功能表中，選取 [**虛擬機器**]。
3.  選取 [持有 VHD 租用 VM]。
4.  請確定不正在使用的虛擬機器，而且不再需要虛擬機器。
5.  在**VM 詳細資料**刀頂端，選取 [**刪除**]，然後按一下**[是]**以確認。
6.  VM 應刪除，但保留 VHD。 不過，VHD 應該不再有租用。 可能需要幾分鐘讓發行租用。 若要確認租用已發行，請移至**所有資源** > **儲存體帳戶名稱** > **Blob** > **vhd**。 在 [ **Blob 屬性**] 窗格中，**租用狀態**的值應該是**Unlocked**。

若要中斷連結 VHD 從 VM 使用該 （適用於資料磁碟）︰

1.  [Azure 入口網站](https://portal.azure.com)登入。
2.  在 [**中心**] 功能表中，選取 [**虛擬機器**]。
3.  選取 [持有 VHD 租用 VM]。
4.  選取**磁碟**上的 [ **VM 詳細資料**刀。
5.  選取 [持有 VHD 租用資料磁碟]。 您可以決定 VHD 附加中的核取 VHD 的 URL。
6.  決定使用不正在使用的資料磁碟確定性。
7.  按一下 [**磁碟詳細資料**刀上的 [**中斷連結**]。
8.  磁碟現在會中斷 VM 和 VHD 不再應有租用它。 可能需要幾分鐘讓發行租用。 若要確認已經發行租用，請移至**所有資源** > **儲存體帳戶名稱** > **Blob** > **vhd**。 在 [ **Blob 屬性**] 窗格中，**租用狀態**的值應該是**Unlocked**。

## <a name="what-is-a-lease"></a>什麼是租用？

使用期是鎖定，可用來控制存取權的 blob (例如，VHD)。 當、 租賃 blob 只使用期的擁有者可以存取 blob。 使用期務必原因如下︰

-   如果嘗試撰寫相同的部分 blob 的同時在多個擁有者，它會防止資料損毀。
-   避免 blob 項目正在使用它 (例如，VM) 遭到刪除。
-   避免儲存帳戶項目正在使用它 (例如，VM) 遭到刪除。



## <a name="next-steps"></a>後續步驟

- [刪除儲存帳戶](storage-create-storage-account.md#delete-a-storage-account)
- [如何在 Microsoft Azure (PowerShell) 鎖定的租用的 blob 儲存體](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
