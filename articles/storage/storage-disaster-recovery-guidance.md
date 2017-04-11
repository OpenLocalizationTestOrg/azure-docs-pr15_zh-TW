<properties
    pageTitle="如何在 Azure 儲存體中斷時 |Microsoft Azure"
    description="如何在 Azure 儲存體中斷時"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>如果 Azure 儲存體過時發生時該怎麼辦

在 Microsoft，我們使用實，請確定仍可以使用我們的服務。 有時候，強制超出我們控制項影響我們在一或多個區域內導致非預期的服務中斷的方式。 若要可協助您處理這些少見的項目，我們會提供下列高層級的指導方針，Azure 儲存體服務。

## <a name="how-to-prepare"></a>如何準備 

這是關鍵的每一位客戶，準備自己復原計劃。 通常是從儲存中斷復原的投入比涉及作業人員和自動化程序，以重新啟動您的應用程式中的運作狀態。 請參閱 Azure 下建立您自己的損毀復原計劃的文件︰

-   [損毀修復和 Azure 應用程式的可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Azure 恢復技術指南](../resiliency/resiliency-technical-guidance.md)

-   [Azure 網站復原服務](https://azure.microsoft.com/services/site-recovery/)

-   [Azure 儲存複寫](storage-redundancy.md)

-   [Azure 備份服務](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>如何偵測 

建議的方式來判斷 Azure 服務的狀態是訂閱[Azure 服務健康狀況儀表板](https://azure.microsoft.com/status/)。

## <a name="what-to-do-if-a-storage-outage-occurs"></a>如果儲存過時發生時該怎麼辦

如果在一或多個區域一或多個儲存服務暫時無法使用，有兩個選項，供您。 如果您的需要立即存取您的資料，請考慮選項 2。

### <a name="option-1-wait-for-recovery"></a>選項 1︰ 等待復原

在此情況下，不不需要任何動作，在您的組件。 我們正在努力還原 Azure 服務可用性。 您可以監視[Azure 服務健康狀況儀表板](https://azure.microsoft.com/status/)上的服務狀態。

### <a name="option-2-copy-data-from-secondary"></a>選項 2︰ 從次要複製資料

如果您選擇[讀取權限地理多餘的儲存空間 (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) （建議使用） 的儲存空間帳戶，則必須讀取權限至您的資料從第二個區域。 您可以使用工具，例如[AzCopy](storage-use-azcopy.md)、 [PowerShell 的 Azure](storage-powershell-guide-full.md)與[Azure 資料移動文件庫](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)至資料複製到另一個儲存在 unimpacted 區域中，第二個區域，然後指向您的儲存空間帳戶兩者的應用程式讀取和寫入的可用性。

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>預期狀況如果發生儲存容錯移轉

如果您選擇[地理多餘的儲存空間 (GRS)](storage-redundancy.md#geo-redundant-storage)或[讀取權限地理多餘的儲存空間 (RA GRS)](storage-redundancy.md#read-access-geo-redundant-storage) （建議使用），Azure 儲存體會將資料保留長期在兩個區域 （主要和次要）。 在兩個區域中，Azure 儲存體持續維護您的資料之多份複本。

當地區損毀會影響您的主要區域時，我們先嘗試還原該區域中的服務。 取決於性質損毀，其的影響，在某些少見的情況下，我們可能無法還原的主要區域中。 此時，我們會執行地理容錯移轉。 跨地區資料複製為非同步處理其中涉及延遲，因此尚未複寫到第二個區域的變更可能會遺失。 您可以查詢[「 上次同步處理 」 的時間您儲存的帳戶](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)取得複寫狀態詳細資料。

許多儲存空間地理容錯體驗相關要點︰

-   儲存地理容錯將只會觸發 Azure 儲存體小組-不需要的客戶動作。

-   現有的儲存空間服務端點的二進位大型物件、 表格、 佇列和檔案會維持不變後移轉後;DNS 項目必須更新，以切換到第二個區域的主要區域。

-   之前和地理容錯移轉時，您不能寫入存取權，由於損壞的影響，因此您儲存的帳戶，但如果您儲存的帳戶已設定為 RA GRS 時，您可以仍在次要從讀取。

-   地理容錯移轉完成後，然後傳播 DNS 變更，會繼續您已讀取和寫入存取您儲存的帳戶。 您可以查詢[」 最後一個地理容錯移轉 」 的時間您儲存的帳戶](https://msdn.microsoft.com/library/azure/ee460802.aspx)以取得更多詳細資料。

-   後移轉後，您儲存的帳戶會完全運作，但在 「 降級 」 的狀態，時，事實上沒有地理複寫儘可能使用獨立的區域。 減輕風險，我們會還原原始的主要區域，然後執行地理回復還原原始的狀態。 如果無法修復原始的主要區域，我們會配置其他的第二個區域。
如需 Azure 儲存體地理複寫的基礎結構的詳細資訊，請參閱瞭解[重複選項和 RA GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)的儲存空間小組部落格上的文件。

##<a name="best-practices-for-protecting-your-data"></a>保護您的資料的最佳作法

有一些建議定期備份儲存資料的方法。

-   VM 磁碟 – 使用[Azure 備份服務](https://azure.microsoft.com/services/backup/)使用 Azure 虛擬機器 VM 磁碟備份。

-   封鎖 blob – 建立[快照](https://msdn.microsoft.com/library/azure/hh488361.aspx)的每一個區塊 blob 或複製到另一個儲存體 blob 帳戶使用[AzCopy](storage-use-azcopy.md)、 [PowerShell 的 Azure](storage-powershell-guide-full.md)或[Azure 資料移動文件庫](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)的另一個區域中。

-   表格 – 使用[AzCopy](storage-use-azcopy.md)表格資料匯出到另一個區域中的其他儲存帳戶。

-   檔案 – 使用[AzCopy](storage-use-azcopy.md)或[PowerShell 的 Azure](storage-powershell-guide-full.md)將檔案複製到另一個區域中的其他儲存帳戶。
