<properties 
   pageTitle="Azure SQL 資料庫的常見問題集" 
   description="常見的問題客戶的解答問雲端資料庫與 Azure SQL 資料庫、 Microsoft 的關聯式資料庫管理系統 (RDBMS) 和資料庫以在雲端服務。" 
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
   ms.workload="data-management" 
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>SQL 資料庫的常見問題集

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>如何會使用 SQL 資料庫的方式顯示的帳單上？ 
SQL 資料庫帳單上如預期呈現的時薪根據服務層級 + 單一資料庫或每個彈性的資料庫集區 eDTUs 的效能等級。 計算和累每小時，因此您的帳單可能會顯示一小時的分數實際的使用方式。 例如，如果資料庫有一個月 12 小時，您的帳單便會顯示 0.5 天的使用方式。 此外，服務層 + 效能層級和每個集區 eDTUs 發生在使其看起來更清楚您用的每一個月的資料庫天數的帳單。

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>該怎麼辦單一資料庫均可使用不超過一個小時，或小於一個小時使用較高的服務層級？
是向您收費每個資料庫存在使用最高的服務層級的小時 + 套用該小時，無論分派狀況] 或 [資料庫是否作用中的一個小時期間的效能層級。 例如，如果您建立單一資料庫，並將其刪除五分鐘之後您的帳單會反映出一個資料庫小時的費用。 

範例
    
- 如果您建立基本的資料庫，然後立即升級至標準 S1 時，您會 S1 標準工資率支付薪資的第一個小時。

- 如果您將升級資料庫的基本 premium 下午 10:00 升級完成上午 1:35 在 [動作] 功能表中，您是進階版工資率支付薪資 1:00 a.m.開始 

- 如果您 11:00 a.m.降級為基本從進階資料庫 完成下午 2:15，然後資料庫是進階版工資率支付薪資 3:00 pm，直到它之後, 薪資的基本的工資率。

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>如何會彈性的資料庫集區的使用方式顯示的帳單和變更時，會發生什麼情況 eDTUs 每個集區？
彈性的資料庫資料庫費用顯示帳單上為彈性 DTUs (eDTUs) 顯示在每個[[價格] 頁面](https://azure.microsoft.com/pricing/details/sql-database/)上的集區 eDTUs 幅度。 為每個資料庫免費彈性的資料庫集區。 是向您收費集區已存在於最高 eDTU，無論分派狀況] 或 [是否集區是作用中的一個小時每小時。 

範例

- 若您建立的標準的彈性資料庫集區 200 eDTUs 上午 11:18，新增五個資料庫集區，您會為 200 eDTUs 的整個小時、 11 上午開頭 透過一天的其餘部分。
- 在日 2，上午 5:05，資料庫第 1 開始分成 50 eDTUs，並保留穩定的天。 介於 0 到 80 eDTUs，波動資料庫 2-5。 在日期中，您可以新增五個取用每天的不同 eDTUs 其他資料庫。 日 2 是在 200 eDTU 計費一天。 
- 上午 5 天 3 您新增另一個 15 的資料庫。 資料庫使用量增加點，您可以決定要增加從 200 集區到 400 eDTUs 下午 8:05 日常 200 eDTU 層級的費用是作用中直到 8 pm，並增加到 400 eDTUs 剩餘的四個小時。 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>如何會使用的作用中地理複寫彈性的資料庫資料庫中顯示的帳單上？
不同於單一資料庫]，使用[作用中的地理複寫](sql-database-geo-replication-overview.md)彈性的資料庫時沒有直接帳單的影響。  您只會針對每個資料庫 （主要集區和次要資料庫） 佈建 eDTUs

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>使用稽核功能如何影響帳單？ 
稽核內建沒有額外的 SQL 資料庫服務成本，並提供基本、 標準和進階版的資料庫。 不過，若要儲存稽核記錄，Azure 儲存體帳戶，與的表格和 Azure 儲存體中的佇列套用稽核 」 功能會使用根據您的稽核記錄檔的大小。

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>如何找到正確的服務層和效能層級的單一資料庫和彈性的資料庫集區中？ 
有可用的幾個工具。 

- 對於內部部署的資料庫，建議您資料庫並 DTUs 必要，請使用[DTU 縮放顧問](http://dtucalculator.azurewebsites.net/)和評估彈性的資料庫集區的多個資料庫。
- 如果單一資料庫會因集區中，Azure 的智慧引擎會建議彈性的資料庫資料庫，如果它發現可將其歷史使用圖樣。 請參閱[監控和管理 Azure 入口網站的彈性的資料庫集區](sql-database-elastic-pool-manage-portal.md)。 如需有關如何自行執行數學運算的詳細資訊，請參閱[的彈性的資料庫資料庫價格與效能考量](sql-database-elastic-pool-guidance.md)
- 若要查看您是否要向上或向下撥打單一資料庫，請參閱[效能指引單一的資料庫](sql-database-performance-guidance.md)。

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>頻率可以變更的服務層] 或 [效能層級單一資料庫？ 
V12 資料庫時，您可以變更服務層級 （之間 Basic、 標準和進階版） 或 （例如，給 S2 S1） 服務層級內的效能層級所要的頻率。 舊版資料庫，您可以變更的服務層] 或 [效能層級四個時間 24 小時的總計。

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>頻率可以調整每個集區 eDTUs？ 
視您想要。

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>多久需要變更單一資料庫的服務層或效能層級或移動資料庫與彈性的資料庫資料庫登出？ 
變更資料庫的服務層及移動與集區登出需要複製為背景作業平台上的資料庫。 變更服務層可以需要幾分鐘數根據資料庫的大小。 在這兩種情況下，資料庫會在移動保持線上且有空。 變更單一資料庫的詳細資訊，請參閱[變更資料庫的服務層級](sql-database-scale-up.md)。 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>何時應使用單一彈性的資料庫對資料庫？ 
一般而言，彈性的資料庫集區專為一般[軟體為-的-服務 (SaaS) 應用程式模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)，客戶或租用戶每一個資料庫的位置。 購買個別資料庫及 overprovisioning 符合變數和尖峰的每個資料庫是成本通常不有效的。 使用集區，您的資料庫，集體效能，並管理資料庫不按比例縮放向上和向下自動。 

Azure 的智慧引擎會建議資料庫的資料庫時使用模式保證它。 如需詳細資訊，請參閱[SQL 資料庫價格層建議](sql-database-service-tier-advisor.md)。 關於選擇單一和彈性資料庫之間的詳細指示，請參閱[彈性的資料庫集區的價格和效能考量](sql-database-elastic-pool-guidance.md)。

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>最多 200%的最大能夠的資料庫備份的儲存空間儲存空間什麼意思？ 
備份的儲存空間是自動化的資料庫備份所使用的[點-中--還原時間的相關聯的儲存空間](sql-database-recovery-using-backups.md#-point-in-time-restore)與[地理還原](sql-database-recovery-using-backups.md#geo-restore)。 Microsoft Azure SQL 資料庫提供最多 200%的最大能夠的資料庫儲存空間的免費備份的儲存空間。 例如，如果您有 250 GB 的能夠 DB 大小的標準 DB 執行個體，您可以用 500 GB，不另外收費備份的儲存空間。 如果您的資料庫超過提供備份的儲存空間，您可以選擇連絡支援 Azure 減少保留期限或支付計費在標準的讀取權限地理區域多餘的儲存 (RA GRS) 工資率備份額外儲存空間。 如需有關 RA GRS 帳單的詳細資訊，請參閱儲存定價詳細資料。

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>我所移動 Web/商務至新的服務層，請我需要知道什麼？
Azure SQL 網頁和商務資料庫現在已停用。 基本、 標準、 進階版及彈性層取代淘汰網站和商務資料庫。 我們已可協助您在此轉場效果期間內的其他常見問題集。 [網站與商務版日落的常見問題集](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>什麼是預期的複寫延隔時間地理複寫時相同的 Azure 地理區域內的兩個區域間資料庫？  
我們目前支援的五個秒 RPO 和複寫延隔時間已經過小於地理次要時裝載於 Azure 建議使用成對的地區和在同一個服務層。

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>地理次要建立相同的區域為主要資料庫時，為何預期的複寫延隔時間？  
根據經驗的資料，有太多內部地區碼和間的地區複寫延隔時間之間的差異時不 Azure 建議使用成對的區域。 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>如果有兩個區域間的網路問題，請重試邏輯運作方式設定後地理複寫為何？  
如果中斷連線，我們再試一次每 10 秒重新建立連線。

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>確保複寫的要徑上的主要資料庫變更該怎麼辦？
地理次要非同步複本，而我們不會嘗試將它放在與主要的完整同步處理。 但我們提供的方法，若要強制同步處理，以確保複寫的重大變更 （例如密碼更新）。 強制同步處理會影響效能，因為它會封鎖呼叫執行緒，直到所有認可的交易複寫。 如需詳細資訊，請參閱[sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx)。 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>哪些工具可監控之間的主要資料庫及地理次要複寫延隔時間？
我們會揭露之間的主要資料庫及地理-次要透過 DMV 即時複寫延隔時間。 如需詳細資訊，請參閱[sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx)。
