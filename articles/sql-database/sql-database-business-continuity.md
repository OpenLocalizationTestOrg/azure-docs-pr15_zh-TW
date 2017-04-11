<properties
   pageTitle="雲端業務連續性-資料庫復原-SQL 資料庫 |Microsoft Azure"
   description="瞭解如何 Azure SQL 資料庫支援雲端業務連續性及修復資料庫，可以協助您隨時執行的重要的雲端應用程式。"
   keywords="業務連續性、 雲端業務連續性、 資料庫損毀修復資料庫復原"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>業務連續性 Azure SQL 資料庫的概觀

此概觀說明 Azure SQL 資料庫提供的業務連續性和損壞修復功能。 會提供選項、 建議和教學課程從破壞事件可能會造成資料遺失或造成您的資料庫和應用程式無法復原。 討論包含該怎麼辦使用者或應用程式錯誤會影響資料完整性、 Azure 的地區有中斷，或您的應用程式需要進行的維修作業。 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL 資料庫功能可供您提供業務連續性

SQL 資料庫提供多個商務連續性功能，包括自動的備份和選擇性的資料庫複寫。 每一個估計的復原時間 (ERT) 和最近的交易的潛在資料損有不同的特性。 只要您瞭解這些選項，您可以選擇-，在大部分情況下，將它們放在一起用於不同情況。 當您開發您的業務連續性計劃，必須先瞭解的最大可接受時間才能完全復原破壞事件後的應用程式-這是您的復原時間目標 (RTO)。 您也必須瞭解最新資料的更新 （時間間隔） 應用程式可容許的最大復原之後破壞事件-復原點目標 (RPO) 時，會遺失。 

下表將比較 ERT 和 RPO 三種最常見的情況。

| 功能 |  基本層 | 標準層  | 進階版層 |
|---|---|---|---|
| 從備份還原時間點 | 任何 7 天內還原點   | 任何 35 天內還原點  | 任何 35 天內還原點 |
地理-還原從地理複寫的備份 | ERT < 12 h、 的 RPO < 1 h   | ERT < 12 h、 的 RPO < 1 h   | ERT < 12 h、 的 RPO < 1 h |
|作用中的地理複寫 | ERT < 30 秒，RPO < 5s   | ERT < 30 秒，RPO < 5s | ERT < 30 秒，RPO < 5s |


### <a name="use-database-backups-to-recover-a-database"></a>使用資料庫備份還原資料庫

SQL 資料庫會自動執行完整資料庫備份每週] 差異的組合，您的業務防止資料遺失資料庫備份每小時、 與交易記錄檔備份每隔五分鐘。 這些備份資料庫中的標準和進階版服務層和資料庫中的基本服務層七天 35 天會儲存在本機多餘的儲存空間，請參閱[服務層](sql-database-service-tiers.md)詳細服務層上。 如果您的服務層級的保留期限不符合您的業務需求，您可以增加保留期限藉由[變更服務層級](sql-database-scale-up.md)。 完整及差異資料庫備份也會被複寫到[成對的資料中心](../best-practices-availability-paired-regions.md)的資料中心中斷-保護，請參閱[自動資料庫備份](sql-database-automated-backups.md)，如需詳細資訊。

您可以使用這些自動資料庫備份您的資料中心的及其他資料中心，從不同的破壞事件，復原資料庫。 使用自動資料庫備份，取決於幾項因素包括資料庫在同一時間、 資料庫大小、 交易記錄檔大小和網路頻寬復原相同的區域中的總數的估計的復原的時間。 在大多數情況下，復原時間會是 12 小時。 復原至另一個資料區域，可能的資料遺失時，所地理多餘的儲存空間，每小時差異資料庫備份的限制為 1 小時。 

> [AZURE.IMPORTANT] 若要復原使用自動的備份，您必須是成員的 SQL Server 參與者角色或訂閱的擁有者-請參閱[RBAC︰ 內建的角色](../active-directory/role-based-access-built-in-roles.md)。 您可以復原使用 Azure 入口網站、 PowerShell 或 REST API。 您無法使用 TRANSACT-SQL。

如果您的業務連續性及修復機制為使用自動的備份您的應用程式︰

- 不會視為任務要徑。
- 沒有繫結 SLA，因此停機時間 24 小時的時間或更長的時間不會導致財務責任。
- 有低工資率 （每小時低交易） 的資料變更，而且遺失達一小時的變更是可接受的資料遺失。 
- 會成本區分。 

