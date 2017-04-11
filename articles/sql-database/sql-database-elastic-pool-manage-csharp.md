<properties
    pageTitle="監控和管理與 C# 彈性的資料庫集區 |Microsoft Azure"
    description="您可以使用 C# 資料庫開發技術來管理 Azure SQL 資料庫彈性的資料庫集區。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>監控和管理與 C 與 #x 23; 彈性的資料庫集區 

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T SQL](sql-database-elastic-pool-manage-tsql.md)


瞭解如何管理使用 C #x 23;[彈性的資料庫集區](sql-database-elastic-pool.md)。 

>[AZURE.NOTE] 您使用的[Azure 資源管理員部署模型](../azure-resource-manager/resource-group-overview.md)，因此您應該一律使用最新版時，才支援的 SQL 資料庫的許多新功能**Azure SQL 資料庫管理文件庫的.NET ([文件](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**。 較舊的[傳統部署模型為基礎的文件庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)支援的回溯相容性，因此我們建議您使用較新的資源管理員根據文件庫。

若要完成此文件中的步驟進行，您需要下列項目︰

- 彈性資料庫 （要管理的資料庫）。 若要建立資料庫，請參閱[建立與 C# 彈性的資料庫集區](sql-database-elastic-pool-create-csharp.md)。
- Visual Studio 中。 Visual Studio 免費複本，請參閱[Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs)頁面。


## <a name="move-a-database-into-an-elastic-pool"></a>將資料庫移到彈性的資料庫

您可以移動獨立的資料庫，近或拉集區。  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>彈性的資料庫中的清單資料庫

若要擷取的資料庫中的所有資料庫，請連絡[ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases)方法。

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>變更集區的效能設定

擷取現有的資料庫屬性。 修改值，然後執行 CreateOrUpdate 方法。

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>彈性的資料庫作業的延遲

- 在 5 分鐘之內，通常變更每個資料庫的資料庫或最大值 eDTUs 每 min eDTUs 會完成。
- 若要變更資料庫大小 (eDTUs) 的時間取決於集區中的所有資料庫的組合大小。 變更計算平均值 90 分鐘或更少每 100 GB。 例如，如果變更每個集區資料庫 eDTU 是 3 個小時之內的集區中的所有資料庫的總空間 200 GB，然後預期的延遲。




## <a name="additional-resources"></a>其他資源

- [SQL 資料庫用戶端應用程式的 SQL 錯誤碼︰ 資料庫連線錯誤及其他問題](sql-database-develop-error-messages.md)。
- [SQL 資料庫](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure 資源管理 Api](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [建立新的彈性的資料庫集區 C#](sql-database-elastic-pool-create-csharp.md)
- [何時使用彈性的資料庫資料庫？](sql-database-elastic-pool-guidance.md)
- 請參閱[Azure SQL 資料庫時的縮放比例](sql-database-elastic-scale-introduction.md)︰ 若要向外，移動資料，請使用彈性的資料庫工具查詢，或建立交易。

