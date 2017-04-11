<properties
   pageTitle="移轉至 SQL 資料庫交易複寫 |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉匯入資料庫交易複寫"
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
   ms.workload="sqldb-migrate"
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>將 SQL Server 資料庫移轉到使用交易複寫 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [交易複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

本文中，您瞭解如何將相容的 SQL Server 資料庫移轉到 Azure SQL 資料庫中，以使用 SQL Server 交易複寫的最小停機時間。

## <a name="understanding-the-transactional-replication-architecture"></a>了解交易複寫架構

當您不能從生產移除 SQL Server 資料庫，移轉發生時，您可以使用 SQL Server 交易複寫為您移轉的解決方案。 若要使用此方案，您會為您想要移轉的內部部署 SQL Server 執行個體的訂閱者設定 Azure SQL 資料庫。 內部部署交易複寫經銷商同步處理進行同步處理 （發行者），而新的交易繼續發生內部資料庫中的資料。 

您也可以使用交易複寫移轉內部部署資料庫的子集合。 您複製到 Azure SQL 資料庫出版物可以限制複寫資料庫資料表中的子集。 複寫每個資料表，您可以限制的資料列的子集，及/或資料行的子集合。

交易式複寫到您的資料或結構描述的所有變更都顯示在您 Azure SQL 資料庫。 同步處理已完成並準備好要移轉之後，變更您的應用程式指引他們參考 Azure SQL 資料庫的連線字串。 一旦交易複寫清空您內部部署的資料庫，所有 Azure DB 指向您的應用程式都上的任何變更，您可以解除安裝交易複寫。 Azure SQL 資料庫現在生產系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>交易複寫需求

交易複寫自 SQL Server 6.5 是內建和整合式的 SQL Server 技術。 它是成熟而證明技術，大部分的 Dba 知道具有其體驗。 使用[SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)中，您就可以為內部部署出版物的[交易複寫訂閱者](https://msdn.microsoft.com/library/mt589530.aspx)設定 Azure SQL 資料庫。 如果您設定的內部部署伺服器上的交易複寫訂閱者您存取設定從 Management Studio 中的體驗都是一樣的。 當發行者以及散發至少一個下列 SQL Server 版本，支援的方案支援這種情況︰

 - SQL Server 2016 及以上 
 - SQL Server 2014 SP1 CU3 及以上
 - SQL Server 2014 RTM CU10 及以上
 - SQL Server 2012 SP2 CU8 及以上
 - SQL Server 2012 SP3 及以上


> [AZURE.IMPORTANT] 使用最新版本的 SQL Server Management Studio 維持同步處理更新 Microsoft Azure SQL 資料庫。 較舊版本的 SQL Server Management Studio 無法設定為訂閱者 SQL 資料庫。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


## <a name="next-steps"></a>後續步驟

- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>其他資源

- [交易複寫](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
