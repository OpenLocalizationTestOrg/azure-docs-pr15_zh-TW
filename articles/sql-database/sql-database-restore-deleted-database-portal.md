<properties
    pageTitle="還原已刪除的 Azure SQL 資料庫 （Azure 入口網站） |Microsoft Azure"
    description="還原已刪除的 Azure SQL 資料庫 （Azure 入口網站）。"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>還原已刪除的 Azure SQL 資料庫，使用 [Azure 入口網站

> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [**還原刪除的 DB︰ 入口網站**](sql-database-restore-deleted-database-portal.md)
- [還原刪除的 DB: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>選取要還原的資料庫 

若要還原已刪除的資料庫中 Azure 入口網站︰

1.  在 [ [Azure 入口網站](https://portal.azure.com)中，按一下 [**更多服務** > **SQL server**。
3.  選取包含您要還原的資料庫伺服器。
4.  向您的伺服器刀 [**作業**] 區段下捲動，然後選取 [**刪除資料庫**︰![還原 Azure SQL 資料庫](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  選取您要還原的資料庫。
6.  指定資料庫的名稱，然後按一下**[確定]**:

    ![還原 Azure SQL 資料庫](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
