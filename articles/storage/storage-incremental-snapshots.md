<properties
    pageTitle="使用累加快照集進行備份及復原 Azure 虛擬機器 |Microsoft Azure"
    description="建立備份及復原的 Azure 虛擬機器磁碟使用累加快照的自訂解決方案。"
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>


# <a name="back-up-azure-virtual-machine-disks-with-incremental-snapshots"></a>備份和累加快照 Azure 虛擬機器磁碟

## <a name="overview"></a>概觀

Azure 儲存體提供 blob 的快照的功能。 快照擷取 blob 狀態的時間點。 在本文中，我們會說明的方式，您可以維護備份使用快照的虛擬機器磁碟的情況。 當您選擇不使用 Azure 備份及復原服務，並想要建立自訂的虛擬機器磁碟備份策略時，您可以使用這種方法。

Azure 虛擬機器磁碟會儲存為網頁二進位大型物件中 Azure 儲存體。 由於備份策略虛擬機器磁碟本文中所討論，我們將參考頁面 blob 的內容中的快照。 若要進一步瞭解快照，請參閱[建立 Blob 的快照](https://msdn.microsoft.com/library/azure/hh488361.aspx)。

## <a name="what-is-a-snapshot"></a>什麼是快照？

Blob 快照是唯讀版本的時間點擷取 blob。 一旦建立快照，它可以讀取、 複製或刪除，但無法修改。 快照提供備份 blob 時間內所顯示的樣子。 其餘版本 2015年-04-05 直到您已複製完整的快照的功能。 與其他版本 2015年-07-08 和上方，您也可以複製遞增的快照。

## <a name="full-snapshot-copy"></a>完整的快照複本

快照集可以為要保留基底 blob 的備份的 blob 複製到其他儲存的帳戶。 您也可以在其基底 blob，就像還原舊版 blob 複製快照。 當快照從一個儲存帳戶複製到另一個時，它會佔用基底頁面 blob 為相同的空間。 因此，複製整個的快照集儲存帳戶到另一個會變得很慢，也會使用許多在目標儲存帳戶的空間。

>[AZURE.NOTE] 如果您將基底 blob 複製到另一個目的地，以及其將不會複製 blob 的快照。 同樣地，如果您以覆寫基底 blob 複本，基底 blob 相關聯的快照不受影響，並保持不變基底 blob 的名稱。

### <a name="back-up-disks-using-snapshots"></a>備份磁碟使用快照

為備份的虛擬機器磁碟策略，就可以磁碟或頁面 blob 的週期性快照，將它們複製到其他儲存帳戶使用[複製 Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)作業或[AzCopy](storage-use-azcopy.md)等工具。 您可以將快照複製到目的地頁面 blob 使用不同的名稱。 產生的目的地頁面 blob 是可寫入頁面 blob 並不快照。 本文稍後的我們會說明步驟，才能使用快照的虛擬機器磁碟的備份。

### <a name="restore-disks-using-snapshots"></a>還原使用快照的磁碟

時若要將您的磁碟還原備份快照的其中一種擷取穩定舊版，您可以複製透過基底頁面 blob 的快照。 基底頁面升級快照集後 blob，快照仍會保留，但其來源會覆寫可會同時讀取與寫入的複本。 本文稍後的我們會說明還原舊版的磁碟快照的步驟。

### <a name="implementing-full-snapshot-copy"></a>實作完整的快照複本

您可以執行下列動作，來實作完整的快照複本

-   首先，需要使用[快照 Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx)作業基底 blob 的快照。
-   然後，複製到目標儲存帳戶使用[複製 Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)的快照。
-   重複此程序，維護您基底 blob 的備份複本。

## <a name="incremental-snapshot-copy"></a>累加快照複本

[GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API 的新功能提供更有效備份頁面 blob 或磁碟的快照。 API 會傳回基底 blob 和快照之間的變更清單。 這會減少備份的帳戶所用的儲存空間量。 API 支援頁面 blob 進階版儲存空間，以及您在標準的儲存空間。 使用此 API，您可以立即建立更快且有效率的備份解決方案的 Azure Vm。 這是用於其他版本 2015年-07-08 及更新版本。

累加快照複本可讓您複製一個儲存帳戶到另一個，之間的差異

-   基底 blob 或其快照
-   基底 blob 的任何兩個快照集

提供符合下列條件，

- Blob 建立 Jan-1-2016年或更新版本。
- Blob 已不會覆寫[PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx)或[複製 Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)兩個快照之間。


**注意**︰ 這項功能適用於進階版和標準 Azure 頁面二進位大型物件。

當您使用快照，自訂備份策略快照複製到另一個儲存帳戶可能非常緩慢並會使用許多儲存空間。 而不是複製整個快照集到備份儲存的帳戶，您可以撰寫要備份的頁面 blob 的連續快照之間的差異。 如此一來，大幅降低複製及儲存備份的空間的時間。

### <a name="implementing-incremental-snapshot-copy"></a>實作累加快照複本

您可以執行下列動作，實作累加快照複本

-   需要使用[快照 Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx)基底 blob 的快照。
-   複製到目標備份儲存帳戶使用[複製 Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)的快照。 這是備份頁面 blob。 此備份頁面 blob 的快照，並儲存備份的帳戶。
-   需要使用快照 Blob 基底 blob 的另一個快照。
-   取得使用[GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx)基底 blob 的第一個和第二個快照之間的差異。 使用新的參數**prevsnapshot**指定您要使用的差異快照的 DateTime 值。 這個參數簡報時，其餘回應會包含目標快照，並包含清除頁面的先前快照之間已變更的頁面。
-   若要將這些變更套用至備份頁面 blob 使用[PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) 。
-   最後，備份頁面 blob 的快照，並將其儲存在備份的儲存空間的帳戶。

在下一步] 區段中，我們會說明的更多詳細資料您可以在如何維護備份磁碟使用累加快照複製的

## <a name="scenario"></a>案例

此區段中，我們會說明案例中，使用快照的虛擬機器磁碟自訂備份策略。

考慮 DS 數列 Azure VM 使用連接的進階版儲存 P30 磁碟。 稱為*mypremiumdisk* P30 磁碟會儲存於稱為*mypremiumaccount*進階版儲存帳戶。 稱為*mybackupstdaccount*標準儲存帳戶會用於儲存*mypremiumdisk*的備份。 我們想要保留的*mypremiumdisk*快照每 12 小時。

若要瞭解建立儲存的帳戶和磁碟，請參閱[關於 Azure 儲存體帳戶](storage-create-storage-account.md)。

若要瞭解備份 Azure Vm，請參閱[規劃 Azure VM 備份](../backup/backup-azure-vms-introduction.md)。

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>若要維持備份磁碟使用累加快照的步驟

如下所述的步驟將*mypremiumdisk*的快照，並維持在*mybackupstdaccount*備份。 備份會稱為*mybackupstdpageblob*標準頁面 blob。 備份頁面 blob 一律會反映*mypremiumdisk*相同的狀態為 [最後一次的快照。

1.  首先，建立備份頁面 blob 的進階版儲存磁碟。 若要這麼做，請*mypremiumdisk*快照稱為*mypremiumdisk_ss1*。
2.  複製為稱為*mybackupstdpageblob*頁面 blob mybackupstdaccount 這個快照集。
3.  擷取*mybackupstdpageblob*稱為*mybackupstdpageblob_ss1*，使用[快照 Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx)的快照，並將其儲存在*mybackupstdaccount*。
4.  在備份，建立另一個快照*mypremiumdisk*、 說出*mypremiumdisk_ss2*，並且儲存在*mypremiumaccount*。
5.  取得*mypremiumdisk_ss2*和*mypremiumdisk_ss1*， [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) *mypremiumdisk_ss2*上使用**prevsnapshot**參數設定為*mypremiumdisk_ss1*的時間戳記兩個快照之間的累加變更。 寫入*mybackupstdaccount*備份頁面 blob *mybackupstdpageblob*這些累加的變更。 如果的累加變更有已刪除的範圍，請他們必須從備份頁面 blob 清除。 使用[PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx)寫入備份頁面 blob 的累加的變更。
6.  需要備份頁面 blob *mybackupstdpageblob*，稱為*mybackupstdpageblob_ss2*的快照。 刪除先前快照*mypremiumdisk_ss1*進階版儲存帳戶。
7.  重複步驟 4-6 每個備份的視窗。 如此一來，您可以維護*mypremiumdisk*標準儲存帳戶的備份。

![備份磁碟使用累加快照](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>若要還原的磁碟從快照的步驟

如下所述的步驟會還原進階版磁碟， *mypremiumdisk*舊版的快照從備份儲存帳戶*mybackupstdaccount*。

1.  識別您想要還原的進階版磁碟的時間點。 例如，假設這是快照*mybackupstdpageblob_ss2*，儲存在備份儲存帳戶*mybackupstdaccount*中。
2.  在 [mybackupstdaccount，升階為新的備份基底頁面 blob *mybackupstdpageblobrestored*的快照*mybackupstdpageblob_ss2* 。
3.  需要稱為*mybackupstdpageblobrestored_ss1*還原的備份頁面 blob 的快照。
4.  複製還原的頁面 blob *mybackupstdpageblobrestored* *mybackupstdaccount*到*mypremiumaccount*為新的進階版磁碟*mypremiumdiskrestored*。
5.  需要*mypremiumdiskrestored*，要求*mypremiumdiskrestored_ss1*未來的累加備份的快照。
6.  若要還原的點 DS 數列 VM 磁碟*mypremiumdiskrestored* ，及卸離 VM 從舊*mypremiumdisk* 。
7.  開始前一節，還原的磁碟*mypremiumdiskrestored*，為備份頁面 blob 使用*mybackupstdpageblobrestored*所述的備份程序。

![從快照還原磁碟](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>後續步驟

深入瞭解建立 blob 的快照集及規劃 VM 備份基礎結構使用下列連結。

- [建立 Blob 的快照](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [規劃您 VM 備份基礎結構](../backup/backup-azure-vms-introduction.md)
