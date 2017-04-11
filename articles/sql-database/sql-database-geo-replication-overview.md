<properties
    pageTitle="作用中 Azure SQL 資料庫地理複寫"
    description="作用中的地理複寫可讓您設定的 Azure 資料中心的所有資料庫 4 的複本。"
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>概觀︰ SQL 資料庫作用中地理複寫

作用中的地理複寫可讓您設定的相同或不同的資料中心位置 （區域） 的四個可讀的次要資料庫。 第二個資料庫可查詢和容錯移轉若是資料中心中斷或無法連線至主要資料庫。

>[AZURE.NOTE] 現在使用的所有服務層中的所有資料庫作用中地理複寫 （可讀取次要連結）。 在年 4 月 2017年無法讀取副類型會已停用，無法讀取中現有的資料庫會自動升級到可讀的次要連結。

 您可以設定作用中地理-複寫使用[Azure 入口網站](sql-database-geo-replication-portal.md) [PowerShell](sql-database-geo-replication-powershell.md)、 [TRANSACT-SQL](sql-database-geo-replication-transact-sql.md)，或[REST API-建立或更新資料庫](https://msdn.microsoft.com/library/azure/mt163685.aspx)。

> [AZURE.SELECTOR]
- [設定︰ Azure 入口網站](sql-database-geo-replication-portal.md)
- [設定︰ PowerShell](sql-database-geo-replication-powershell.md)
- [設定︰ T SQL](sql-database-geo-replication-transact-sql.md)

如果的任何原因您的主要資料庫失敗，或只需要離線時，您可以*容錯移轉*給任何第二個資料庫。 當其中一個次要資料庫啟動時容錯移轉時，將所有其他次要連結自動連結至新的主要。

您可以使用[Azure 入口網站](sql-database-geo-replication-failover-portal.md)、 [PowerShell](sql-database-geo-replication-failover-powershell.md)、 [TRANSACT-SQL](sql-database-geo-replication-failover-transact-sql.md)、 [REST API-計劃的容錯移轉](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)或[REST API-意外的容錯移轉](https://msdn.microsoft.com/library/azure/mt582027.aspx)次要容錯移轉。


> [AZURE.SELECTOR]
- [容錯移轉︰ Azure 入口網站](sql-database-geo-replication-failover-portal.md)
- [容錯移轉︰ PowerShell](sql-database-geo-replication-failover-powershell.md)
- [容錯移轉︰ T SQL](sql-database-geo-replication-failover-transact-sql.md)

後移轉後，請確定在新的主要上設定您的伺服器及資料庫的驗證需求。 如需詳細資訊，請參閱[SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md)。


作用中的地理複寫功能實作機制提供資料庫重複相同的 Microsoft Azure 區域內，或在不同區域內 （地理重複）。 作用中的地理複寫非同步複製確認的交易從資料庫四個複本上使用不同伺服器的資料庫閱讀隔離的認可的快照隔離 (RCSI)。 作用中的地理複寫設定時，指定伺服器上建立第二個資料庫。 原始的資料庫會變成主要資料庫。 非同步主要資料庫會將確認的交易複製到每個次要資料庫。 完整的交易複寫。 在任何指定的點，而第二個資料庫可能稍有位於主要資料庫，保證次要資料永遠不會有部分交易]。 特定 RPO 資料位於[業務連續性概觀](sql-database-business-continuity.md)。

其中的作用中地理複寫的主要優點是，它提供資料庫層級損壞修復解決方案低復原時間。 當您將在不同區域的伺服器上的次要資料庫時，則會新增至您的應用程式的最大的彈性。 跨地區複本可讓應用程式復原永久遺失整個資料中心或自然損毀，嚴重的人力錯誤或惡意動作所造成的資料中心的組件中。 下圖顯示美國中部南部地區主要和次要美國中部北美地區設定的作用中地理複寫的範例。

![地理複寫關聯](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

另一個主要優點是次要資料庫可讀取，而且可以用於卸載唯讀的工作量，例如報告工作。 如果您只想要用於負載平衡次要資料庫，您可以在同一個為主要區域中建立它。 建立次要相同的區域，並不會增加嚴重失敗的應用程式的恢復功能。  

在作用中的地理複寫可供其他情況包括︰

- **資料庫移轉**︰ 您可以使用 [作用中的地理複寫移轉資料庫伺服器到另一個線上以最小的停機時間。
- **應用程式升級**︰ 您可以建立額外的次要失敗返回複本作為應用程式升級期間。

若要達到實數的業務連續性，新增資料庫重複的資料中心之間是解決方案的的組件。 嚴重失敗需要構成服務的所有元件和任何相關的服務的復原之後，請修復應用程式 （服務） 端對端。 這些元件的範例包括用戶端軟體 （例如，在瀏覽器使用自訂的 JavaScript）、 web 前端、 儲存及 DNS。 這是所有元件都有彈性的失敗，以及，就可以使用復原時間目標 (RTO) 為您的應用程式中的關鍵。 因此，您需要找出所有相關的服務，並瞭解的保證與他們所提供的功能。 然後，您必須採取適當的步驟，以確保您的服務函數期間的服務依賴的容錯移轉。 如需有關損毀修復設計方案的詳細資訊，請參閱[設計雲端解決方案的損毀復原使用 Active 地理複寫](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="active-geo-replication-capabilities"></a>作用中的地理複寫功能
作用中的地理複寫功能提供下列重要功能︰

- **自動非同步複寫**︰ 您僅可以建立第二個資料庫新增到現有的資料庫。 次要只可以建立在不同的 Azure SQL 資料庫伺服器。 建立後，次要資料庫會填入複製從主要資料庫的資料。 此程序稱為種子。 已建立及定位次要資料庫後，主要資料庫的更新會非同步自動複寫到次要資料庫。 非同步複寫表示交易認可上主要資料庫之前其複寫到第二個資料庫。 

- **多個次要資料庫**︰ 兩個或多個次要資料庫增加重複性和的主要資料庫及應用程式的保護層級。 如果有多個次要資料庫，應用程式會保持受保護，即使其中一個次要資料庫失敗。 如果只有一個次要資料庫，而且會失敗，應用程式會公開更高的風險，直到建立新的次要資料庫。

- **可讀取次要資料庫**︰ 應用程式，可以存取次要資料庫的唯讀作業使用的相同或不同的安全性原則，用來存取主要資料庫。 第二個資料庫運作快照隔離模式，以確保複寫的更新 （重新顯示記錄） 的主要的不在次要上執行的查詢延遲。

>[AZURE.NOTE] 如果有結構描述更新收到從主要的需要次要資料庫的結構描述鎖定次要資料庫被延遲重新顯示記錄。

- **彈性的資料庫的資料庫的作用中地理複寫**︰ 作用中的地理複寫可以設定任何彈性的資料庫資料庫中的任何資料庫。 次要資料庫可以在另一個彈性的資料庫的資料庫。 一般的資料庫，次要可彈性的資料庫的資料庫，反之亦然，只要服務層都是一樣的。 

- **第二個資料庫可設定效能層級**︰ 您可以使用效能低於主要建立第二個資料庫。 主要和次要資料庫必須有相同的服務層級。 這個選項，不建議的應用程式與高資料庫寫入活動因為增加的複寫延隔時間增加後移轉後的大量資料遺失的風險。 此外後移轉後, 的應用程式的效能影響直到新的主要升級為較高的效能。 記錄 IO 百分比圖表 Azure 入口網站提供估計，才能維持複寫下載的次要的最小的效能層級的好方法。 例如，如果您的主要資料庫 P6 (1000 DTU) 和其記錄 IO 百分比是次要必須至少為 50%第 4 頁 (500 DTU)。 您也可以擷取記錄 IO 資料使用[sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)或[sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx)資料庫檢視。  如需有關 SQL 資料庫效能層級的詳細資訊，請參閱[SQL 資料庫選項與效能](sql-database-service-tiers.md)。 

- **使用者控制的錯誤移轉及回復**︰ 次要資料庫明確可切換的主要角色隨時應用程式或使用者。 實際的中斷時 「 未計劃 」 的選項應，其中立即升階為次要的主要。 當失敗的主要復原，且可一次時，系統會自動標記復原的主要與次要，並將其最新的主要。 複寫的非同步性質，因為少量的資料可能會遺失期間意外的錯誤後移轉如果主要失敗之前，會將最新變更複製到次要。 當主要與次要連結多個無法透過時，系統會自動重新設定複寫的關聯，並且連結至新升級的主要的剩餘的次要連結，而不需要任何使用者互動。 降低中斷造成容錯移轉之後，則可能會希望傳回的主要區域應用程式。 若要執行這項作業，應容錯移轉命令叫用 「 計劃 」 的選項。 

- **保留認證同步的防火牆規則**︰ 建議使用[資料庫防火牆規則](sql-database-firewall-configure.md)的地理複寫的資料庫，這些規則，可將複寫以確保所有的次要資料庫擁有相同的防火牆規則，為主要資料庫。 這種方法讓您不需要的客戶，以手動方式設定和維護裝載同時主要和次要資料庫伺服器上的防火牆規則。 同樣地，使用的資料存取可確保主要和次要資料庫永遠具有相同的使用者[所包含的資料庫使用者](sql-database-manage-logins.md)認證，故障時，不中斷，因為不相符的登入名稱與密碼。 [Azure Active Directory](../active-directory/active-directory-whatis.md)加入，客戶可以管理主要和次要資料庫並不需要認證資料庫完全中的管理使用者存取權。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級為主要資料庫
您可以升級或降級至不同的效能層級 （在同一個服務層） 的主要資料庫，而不中斷連線的任何次要資料庫。 升級時，建議您第一次，升級的次要資料庫，然後再升級主要。 當降級，使用相反順序︰ 首先，降級主要，然後降級次要。 

次要資料庫必須在同一個服務層為主要，以便將您的主要資料庫移轉到不同的服務層需要您終止地理複製連結並重新命名的次要的資料庫，或直接放。 接著將主要移轉至新的服務層，然後重新設定地理複寫。 新增第二個會自動建立相同的效能層級，為主要預設。

## <a name="preventing-the-loss-of-critical-data"></a>防止資料遺失的要徑
寬的區域網路的高延遲，因為連續的複本會使用非同步複寫機制。 非同步複寫會遺失某些資料無法避免發生故障。 不過，部分應用程式可能會要求遺失任何資料。 若要保護這些重大更新，應用程式開發人員可以緊接認可交易呼叫[sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx)系統程序。 呼叫**sp_wait_for_database_copy_sync**區塊呼叫執行緒直到最後的認可的交易複寫到次要資料庫。 程序會在第二個資料庫已經認知所有佇列的交易等候。 **sp_wait_for_database_copy_sync**限定為特定的連續複製連結。 任何與主要資料庫的連線權限的使用者可以呼叫此程序。

>[AZURE.NOTE] **Sp_wait_for_database_copy_sync**程序呼叫所造成的延遲可能很長。 延遲時間而定的交易記錄檔長度大小，直到複製整個記錄將不會傳回此通話。 避免呼叫此程序除非絕對必要。

## <a name="programmatically-managing-active-geo-replication"></a>以程式設計方式管理 [作用中的地理複寫

如先前所述，作用中的地理複寫也可加以使用 PowerShell 的 Azure 和 REST API，以程式設計方式。 下表說明一組可用的命令。

- **Azure 資源管理員 API 及角色為基礎的安全性**︰ 作用中的地理複寫包含一組[Azure 資源管理員 Api]( https://msdn.microsoft.com/library/azure/mt163571.aspx)進行管理，包括[Azure 資源管理員為基礎的 PowerShell 指令程式](sql-database-geo-replication-powershell.md)。 這些 Api 需要使用資源群組，與支援角色為基礎的安全性 (RBAC)。 如需有關如何實作存取角色，請參閱[Azure Role-Based Access 控制項](../active-directory/role-based-access-control-configure.md)。

>[AZURE.NOTE] 許多新功能的作用中地理複寫只支援使用[Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)根據[Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)與[Azure SQL 資料庫 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx)。 （傳統） 的 REST API] (https://msdn.microsoft.com/library/azure/dn505719.aspx) 和[Azure SQL 資料庫 （傳統） 的指令程式](https://msdn.microsoft.com/library/azure/dn546723.aspx)已經支援的回溯相容性，因此使用 Azure 資源管理員為基礎的 Api 所建議。 


### <a name="transact-sql"></a>TRANSACT-SQL

|] 命令|描述|
|-------|-----------|
|[ALTER 資料庫 （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt574871.aspx)|若要建立的現有的資料庫，以及開始資料複製為第二個資料庫中使用新增次要開啟伺服器引數|
|[ALTER 資料庫 （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt574871.aspx)|使用容錯移轉或 FORCE_FAILOVER_ALLOW_DATA_LOSS 切換次要為主要啟動容錯移轉資料庫
|[ALTER 資料庫 （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt574871.aspx)|移除次要開啟伺服器可用於終止 SQL 資料庫與指定的第二個資料庫之間的資料複製。|
|[sys.geo_replication_links （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt575501.aspx)|傳回邏輯 Azure SQL 資料庫伺服器上的所有現有複製連結，每個資料庫的相關資訊。|
|[sys.dm_geo_replication_link_status （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt575504.aspx)|取得特定的 SQL 資料庫複寫上次、 最後一個試驗延隔時間，以及其他相關資訊複寫連結。|
|[sys.dm_operation_status （Azure SQL 資料庫）](https://msdn.microsoft.com/library/dn270022.aspx)|顯示所有的資料庫作業，包括複製連結的狀態的狀態。|
|[sp_wait_for_database_copy_sync （Azure SQL 資料庫）](https://msdn.microsoft.com/library/dn467644.aspx)|使等候，直到所有認可的交易是複寫通知作用中的次要資料庫應用程式。|
||||

### <a name="powershell"></a>PowerShell

|Cmdlet|描述|
|------|-----------|
|[取得 AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|取得一或多個資料庫。|
|[新 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|建立現有資料庫的次要資料庫並啟動資料複製。|
|[設定 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|切換為主要啟動容錯移轉次要資料庫。|
|[移除 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|結束 SQL 資料庫與指定的第二個資料庫之間的資料複製。|
|[取得 AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|取得 Azure SQL 資料庫和資源群組或 SQL Server 之間的地理複寫連結。|
||||

### <a name="rest-api"></a>REST API

|API|描述|
|---|-----------|
|[建立或更新資料庫 (createMode = 還原)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|建立、 更新，或還原主要或次要的資料庫。|
|[取得建立或更新資料庫的狀態](https://msdn.microsoft.com/library/azure/mt643934.aspx)|建立作業期間會傳回狀態。|
|[將副資料庫設定為主要 （計劃容錯移轉）](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|升階次要資料庫中的地理複寫合作關係，成為新的主要資料庫。|
|[將副資料庫設定為主要 （意外的容錯移轉）](https://msdn.microsoft.com/library/azure/mt582027.aspx)|若要強制容錯移轉至次要資料庫並設定次要為主要。|
|[取得複製連結](https://msdn.microsoft.com/library/azure/mt600929.aspx)|取得指定 SQL 資料庫中的地理複寫合作關係中的所有複製連結。 它會擷取 sys.geo_replication_links 類別目錄檢視] 中顯示的資訊。|
|[取得複寫連結](https://msdn.microsoft.com/library/azure/mt600778.aspx)|取得特定複寫連結指定 SQL 資料庫中的地理複寫合作關係。 它會擷取 sys.geo_replication_links 類別目錄檢視] 中顯示的資訊。|
||||



## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)。
- 若要瞭解如何使用復原自動的備份，請參閱[還原服務啟動備份的資料庫](sql-database-recovery-using-backups.md)。
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複製](sql-database-copy.md)。
- 若要瞭解新的主要伺服器與資料庫的驗證需求，請參閱[SQL 資料庫安全性之後損毀修復](sql-database-geo-replication-security-config.md)。
