
<properties
   pageTitle="將 SQL Server 資料庫匯出到 BACPAC 使用 SQL Server Management Studio |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉匯出資料庫]、 [匯出 BACPAC 檔案]，匯出資料層應用程式精靈"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>使用 SQL Server Management Studio BACPAC 檔案匯出的 SQL Server 資料庫

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
本文將示範如何將 SQL Server 資料庫匯出到 SQL Server Management Studio 中使用 [匯出資料層應用程式精靈[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案。 

1. 確認您擁有最新版本的 SQL Server Management Studio 中。 每月維持更新 Azure 入口網站的同步處理更新 Management Studio 中的最新版本。

     > [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 開啟 Management Studio 中，並連線到您的來源資料庫物件的檔案總管中。

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 在來源資料庫物件總管] 中的以滑鼠右鍵按一下，指向 [**工作**] 中，按一下 [**匯出資料層應用程式...**

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在匯出精靈] 中，設定匯出至 BACPAC 將檔案儲存至本機磁碟位置或至 Azure blob。 匯出的 BACPAC 永遠包含完整的資料庫結構描述，根據預設，所有資料表的資料。 如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。 您可能會例如選擇只將資料匯出的參照資料表，而不是從所有資料表。

***重要***匯出 Azure blob 儲存體 BACPAC，使用標準的儲存空間。 不支援從進階版儲存匯入 BACPAC。

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


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
