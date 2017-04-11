<properties
   pageTitle="移轉到使用部署至 Microsoft Azure 資料庫] 精靈的資料庫的 SQL 資料庫的 SQL Server 資料庫 |Microsoft Azure"
   description="Microsoft Azure SQL 資料庫，資料庫移轉、 Microsoft Azure 資料庫] 精靈"
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

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>將 SQL Server 資料庫移轉到使用部署至 Microsoft Azure 資料庫] 精靈的資料庫的 SQL 資料庫


> [AZURE.SELECTOR]
- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [交易複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Microsoft Azure 資料庫精靈在 SQL Server Management Studio 中部署資料庫可直接將 Azure SQL 資料庫伺服器移轉[相容的 SQL Server 資料庫](sql-database-cloud-migrate.md)。

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>使用 Microsoft Azure 資料庫] 精靈的部署資料庫

> [AZURE.NOTE] 下列步驟假設您有[佈建 SQL 資料庫伺服器](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)。

1. 確認您擁有最新版本的 SQL Server Management Studio 中。 每月維持更新 Azure 入口網站的同步處理更新 Management Studio 中的最新版本。

    > [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。

2. 開啟 Management Studio 中，並連線到您要移轉物件總管] 中的 SQL Server 資料庫。
3. 以滑鼠右鍵按一下物件總管] 中的資料庫，指向 [**工作**]，按一下 [**將資料庫部署到 Microsoft Azure SQL 資料庫...**

    ![部署至 Azure [工作] 功能表](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  在 [部署] 精靈中，按一下 [**下一步**，，然後按一下以設定連線至 SQL 資料庫伺服器的**連線**。

    ![部署至 Azure [工作] 功能表](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. 在 [連線至伺服器] 對話方塊中，輸入您要連線至 SQL 資料庫伺服器的連線資訊。

    ![部署至 Azure [工作] 功能表](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  提供下列內容以取得此精靈會建立在移轉過程[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案︰

 - **新的資料庫名稱** 
 - **版本的 Microsoft Azure SQL 資料庫**（[層](sql-database-service-tiers.md)）
 - **資料庫大小上限**
 - **服務目標**（效能層級）
 - **暫存檔案名稱**  

    ![匯出的設定](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  完成精靈。 根據大小和複雜程度的資料庫，部署可能需要幾分鐘數。 如果此精靈會偵測相容性問題，畫面會顯示錯誤，並不會繼續移轉。 如何修正資料庫相容性問題的指引，請移至[修正資料庫相容性問題](sql-database-cloud-migrate-fix-compatibility-issues.md)。

7.  使用物件檔案總管，連線到您移轉的資料庫，在您的 Azure SQL 資料庫伺服器。
8.  使用 [Azure 入口網站，請檢視您的資料庫，其內容。

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
