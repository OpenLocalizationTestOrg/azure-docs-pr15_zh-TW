<properties 
    pageTitle="建立或將 Azure SQL 資料庫移至使用 T SQL 彈性資料庫 |Microsoft Azure" 
    description="使用 T SQL 彈性的資料庫中建立 Azure SQL 資料庫。 或使用 T SQL 移動 datbase 與集區登出。" 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>監控和管理 TRANSACT-SQL 彈性的資料庫集區  

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T SQL](sql-database-elastic-pool-manage-tsql.md)

使用 [[建立資料庫 （Azure SQL 資料庫）](https://msdn.microsoft.com/library/dn268335.aspx)及[變更 Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx)命令以建立並移動資料庫入和登出彈性的資料庫。 必須存在於彈性的資料庫，才能使用這些命令。 這些命令會影響只有資料庫。 建立新的集區和集區內容 （例如 min 與 max eDTUs) 設定無法變更的 T SQL 命令。

## <a name="create-a-new-database-in-an-elastic-pool"></a>彈性的資料庫中建立新的資料庫
使用 [建立資料庫命令 SERVICE_OBJECTIVE 選項。   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

彈性的資料庫中的所有資料庫都繼承彈性的資料庫 （Basic、 標準、 Premium） 服務層的級。 


## <a name="move-a-database-between-elastic-pools"></a>彈性的集區之間移動資料庫
使用 [修改 ALTER DATABASE 命令和設定服務\_為彈性的目標選項\_集區。設定名稱的目標資料庫名稱。

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移到彈性的資料庫 
使用 [修改 ALTER DATABASE 命令和設定服務\_ELASTIC_POOL; 為目標選項設定名稱的目標資料庫名稱。

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>將資料庫移出彈性的資料庫
使用 ALTER DATABASE 命令，並設定 SERVICE_OBJECTIVE 到其中的效能層級 （S0、 S1 等）。

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>彈性的資料庫中的清單資料庫
使用[sys.database\_服務\_目標檢視](https://msdn.microsoft.com/library/mt712619)清單彈性的資料庫中的所有資料庫。 登入母片來查詢檢視的資料庫。

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>取得集區的 [資源使用狀況

使用[sys.elastic\_資料庫\_資源\_統計檢視](https://msdn.microsoft.com/library/mt280062.aspx)檢查邏輯伺服器上彈性集區的資源使用狀況統計資料。 登入母片來查詢檢視的資料庫。

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>取得彈性的資料庫中的資源使用狀況

使用[sys.dm\_ db\_資源\_統計檢視](https://msdn.microsoft.com/library/dn800981.aspx)或[sys.resource\_統計檢視](https://msdn.microsoft.com/library/dn269979.aspx)檢查 [資源使用統計的彈性的資料庫中的資料庫。 此程序很類似查詢的任何單一資料庫的 [資源使用狀況。

## <a name="next-steps"></a>後續步驟

之後建立彈性的資料庫資料庫，您可以藉由建立彈性的工作管理集區中有彈性的資料庫。 彈性的工作順利執行 T 指令碼對資料庫集區中的任何數字。 如需詳細資訊，請參閱[彈性的資料庫工作概觀](sql-database-elastic-jobs-overview.md)。 

請參閱[Azure SQL 資料庫時的縮放比例](sql-database-elastic-scale-introduction.md)︰ 若要向外，移動資料，請使用彈性的資料庫工具查詢，或建立交易。
