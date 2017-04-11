<properties
   pageTitle="移轉到 Azure SQL 資料庫的 SQL Server 資料庫 |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫部署資料庫移轉，匯入資料庫，匯出資料庫，移轉精靈"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>從 BACPAC 匯入至 SSMS SQL 資料庫

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

本文說明如何從[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案匯入 SQL 資料庫至 SQL Server Management Studio 中使用 [匯出資料層應用程式精靈。

> [AZURE.NOTE] 下列步驟假設您已經有提供您 Azure SQL 邏輯的執行個體，並準備妥當的連線資訊。

1. 確認您擁有最新版本的 SQL Server Management Studio 中。 每月維持更新 Azure 入口網站的同步處理更新 Management Studio 中的最新版本。

     > [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 連線至 Azure SQL 資料庫伺服器，以滑鼠右鍵按一下 [**資料庫**] 資料夾，按一下 [**匯入資料層應用程式...**

    ![匯入資料層應用程式] 功能表項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  若要在 Azure SQL 資料庫中建立資料庫，BACPAC 檔案匯入您的本機磁碟或選取的 Azure 儲存體帳戶與容器您上傳 BACPAC 檔案。

    ![匯入設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] 從 Azure blob 儲存體中匯入 BACPAC，使用標準的儲存空間。 不支援從進階版儲存匯入 BACPAC。

4.  提供 Azure SQL 資料庫上的資料庫的**新的資料庫名稱**，設定**版本的 Microsoft Azure SQL 資料庫**（服務層）、**資料庫最大值**] 及 [**服務目標**（效能層級）。

    ![資料庫的設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  按一下 [**下一步**，然後按一下 [**完成**] BACPAC 檔案匯入新的資料庫中的 Azure SQL 資料庫伺服器。

6. 使用物件檔案總管，連線到您移轉的資料庫，在您的 Azure SQL 資料庫伺服器。

6.  使用 [Azure 入口網站，請檢視您的資料庫，其內容。

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
