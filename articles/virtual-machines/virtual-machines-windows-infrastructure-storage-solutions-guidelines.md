<properties
    pageTitle="儲存空間方案指導方針 |Microsoft Azure"
    description="深入了解部署 Azure 基礎結構服務中的儲存空間方案的重要的設計及實作方針。"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="storage-infrastructure-guidelines"></a>儲存基礎結構的指導方針

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文著重於了解儲存空間的需求和設計考量達到最佳的虛擬機器 (VM) 效能。


## <a name="implementation-guidelines-for-storage"></a>實作準則的儲存空間

決策︰

- 您需要使用您的工作量標準或進階版的儲存空間嗎？
- 您需要建立磁碟大於 1023 GB 具嗎？
- 您需要以獲得最佳的 I/O 效能，為您的工作量具嗎？
- 哪些設定的儲存空間帳戶需要主控您的 IT 工作量或基礎結構嗎？

工作︰

- 檢查您的部署及規劃的適當的數字與儲存的帳戶類型的應用程式的 I/O 要求。
- 建立一組儲存帳戶使用您的命名慣例。 您可以使用 PowerShell 的 Azure 或入口網站。


## <a name="storage"></a>儲存空間

Azure 儲存體屬於索引鍵的部署及管理虛擬機器 (Vm) 和應用程式。 Azure 儲存為提供服務儲存檔案資料、 非結構化的資料，以及郵件，而且也支援 Vm 基礎結構的一部分。

有適用於支援 Vm 的兩種類型的儲存空間帳戶︰

- 標準儲存帳戶授與您存取 blob 儲存體 （用來儲存 Azure VM 磁碟），資料表儲存體與佇列中儲存檔案儲存空間。
- [進階版儲存](../storage/storage-premium-storage.md)帳戶傳送 I/O 大量負載，例如 SQL Server AlwaysOn 叢集支援高效能、 低延遲磁碟。 進階版存放目前支援 Azure VM 磁碟才。

Azure 建立 Vm 作業系統磁碟、 磁碟暫存與零個或多個選用資料磁碟。 作業系統磁碟和資料磁碟是頁面 Azure blob，而暫存的磁碟儲存在本機電腦所在的節點。 請注意設計僅用於此暫存的磁碟非持續資料為 VM 可能也會移轉主機期間進行的維修作業事件之間的應用程式時。 暫時磁碟上的任何資料就會遺失。

若要確保您的資料對非計劃中進行的維修作業或硬體失敗受保護的基礎 Azure 儲存體環境提供持續性及高可用性。 在設計 Azure 儲存體環境時，您可以選擇複製 VM 儲存空間︰

- 在本機中指定的 Azure 資料中心
- 透過 Azure 指定區域內的資料中心
- 跨不同區域的 Azure 資料中心

您可以瞭解[更多有關可用性的複寫選項](../storage/storage-introduction.md#replication-for-durability-and-high-availability)。

作業系統及資料磁碟有大小上限為 1023 gb。 Blob 的大小上限為 1024 GB，且必須包含 VHD 檔案 （GB 為 1024年<sup>3</sup>個位元組） 的中繼資料 （頁尾）。 您可以使用在 Windows Server 2012 中的儲存空間，來共用共同資料磁碟邏輯區大於 1023 GB 展示您 vm 超過此限制。

有一些延展性限制設計 Azure 儲存體部署時，請參閱[Microsoft Azure 訂閱和服務限制，配額和限制式](azure-subscription-service-limits.md#storage-limits)如需詳細資訊。 請參閱[Azure 儲存體延展性和效能目標](../storage/storage-scalability-targets.md)。

應用程式儲存空間，您可以儲存非結構化的物件的資料，例如文件、 圖像、 備份、 設定資料、 記錄等。 使用 blob 儲存體。 而不是您的應用程式虛擬磁碟附加至 VM 撰寫應用程式可寫入直接 Azure blob 儲存體。 Blob 儲存體也會提供選項，根據您的可用性需求和成本限制式的[作用中且製作出酷炫的儲存空間層](../storage/storage-blob-storage-tiers.md)。


## <a name="striped-disks"></a>條紋的磁碟
除了讓您可以在許多情況下，建立磁碟大於 1023 GB，用資料磁碟等量，進而提升效能允許多個 blob 移到最下層單一的大量的儲存空間。 與等量，撰寫及讀取單一邏輯磁碟上的資料所需的 I/O 進行平行。

Azure 會採用資料磁碟數] 及 [可用的圖片，請根據虛擬記憶體大小的頻寬限制。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。

如果您使用 Azure 資料磁碟具，請考慮下列方針︰

- 資料磁碟一律採用的最大值 (1023 GB)。
- 附加虛擬記憶體大小所允許的最大資料磁碟。
- 使用儲存空間。
- 避免使用 Azure 資料磁碟快取選項 (快取原則 = 無)。

如需詳細資訊，請參閱[儲存空間-設計的效能](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx)。


## <a name="multiple-storage-accounts"></a>多個儲存帳戶

設計 Azure 儲存體環境時，您可以使用多個儲存帳戶的 Vm 部署增加。 這可協助出 I/O 分散 Azure 儲存體基礎若要維持最佳效能 Vm 和應用程式。 設計要部署的應用程式時，請考慮 I/O 的需求，每個 VM 並查看這些 Vm 平衡 Azure 儲存體帳戶之間。 請嘗試以避免分組要求 Vm 中的只有一個或兩個儲存帳戶的所有高 I/O。

不同的 Azure 儲存體選項的 I/O 功能的詳細資訊，以及一些建議最大值，請參閱[Azure 儲存體延展性和效能目標](../storage/storage-scalability-targets.md)。


## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 