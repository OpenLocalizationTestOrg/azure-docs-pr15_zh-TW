<properties
   pageTitle="從 Azure 地區技術指南影響復原恢復 |Microsoft Azure"
   description="了解並設計，可用性故障容錯應用程式，以及損毀修復規劃文件"
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

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Azure 恢復技術指南︰ 修復整個區域的服務中斷

Azure 分成實際和邏輯稱為區域的單位。 區域所組成關閉鄰近範圍中的一或多個資料中心。 在撰寫時，Azure 會有世界各地的 24 個區域。

在少見的情況下，可能是整個區域中的功能可能無法存取，例如因為網路問題。 或功能可能會遺失，完全，例如因為自然損毀。 本節說明 Azure 的功能來建立分佈區域的應用程式。 這類分配有助於減少一個區域失敗，可能會影響其他區域。

##<a name="cloud-services"></a>雲端服務

###<a name="resource-management"></a>資源管理

您可以分散運算執行個體區域中每個目標區域] 建立另一個雲端服務，然後每個雲端服務發佈部署套件。 不過，請注意，將流量分散到不同區域中的雲端服務必須實作應用程式開發人員或與流量管理服務使用。

決定要部署事先損毀修復備用角色執行個體數目是重要的容量計劃。 有全面次要部署可確保容量已提供需要; 時不過，這一倍成本。 常見的模式就是擁有較小的次要部署，就足以執行重要的服務。 這個小型的次要部署最好，兩者都保留容量，以及測試次要環境的設定。

>[AZURE.NOTE]訂閱配額不容量保證。 配額是只要信用額度。 若要確保容量，所需的角色數目必須定義在服務模型，且必須部署角色。

###<a name="load-balancing"></a>負載平衡

若要負載平衡各區域的流量會需要流量管理解決方案。 Azure 提供[Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)。 您也可以利用的管理功能提供類似流量的協力廠商服務。

###<a name="strategies"></a>策略

許多不同的策略可供實作分散的運算整個區域。 這些必須符合的特定的業務需求和應用程式的情況。 在高的層級，方法可以分成下列類別︰

  * __重新部署上損毀__︰ 在這種方式應用程式從頭部署損毀的時間。 這是適用於非要徑不需要保證的復原時間的應用程式。

  * __暖色程度備用 （主動/被動）__︰ 次要裝載的服務會建立在替代區域中，並保證最小的容量部署角色不過，角色沒有收到生產流量。 這個方法最適合不設計流量分散區域的應用程式。

  * __熱門備用 （作用/作用中）__︰ 應用程式設計用來在多個區域內接收生產載入。 在每個區域中的雲端服務可能會比需要較高的容量損毀修復用途的設定。 或者，雲端服務可能不按比例縮放視分門別類損毀和容錯移轉次。 這種方法，需要獲得超乎投資設計應用程式，但有有效的優點。 包括低和保證復原時間，連續測試所有的復原位置和有效的使用方式的容量。

