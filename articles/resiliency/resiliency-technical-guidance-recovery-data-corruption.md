<properties
   pageTitle="從資料損毀或不慎刪除復原的恢復技術指南 |Microsoft Azure"
   description="瞭解如何復原資料損毀的資料或不小心刪除資料至和設計，可用性故障容錯應用程式，以及損毀修復規劃文件"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Azure 恢復技術指南︰ 從資料損毀或不慎刪除復原

有計劃強大的業務連續性計劃的一部分，如果您的資料損毀或不小心刪除。 以下是之後已損毀或不小心刪除，因為應用程式錯誤或運算子錯誤資料的復原的相關資訊。

##<a name="virtual-machines"></a>虛擬機器

若要從應用程式錯誤或不慎刪除保護 Azure 虛擬機器 （有時稱為基礎結構為-的-服務 Vm），請使用[Azure 備份](https://azure.microsoft.com/services/backup/)。 Azure 備份可在多個 VM 磁碟一致的備份的建立。 此外，備份保存庫，可將複寫跨提供復原地區影響的區域。

##<a name="storage"></a>儲存空間

透過意外或非預期刪除、 更新、 等等，請注意，Azure 儲存體可提供資料透過自動化複本，而這不會阻止您的應用程式碼 （或開發人員/使用者） 損毀的資料。 維護面對應用程式或使用者錯誤的資料逼真度需要更多進階的技巧，例如將資料複製到次要存放位置的稽核記錄。 開發人員可以利用 blob 的[快照集功能](https://msdn.microsoft.com/library/azure/ee691971.aspx)，可以建立的 blob 內容的唯讀時間點快照集。 這可以用於做為基礎的資料逼真度解決方案 Azure 儲存體二進位大型物件。

###<a name="blob-and-table-storage-backup"></a>Blob 和表格儲存備份

二進位大型物件和表格高度長期時，它們永遠代表目前狀態的資料。 復原不想要的修改或刪除資料可能會要求您還原成先前的狀態的資料。 這可以達成利用 Azure 儲存和保留時間點所提供的功能。

Azure Blob 的您可以執行使用[blob 快照集功能](https://msdn.microsoft.com/library/ee691971.aspx)的時間點備份。 針對每個快照，您只會儲存在 blob 的差異，因為上次快照狀態所需儲存空間。 快照是取決於其為基礎，原始 blob 存在，因此建議您複製到另一個 blob 或甚至其他的儲存空間帳戶作業。 如此一來，可確保正確意外刪除保護備份資料。 Azure 資料表中，您可以讓時間點複製到不同的資料表或 Azure blob。 更詳細的指南和範例執行應用程式層級備份的資料表，可以在這裡找到二進位大型物件︰

  * [保護您對錯誤的資料表](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [保護您的二進位大型物件對錯誤](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>資料庫

有適用於 Azure SQL 資料庫的多個[業務連續性](../sql-database/sql-database-business-continuity.md)（備份，還原） 選項。 使用[資料庫複製](../sql-database/sql-database-copy.md)功能，或[匯出](../sql-database/sql-database-export.md)及[匯入](https://msdn.microsoft.com/library/hh710052.aspx)SQL Server bacpac 檔案，可以複製資料庫。 資料庫複製提供交易一致的結果，不會 bacpac （透過匯入/匯出服務）。 兩個選項都佇列型服務資料中心，以執行和其目前不提供時間來完成 SLA。

>[AZURE.NOTE]資料庫複製] 和 [匯入/匯出] 選項會將載入重要程度在來源資料庫。 他們可以觸發資源競爭或節流事件。

###<a name="sql-database-backup"></a>SQL 資料庫備份

Microsoft Azure SQL 資料庫的時間點備份被達成[複製 Azure SQL 資料庫](../sql-database/sql-database-copy.md)。 您可以使用這個命令來建立資料庫交易一致的複本，相同的邏輯資料庫伺服器上或到不同的伺服器。 在任一情況，資料庫複製是完整的功能及在來源資料庫的完全獨立。 您建立的每個複本代表一個時間點復原選項。 您可以重新命名的資料庫與來源資料庫的名稱，完全復原的資料庫狀態。 或者，您可以使用的查詢，從新的資料庫復原特定的資料子集。 如需關於 SQL 資料庫的詳細資訊，請參閱[Azure SQL 資料庫的業務連續性的概觀](../sql-database/sql-database-business-continuity.md)。

###<a name="sql-server-on-virtual-machines-backup"></a>[虛擬機器備份的 SQL Server

Azure 基礎結構 （通常稱為 IaaS 或 IaaS Vm） 服務虛擬機器所做的 SQL Server，有兩個選項︰ 傳統的備份與記錄傳送。 使用傳統的備份可讓您在時間中還原的特定點，但修復程序速度很慢。 還原備份傳統需要初始完整備份，開始著手，然後再套用之後所做的任何備份。 第二個選項可設定記錄傳送工作階段，延遲的記錄檔備份還原 （例如，藉由兩個小時）。 這會提供復原從主要上所做的錯誤] 視窗。

##<a name="other-azure-platform-services"></a>其他平台 Azure 服務

某些 Azure 平台服務會將資訊儲存在使用者控制儲存帳戶或 Azure SQL 資料庫。 如果帳戶或儲存空間資源可刪除或損毀，這可能會造成錯誤的服務。 在下列情況下，會維持可讓您重新建立這些資源，如果被刪除或損毀的備份。

Azure 網站和 Azure 行動服務，您必須備份及維護相關聯的資料庫。 如需 Azure 媒體服務與虛擬機器中，您必須維護相關聯的 Azure 儲存體帳戶和帳戶中的所有資源。 例如，虛擬機器中，您必須備份並管理 VM 磁碟 Azure blob 儲存體中。

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>不慎刪除資料損毀的檢查清單

##<a name="virtual-machines-checklist"></a>虛擬機器檢查清單

  1. 請檢閱這份文件的虛擬機器一節。
  2. 備份及維護 VM 磁碟 Azure 備份 （或您自己的備份系統使用 Azure blob 儲存體和 VHD 快照集）。

##<a name="storage-checklist"></a>儲存空間的檢查清單

  1. 請檢閱這份文件的 [儲存] 區段。
  2. 定期備份重要儲存資源。
  3. 請考慮使用 blob 快照功能。

##<a name="database-checklist"></a>資料庫的檢查清單

  1. 請檢閱這份文件的資料庫一節。
  2. 使用 [資料庫複製] 命令，以建立時間點備份。

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>在虛擬機器備份檢查清單的 SQL Server

  1. 請檢閱 SQL Server 上的 [這份文件的虛擬機器備份區段。
  2. 使用傳統備份和還原技巧。
  3. 建立延遲的記錄傳送工作階段。

##<a name="web-apps-checklist"></a>Web 應用程式檢查清單

  1. 備份及維護相關聯的資料庫，如果有的話。

##<a name="media-services-checklist"></a>媒體服務檢查清單

  1. 備份及維護相關聯的儲存空間資源。

##<a name="more-information"></a>詳細資訊

如需有關 Azure 中的備份與還原功能的詳細資訊，請參閱[儲存空間、 備份及復原案例](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/)。

##<a name="next-steps"></a>後續步驟

本文是著重於[Azure 恢復技術指南](./resiliency-technical-guidance.md)一系列的一部分。 如果您要尋找更多的彈性，損毀修復及可用性資源，請參閱 Azure 恢復技術指南[額外的資源](./resiliency-technical-guidance.md#additional-resources)。