<properties
   pageTitle="決定使用 SqlPackage.exe SQL 資料庫相容性 |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉、 SQL 資料庫的相容性，SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>決定使用 SqlPackage.exe SQL 資料庫相容性

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [升級指導](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

本文中，您瞭解如何判斷是否相容移轉到使用[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)命令提示字元公用程式的 SQL 資料庫的 SQL Server 資料庫。

## <a name="using-sqlpackageexe"></a>使用 SqlPackage.exe

1. 開啟命令提示字元，並變更包含 sqlpackage.exe 的最新版本的目錄。 此公用程式隨附的最新版本的[SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)和[SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您可以直接從 Microsoft 下載中心下載最新版的[SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) 。
2. 執行下列 SqlPackage 命令與您的環境的下列引數︰

    「 sqlpackage.exe /Action:Export /ssn: < 管理員 > /sdn: < database_name > /tf: < target_file > /p:TableData = < schema_name.table_name >>< 輸出檔案 > 2 > & 1'

  	| 引數  | 描述  |
  	|---|---|
  	| < 管理員 >  | 來源伺服器名稱  |
  	| < database_name >  | 來源資料庫名稱  |
  	| < target_file >  | 檔案名稱和 BACPAC 檔案的位置  |
  	| < schema_name.table_name >  | 資料表的資料是輸出至的目標檔案  |
  	| < 輸出檔案 >  | 檔案名稱和位置的錯誤，如果有任何輸出檔案  |

    /P:TableName 引數的原因是，我們只想要測試的資料庫的相容性匯出至 Azure SQL 資料庫 V12，而不是所有資料表中的資料匯出。 很抱歉，sqlpackage.exe 的匯出引數不支援從零的資料表。 您要指定至少有一個表格，例如單一的小型表格。 < 輸出檔案 > 包含任何錯誤的報告。 "> 2 > 與 1 」 字串管道標準和指定的輸出檔案命令執行所產生的標準誤差。

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 開啟輸出檔案並檢閱相容性錯誤，如果有的話。 

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [修正資料庫移轉的相容性問題](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [相容的 SQL Server 資料庫移轉到 SQL 資料庫](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
