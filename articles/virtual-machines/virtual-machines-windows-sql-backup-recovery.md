<properties
    pageTitle="備份及還原的 SQL Server |Microsoft Azure"
    description="說明執行 Azure 虛擬機器上的商務連絡人的 SQL Server 資料庫的備份與還原考量。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>備份與還原 Azure 虛擬機器中的 SQL server

## <a name="overview"></a>概觀

備份 SQL Server 資料庫中的資料是在防止資料遺失應用程式或使用者錯誤而策略很重要的一部分。 同樣適用於 SQL Server 執行上 Azure 虛擬機器 (Vm)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

用於 SQL Server Azure Vm 中執行，您可以使用原生備份，並還原備份檔案的目的使用連接的磁碟的技巧。 然而，有可附加至 Azure 虛擬機器，根據[的虛擬機器大小](virtual-machines-linux-sizes.md)的磁碟數目上限。 此外，還有的考量磁碟管理負擔。

從 SQL Server 2014 開始，您可以在簡報中備份及還原至 Microsoft Azure Blob 儲存體。 SQL Server 2016 也會提供此選項的增強功能。 此外，用於儲存在 Microsoft Azure Blob 儲存體中的資料庫檔案，SQL Server 2016 提供的選項幾乎瞬間完成的效能的備份，以及使用 Azure 快照的快速還原。 本文提供這些選項的概觀，可以在[SQL Server 備份與還原與 Microsoft Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148.aspx)找到其他資訊。

>[AZURE.NOTE] 如很大的資料庫備份選項的說明，請參閱[多 Tb SQL Server 資料庫備份策略的 Azure 虛擬機器](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx)。

以下各節包含不同版本的 SQL Server Azure 虛擬機器中支援的特定資訊。

## <a name="sql-server-virtual-machines"></a>SQL Server 虛擬機器

Azure 虛擬機器上執行時您 SQL Server 執行個體，您的資料庫檔案已存在於資料磁碟 Azure 中。 Azure Blob 儲存體中的 [即時這些磁碟。 因此的原因備份您的資料庫及方法您需要變更稍微。 請考慮下列項目。 

- 您不再需要執行資料庫備份提供保護硬體或媒體失敗，因為 Microsoft Azure 提供此保護 Microsoft Azure 服務的一部分。

- 您仍需要執行提供對使用者的錯誤，或是保存之用，法律原因或管理用途的保護資料庫備份。

- 您可以直接在 Azure 中儲存的備份檔案。 如需詳細資訊，請參閱下列各節提供不同版本的 SQL Server 的相關指引。

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 支援的 SQL Server 2014 中找到的[備份與還原 Azure blob](https://msdn.microsoft.com/library/jj919148.aspx)功能。 不過，它也包含下列增強功能︰

| 2016 增強功能               | 詳細資料                          |
|---------------------|-------------------------------|
| **等量**              | 備份到 Microsoft Azure blob 儲存體，SQL Server 2016 支援備份到多個 blob 啟用備份大型的資料庫，上限為 12.8 TB。      |
| **快照備份**                | 透過 Azure 快照，使用 SQL Server 檔案快照備份提供幾乎瞬間完成的效能的備份與使用 Azure Blob 儲存體服務來儲存資料庫檔案的快速還原。 此功能可讓您以簡化備份和還原原則。 檔案快照備份也支援還原時間點。 如需詳細資訊，請參閱[Azure 中的資料庫檔案的快照備份](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx)。   |
| **受管理的備份排程**            | SQL Server 管理備份] 可 Azure 現在支援自訂的排程。 如需詳細資訊，請參閱[SQL Server 管理備份到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)。   |

教學課程中的 SQL Server 2016 使用 Azure Blob 儲存體時的功能，請參閱[教學課程︰ 使用 SQL Server 2016 資料庫中的 Microsoft Azure Blob 儲存體服務](https://msdn.microsoft.com/library/dn466438.aspx)。

## <a name="sql-server-2014"></a>SQL Server 2014

SQL Server 2014 包含下列增強功能︰

1. **備份及還原到 Azure**:

 - *Url 的 SQL Server 備份*現在會在 SQL Server Management Studio 中有支援。 使用 [SQL Server Management Studio 中的 [備份或還原工作或維護計劃精靈時，現在使用備份到 Azure 選項。 如需詳細資訊，請參閱[SQL Server 備份的 url](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)。
 - *SQL Server 管理備份到 Azure*有啟用自動備份管理的新功能。 這是自動執行的 SQL Server 2014 Azure 電腦上執行的執行個體的備份管理尤其有用。 如需詳細資訊，請參閱[SQL Server 管理備份到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)。
 - *自動備份*提供額外的自動化 Azure 中的 SQL Server vm 自動啟用所有現有和新資料庫的*SQL Server 管理備份] 可 Azure* 。 如需詳細資訊，請參閱[自動備份的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-automated-backup.md)。
 - 如需 Azure SQL Server 2014 備份的所有選項的概觀，請參閱[SQL Server 備份與還原與 Microsoft Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)。

1. **加密**︰ SQL Server 2014 支援的資料建立備份時加密。 支援幾個加密演算法和使用 osf 憑證或非對稱金鑰。 如需詳細資訊，請參閱[備份加密](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx)。

## <a name="sql-server-2012"></a>SQL Server 2012

SQL Server 備份與還原 SQL Server 2012 中的詳細資訊，請參閱[備份與還原的 SQL Server 資料庫 (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)。

您可以開始在 SQL Server 2012 SP1 積存更新 2，備份和還原從 Azure Blob 儲存體服務。 這個增強功能可在 SQL Server Azure 虛擬機器或內部部署執行個體上執行備份的 SQL Server 資料庫。 如需詳細資訊，請參閱[SQL Server 備份與還原 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)。

使用 Azure Blob 儲存體服務的優點包括略過的 16 磁碟限制附加磁碟，輕鬆管理，直接可用性移轉或損毀修復用途 Azure 虛擬機器上執行的 SQL Server 執行個體的另一個執行個體或內部部署執行個體的備份檔案的能力。 若要使用的 SQL Server 備份 Azure blob 儲存體服務的優勢的完整清單，請參閱*優惠*] 區段中的[SQL Server 備份與還原 Azure Blob 儲存體服務](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)。

最佳作法建議及疑難排解資訊，請參閱[備份及還原的最佳作法 （Azure Blob 儲存體服務）](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx)。

## <a name="sql-server-2008"></a>SQL Server 2008

SQL Server 備份與還原在 SQL Server 2008 R2 中的，請參閱[備份和還原 SQL Server (SQL Server 2008 R2) 的資料庫](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)。

SQL Server 備份與還原 SQL Server 2008，請參閱[備份和還原 SQL Server (SQL Server 2008) 的資料庫](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)。

## <a name="next-steps"></a>後續步驟

如果您打算部署的 SQL Server Azure VM 中，您可以尋找佈建指南下列教學課程︰[佈建 SQL Server 虛擬機器上 Azure 與 Azure 資源管理員](virtual-machines-windows-portal-sql-server-provision.md)。

雖然備份與還原，都可以用來移轉您的資料，有可能會更容易資料移轉路徑上 Azure VM SQL Server。 完整的討論的移轉選項與建議，請參閱[移轉 Azure VM 上 SQL Server 資料庫](virtual-machines-windows-migrate-sql.md)。

檢閱其他[資源執行 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
