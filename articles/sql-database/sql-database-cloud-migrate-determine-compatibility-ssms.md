<properties
   pageTitle="使用 SQL Server Management Studio 決定 SQL 資料庫移轉到 Azure SQL 資料庫前的相容性 |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉、 SQL 資料庫相容性、 匯出資料層應用程式精靈"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>使用 SQL Server Management Studio 決定 SQL 資料庫移轉到 Azure SQL 資料庫前的相容性

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [升級指導](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
在您瞭解如何判斷是否 SQL Server 資料庫這份文件是相容移轉到 SQL 資料庫的 SQL Server Management Studio 中使用 [匯出資料層應用程式精靈。

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio 中

1. 確認您擁有最新版本的 SQL Server Management Studio 中。 每月維持更新 Azure 入口網站的同步處理更新 Management Studio 中的最新版本。

     > [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 開啟 Management Studio 中，並連線到您的來源資料庫物件的檔案總管中。
3. 在來源資料庫物件總管] 中的以滑鼠右鍵按一下，指向 [**工作**] 中，按一下 [**匯出資料層應用程式...**

    ![匯出資料層應用程式從 [工作] 功能表](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在匯出精靈] 中，按 [**下一步**，然後 [**設定**] 索引標籤設定匯出至本機磁碟位置或 Azure blob 儲存 BACPAC 檔案。 如果您有沒有資料庫相容性問題，BACPAC 檔案會儲存。 如果有相容性問題，他們會顯示在主控台中。

    ![匯出的設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. 若要跳過匯出資料，請按一下**進階] 索引標籤**，然後清除**所有選取**的核取方塊。 現在，我們的目標是只若要測試的相容性。

    ![匯出的設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. 按一下 [**下一步**，然後按一下 [**完成]**。 資料庫相容性問題，如果有的話之後，出現 「 精靈 」 驗證結構描述。

    ![匯出的設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. 如果沒有錯誤出現，您的資料庫相容，而您已準備好移轉。 如果您有錯誤時，必須先加以修正。 若要查看錯誤，請按一下 [ **Validating**結構描述的 [**錯誤**]。 
    ![匯出的設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  如果 *。成功產生 BACPAC 檔案，然後資料庫相容於 SQL 資料庫]，而您已準備好移轉。

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [修正資料庫移轉的相容性問題](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [相容的 SQL Server 資料庫移轉到 SQL 資料庫](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
