<properties
   pageTitle="SQL Data Warehouse 備份 |Microsoft Azure"
   description="深入了解 SQL Data Warehouse 內建的資料庫備份，讓您能夠還原 Azure SQL Data Warehouse 還原點或不同的地理區域。"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>SQL Data Warehouse 備份

SQL Data Warehouse 提供本機和地理備份其資料倉庫備份功能的一部分。 包括 Azure 儲存體 Blob 快照和地理多餘的儲存空間。 使用資料倉庫備份還原您的資料倉庫中的主要區域，還原點或還原到不同的地理區域。 本文說明在 SQL Data Warehouse 備份的細節。

## <a name="what-is-a-data-warehouse-backup"></a>什麼是資料倉庫備份？

倉庫備份資料是您可以使用資料倉庫還原的特定時間的資料。  由於 SQL Data Warehouse 分散式的系統，資料倉庫備份包括中 Azure blob 儲存的多個檔案。 

資料庫備份的業務連續性和損壞復原策略不可或缺的一部分，，從意外損毀或刪除保護您的資料。 如需詳細資訊，請參閱[業務連續性概觀](../sql-database/sql-database-business-continuity.md)。

## <a name="data-redundancy"></a>資料重複

SQL Data Warehouse 會將您的資料儲存在本機多餘的 (LRS) Azure 進階版儲存用來保護您的資料。 Azure 儲存體功能會同步的多份複本資料儲存本機資料中心以確保透明資料保護，如果有本地化的失敗。 資料重複可確保您的資料不會受基礎結構的問題，例如磁碟失敗。 業務連續性與容錯和高度可用的基礎結構，可確保資料重複。

若要深入瞭解︰

- Azure 進階版的儲存空間，請參閱[Azure 進階版儲存空間的簡介](../storage/storage-premium-storage.md)。
- 在本機上多餘的儲存空間，請參閱[Azure 儲存體複寫](../storage/storage-redundancy.md#locally-redundant-storage)。


## <a name="azure-storage-blob-snapshots"></a>Azure Blob 儲存體快照集

使用 Azure 進階版儲存空間的優點，為 SQL Data Warehouse 會使用備份資料倉庫本機 Azure 儲存體 Blob 快照集。 您可以將資料倉庫還原快照還原點。 快照啟動每個八小時的最小值，並可七天。  

若要深入瞭解︰

- Azure blob 快照，請參閱[建立 blob 快照](../storage/storage-blob-snapshots.md)。


## <a name="geo-redundant-backups"></a>地理多餘的備份

每 24 小時 SQL Data Warehouse 會完整資料倉庫儲存在標準的儲存空間。 完整資料倉庫會建立以符合最後一次快照的時間。 標準的儲存空間所屬的讀取權限 (RA GRS) 的地理多餘的儲存空間，帳戶。 Azure 儲存體 RA GRS 功能會將備份檔案複製到[成對的資料中心](../best-practices-availability-paired-regions.md)。 此地理複寫可確保您可以還原資料倉庫，以防您無法存取您的主要區域中的快照。 

這項功能預設為開啟。 如果您不想要使用地理多餘的備份，您可以退出。 

>[AZURE.NOTE] Azure 儲存空間，在字詞*複寫*是指檔案複製到另一個位置。 SQL 的*資料庫複寫*指的是保留多重副的資料庫與主要資料庫同步處理。 

若要深入瞭解︰
- 地理多餘的儲存空間，請參閱[Azure 儲存體複寫](../storage/storage-redundancy.md)。
- RA GRS 儲存空間，請參閱[讀取權限地理多餘的儲存空間](../storage/storage-redundancy.md#read-access-geo-redundant-storage)。

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>資料倉庫備份排程和保留期限

SQL Data Warehouse 建立快照，在您的線上資料倉儲每四到八小時，並保留七天的每個快照集。 您可以還原線上資料庫的其中一個還原點在過去七天。 

若要查看的最後一個快照集啟動時，請在 [您的線上 SQL Data Warehouse 上執行這個查詢。 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

如果您需要保留超過七天快照，可以在新的資料倉庫還原還原點。 還原完成之後，SQL Data Warehouse 開始建立新的資料倉庫快照集。 如果您沒有新的資料倉庫進行變更，請快照保持空白，因此快照成本最小。 您也可以暫停資料庫，以防止 SQL Data Warehouse 建立快速查詢。


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>是什麼？ 我備份保留以暫停我資料倉庫時

SQL Data Warehouse 不會建立快速查詢和資料倉庫暫停時，不會過期的快照。 資料倉庫暫停時，不會變更快照年齡。 快照保留為基礎的資料倉庫在線上，不行事曆日的天數。

例如，如果快照開始編號，下午 4 年 10 月 1 資料倉庫已暫停下午 4 年 10 月 3，快照集就是兩天。 資料倉庫上線快照集有兩天。 如果資料倉庫上線年 10 月 5，4 pm，快照是兩天，並保持 5 天。

資料倉庫恢復連線時，SQL Data Warehouse 時繼續採用新的快照，到期時有超過七天內的資料的快照。

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>中斷的資料倉庫的保留期限是多久？
當資料倉庫卸除之後時，會儲存七天資料倉庫並快照，然後移除。 您可以將資料倉庫還原任何已儲存的還原點。

> [AZURE.IMPORTANT] 如果您刪除邏輯的 SQL server 執行個體，也會一併刪除所有執行個體所屬的資料庫，且無法復原。 您無法還原已刪除的伺服器。

## <a name="data-warehouse-backup-costs"></a>資料倉庫備份成本

總成本您的主要資料倉庫和 Azure Blob 快照的七天四捨五入到最接近的 TB。 例如，如果您的資料倉庫是 1.5 TB 快照使用 100 GB，是向您收費為 2 TB Azure 進階版儲存體率的資料。 

>[AZURE.NOTE] 每個快照最初是空值，然後隨著主資料倉庫中進行變更。 當資料倉庫變更大小增加所有快照集。 因此，快照的儲存空間成本增加根據變更率。

如果您使用的地理多餘的儲存空間，您會收到不同的儲存空間收費。 在標準的讀取權限地理區域多餘的儲存 (RA GRS) 工資率為付費地理多餘的儲存空間。

如需有關 SQL Data Warehouse 價格的詳細資訊，請參閱[SQL 資料倉庫價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="using-database-backups"></a>使用資料庫備份

SQL 資料倉庫備份的主要用途是保留期間內的其中一個還原點還原資料倉庫。  

- 若要還原 SQL 資料倉庫，請參閱[還原 SQL 資料倉庫](sql-data-warehouse-restore-database-overview.md)。


## <a name="related-topics"></a>相關的主題

### <a name="scenarios"></a>案例

- 業務連續性的概觀資訊，請參閱[業務連續性概觀](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- 若要還原資料倉庫，請參閱[還原 SQL 資料倉庫](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

