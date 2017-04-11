<properties
   pageTitle="修正之前移轉到 SQL 資料庫的 SQL Server 資料庫相容性問題 |Microsoft Azure"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>使用 SQL Azure 移轉精靈來修正 SQL Server 資料庫相容性問題，再移轉到 Azure SQL 資料庫

> [AZURE.SELECTOR]
- 使用[SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
- 使用[SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- 使用[SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

在本文中，您學習偵測並修正使用 SQL Azure 移轉精靈之前移轉到 Azure SQL 資料庫的 SQL Server 資料庫相容性問題。

## <a name="using-sql-azure-migration-wizard"></a>使用 SQL Azure 移轉精靈

使用產生 T SQL 指令碼，從相容的來源資料庫的[SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)CodePlex 工具。 這個指令碼然後轉換精靈，使其相容於 SQL 資料庫。 您再連線至執行指令碼 Azure SQL 資料庫。 這項工具也會追蹤檔案來決定相容性問題。 指令碼可以產生只結構描述，或可以包含 BCP 格式的資料。 其他的文件，包括逐步指引會提供 CodePlex 在[SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)。  

 ![SAMW 移轉圖表](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] 並非所有不相容的結構描述未偵測到精靈可以修正的內建的轉換。 不相容的指令碼無法收件者的報告為錯誤，插入產生的指令碼的註解。 如果未偵測到多個錯誤，使用 Visual Studio 或 SQL Server Management Studio 中逐步執行並修正無法修正使用 SQL Server 移轉精靈每個錯誤。

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [相容的 SQL Server 資料庫移轉到 SQL 資料庫](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
