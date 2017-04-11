<properties 
    pageTitle="Azure SQL 資料庫 Azure 入口網站設定地理複寫 |Microsoft Azure" 
    description="使用 [Azure 入口網站的 Azure SQL 資料庫設定地理複寫" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>設定地理複寫 Azure SQL 資料庫 Azure 入口網站


> [AZURE.SELECTOR]
- [概觀](sql-database-geo-replication-overview.md)
- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T SQL](sql-database-geo-replication-transact-sql.md)

本文將說明如何設定使用中的地理複寫與[入口網站 Azure](http://portal.azure.com)SQL 資料庫。

若要啟動容錯移轉 Azure 入口網站，請參閱[啟動 Azure SQL 資料庫 Azure 入口網站的計畫之內容錯移轉](sql-database-geo-replication-failover-portal.md)。

>[AZURE.NOTE] 現在使用的所有服務層中的所有資料庫作用中地理複寫 （可讀取次要連結）。 在年 4 月 2017年無法讀取副類型會已停用，，無法讀取中現有的資料庫會自動升級到可讀的次要連結。

若要設定地理複寫使用 Azure 入口網站，您需要下列資源︰

- Azure SQL 資料庫-您想要複製到不同的地理區域的主要資料庫。

## <a name="add-secondary-database"></a>新增第二個資料庫

下列步驟會建立新的次要資料庫地理複寫合作。  

若要新增次要，您必須是訂閱擁有者或共同擁有者。 

次要資料庫會有相同的主要資料庫名稱，並會根據預設，有相同的服務層級。 次要資料庫可以是單一資料庫或彈性的資料庫。 如需詳細資訊，請參閱[服務層](sql-database-service-tiers.md)。
建立和定位次要後，資料就會開始到新的次要資料庫複寫從主要資料庫。 

> [AZURE.NOTE] 如果合作夥伴資料庫已經存在 （例如-當做結束前一個地理複寫關聯）] 命令將會失敗。

### <a name="add-secondary"></a>新增第二個

1. 在[Azure 入口網站](http://portal.azure.com)中，瀏覽至您想要設定的地理複寫的資料庫。
2. 在 [SQL 資料庫] 頁面上選取**地理複寫**]，然後選取要建立第二個資料庫的區域。 您可以選取任何區域以外的地區裝載的主要的資料庫，但建議，[即成對的區域](../best-practices-availability-paired-regions.md)。

    ![設定地理複寫](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. 選取或設定伺服器，以及價格層次要資料庫。

    ![設定次要](./media/sql-database-geo-replication-portal/create-secondary.png)

5. 或者，您可以新增彈性的資料庫資料庫次要資料庫︰

 若要建立第二個資料庫在資料庫中，按一下**彈性的資料庫資料庫**並選取目標伺服器上的資料庫。 此工作流程不會建立資料庫時，資料庫必須已存在目標伺服器上。

6. 按一下 [**建立**]，新增第二個。
 
6. 隨即會建立第二個資料庫，並開始種子程序。 
 
    ![設定次要](./media/sql-database-geo-replication-portal/seeding0.png)

7. 種子程序完成時，第二個資料庫會顯示其狀態。

    ![種子完成](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>移除次要資料庫

操作永久終止次要資料庫複寫，並變更一般讀寫資料庫中的次要的角色。 如果第二個資料庫的連線中斷，命令順利完成，但不是會變成讀寫連線之後，直到第二個會還原。  

1. 在[Azure 入口網站](http://portal.azure.com)中，瀏覽至主要資料庫中的地理複寫合作關係。
2. 在 [SQL 資料庫] 頁面中，選取 [**地理複寫**]。
3. 在**次要連結**清單中，選取您想要移除的地理複寫合作關係的資料庫。
4. 按一下 [**停止複寫**。

    ![移除次要](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. 按一下 [**停止複寫**開啟在確認視窗，按一下 [****若要移除的地理複寫合作關係的資料庫 （將它設定為讀寫資料庫不屬於任何複寫）。


## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解使用地理複寫，請參閱-[作用中的地理複寫](sql-database-geo-replication-overview.md)
- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)

