<properties 
   pageTitle="SQL 資料庫損毀復原切入 |Microsoft Azure" 
   description="瞭解指引與執行損毀復原切入，使用 Azure SQL 資料庫的最佳做法保留您的任務要徑商務應用程式，同時以失敗及中斷。" 
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
   ms.date="07/31/2016"
   ms.author="sstein; sashan"/>

#<a name="performing-disaster-recovery-drill"></a>執行損毀復原向下切入

建議的應用程式整備復原工作流程驗證會定期執行。 驗證應用程式的行為和影響的資料遺失及/或中斷的容錯移轉涉及工程考慮。 也是屬於業務連續性憑證大部分業界標準的需求。

執行損毀復原切入所組成︰

- 模擬資料層中斷
- 復原 
- 驗證應用程式完整性文章復原

根據如何您[設計您的業務連續性的應用程式](sql-database-business-continuity.md)，請執行查看工作流程可以視情況而定。 以下我們會說明執行損毀復原查看 Azure SQL 資料庫的內容中的最佳作法。 

##<a name="geo-restore"></a>地理還原

若要防止資料遺失，進行損毀復原切入時，我們建議您執行建立生產環境的複本，並使用它來驗證應用程式的容錯移轉工作流程使用的測試環境向下切入。
 
####<a name="outage-simulation"></a>中斷模擬

若要模擬中斷，您可以刪除或重新命名在來源資料庫。 這會使應用程式連線失敗。 

####<a name="recovery"></a>復原

- 以 [所述[以下](sql-database-disaster-recovery.md)到不同的伺服器執行地理還原的資料庫。 
- 變更應用程式設定連線到復原的資料庫，並依照完成復原[設定資料庫復原後](sql-database-disaster-recovery.md)指南。

####<a name="validation"></a>驗證

- 驗證應用程式完整性文章復原 （亦即連接字串，登入、 基本功能測試或其他驗證標準應用程式 signoffs 程序的一部分），完成的向下切入。

##<a name="geo-replication"></a>地理複寫

受到保護使用地理複寫資料庫的向下切入練習會涉及計劃的容錯移轉至次要資料庫。 計劃的容錯移轉可確保您的主要及次要資料庫仍保留在同步處理時切換角色。 不同於意外的容錯移轉，這項作業不會導致資料遺失，所以向下切入可以執行生產環境中。 

####<a name="outage-simulation"></a>中斷模擬

若要模擬的中斷連線至資料庫的虛擬機器的 web 應用程式，可以停用。 這會導致連線失敗，適用於網頁用戶端。

####<a name="recovery"></a>復原

- 請確定應用程式中設定的 DR 地區指向離職次要將變成完全可存取的新主要。 
- 執行[計劃容錯移轉](sql-database-geo-replication-powershell.md#initiate-a-planned-failover)進行新的主要的次要資料庫
- 依照完成復原[設定資料庫復原後](sql-database-disaster-recovery.md)指南。

####<a name="validation"></a>驗證

- 驗證應用程式完整性文章復原 （亦即連接字串，登入、 基本功能測試或其他驗證標準應用程式 signoffs 程序的一部分），完成的向下切入。


## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解業務連續性案例，請參閱[連續性案例](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
