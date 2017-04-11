<properties
   pageTitle="從 SqlPackage BACPAC 檔案匯入到 SQL 資料庫"
   description="Microsoft Azure SQL 資料庫，資料庫移轉匯入資料庫，匯入 BACPAC 檔案，sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>從 SqlPackage BACPAC 檔案匯入到 SQL 資料庫

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

本文說明如何從使用[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)命令列公用程式[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案匯入到 SQL 資料庫。 此公用程式隨附的最新版本的[SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)和[SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)，或者您可以直接從 Microsoft 下載中心下載最新版的[SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) 。


> [AZURE.NOTE] 下列步驟假設您已經有提供 SQL 資料庫伺服器、 連線資訊準備，並確認您原始檔資料庫是相容。

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>從 BACPAC 檔案匯入使用 SqlPackage Azure SQL 資料庫

使用下列步驟使用[SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)命令列公用程式匯入相容的 SQL Server 資料庫 （或 Azure SQL 資料庫） 從 BACPAC 檔案。

> [AZURE.NOTE] 下列步驟假設您已經有佈建 Azure SQL 資料庫伺服器，並準備妥當的連線資訊。

1. 開啟命令提示字元中，變更包含 sqlpackage.exe 命令列公用程式的目錄-此公用程式隨附 Visual Studio 和 SQL Server。
2. 執行下列 sqlpackage.exe 命令與您的環境的下列引數︰

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| 引數  | 描述  |
  	|---|---|
  	| < 管理員 >  | 目標伺服器名稱  |
  	| < database_name >  | 目標資料庫名稱  |
  	| < 電腦 >  | 在目標伺服器的使用者名稱 |
  	| < 密碼 >  | 使用者的密碼  |
  	| < source_file >  | 匯入 BACPAC 檔案的位置與檔案名稱  |

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
