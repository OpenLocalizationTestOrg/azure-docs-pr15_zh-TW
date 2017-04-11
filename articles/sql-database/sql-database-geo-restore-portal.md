<properties
    pageTitle="從 （Azure 入口網站） 的自動備份還原 Azure SQL 資料庫 |Microsoft Azure"
    description="還原 Azure SQL 資料庫，從自動備份 （Azure 入口網站）。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>使用 [Azure 入口網站的自動備份還原 Azure SQL 資料庫


> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md#geo-restore)
- [地理還原︰ PowerShell](sql-database-geo-restore-powershell.md)

本文將示範如何從[自動備份](sql-database-automated-backups.md)還原資料庫至新的伺服器與[地理還原](sql-database-recovery-using-backups/.md#geo-restore)使用 Azure 入口網站。

## <a name="select-a-database-to-restore"></a>選取要還原的資料庫

若要還原資料庫中 Azure 入口網站，請執行下列步驟︰

1.  移至[Azure 入口網站](https://portal.azure.com)。
2.  在左側的畫面上選取 [ **+ 新增** > **資料庫** > **SQL 資料庫**︰

    ![還原 Azure SQL 資料庫](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  選取 [來源]，為 [**備份**，然後選取 [您要還原的備份。 指定資料庫的名稱，而您想要還原的資料庫，到伺服器，然後按一下 [**建立**]:
  
    ![還原 Azure SQL 資料庫](./media/sql-database-geo-restore-portal/geo-restore.png)

按一下頁面右上方的上方的 [通知] 圖示來監控還原作業的狀態。 


## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
