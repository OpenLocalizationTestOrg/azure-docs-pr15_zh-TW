<properties
   pageTitle="雲端業務連續性-還原已刪除的資料庫的 SQL 資料庫 |Microsoft Azure"
   description="瞭解時間點還原，可讓您回復 Azure SQL 資料庫到上一個點時間 （最多 35 天）。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>復原使用自動化的資料庫備份的 Azure SQL 資料庫

SQL 資料庫提供三個選項可以使用 [ [SQL 資料庫自動備份](sql-database-automated-backups.md)的資料庫復原。 您可以在其[保留期限](sql-database-service-tiers.md)至服務啟動的備份還原的資料庫︰

- 保留期間內的時間至指定的點復原相同的邏輯伺服器上新的資料庫。 
- 復原已刪除的資料庫的刪除時間至相同邏輯伺服器的資料庫。
- 復原最近的每日備份地理複寫 blob 儲存體 (RA GRS) 中的任何區域中的任何邏輯伺服器上新的資料庫。

您也可以使用[SQL 資料庫自動備份](sql-database-automated-backups.md)，若要建立與目前的 SQL 資料庫交易一致的任何區域中的任何邏輯伺服器上的 [[複製資料庫](sql-database-copy.md)。 您可以使用資料庫複製] 和 [[匯出至 BACPAC](sql-database-export.md)封存交易一致的資料庫，以長期儲存您的保留期間，除了複本或資料庫的副本傳送至內部部署或 SQL Server Azure VM 執行個體。

## <a name="recovery-time"></a>復原時間

若要還原使用自動化的資料庫備份的資料庫復原時間受到許多因素︰ 
 - 資料庫大小
 - 資料庫的效能層級
 - 交易記錄檔的相關的數字
 - 必須重新執行復原還原點之活動的數量
 - 如果還原的不同區域網路頻寬 
 - 在 [目標區域正在處理的同時還原要求數目。 
 
 為很大，及/或作用中的資料庫還原可能需要幾個小時。 如果區域中有長期的資料，則可能會有大量地理還原要求正在處理的其他區域。 如果有大量的要求，這可能會增加復原時間，在該區域的資料庫。 大部分的資料庫還原 12 小時內完成。

 有任何內建的功能，若要執行大量還原。 [Azure SQL 資料庫︰ 完整伺服器復原](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)指令碼是一種完成這項工作的範例。

> [AZURE.IMPORTANT] 若要復原使用自動的備份，您必須是訂閱中的 SQL Server 參與者角色的成員，或是訂閱擁有者。 您可以復原使用 PowerShell 或 REST API Azure 中心。 您無法使用 TRANSACT-SQL。 

## <a name="point-in-time-restore"></a>時間點還原

還原時間點，可讓您在使用[SQL 資料庫自動備份](sql-database-automated-backups.md)相同邏輯伺服器上的時間，為新資料庫至之前還原現有的資料庫。 您無法覆寫現有的資料庫。 使用[Azure 入口網站](sql-database-point-in-time-restore-portal.md)、 [PowerShell](sql-database-point-in-time-restore-powershell.md)或[REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx)的時間，您可以還原舊版的點。

> [AZURE.SELECTOR]
- [時間點還原︰ Azure 入口網站](sql-database-point-in-time-restore-portal.md)
- [時間點還原︰ PowerShell](sql-database-point-in-time-restore-powershell.md)

資料庫可還原至任何效能層級 」 或 「 彈性資料庫。 您必須以確定您有足夠的 DTU 配額邏輯伺服器或彈性的資料庫。 請記住，還原會建立一個新的資料庫，並還原資料庫的服務層和效能層級可能會不同於目前的即時資料庫狀態。 完成之後，還原的資料庫是標準完全可存取線上資料庫根據其服務層和效能層級的標準工資率支付薪資。 資料庫還原完成後，才不會造成費用。

您通常會還原為 earler 點來復原的資料庫。 當這麼一來，您可以還原的資料庫視為取代原始資料庫，或用它來擷取資料，並更新原始的資料庫。 

- ***資料庫取代︰***如果已還原的資料庫要做為取代原始資料庫，您應該確認效能層級及/或服務層適當和縮放必要的資料庫。 您可以重新命名原始的資料庫，，然後將 T SQL 中使用的 ALTER DATABASE 命令的原始名稱還原的資料庫。 
- ***資料修復︰***如果您打算擷取從使用者或應用程式錯誤修復還原的資料庫中的資料，您會分別要撰寫及執行還原的資料庫中的資料擷取原始資料庫，您需要的任何資料復原指令碼。 雖然還原作業可能會花很長的時間才能完成，則會顯示整個資料庫清單中還原的資料庫。 如果您刪除資料庫還原時，它會取消作業，您不需要付費的未完成還原資料庫。 

如需詳細瞭解如何使用時間點還原来還原的使用者和應用程式錯誤的詳細資訊，請參閱[時間點還原](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>還原已刪除的資料庫

還原已刪除的資料庫可讓您將已刪除的資料庫還原已刪除的資料庫，使用 [ [SQL 資料庫自動備份](sql-database-automated-backups.md)相同的邏輯伺服器上的刪除時間。 

> [AZURE.IMPORTANT] 如果您刪除 Azure SQL 資料庫伺服器執行個體，所有其資料庫也會一併刪除，且無法復原。 有不支援這次還原已刪除的伺服器。

您可以使用相同或已還原的資料庫的新資料庫名稱。 您可以使用[Azure 入口網站](sql-database-restore-deleted-database-portal.md)， [PowerShell](sql-database-restore-deleted-database-powershell.md)或[其餘 (createMode = 還原)](https://msdn.microsoft.com/library/azure/mt163685.aspx)。 

> [AZURE.SELECTOR]
- [刪除資料庫還原︰ Azure 入口網站](sql-database-restore-deleted-database-portal.md)
- [刪除資料庫還原︰ PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>地理還原

地理還原可讓您從最新地理複寫[自動化日常備份](sql-database-automated-backups.md)還原任何 Azure 區域中的任何伺服器上 SQL 資料庫。 地理還原為其來源使用地理多餘的備份，可以用來復原資料庫，即使資料中心的資料庫是無法中斷。 您可以使用[Azure 入口網站](sql-database-geo-restore-portal.md)， [PowerShell](sql-database-geo-restore-powershell.md)或[其餘 (createMode = 復原)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [地理還原︰ Azure 入口網站](sql-database-geo-restore-portal.md)
- [地理還原︰ PowerShell](sql-database-geo-restore-powershell.md)

地理還原資料庫裝載於何處地區事件因為無法使用您的資料庫時的預設復原選項。 如果在區域中無法使用應用程式的結果資料庫大型事件，您可以使用地理還原至任何其他區域中的伺服器最近的備份還原的資料庫。 所有的備份地理複寫，而且可以有延遲時間備份時拍攝和地理將複寫到 Azure blob 不同區域。 此延遲可達一小時，因此損毀事件設定 1 小時資料遺失，亦即 RPO 最 1 小時。 下列顯示的最後一個的每日備份還原的資料庫。

![地理還原](./media/sql-database-geo-restore/geo-restore-2.png)

詳細瞭解如何使用地理還原從中斷復原的詳細資訊，請參閱[從中斷復原](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] 可用的所有服務層地理還原時，則基本的 SQL 資料庫的最長 RPO 和估計復原時間 (ERT) 中的損毀復原解決方案。 基本資料庫的最大 2 GB 的地理還原提供適當的 DR 解決方案，以 12 小時的 ERT。 對於較大的標準或進階版資料庫，如果大幅需要為較短復原次數，或以避免資料遺失您應該考慮使用作用中的地理複寫。 作用中的地理複寫提供更低的 RPO 和 ERT 時，只需要啟動以持續複寫次要容錯移轉。 如需詳細資訊，請參閱[使用中的地理複寫](sql-database-geo-replication-overview.md)。

## <a name="programmatically-performing-recovery-using-automated-backups"></a>以程式設計方式執行修復使用自動的備份

如上述述 addiition Azure 入口網站，就可以 programmically 使用 PowerShell 的 Azure 和 REST API 來執行資料庫復原。 下表說明一組可用的命令。

### <a name="powershell"></a>PowerShell

|Cmdlet|描述|
|------|-----------|
|[取得 AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|取得一或多個資料庫。|
|[取得 AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|取得您可以還原已刪除的資料庫。|
|[取得 AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|取得地理多餘資料庫的備份。|
|[還原 AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|還原 SQL 資料庫。|
||||

### <a name="rest-api"></a>REST API

|API|描述|
|---|-----------|
|[其餘 (createMode = 復原)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|還原資料庫|
|[取得建立或更新資料庫的狀態](https://msdn.microsoft.com/library/azure/mt643934.aspx)|還原作業期間會傳回狀態|
||||



## <a name="summary"></a>摘要

自動備份使用者和應用程式錯誤、 意外資料庫刪除和延長的中斷保護您的資料庫。 此零成本零管理員方案中皆可取得所有 SQL 資料庫。 

## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