如果您需要更快速地修復，請使用 [[作用中的地理複寫](sql-database-geo-replication-overview.md)（討論下一步）。 如果您必須能夠 35 天期間復原資料，請考慮封存資料庫定期以 BACPAC 檔案 （壓縮的檔案包含您的資料庫結構描述和相關聯的資料） 會儲存您所選擇的另一個位置中或是 Azure blob 儲存體中。 如需有關如何建立資料庫交易一致封存的詳細資訊，請參閱[建立資料庫複本](sql-database-copy.md)，並[匯出資料庫複製](sql-database-export.md)。 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>以減少復原時間與限制其發生資料遺失復原相關聯的作用中的地理複寫

除了使用資料庫備份資料庫復原業務中斷時，您可以使用 [[作用中的地理複寫](sql-database-geo-replication-overview.md)設定的資料庫，您所選擇的區域中有四個可讀的次要資料庫。 這些次要資料庫與主要資料庫使用非同步複寫機制會保留已同步處理。 此功能用來防範業務中斷時資料中心中斷或應用程式升級期間。 作用中的地理複寫可以用於地理位置分散使用者提供更佳的唯讀查詢的查詢效能。

如果主要資料庫離線意外或您要使離線維護活動，您可以快速將升級成為主要 （也稱為容錯移轉），並設定應用程式連線至新升級的主要次要。 計劃的容錯移轉時，會遺失任何資料。 使用意外的容錯移轉，可能會有一些少量的資料遺失，因為性質的非同步複寫的最新交易。 後移轉後，您可以稍後回復-根據計劃中，或資料中心回到線上時。 在所有的情況下，使用者體驗少量停機時間和需要重新連線。 

> [AZURE.IMPORTANT] 若要使用作用中的地理複寫，您必須是訂閱擁有者，或在 SQL Server 中具有系統管理權限。 您可以設定和使用 Azure 入口網站、 PowerShell 或使用訂閱的權限，或使用 TRANSACT-SQL REST API 的容錯移轉使用 SQL Server 中的權限。

如果您的應用程式任何符合以下準則，請使用 [作用中的地理複寫︰

- 是任務的關鍵。
- 具有服務等級協定 (SLA) 不允許 24 小時的時間或多個停機時間。
- 財務責任會造成停機時間。
- 具有高率的資料變更為高，而不接受遺失一小時的資料。
- 作用中的地理複寫的其他成本低於財務責任和商務相關聯的影響。

## <a name="recover-a-database-after-a-user-or-application-error"></a>復原之後的使用者或應用程式錯誤的資料庫

* 沒有人會完美 ！ 使用者可能不小心刪除某些資料、 不小心卸除重要的資料表，或甚至捨棄整個資料庫。 或者，如果應用程式可能不小心覆寫良好的資料不正確的資料，因為應用程式缺失。 

在此案例中，這些是復原選項。

### <a name="perform-a-point-in-time-restore"></a>執行時間點還原

前提是資料庫保留期間內的時間，您可以使用自動的備份的時間，復原已知的好您的資料庫的複本。 還原資料庫後，您可以取代還原的資料庫中的原始資料庫或還原資料所需的資料複製到原始的資料庫。 如果資料庫使用的是作用中的地理複寫，建議您複製還原到原始資料庫的複本中的所需的資料。 如果您在原始資料庫和還原的資料庫，您必須重新設定，然後重新同步處理作用中的地理複寫 （這可能需要很久大型的資料庫）。 

如需詳細資訊和時間點還原資料庫的詳細步驟使用 [Azure 入口網站，或使用 PowerShell，請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。 您無法復原使用 TRANSACT-SQL。

### <a name="restore-a-deleted-database"></a>還原已刪除的資料庫

如果資料庫會刪除，但尚未刪除邏輯伺服器，您可以還原已刪除的資料庫已遭刪除的點。 這將資料庫備份還原已刪除的 SQL server 相同邏輯。 您可以還原原始的名稱，或提供的新名稱或已還原的資料庫。

如需詳細資訊和還原已刪除的資料庫的詳細步驟使用 [Azure 入口網站，或使用 PowerShell，請參閱[還原已刪除的資料庫](sql-database-recovery-using-backups.md#deleted-database-restore)。 您無法還原使用 TRANSACT-SQL。

> [AZURE.IMPORTANT] 如果刪除邏輯的伺服器時，就無法復原已刪除的資料庫。 

### <a name="import-from-a-database-archive"></a>匯入資料庫封存

如果外部自動備份的目前保留期間發生的資料遺失，您必須已封存資料庫可以[匯入封存的 BACPAC 檔案](sql-database-import.md)到新的資料庫。 此時，您可以取代原來的資料庫匯入資料庫，或所需的資料複製到原始資料庫匯入的資料。 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>從 Azure 的區域資料中心中斷復原到另一個區域的資料庫

<!-- Explain this scenario -->

雖然少，Azure 資料中心可以讓中斷。 中斷後，就會導致業務中斷的可能僅最後幾分鐘的時間，或可能最後一個小時。 

- 其中一個選項是您的資料庫重新連線後，在資料中心中斷時，請等候。 這適用於能負擔離線有資料庫的應用程式。 例如，開發專案或免費試用版不需要不斷地處理。 當資料中心中斷時，您就不會知道資料會持續時間，，這個選項只有當您不需要一段時間的資料庫。
- 如果您使用的作用中的地理複寫或使用地理多餘的資料庫備份 （地理還原） 復原任一容錯移轉到另一個資料地區是另一個選項。 容錯幾秒鐘，移轉時從備份復原小時。

當您採取動作、 所需的時間來復原，您和您造成資料中心中斷時多少資料遺失，取決於您如何決定使用上述應用程式中的業務連續性功能。 實際上，您可以選擇要使用的資料庫備份與應用程式的需求而定作用中地理複寫組合。 獨立的資料庫，以及使用這些業務連續性功能的彈性集區的應用程式的設計考量的討論，請參閱[設計雲端損毀修復的應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)] 和 [[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

以下各節提供步驟來修復使用 [資料庫備份] 或 [作用中的地理複寫的概觀。 如需詳細步驟，包括規劃執行損毀復原向下切入的需求、 文章修復步驟及如何模擬中斷的相關資訊，請參閱[復原中斷從 SQL 資料庫](sql-database-disaster-recovery.md)。

### <a name="prepare-for-an-outage"></a>準備資料

無論您使用業務連續性功能，您必須︰

- 找出並準備目標伺服器，包括伺服器層級的防火牆規則，登入和主要資料庫層級權限。
- 決定如何重新導向用戶端和新的伺服器的用戶端應用程式
- 其他相依性，例如稽核設定和通知的文件 
 
如果您不計劃，準備正常運作，使您的應用程式，線上之後的容錯移轉或復原需要額外的時間，以及可能也需要疑難排解壓力-不正確的組合一次。

### <a name="failover-to-a-geo-replicated-secondary-database"></a>容錯移轉至地理複製的第二個資料庫 

如果您使用的作用中的地理複寫為您修復機制，[強制地理複寫次要容錯移轉](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database)。 在秒內，次要會升級成為新的主要並準備錄製新的交易和回應任何查詢-只有幾秒鐘的資料遺失有尚未複寫的資料。 自動化容錯移轉程序的詳細資訊，請參閱[設計雲端損毀修復的應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

> [AZURE.NOTE] 資料中心恢復連線時，您可以回復到原來的主要 （或不）。

### <a name="perform-a-geo-restore"></a>執行地理還原 

如果您使用自動備份地理多餘的儲存空間複寫為您修復機制，[開始使用地理還原資料庫復原](sql-database-disaster-recovery.md#recover-using-geo-restore)。 復原通常會出現在 12 小時內-與資料遺失的一個小時取決於何時複寫拍攝與最後一個小時差異的備份。 復原完成之後，直到資料庫是無法錄製任何交易或回應任何查詢。 

> [AZURE.NOTE] 如果資料中心上線切換應用程式復原資料庫之前，您可以直接取消復原。  

### <a name="perform-post-failover--recovery-tasks"></a>執行張貼容錯移轉 / 復原工作 

後從任一修復機制復原，您的使用者之前，您必須執行下列其他工作，且應用程式回執行︰

- 重新導向用戶端和用戶端應用程式到新的伺服器，並還原的資料庫
- 確定使用者連線 （或使用[資料庫層級防火牆](sql-database-firewall-configure.md#creating-database-level-firewall-rules)） 的位置適當的伺服器層級防火牆規則
- 確定適當的登入與主要資料庫層級權限的位置 （或使用[包含使用者](https://msdn.microsoft.com/library/ff929188.aspx)）
- 設定稽核，視
- 設定通知視

## <a name="upgrade-an-application-with-minimal-downtime"></a>升級 [應用程式，以最小的停機時間

有時需要離線由於預定進行的維護等應用程式升級應用程式。 [管理應用程式升級](sql-database-manage-application-rolling-upgrade.md)會說明如何使用作用中的地理複寫來啟用循環升級應用程式的雲端升級期間將停機時間最小化，並提供的復原路徑，在事件發生錯誤。 本文會檢查兩個不同的方法來協調升級程序，並討論的優點和每個選項的取捨。

## <a name="next-steps"></a>後續步驟

獨立的資料庫，以及彈性的集區的應用程式的設計考量的討論，請參閱[設計雲端損毀修復的應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)] 和 [[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。






