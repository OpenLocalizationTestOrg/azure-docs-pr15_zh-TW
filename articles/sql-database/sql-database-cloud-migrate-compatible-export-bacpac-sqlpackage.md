<properties
   pageTitle="將 SQL Server 資料庫匯出至 BACPAC 檔案 SqlPackage |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉匯出資料庫]、 [匯出 BACPAC 檔案，sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>將 SQL Server 資料庫匯出至 SqlPackage BACPAC 檔案

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

本文將示範如何將 SQL Server 資料庫匯出到[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)命令列公用程式。 此公用程式隨附的最新版本的[SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)和[SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您可以直接從 Microsoft 下載中心下載最新版的[SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) 。

1. 開啟命令提示字元中，變更包含 sqlpackage.exe 命令列公用程式的目錄-此公用程式隨附 Visual Studio 和 SQL Server。 使用您的電腦上的搜尋，尋找您的環境中的路徑。
2. 執行下列 sqlpackage.exe 命令與您的環境的下列引數︰

    「 sqlpackage.exe /Action:Export /ssn: < 管理員 > /sdn: < database_name > /tf: < target_file >

  	| 引數  | 描述  |
  	|---|---|
  	| < 管理員 >  | 來源伺服器名稱  |
  	| < database_name >  | 來源資料庫名稱  |
  	| < target_file >  | 檔案名稱和 BACPAC 檔案的位置  |

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [匯入 BACPAC 使用 SSMS Azure SQL 資料庫](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [匯入至 Azure SQL 資料庫 SqlPackage 的 BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [匯入 BACPAC Azure SQL 資料庫 Azure 入口網站](sql-database-import.md)
- [匯入 BACPAC Azure SQL 資料庫 PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
