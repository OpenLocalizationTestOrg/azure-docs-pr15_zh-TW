<properties
   pageTitle="管理運算能力中 Azure SQL 資料倉庫 （剩餘） |Microsoft Azure"
   description="若要管理的 PowerShell 工作計算 power。 縮放比例調整 DWUs 計算資源。 或者，將游標暫停於，然後繼續儲存成本的計算資源。"
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

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>管理運算能力中 Azure SQL 資料倉庫 （剩餘）

> [AZURE.SELECTOR]
- [概觀](sql-data-warehouse-manage-compute-overview.md)
- [入口網站](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [其餘](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


藉由出縮放比例效能計算資源和記憶體以符合您的工作量的變更要求。 儲存成本縮放比例返回資源峰時間或暫停計算期間完全。 

此工作的集合使用 Azure 入口網站︰

- 小數位數計算
- 暫停運算
- 履歷表運算

若要瞭解此問題，請參閱[管理計算概觀][]。

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>縮放比例運算能力

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要變更 DWUs，使用 [[建立或更新資料庫][]的 REST API。 以下範例將 DW1000 的服務等級目標設定資料庫是裝載在伺服器 MyServer MySQLDW。 伺服器是在名為 ResourceGroup1 Azure 資源群組。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>暫停運算

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

若要暫停資料庫，請使用[暫停資料庫][]REST API。 下列範例會暫停名為 Database02 名為 Server01 的伺服器上的資料庫。 伺服器是在名為 ResourceGroup1 Azure 資源群組。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>履歷表運算

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

若要開始資料庫，請使用[履歷表資料庫][]REST API。 下列範例會啟動名為 Database02 名為 Server01 的伺服器上的資料庫。 伺服器是在名為 ResourceGroup1 Azure 資源群組。 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>後續步驟

其他管理工作，請參閱[管理概觀][]。

<!--Image references-->

<!--Article references-->
[管理概觀]: ./sql-data-warehouse-overview-manage.md
[管理計算概觀]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[暫停資料庫]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[履歷表資料庫]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[建立或更新資料庫]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
