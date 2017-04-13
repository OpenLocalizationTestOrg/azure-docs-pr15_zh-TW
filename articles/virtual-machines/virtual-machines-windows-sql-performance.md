<properties
    pageTitle="效能最佳作法 SQL Server |Microsoft Azure"
    description="Microsoft Azure Vm 中的 SQL Server 效能最佳化提供最佳作法。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/07/2016"
    ms.author="jroth" />

# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>效能最佳作法 SQL Server Azure 虛擬機器中的商務連絡人

## <a name="overview"></a>概觀

本主題提供最佳化 Microsoft Azure 虛擬機器中的 SQL Server 效能的最佳作法。 在執行 SQL Server Azure 虛擬機器中的商務連絡人時，建議您繼續使用相同的資料庫效能調整適用於 SQL Server 內部部署伺服器的環境中的選項。 不過，公用的雲端關聯式資料庫的效能許多因素的虛擬機器，大小等資料磁碟的設定而定。

當建立 SQL Server 圖像，[請考慮佈建您 Vm Azure 入口網站中](virtual-machines-windows-portal-sql-server-provision.md)。 佈建後，在入口網站與資源管理員的 SQL Server Vm 實作所有這些最佳做法，包括儲存設定。

本文將焦點放在 Azure Vm 獲得*最佳*效能，用於 SQL Server。 如果您的工作量需求較低，您可能不需要下方所列的每個最佳化。 當您評估這些建議，請考慮您的效能需求和工作負載的模式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>快速的檢查清單

以下是快速核取清單的最佳效能的 SQL Server Azure 虛擬機器上的商務連絡人︰

