<properties
    pageTitle="從 Azure SQL 資料庫備份還原單一資料表 |Microsoft Azure"
    description="瞭解如何從 Azure SQL 資料庫備份還原單一資料表。"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>如何從 Azure SQL 資料庫備份還原單一資料表

您可能會遇到的情況下，您不小心修改 SQL 資料庫中的某些資料和現在想要復原受影響的資料表。 本文將說明如何從 SQL 資料庫[自動備份](sql-database-automated-backups.md)還原資料庫中的單一資料表。

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>準備步驟︰ 重新命名資料表與還原資料庫的複本
1. 找出您要還原的複本以取代 Azure SQL 資料庫中的資料表。 使用 Microsoft SQL Management Studio 中重新命名資料表。 例如，重新命名資料表&lt;資料表名稱&gt;_old。

    **附註**若要避免遭封鎖，請確認沒有在您要重新命名資料表上執行的活動。 如果您遇到問題，請確定所進行的維修作業期間執行此程序。

2. 您想要使用的[點 In_Time 還原](sql-database-recovery-using-backups.md#point-in-time-restore)步驟復原的時間點還原資料庫的備份。

    **附註**︰
    - 還原資料庫的名稱會 dbname 引數 + 時間戳記的格式。例如， **Adventureworks2012_2016 01-01T22 12Z**。 此步驟，就不會覆寫現有的資料庫名稱，在伺服器上。 這是安全量值，其具有可讓您在捨棄其目前的資料庫和重新命名還原的資料庫，用於生產環境之前，請確認還原的資料庫。
    - 所有效能層從基本 premium 自動都備份服務，以不同備份保留指標，根據層︰

| DB 還原 | 基本層 | 標準的層級 | 進階版層 |
| :-- | :-- | :-- | :-- |
|  還原的時間點 |  任何 7 天內還原點|任何 35 天內還原點| 任何 35 天內還原點|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>複製還原的資料庫中的表格，使用 [SQL 資料庫移轉工具
1. 下載並安裝[SQL 資料庫移轉精靈]](https://sqlazuremw.codeplex.com)。

2. 開啟 [SQL 資料庫移轉精靈，**選取 [程序**] 頁面選取 [**分析/移轉資料庫**]，再按一下 [**下一步**。
![SQL 資料庫移轉精靈-選取 [處理程序](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. 在 [**伺服器的連線**] 對話方塊中，套用下列設定︰
 - **伺服器名稱**︰ 您的 SQL Azure 執行個體
 - **驗證**︰ **SQL Server 驗證**。 輸入您的登入認證。
 - **資料庫**︰**母片 DB （列出所有資料庫）**。
 - **附註**依預設精靈會儲存您的登入資訊。 如果您不想要請選取 [**忘記登入資訊**]。
![SQL 資料庫移轉精靈-選取來源-步驟 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. 在 [**選取來源**] 對話方塊中，從您的來源，以**準備步驟**區段中選取 [還原的資料庫名稱，然後按 [**下一步**。

    ![SQL 資料庫移轉精靈-選取來源-步驟 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. 在 [**選擇物件**] 對話方塊中，選取 [**選取特定的資料庫物件**的選項，，然後選取您想要移轉至目標伺服器 table(or tables)。
![SQL 資料庫移轉精靈-選擇物件](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. 當系統提示您瞭解您是否可以產生 SQL 指令碼時，請在 [**指令碼精靈摘要**] 頁面上按一下**[是]** 。 您也可以儲存以供日後使用 TSQL 指令碼的選項。
![SQL 資料庫移轉精靈-指令碼精靈摘要](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. 在 [**結果摘要**] 頁面中，按一下 [**下一步**]。
![SQL 資料庫移轉精靈-摘要結果](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. **設定目標伺服器連線**在頁面上，按一下 [**連線至伺服器**]，然後輸入詳細資料，如下所示︰
    - **伺服器名稱**︰ 目標伺服器執行個體
    - **驗證**︰ **SQL Server 驗證**。 輸入您的登入認證。
    - **資料庫**︰**母片 DB （列出所有資料庫）**。 這個選項會列出目標伺服器上的所有資料庫。

    ![SQL 資料庫移轉精靈-設定目標伺服器連線](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. 按一下 [**連線**]，選取您想要移動的資料表的目標資料庫，再按一下 [**下一步**。 此應該完成執行先前產生的指令碼，並您應該會看到複製到目標資料庫的新移動的資料表。

## <a name="verification-step"></a>驗證步驟
1. 查詢並測試新複製的表格，以確保資料是不變。 根據確認，您可以刪除**準備步驟**] 區段中的 [重新命名的表格] 表單。 (例如，&lt;資料表名稱&gt;_old)。

## <a name="next-steps"></a>後續步驟

[SQL 資料庫自動備份](sql-database-automated-backups.md)
