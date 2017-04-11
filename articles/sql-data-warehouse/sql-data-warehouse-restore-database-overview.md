<properties
   pageTitle="SQL Data Warehouse 還原 |Microsoft Azure"
   description="復原資料庫中 Azure SQL Data Warehouse 資料庫還原選項的概觀。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>SQL Data Warehouse 還原

> [AZURE.SELECTOR]
- [概觀][]
- [入口網站][]
- [PowerShell][]
- [其餘][]

SQL Data Warehouse 提供本機和地理還原為其資料倉庫損毀的部分修復功能。 使用資料倉庫備份還原您的資料倉庫還原中的點的主要區域，或使用地理多餘的備份還原到不同的地理區域。 本文說明還原資料倉庫的細節。

## <a name="what-is-a-data-warehouse-restore"></a>什麼是資料倉庫還原？

資料倉庫還原是從現有的備份檔案所建立的新資料倉庫或刪除的資料倉庫。 還原的資料倉庫重新建立備份資料倉庫在特定時間。 由於 SQL Data Warehouse 分散式的系統，會建立多個備份檔案儲存於 Azure blob 資料倉庫還原。 

還原資料庫是業務連續性和損壞復原策略不可或缺的一部分，因為意外損毀或刪除後重新建立您的資料。

如需詳細資訊，請參閱︰

-  [SQL Data Warehouse 備份](sql-data-warehouse-backups.md)
-  [業務連續性概觀](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>倉庫還原的資料點

使用 Azure 進階版儲存空間的優點，為 SQL Data Warehouse 會使用備份主要資料倉庫 Azure 儲存體 Blob 快照集。 每個快照有還原點代表快照開始的時間。 若要還原資料倉庫，您可以選擇還原點，並還原命令。  

SQL Data Warehouse 一律會還原至新的資料倉庫。 您可以保留還原的資料倉庫 」 與 「 目前項目，或刪除其中之一。 如果您想要還原的資料倉庫取代目前的資料倉庫，您就可以將它重新命名。

如果您要還原已刪除或暫停資料倉庫，您可以[建立的支援票證](sql-data-warehouse-get-started-create-support-ticket.md)。 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>地理多餘還原

如果您使用的地理多餘的儲存空間，您可以還原資料倉庫，[即成對的資料中心](../best-practices-availability-paired-regions.md)不同地理區域。 資料倉庫會從上一次的每日備份還原。 

## <a name="restore-timeline"></a>還原時間表

您可以在過去七天內的任何位置使用還原還原資料庫。 快照開始每四到八小時，可以使用七天。 快照超過七天後，請將其到期，而且其還原點已不再提供。

## <a name="restore-costs"></a>還原成本

還原的資料倉庫儲存費用被付費 Azure 進階版儲存空間的速度。 

如果您將游標停留還原的資料倉庫，您會在 Azure 進階版儲存體工資率的儲存空間。 暫停的優點是您不會 DWU 計算資源的。

如需有關 SQL Data Warehouse 價格的詳細資訊，請參閱[SQL 資料倉庫價格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="uses-for-restore"></a>還原的用途

資料倉庫還原的主要用途是復原之後意外的資料遺失或損毀的資料。

您也可以使用資料倉庫還原保留超過七天備份。 之後還原備份，您有資料倉庫線上，而且可以將游標暫停於其無限儲存計算成本。 暫停的資料庫會儲存費用 Azure 進階版儲存空間的速度。 

## <a name="related-topics"></a>相關的主題

### <a name="scenarios"></a>案例

- 業務連續性的概觀資訊，請參閱[業務連續性概觀](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

若要執行資料倉庫還原，還原使用︰

- Azure 入口網站，請參閱[還原使用 Azure 入口網站的資料倉庫](sql-data-warehouse-restore-database-portal.md)
- PowerShell cmdlet，請參閱[還原使用 PowerShell cmdlet 資料倉庫](sql-data-warehouse-restore-database-powershell.md)
- 將 Api，請參閱[還原使用 REST Api 資料倉庫](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[概觀]: ./sql-data-warehouse-restore-database-overview.md
[入口網站]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[其餘]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
