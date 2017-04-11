<properties
    pageTitle="時間 （Azure 入口網站） 將 Azure SQL 資料庫還原到上一個 |Microsoft Azure"
    description="還原時間點 Azure SQL 資料庫。"
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


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Azure SQL 資料庫還原至先前的點，Azure 入口網站的時間


> [AZURE.SELECTOR]
- [概觀](sql-database-recovery-using-backups.md)
- [時間點還原︰ PowerShell](sql-database-point-in-time-restore-powershell.md)

本文將示範如何從[SQL 資料庫自動備份](sql-database-automated-backups.md)時間還原資料庫至之前使用 Azure 入口網站。

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>將 SQL 資料庫還原時間點

選取要還原 Azure 入口網站中的資料庫︰

1.  開啟[Azure 入口網站](https://portal.azure.com)。
2.  在左側的畫面中，選取 [**更多服務** > **SQL 資料庫**。
3.  按一下您要還原的資料庫。
4.  在您的資料庫] 頁面的頂端，選取 [**還原**︰

    ![還原 Azure SQL 資料庫](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  在 [**還原**] 頁面上，選取的日期和時間 （以 UTC 時間） 若要還原的資料庫，然後再按一下**[確定]**:

    ![還原 Azure SQL 資料庫](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>監控還原作業

1. 之後在上一個步驟中，按一下**[確定]** ，按一下 [通知] 圖示，在頁面右上方，然後按一下 [**還原 SQL 資料庫**通知，如需詳細資訊。

    ![還原 Azure SQL 資料庫](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. 還原 SQL 資料庫] 頁面隨即開啟還原的狀態相關資訊。 您可以按一下以取得詳細資料行項目︰

    ![還原 Azure SQL 資料庫](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>後續步驟

- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
- 若要進一步瞭解自動 Azure SQL 資料庫備份，請參閱[SQL 資料庫自動備份](sql-database-automated-backups.md)
- 若要瞭解如何使用復原自動的備份，請參閱[還原在服務啟動備份資料庫](sql-database-recovery-using-backups.md)
- 若要深入了解更快速地復原選項，請參閱[作用地理複寫](sql-database-geo-replication-overview.md)  
- 若要瞭解如何使用自動的備份的封存，請參閱[資料庫複本](sql-database-copy.md)
