<properties
   pageTitle="雲端損毀修復解決方案 SQL 資料庫作用中地理複寫 |Microsoft Azure"
   description="瞭解如何設計雲端損毀修復解決方案的業務連續性規劃地理複寫用應用程式資料與 Azure SQL 資料庫的備份。"
   keywords="雲端損毀修復、 損毀修復解決方案、 應用程式資料備份、 地理複寫業務連續性規劃"
   services="sql-database"
   documentationCenter=""
   authors="anosov1960"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="07/20/2016"
   ms.author="sashan"/>

# <a name="design-an-application-for-cloud-disaster-recovery-using-active-geo-replication-in-sql-database"></a>設計雲端損毀修復使用 SQL 資料庫中的 [作用中的地理複寫的應用的程式


> [AZURE.NOTE] [作用中的地理複寫](sql-database-geo-replication-overview.md)現在適用於所有層中的所有資料庫。



瞭解如何使用[作用中的地理複寫](sql-database-geo-replication-overview.md)SQL 資料庫在設計資料庫應用程式，同時以地區失敗及嚴重中斷。 如需業務連續性計劃，請考慮應用程式部署拓撲，您的目標，服務等級協定流量延遲和成本。 本文中，我們將常見的應用程式模式，請查看並討論的優點及折衷方案的每個選項。 如需作用中地理複寫有彈性的資料庫時，請參閱[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>設計模式 1︰ 主動被動部署雲端損毀修復放在一起的資料庫

適用於下列特性的應用程式時，此選項︰

+ 在單一 Azure 區域中的作用中執行個體
+ 加強讀寫 (RW) 存取資料之間的相依性
+ 跨地區應用程式邏輯和資料庫之間的連線不接受因為延遲和流量的成本    

在此情況下，應用程式部署拓撲最適合應用程式的所有元件影響，以及需要為單位的容錯移轉時，處理地區損毀。 地理重複，應用程式邏輯和資料庫複寫到另一個區域，但不是使用應用程式工作負載在標準的情況下。 在第二區域中的應用程式應該設定為使用次要資料庫的 SQL 連接字串。 流量管理員已設定為使用[容錯移轉路由方法](../traffic-manager/traffic-manager-configure-failover-routing-method.md)。  

> [AZURE.NOTE] [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)可在本文僅供說明。 您可以使用任何支援容錯移轉路由方法的負載平衡解決方案。    

除了主應用程式執行個體，您應該考慮部署發出定期 T SQL 唯讀 (RO) 命令監視主要資料庫小型[工作者角色應用程式](cloud-services-choose-me.md#tellmecs)。 您可以使用它來自動觸發容錯移轉時，會產生上您的應用程式管理主控台，警示，或以這兩種。 若要確保的監控不受影響整個區域的中斷，您應該監視的應用程式執行個體部署至每個區域，並連線到主要區域中的主要資料庫與本機區域中的次要資料庫每個執行個體。 

> [AZURE.NOTE] 這兩個監視應用程式應該啟用探查主要和次要資料庫。 第二個可用於應用程式不受保護狀態時，在次要地區和提醒偵測失敗。     

下圖顯示前中斷此設定。

![SQL 資料庫地理複製設定。 雲端損毀修復。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

主要區域中斷之後, 監視的應用程式會偵測到主要資料庫是無法存取登錄通知。 根據您的應用程式 SLA，您可以決定多少個連續的監控探查應該失敗之前它宣告資料庫資料。 若要達到互相搭配的容錯移轉應用程式區終端的資料庫，您應該監視的應用程式，請執行下列步驟︰

1. 若要觸發區終端容錯移轉的 [[更新主要區終端的狀態](https://msdn.microsoft.com/library/hh758250.aspx)]。
2. [撥號給次要進行[資料庫容錯移轉](sql-database-geo-replication-portal.md)資料庫。

後移轉後，應用程式會處理第二區域中的使用者要求，但會保留與資料庫放在一起，因為主要資料庫現在會在第二區域中。 下圖說明這種情況。 在所有的圖表，實線表示作用中的連線、 虛線表示已擱置的連線和停駐點符號表示巨集指令引動程序。


![地理複寫︰ 容錯移轉至次要資料庫。 應用程式資料的備份。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

如果發生次要區域中的資料，暫停之間的主要及次要資料庫複寫連結，並監視的應用程式登錄主要資料庫會公開顯示通知。 應用程式的效能不會受到影響，但應用程式的運作方式公開，因此風險較高的大小寫兩個區域失敗連續。

> [AZURE.NOTE] 我們只建議您部署與單一的 DR 區域的設定。 這是因為大部分的 Azure 地區有兩個區域。 這些設定將不會從兩個區域嚴重失敗保護您的應用程式。 在不太事件的這類失敗，您可以復原資料庫中使用[地理還原作業](sql-database-disaster-recovery.md#recovery-using-geo-restore)的第三個區域。

後降低中斷，次要資料庫會自動與主要同步處理。 在同步處理，效能的主要可能稍有影響根據需要進行同步處理的資料量。 下圖顯示在第二區域中的資料。

![第二個的資料庫與主要同步處理。 雲端損毀修復。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


此設計模式的主要**優點**是︰

+ SQL 連接字串中每個地區設定應用程式部署時，不會變更後移轉後。
+ 應用程式的效能不受到容錯移轉應用程式和資料庫一律是放在一起。

主要**取捨**是損毀修復只會用在第二區域中的重複的應用程式執行個體。

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>設計模式 2︰ 主動-主動部署的應用程式負載平衡
適用於下列特性的應用程式時，這個雲端損毀復原選項︰

+ 高比例的資料庫讀取與寫入
+ 資料庫寫入延遲並不會影響使用者體驗  
+ 唯讀邏輯可以讀寫邏輯分隔使用不同的連接字串
+ 唯讀邏輯不會根據資料完整同步處理最新的更新  

如果您的應用程式有下列特性，負載平衡跨多個應用程式執行個體，在不同區域中的使用者連線可以改善效能與使用者體驗。 若要實作負載平衡，每個區域應有使用中應用程式的執行個體以讀寫 (RW) 邏輯連接到主要區域中的主要資料庫。 唯讀 (RO) 邏輯應該連接到第二個資料庫中的應用程式執行個體為相同的區域。 流量管理員應該會設定為使用[循環路由](../traffic-manager/traffic-manager-configure-round-robin-routing-method.md)或[效能路由](../traffic-manager/traffic-manager-configure-performance-routing-method.md)[區終端監控](../traffic-manager/traffic-manager-monitoring.md)啟用的每個應用程式執行個體。

如模式 #1，您應該考慮部署類似監視應用程式。 但不同於模式 #1，監視的應用程式不會負責觸發區終端容錯移轉。

> [AZURE.NOTE] 當此模式使用一個以上的次要資料庫時，只有一個次要連結會能用於容錯移轉先前所述的原因。 此模式需要次要唯讀存取，因為它會需要作用中的地理複寫。

流量管理員應設定路由，將使用者連線至最接近使用者的地理位置的應用程式執行個體的效能。 下圖說明之前中斷此設定。
![沒有資料︰ 效能路由至最接近的應用程式。 地理複寫。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

如果資料庫中斷偵測到的主要區域中，開始第二區域中，其中主要資料庫的容錯的移轉變更主要資料庫的位置。 流量管理員會自動從 [路由] 資料表中排除離線端點，但會繼續使用者流量路由至剩餘的線上執行個體。 主要資料庫現在位於不同的區域，因為所有線上的執行個體必須變更連線至新的主要其讀寫 SQL 連接字串。 請務必您進行這項變更之前初始資料庫容錯移轉。 唯讀 SQL 連接字串應該保持不變，它們永遠指向相同的區域中的資料庫。 容錯移轉步驟如下︰  

1. 變更為指向新的主要讀寫 SQL 連接字串。
2. 呼叫指定的次要資料庫中的順序來[啟動資料庫容錯移轉](sql-database-geo-replication-portal.md)。

下圖顯示新的設定後移轉後。
![後移轉後的設定。 雲端損毀修復。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

第二個區域的其中一種中斷，若流量管理員自動移除離線區終端的區域路由表格。 暫停，該範圍中第二個資料庫複寫頻道。 剩餘的區域在這個案例中，取得其他使用者流量，因為應用程式的效能影響過時期間。 一旦降低中斷，受影響的區域中的次要資料庫會立即與主要同步處理。 同步處理期間的主要效能可能稍有影響根據需要進行同步處理的資料量。 下圖顯示第二個區域的其中一種中斷。

![在第二區域中的資料。 雲端損毀修復-地理複寫。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

此設計模式的主要**優點**是，您可以調整應用程式工作負載跨多個次要連結，以獲得最佳的使用者效能。 這個選項**必須做的取捨**是︰

+ 在應用程式的執行個體和資料庫之間的讀寫連線有不同的延遲及成本
+ 應用程式的效能影響過時期間
+ 應用程式的執行個體，才能動態變更後資料庫移轉後的 [SQL 連接字串。  

> [AZURE.NOTE] 類似的方法可以用於卸載特殊的工作量，例如報告工作、 商務智慧工具或備份。 這些工作負載通常是使用大量的資料庫資源，因此，建議您指定一個次要資料庫，以符合預期的工作量效能層級。

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>設計模式 3︰ 資料保留主動被動部署  
適用於下列特性的應用程式時，此選項︰

+ 資料遺失是高的商業風險。 資料庫容錯移轉只能用於的最後一個步驟，如果資料是永久。
+ 「 唯讀 」 可以運作的應用程式一段時間。

在此模式中，應用程式會切換至連線至次要資料庫時的唯讀模式。 應用程式中的邏輯的主要區域與主要資料庫放在一起，而讀寫模式 (RW) 中的運作方式。 在第二區域中的應用程式邏輯與次要資料庫放在一起，而已準備好操作以唯讀模式 (RO)。  流量管理員應該會設定為使用[容錯移轉路由](../traffic-manager/traffic-manager-configure-failover-routing-method.md)[區終端監控](../traffic-manager/traffic-manager-monitoring.md)啟用的兩個應用程式執行個體。

下圖說明之前中斷此設定。
![容錯移轉之前主動被動部署。 雲端損毀修復。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

當流量管理員偵測到的主要區域連線失敗時，它會自動切換使用者流量至應用程式中的第二個區域。 使用這個模式中，請務必後中斷偵測到您**不要**起始資料庫容錯移轉。 在第二區域中的應用程式已啟動，並使用次要資料庫的唯讀模式中的運作方式。 下圖說明。

![在唯讀模式中的資料︰ 應用程式。 雲端損毀修復。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

一旦降低主要區域中斷，流量管理員偵測還原連線的主要區域，並切換回 [主要區域中的應用程式執行個體的使用者流量。 該應用程式執行個體繼續，並使用的主要資料庫的讀寫模式中的運作方式。

> [AZURE.NOTE] 因為此模式需要唯讀存取次要需要作用中的地理複寫。

若在第二區域中的資料，流量管理員標記主要區域中的應用程式區終端降級與暫停複寫頻道。 不過，此資料不會影響應用程式的中斷的效能。 一旦降低中斷，次要資料庫會立即與主要同步處理。 同步處理期間的主要效能可能稍有影響根據需要進行同步處理的資料量。

![中斷︰ 次要資料庫。 雲端損毀修復。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

此設計模式，有幾項**優點**︰

+ 該期間暫時關閉避免資料遺失。
+ 它不需要您部署監視的應用程式復原觸發流量管理員。
+ 停機時間取決於只流量管理偵測連線失敗，這是可設定的速度。

**必須做的取捨**是︰

+ 應用程式必須能夠以唯讀模式執行。
+ 需要將其連線至唯讀的資料庫時，以動態方式切換至該。
+ 重新繼續讀取寫入作業需要主要區域，這表示完整資料存取可能會停用小時或甚至幾天的復原。

> [AZURE.NOTE] 若永久性服務中斷區域中，您必須以手動方式啟動資料庫容錯移轉，並接受資料遺失。 應用程式就會在與讀寫資料庫的存取權的次要地區功能。

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>業務連續性規劃︰ 選擇雲端損毀修復的應用程式的設計

您特定雲端損毀的復原策略可以結合，或延伸最符合您的應用程式的需求這些設計模式。  如先前所述，您選擇的策略會根據您要提供您的客戶應用程式部署拓撲的 SLA。 為了協助您決定，下表將比較根據估計的資料遺失的選項或復原指向目標 (RPO) 和估計復原時間 (ERT)。

| 圖樣 | RPO | ERT
| :--- |:--- | :---
| 放在一起的資料庫存取損毀修復主動被動部署 | 讀取寫入權限 < 5 秒 | 失敗偵測時間 + 通話的容錯移轉 API + 應用程式驗證測試
| 應用程式負載平衡主動-主動部署 | 讀取寫入權限 < 5 秒 | 失敗偵測時間 + 容錯移轉 API 通話 + SQL 連接字串變更 + 應用程式驗證測試
| 資料保留主動被動部署 | 唯讀存取 < 5 sec 讀取寫入權限 = 零 | 唯讀存取 = 連線失敗偵測時間 + 驗證測試應用程式 <br>讀取寫入權限 = 降低中斷的時間

## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
- 如需作用中地理複寫有彈性的資料庫時，請參閱[彈性的資料庫復原損毀的策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
