<properties
   pageTitle="修正使用 SQL Server 安全性群組 Studio 之前移轉到 SQL 資料庫的 SQL Server 資料庫相容性問題 |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉、 相容性，SQL Azure 移轉精靈"
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
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>修正使用 SQL Server Management Studio 之前移轉到 SQL 資料庫的 SQL Server 資料庫相容性問題

> [AZURE.SELECTOR]
- 使用[SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
- 使用[SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- 使用[SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

進階的使用者可以修正使用 SQL Server Management Studio 之前移轉到 Azure SQL 資料庫的 SQL Server 資料庫相容性問題。


> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio 中

使用 SQL Server Management Studio 修正使用各種 SQL 命令，例如**變更資料庫**的相容性問題。 這個方法主要的進階的使用者，都能輕鬆地處理 TRANSACT-SQL 即時資料庫。 否則，建議您使用 SSDT。 



## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [相容的 SQL Server 資料庫移轉到 SQL 資料庫](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
