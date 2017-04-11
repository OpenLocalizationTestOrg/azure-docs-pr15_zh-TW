<properties
    pageTitle="疑難排解備份與還原 Azure SQL 資料庫"
    description="瞭解如何修復的錯誤和 Azure SQL 資料庫中使用備份與複本中斷雲端資料庫。"
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

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>時間還原至先前的點的資料庫，還原已刪除的資料庫，或從資料中心中斷復原

SQL 資料庫會保留複本的資料庫，讓您可以復原中斷與使用者錯誤。 可用的選項而定的資料庫服務層和您所選擇的選項。 請參閱詳細資料] 與 [設計考量[業務連續性概觀](sql-database-business-continuity.md)。

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>若要將資料庫還原時間點
1.  在[Azure 入口網站](https://azure.microsoft.com/)中，按一下 [ **SQL 資料庫**]。
2.  從清單中選取您的資料庫，然後再按一下 [**還原**]。
3.  輸入資料庫的新名稱，選擇日期與時間還原]，然後按一下**建立。**
4.  調整應用程式視參考新的資料庫。 請參閱[復原資料庫中的時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="to-restore-an-accidentally-deleted-database"></a>若要還原的不小心刪除的資料庫
1.  在[Azure 入口網站](https://azure.microsoft.com/)中，按一下 [ **SQL server**]。
2.  選取裝載於清單中的資料庫伺服器。
3.  在伺服器刀中，向下捲動，然後按一下 [**刪除資料庫]**。
4.  選取要還原的資料庫，然後再按一下 [**建立**]。
5.  調整應用程式視參考新的資料庫。 請參閱[復原已刪除的資料庫](sql-database-recovery-using-backups.md#deleted-database-restore)。

## <a name="to-recover-from-a-regional-datacenter-outage"></a>若要復原的區域資料中心中斷
標準和進階版的資料庫時，如果您設定地理複寫次要連結，您可以復原使用這些次要連結。 這可讓您能夠使用資料遺失較少可能還原資料庫。 如需詳細資訊，請參閱[復原使用自動化的資料庫備份的 Azure SQL 資料庫](sql-database-disaster-recovery.md)。

Azure 也會提供的不同區域 （地理重複備份） 中每個資料庫的備份。 您可以從這些備份，稱為地理還原，來建立新的資料庫，但可能是，您就會遇到資料遺失，如果您是仰賴了解單獨使用這個方法。

**若要復原使用地理還原的資料庫︰**

- 在[Azure 入口網站](https://azure.microsoft.com/)中，按一下 [**新增**]、 按一下**資料和儲存**、 按一下 [ **SQL 資料庫**]，然後選取**備份**做為資料庫來源。 如需詳細資訊，請參閱[復原中斷從 Azure SQL 資料庫](sql-database-disaster-recovery.md)。