分散設計的完整討論超出這份文件的範圍。 如需詳細資訊，請參閱[損毀修復和 Azure 應用程式的可用性](https://aka.ms/drtechguide)。

##<a name="virtual-machines"></a>虛擬機器

復原的基礎結構服務 (IaaS) 虛擬機器 (Vm) 是類似的平台服務 (PaaS) 計算在許多方面的復原。 有重要的差異，不過，因為用 IaaS VM 組成 VM 和 VM 磁碟。

  * __若要建立交叉地區備份的是一致的應用程式的使用 Azure 備份__。
  [Azure 備份](https://azure.microsoft.com/services/backup/)可讓您在多個 VM 磁碟，建立應用程式一致的備份和支援的備份複寫各區域的客戶。 您可以選擇地理複寫備份保存庫一次的建立。 請注意備份的複寫建立時都必須設定保存庫。 無法更新版本設定。 如果遺失區域時，Microsoft 就能備份使用為 [客戶]。 客戶無法還原至任何他們的設定的還原點。

  * __分隔資料從磁碟作業系統磁碟__。 IaaS Vm 重要考量是，您無法變更作業系統磁碟，而不必重新建立 VM。 如果您修復策略重新部署之後損毀，這不是問題。 不過，它可能是問題，如果您使用暖色程度備用方法保留容量。 若要正確實作，您必須正確的作業系統磁碟部署到兩個主要和次要位置，並必須在另一個磁碟機上儲存的應用程式的資料。 如果可以的話，請使用標準作業系統設定可提供這兩個位置上。 後移轉後，您必須再將資料磁碟機附加到您現有的 IaaS Vm 在次要 DC 中。 使用 AzCopy 至遠端網站複製的資料磁碟的快照。

  * __您必須知道的潛在的一致性問題後地理移轉後的多個 VM 磁碟__。 VM 磁碟實作為 Azure 儲存體 blob，且您擁有相同的地理複寫特性。 除非使用[Azure 備份](https://azure.microsoft.com/services/backup/)，並沒有任何保證的一致性磁碟，因為地理複寫是非同步，獨立複製。 個別 VM 磁碟保證為一致損狀態後地理移轉後，但不是一致的磁碟。 在某些情況下 （例如，如果具），這可能會導致問題。

##<a name="storage"></a>儲存空間

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>修復使用地理多餘的儲存空間的 blob、 表格、 佇列中，VM 磁碟的儲存空間

在 Azure blob、 表格、 佇列，與 VM 磁碟會是所有地理複寫預設。 這被指為地理多餘儲存空間 (GRS)。 GRS 會將儲存空間資料複製到特定地理區域內的 [向外張開英哩成對資料中心數百。 GRS 是設計用來提供其他持續性主要資料中心損毀的情況下。 容錯移轉時，Microsoft 控制項和容錯移轉會限制為主要損毀的原始的主要位置會被視為無法復原中合理的時間。 在某些情況下，這可能是幾天。 雖然同步處理間隔還不包含的服務等級協定幾分鐘，通常被複寫資料。

發生地理容錯移轉，將會不會變更帳戶的存取方式 （不會變更 URL] 與 [帳戶金鑰）。 儲存帳戶，不過，會在不同區域後移轉後。 這可能會影響應用程式需要以儲存帳戶的地區相關性。 即使的服務，不需要在相同的資料中心的儲存空間帳戶的應用程式的跨資料中心延遲和頻寬費用可能令人信服的理由暫時移至容錯移轉區塊的流量。 這可能會影響整體損毀復原策略。

除了自動 GRS 所提供的容錯移轉，Azure 介紹了一項服務，可讓您讀取權限的複本中的次要存放位置的資料。 這稱為讀取權限地理多餘的儲存空間 (RA GRS)。

如需有關 GRS 和 RA GRS 儲存空間的詳細資訊，請參閱[Azure 儲存體複寫](../storage/storage-redundancy.md)。

###<a name="geo-replication-region-mappings"></a>地理複寫地區對應︰

請務必知道您資料的地理複寫，若要瞭解如何將部署您需要使用儲存空間的地區相關性的資料之其他執行個體的位置。 下表顯示主要和次要位置配對︰

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>價格地理複寫︰

地理複寫會包含在目前價格 Azure 儲存體。 這稱為地理多餘的儲存空間 (GRS)。 如果您不想資料地理複製您可以針對您的帳戶來停用地理複寫。 這是本機多餘的儲存，及薪資比較 GRS 折扣價格。

###<a name="determining-if-a-geo-failover-has-occurred"></a>決定是否發生地理容錯移轉

如果發生地理容錯移轉，這會張貼到[Azure 服務健康狀況儀表板](https://azure.microsoft.com/status/)。 應用程式可以實作自動化的方式可偵測，不過，藉由監視地理區域為其儲存帳戶。 這可以用於觸發其他修復作業，例如儲存空間移到地理區域中的計算資源的啟動。 您可以執行查詢的服務管理 API，從使用[取得儲存帳戶的內容](https://msdn.microsoft.com/library/ee460802.aspx)。 相關的內容是︰

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>VM 磁碟和地理容錯移轉

VM 磁碟上的 [] 區段中所討論，有資料的一致性不提供任何保證 VM 磁碟後移轉後。 若要確保備份的正確性，備份的產品等資料保護管理員應為備份及還原應用程式的資料。

##<a name="database"></a>資料庫

###<a name="sql-database"></a>SQL 資料庫

Azure SQL 資料庫提供兩種類型的復原︰ 地理還原和作用中的地理複寫。

####<a name="geo-restore"></a>地理還原

[地理還原](../sql-database/sql-database-recovery-using-backups.md#geo-restore)也是使用基本、 標準和進階版的資料庫。 裝載您的資料庫的區域中的事件因為無法使用的資料庫時，它會提供預設的 [復原] 選項。 類似時間點還原，地理還原依賴地理多餘的 Azure 儲存體中的資料庫備份。 將其從地理複寫備份複本還原，因此同時主要區域中的儲存空間中斷。 如需詳細資訊，請參閱[Azure SQL 資料庫或容錯移轉至次要還原](../sql-database/sql-database-disaster-recovery.md)。

####<a name="active-geo-replication"></a>作用中的地理複寫

[作用中的地理複寫](../sql-database/sql-database-geo-replication-overview.md)適用於所有資料庫層。 設計的應用程式的地理還原可以提供更積極的復原需求。 使用作用中的地理複寫時，您可以在不同區域中的伺服器上建立四個可讀取次要連結。 您可以啟動容錯移轉至任何次要連結。 此外，作用中的地理複寫可以用於支援的應用程式的升級或重新配置案例，以及負載平衡的唯讀的工作量。 如需詳細資訊，請參閱[設定地理複寫](../sql-database/sql-database-geo-replication-portal.md)，並[無法透過次要資料庫](../sql-database/sql-database-geo-replication-failover-portal.md)。 請參閱[設計雲端損毀修復的應用程式使用 SQL 資料庫中的作用中地理複寫](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[管理循環升級雲端應用程式使用 SQL 資料庫作用中地理複寫](../sql-database/sql-database-manage-application-rolling-upgrade.md)的設計及實作應用程式及應用程式升級，不停機時間的方式的詳細資料。

###<a name="sql-server-on-virtual-machines"></a>虛擬機器上的 SQL Server

各種不同的選項，可修復與高可用性的 SQL Server 2012 （及更新版本） 執行 Azure 虛擬機器中的商務連絡人。 如需詳細資訊，請參閱[可用性和損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。

##<a name="other-azure-platform-services"></a>其他平台 Azure 服務

嘗試執行多個 Azure 區域中的雲端服務，您必須考慮的每個您相依性的含意。 以下各節中提供的服務的指導方針假設您必須在替代的 Azure 資料中心使用相同的 Azure 服務。 包括 [設定] 和 [資料複製的工作。

>[AZURE.NOTE]在某些情況下，這些步驟可協助降低服務的資料，而不是整個資料中心事件。 從應用程式的觀點，服務特定資料可能只限制，需要暫時將服務移轉到替代的 Azure 區域。

###<a name="service-bus"></a>服務匯流排

Azure 服務匯流排使用未跨越 Azure 區域的唯一命名空間。 第一個需求是使設定替代區域中的必要服務匯流排命名空間。 不過，也有的考量持續性的佇列的訊息。 有數種策略複寫透過 Azure 區域的郵件。 這些複寫策略和其他損毀復原策略詳細資訊，請參閱[最佳作法做針對服務匯流排中斷和損毀的應用程式](../service-bus-messaging/service-bus-outages-disasters.md)。 其他可用性考量，請參閱[服務匯流排 （可用性）](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services)。

###<a name="app-service"></a>應用程式服務

若要移轉 Azure 應用程式服務應用程式，Web 應用程式或行動應用程式，例如次要 Azure 區域，您必須適用於發佈網站的備份。 如果資料不涉及整個 Azure 資料中心，可能會使用 FTP 下載新的網站內容的備份。 然後除非您先前完成保留容量，請在 [替代的區域] 中建立新的應用程式。 將網站發佈至新的區域，然後進行任何必要的設定變更。 這些變更可能會包含資料庫連線字串或其他特定地區設定。 如有需要，將該網站的 SSL 憑證新增及變更 DNS CNAME 記錄，以便自訂網域名稱會指向 redeployed Azure Web 應用程式的 URL。

###<a name="hdinsight"></a>HDInsight

HDInsight 相關聯的資料會儲存在 Azure Blob 儲存體中預設。 HDInsight 需要處理 MapReduce 工作 Hadoop 叢集必須共同位於相同的區域，包含正在分析資料的儲存空間帳戶。 如果您使用地理複寫功能才能 Azure 儲存空間，您可以存取資料位置複寫如果基於某種原因的主要區域已不再提供的第二區域中的資料。 您可以建立新的 Hadoop 叢集地區資料複寫的位置，並繼續處理。 其他可用性考量，請參閱[HDInsight （可用性）](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services)。

###<a name="sql-reporting"></a>SQL 報告

此時，修復遺失的 Azure 地區需要多個 SQL 報告中的執行個體不同 Azure 的區域。 這些 SQL 報告的執行個體應該存取相同的資料，以及資料應有自己復原損毀事件計劃。 您也可以維護每個報告 RDL 檔案的外部備份的複本。

###<a name="media-services"></a>媒體服務

Azure 媒體服務具有編碼方式和串流的不同的復原方法。 一般而言，串流時更重要地區的資料。 若要準備此問題，您應該在兩個不同的 Azure 區域內有媒體服務帳戶。 編碼的內容應該位於兩個區域。 期間失敗，您可以將串流流量重新導向到其他區域。 編碼，才能執行任何 Azure 的區域。 如果編碼區分時間的即時事件處理期間，例如，您必須是準備好提交到替代的資料中心的工作期間失敗。

###<a name="virtual-network"></a>虛擬網路

設定檔案提供設定替代 Azure 區域中的虛擬網路最快速的方法。 在主要 Azure 區域中，為目前的網路，網路設定檔的 [[匯出虛擬網路設定](../virtual-network/virtual-networks-create-vnet-classic-portal.md)設定虛擬網路。 事件的主要區域中的資料，[還原虛擬網路](../virtual-network/virtual-networks-create-vnet-classic-portal.md)從儲存的設定檔。 設定其他雲端服務，虛擬機器或跨內部部署設定以使用新的虛擬網路。

##<a name="checklists-for-disaster-recovery"></a>修復損毀的檢查清單

##<a name="cloud-services-checklist"></a>雲端服務的檢查清單

  1. 請檢閱這份文件的雲端服務一節。
  2. 建立跨地區損毀復原策略。
  3. 了解折衷方案中保留在替代的區域內的容量。
  4. 使用傳輸路由的工具，例如 Azure 流量管理員。

##<a name="virtual-machines-checklist"></a>虛擬機器檢查清單

  1. 請檢閱這份文件的虛擬機器一節。
  2. 您可以使用[Azure 備份](https://azure.microsoft.com/services/backup/)，跨區域建立應用程式一致的備份。

##<a name="storage-checklist"></a>儲存空間的檢查清單

  1. 請檢閱這份文件的 [儲存] 區段。
  2. 請不要停用地理複寫存放資源。
  3. 瞭解地理複寫的其他區域時容錯移轉。
  4. 建立自訂的使用者控制容錯移轉策略的備份策略。

##<a name="sql-database-checklist"></a>SQL 資料庫的檢查清單

  1. 請檢閱這份文件的 [SQL 資料庫] 區段。
  2. 視需要使用[地理還原](../sql-database/sql-database-recovery-using-backups.md#geo-restore)或[地理複寫](../sql-database/sql-database-geo-replication-overview.md)。

##<a name="sql-server-on-virtual-machines-checklist"></a>在虛擬機器檢查清單的 SQL Server

  1. 請檢閱 SQL Server 上的 [這份文件的虛擬機器區段。
  2. 使用跨地區 AlwaysOn 可用性群組或資料庫鏡像。
  3. 或者使用備份與還原至 blob 儲存體。

##<a name="service-bus-checklist"></a>服務匯流排檢查清單

  1. 請檢閱這份文件的服務匯流排一節。
  2. 替代的地區設定服務匯流排命名空間。
  3. 跨區域，請考慮郵件的自訂複寫策略。

##<a name="app-service-checklist"></a>應用程式服務檢查清單

  1. 請檢閱應用程式服務] 區段中的 [這份文件。
  2. 維護網站的主要區域外的備份。
  3. 如果資料是部分，請嘗試擷取 FTP 與目前的網站。
  4. 規劃部署至新的或現有的網站，替代區域中的網站。
  5. 規劃設定變更應用程式] 及 [DNS CNAME 記錄。

##<a name="hdinsight-checklist"></a>HDInsight 檢查清單

  1. 請檢閱這份文件的 HDInsight 一節。
  2. 建立新的 Hadoop 叢集複製的資料區域中。

##<a name="sql-reporting-checklist"></a>SQL 報告的檢查清單

  1. 請檢閱這份文件的 SQL 報告一節。
  2. 維護替代 SQL 報告執行個體中的不同部分。
  3. 維護個別的計劃，以複製到該區域的目標。

##<a name="media-services-checklist"></a>媒體服務檢查清單

  1. 請檢閱這份文件的 [媒體服務] 區段。
  2. 建立替代區域中的媒體服務帳戶。
  3. 編碼中支援串流容錯移轉兩個區域的相同的內容。
  4. 送出到替代的地區發生服務中斷編碼的工作。

##<a name="virtual-network-checklist"></a>虛擬網路檢查清單

  1. 請檢閱這份文件的虛擬網路一節。
  2. 使用匯出虛擬網路設定] 以重新建立另一個區域中。

##<a name="next-steps"></a>後續步驟

本文是著重於[Azure 恢復技術指南](./resiliency-technical-guidance.md)一系列的一部分。 本系列文章著重於[從內部部署資料中心，以 Azure 復原](./resiliency-technical-guidance-recovery-on-premises-azure.md)。