|區域|最佳化|
|---|---|
|[虛擬記憶體大小](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series)或更新版本的 SQL 企業版。<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series)或更新版本的 SQL 標準和 Web 版本。|
|[儲存空間](#storage-guidance)|使用[進階版儲存空間](../storage/storage-premium-storage.md)。 標準的儲存空間只建議，為開發/測試。<br/><br/>保留[儲存帳戶](../storage/storage-create-storage-account.md)和 SQL Server VM 相同的區域。<br/><br/>停用 Azure[地理多餘的儲存空間](../storage/storage-redundancy.md)（地理複寫） 儲存的帳戶。|
|[磁碟](#disks-guidance)|使用 2 [P30 磁碟](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)（的記錄檔 1; 資料檔案和 TempDB 1） 的最小值。<br/><br/>避免使用的作業系統或暫時磁碟資料庫儲存空間或記錄。<br/><br/>啟用閱讀裝載資料檔案和 TempDB 磁碟上快取。<br/><br/>不會啟用主機服務記錄檔的磁碟上的快取。<br/><br/>重要事項︰ 停止變更 Azure VM 磁碟的快取設定時的 SQL Server 服務。<br/><br/>若要取得提高的 IO 處理量的多個 Azure 資料磁碟等量磁碟區。<br/><br/>格式化文件的配置大小。|
|[I/O](#io-guidance)|啟用資料庫頁面壓縮。<br/><br/>啟用立即檔案初始化資料檔案。<br/><br/>限制或停用自動成長資料庫。<br/><br/>停用自動壓縮資料庫。<br/><br/>將資料磁碟，包括系統資料庫中的所有資料庫。<br/><br/>將 SQL Server 錯誤記錄檔和追蹤檔案目錄移至 [資料磁碟。<br/><br/>設定預設備份與資料庫檔案的位置。<br/><br/>啟用鎖定的頁面。<br/><br/>適用於 SQL Server 效能修正。|
|[特定功能](#feature-specific-guidance)|備份直接 blob 儲存體。|

如需有關*如何*以及*為何*，讓這些最佳化的詳細資訊，請檢閱的詳細資料和下列各節中提供的指導方針。

## <a name="vm-size-guidance"></a>VM 大小指南

對於效能的機密應用程式，我們建議您使用下列[虛擬機器大小](virtual-machines-windows-sizes.md)︰

- **SQL Server 企業版**︰ DS3 或更新版本

- **SQL Server 標準 」 和 「 Web 版本**︰ DS2 或更新版本


## <a name="storage-guidance"></a>儲存空間的指導方針

DS 數列 （以及 DSv2 數列和 GS 數列） Vm 支援[進階版儲存空間](../storage/storage-premium-storage.md)。 所有生產負載都建議進階版儲存空間。

> [AZURE.WARNING] 標準的儲存空間有不同的延遲時間及頻寬，而且只建議，為開發/測試負載。 生產負載應該使用進階版儲存空間。

此外，建議您在相同的資料中心，為您以減少傳輸的延遲的 SQL Server 虛擬機器中建立 Azure 儲存體帳戶。 建立時儲存帳戶，請停用地理複寫為不保證一致寫入順序在多個磁碟。 不過，請考慮設定兩個 Azure 資料中心之間的 SQL Server 損毀復原技術。 如需詳細資訊，請參閱[可用性和損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-high-availability-dr.md)。

## <a name="disks-guidance"></a>磁碟指南

Azure VM 有三個主要的磁碟類型︰

- **OS 磁碟**︰ 平台 Azure 虛擬機器建立後，會將至少有一個磁碟 （標示為**C**磁碟機） 附加至作業系統磁碟 VM。 此磁碟是儲存為網頁 blob 儲存體中 VHD。
- **暫存磁碟**︰ Azure 虛擬機器包含稱為暫時磁碟的另一個磁碟 (標示為**D**︰ 磁碟機)。 這是可以使用可用的節點的磁碟。
- **資料磁碟**︰ 您也可以附加到您的虛擬機器額外的磁碟，為資料磁碟，而這些會為頁面 blob 儲存在儲存空間。

下列各節說明使用這些不同的磁碟的建議。

### <a name="operating-system-disk"></a>作業系統磁碟

作業系統磁碟是 VHD 的開機，並裝載為執行作業系統的版本，並標示為**C**磁碟機。

快取原則作業系統磁碟上的預設值為**讀/寫**。 對於效能機密應用程式，我們建議您，而非系統磁碟使用資料磁碟。 請參閱下面的資料磁碟上。

### <a name="temporary-disk"></a>暫存磁碟

標示為**D**的暫存磁碟機︰ 磁碟機，並不會保留 Azure blob 儲存體。 不會儲存您的使用者資料庫檔案或使用者交易記錄檔上**D**︰ 磁碟機。

D 數列、 Dv2 系列，及 G 數列 Vm，這些 Vm 的暫時磁碟機會是 SSD 為基礎。 如果您的工作量，讓您大量使用 TempDB （例如的暫存物件或複雜的聯結），請儲存 TempDB **D**磁碟機上可能會產生更高的 TempDB 處理量，左下 TempDB 延遲。

支援進階版儲存空間 （DS 數列、 DSv2 系列和 GS 數列） 的 Vm，建議將 TempDB 及/或緩衝集區副檔名儲存啟用讀快取支援進階版儲存空間的磁碟上。 有一個例外這個建議。如果您 TempDB 使用量寫入密切，您可以藉由將 TempDB 儲存在本機**D**磁碟機，也是 SSD-根據這些機器大小來達到較高的效能。

### <a name="data-disks"></a>資料磁碟

- **使用資料和記錄檔的資料磁碟**︰ 最少使用 2 進階版存放[P30 磁碟](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)其中一個磁碟含有記錄檔，另一個包含的資料檔案和 TempDB。

- **具**︰ 更多傳輸量，您可以新增其他資料磁碟，並使用具。 若要判斷資料磁碟數目，您需要分析 IOPS 適用於您的資料和記錄磁碟的數目。 該資訊，請參閱資料表上 IOPS 每[虛擬記憶體大小](virtual-machines-windows-sizes.md)和磁碟的大小，以下列文章︰[使用進階版的儲存空間的磁碟](../storage/storage-premium-storage.md)。 使用下列這些指導方針︰

    - Windows 8/Windows Server 2012 或更新版本，使用[儲存空間](https://technet.microsoft.com/library/hh831739.aspx)。 將 OLTP 工作負載的 64 KB 和 256 KB 的資料倉儲負載避免效能的影響，因為分割對齊錯誤等量大小。 此外，設定欄數 = 硬碟的數目。 若要設定 8 個以上的磁碟儲存空間，您必須使用 PowerShell (而非伺服器管理員 UI) 明確設定以符合磁碟數目的欄數。 如需有關如何設定[儲存空間](https://technet.microsoft.com/library/hh831739.aspx)的詳細資訊，請參閱[在 Windows PowerShell 中的儲存空間空格 Cmdlet](https://technet.microsoft.com/library/jj851254.aspx)

    - Windows 2008 R2 或更早版本，您可以使用動態磁碟 （OS 等量磁碟區），而且等量大小一律是 64 KB。 注意︰ 此選項功能已遭取代到 Windows 8/Windows Server 2012。 資訊，請參閱在[Windows 存放裝置管理的 API 轉換後虛擬磁碟服務](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)的支援陳述式。

    - 如果您的工作量大量的記錄，而不需要專用的 IOPs，您可以設定僅在單一儲存集區。 否則，建立兩個一個記錄檔，另一個儲存資料庫的資料檔案和 TempDB 的儲存集區。 決定根據您載入的期望每個儲存集區相關聯的磁碟數目。 請記住，不同的 VM 大小可讓不同的數字的附加資料磁碟。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。

    - 如果您不使用進階版儲存空間 （開發/測試案例），建議新增您的[虛擬記憶體大小](virtual-machines-windows-sizes.md)所支援的資料磁碟的最大數目，並使用具。

- **快取原則**︰ 進階版儲存的資料磁碟，啟用僅裝載您的資料檔案和 TempDB 資料磁碟上的讀取快取。 如果您不使用進階版儲存空間，請不要在任何資料磁碟上任何快取。 指示設定磁碟快取，請參閱下列主題︰[設定 AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847)及[設定 AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。

    >[AZURE.WARNING] 變更以避免發生任何資料庫損毀的 Azure VM 磁碟快取設定時，請停止 SQL Server 服務。

- **NTFS 配置單位大小**︰ 時格式化資料磁碟，建議您的資料和記錄檔以及 TempDB 使用 64 KB 配置單位大小。

- **磁碟管理的最佳作法**︰ 時移除資料光碟或變更其快取類型，停止 SQL Server 服務期間變更。 當 OS 磁碟上的快取設定便會變更時，Azure 會停止 VM、 變更快取類型]，並重新啟動 VM。 當資料磁碟的快取設定變更時，並不會停止 VM，但資料磁碟是中斷 VM 期間變更，然後再重新附加。

    >[AZURE.WARNING] 若要停止作業期間的 SQL Server 服務失敗，可能會導致資料庫損毀。

## <a name="io-guidance"></a>I/O 指南

- 當您平行處理您的應用程式和要求時，會達成進階版儲存最佳的結果。 進階版儲存空間的設計的案例 IO 佇列深度大於 1，因此 （即使是使用大量的儲存空間），您會看到幾乎不單一階層式循序要求的效能提升。 例如，這可能會影響效能分析工具，例如 SQLIO 的單一階層式測試結果。

- 請考慮使用[資料庫頁面壓縮](https://msdn.microsoft.com/library/cc280449.aspx)時，可以協助改善 I/O 大量工作負載的效能。 不過，資料壓縮可能會增加 CPU 消耗資料庫伺服器上。

- 考慮啟用立即檔案初始化減少所需的初始檔案配置的時間。 若要利用立即檔案初始化，您可以授與 SE_MANAGE_VOLUME_NAME SQL Server (MSSQLSERVER) 服務帳戶，並將其新增至**執行大量維護工作**的安全性原則。 如果您使用 Azure SQL Server 的平台圖像，就不會**執行大量維護工作**安全性原則新增預設的服務帳戶 (NT Service\MSSQLSERVER)。 換句話說，立即檔案初始化不會啟用中的 SQL Server Azure 平台圖像。 將 SQL Server 服務帳戶新增至 [**執行大量維護工作**的安全性原則之後, 重新啟動 SQL Server 服務。 可能有使用此功能的安全性考量。 如需詳細資訊，請參閱[資料庫檔案初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

- **自動成長**會被視為只非預期的等比級數應變計劃。 不管理每天成長與您資料和記錄等比級數]。 使用自動成長時，如果預先增加檔案大小切換。

- 請確定**自動壓縮**已停用，避免不必要的負荷的負面影響效能。

- 將資料磁碟，包括系統資料庫中的所有資料庫。 如需詳細資訊，請參閱[移動系統資料庫](https://msdn.microsoft.com/library/ms345408.aspx)。

- 將 SQL Server 錯誤記錄檔和追蹤檔案目錄移至 [資料磁碟。 這可以完成 SQL Server 組態管理員中，以滑鼠右鍵按一下您的 SQL Server 執行個體]，選取屬性。 錯誤記錄檔] 及 [追蹤檔案設定可以變更的 [**啟動參數**] 索引標籤。 在 [**進階**] 索引標籤中指定傾印的目錄。 下圖顯示 [錯誤記錄檔啟動參數查詢的位置。

    ![SQL 錯誤記錄檔的螢幕擷取畫面](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- 設定預設備份與資料庫檔案的位置。 本主題中，請使用建議和伺服器的 [內容] 視窗中進行變更。 如需相關指示，請參閱[檢視或變更預設資料和記錄檔 (SQL Server Management Studio) 的位置](https://msdn.microsoft.com/library/dd206993.aspx)。 下圖示範位置進行這些變更。

    ![SQL 資料記錄和備份檔案](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- 啟用鎖定的頁面，以減少 IO 和任何分頁活動。 如需詳細資訊，請參閱[讓記憶體選項 (Windows) 中鎖定](https://msdn.microsoft.com/library/ms190730.aspx)。

- 如果您正在執行 SQL Server 2012，請安裝 Service Pack 1 積存更新 10。 當您將 SQL Server 2012 中的暫時 table 陳述式執行選取時，此更新包含 i/o 的效能不佳的修正。 資訊，請參閱[知識庫文件](http://support.microsoft.com/kb/2958012)。

- 請考慮壓縮時的 Azure 轉接內部/外部的任何資料檔案。

## <a name="feature-specific-guidance"></a>特定功能的指引

某些部署可能獲得額外的效能優點使用更多進階的組態技巧。 下列清單會醒目提示某些 SQL Server 功能，可協助您獲得更佳的效能︰

- **備份到 Azure 儲存體**︰ 在執行備份的 SQL Server Azure 虛擬機器中執行時，您可以使用[SQL Server 備份的 url](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能可使用的 SQL Server 2012 SP1 CU2 開始著手，建議，為備份到附加的資料磁碟。 當您備份/還原從 Azure 儲存空間，請遵循在[SQL Server 備份 URL 的最佳作法和疑難排解，並還原的備份儲存在 Azure 儲存體中](https://msdn.microsoft.com/library/jj919149.aspx)提供的建議。 您也可以將這些備份，使用 [[自動備份的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-classic-sql-automated-backup.md)。

    SQL Server 2012 之前，您可以使用[SQL Server Azure 工具備份](https://www.microsoft.com/download/details.aspx?id=40740)。 這項工具可協助來增加使用多個備份條紋目標的備份處理量。

- **Azure 中的 SQL Server 資料檔**︰ 這項新功能， [Azure 中的 SQL Server 資料檔](https://msdn.microsoft.com/library/dn385720.aspx)，是使用 SQL Server 2014 開始著手。 執行 SQL Server Azure 中的資料檔案示範相當的效能特性，例如使用 Azure 資料磁碟。

## <a name="next-steps"></a>後續步驟

如果您想要深入探索 SQL Server，進階版的儲存空間，請參閱[使用虛擬機器上的 SQL server Azure 進階版儲存空間](virtual-machines-windows-classic-sql-server-premium-storage.md)。

安全性最佳作法，請參閱[SQL Server Azure 虛擬機器中的商務連絡人的安全性考量](virtual-machines-windows-sql-security.md)。

檢閱在[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)其他 SQL Server 虛擬機器主題。
