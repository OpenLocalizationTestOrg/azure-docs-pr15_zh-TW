<properties
   pageTitle="管理運算能力中 Azure SQL 資料倉庫 （剩餘） |Microsoft Azure"
   description="擴充效能調整 DWUs TRANSACT-SQL (T SQL) 工作。 儲存成本回在非尖峰期間縮放比例。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>管理運算能力中 Azure SQL Data Warehouse (T SQL)

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [其餘](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


藉由出縮放比例效能計算資源和記憶體以符合您的工作量的變更要求。 儲存成本縮放比例返回資源峰時間或暫停計算期間完全。 

此工作的集合使用 T SQL 以︰

- 檢視目前 DWU 設定
- 藉由調整 DWUs 變更計算資源

若要將游標暫停或繼續資料庫，選擇一個頂端的 [這份文件的其他平台選項。

若要瞭解此問題，請參閱[管理計算 power 概觀][]。

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>檢視目前 DWU 設定

若要檢視目前的資料庫 DWU 設定︰

1. 開啟在 Visual Studio 2015 中的 [SQL Server 物件總管]。
2. 連線到母片的邏輯的 SQL 資料庫伺服器相關聯的資料庫。
2. 選取從 sys.database_service_objectives 動態管理檢視]。 以下是範例︰ 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>小數位數計算

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWUs:


1. 連線到母片與邏輯 SQL 資料庫伺服器相關聯的資料庫。
2. 使用[變更資料庫][]的 SQL 陳述式。 以下範例將 DW1000 的服務等級目標設定資料庫 MySQLDW。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟

其他管理工作，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[管理計算 power 概觀]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[變更資料庫]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
