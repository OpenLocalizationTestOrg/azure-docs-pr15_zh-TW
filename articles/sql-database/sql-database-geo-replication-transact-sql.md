<properties
    pageTitle="針對使用 TRANSACT-SQL Azure SQL 資料庫設定地理複寫 |Microsoft Azure"
    description="使用 SQL Azure SQL 資料庫設定地理複寫"
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
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>設定地理複寫與 TRANSACT-SQL Azure SQL 資料庫

> [AZURE.SELECTOR]
- [概觀](sql-database-geo-replication-overview.md)
- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T SQL](sql-database-geo-replication-transact-sql.md)

本文將說明如何設定使用中的地理複寫與 SQL Azure SQL 資料庫。

若要開始使用 TRANSACT-SQL 容錯移轉，請參閱[啟動與 SQL Azure SQL 資料庫的計畫之內容錯移轉](sql-database-geo-replication-failover-transact-sql.md)。

>[AZURE.NOTE] 現在使用的所有服務層中的所有資料庫作用中地理複寫 （可讀取次要連結）。 在年 4 月 2017年無法讀取副類型會停用，無法讀取中現有的資料庫會自動升級到可讀的次要連結。

若要設定作用中的地理複寫使用 TRANSACT-SQL，您需要下列動作︰

- Azure 的訂閱。
- 邏輯 Azure SQL 資料庫伺服器<MyLocalServer>和 SQL 資料庫<MyDB>-您想要複製的主要資料庫。
- 一或多個邏輯 Azure SQL 資料庫伺服器 < MySecondaryServer(n) >-會您要在其中建立第二個資料庫的夥伴伺服器的邏輯伺服器。
- DBManager 位於主要，登入有 db_ownership 您將會地理複寫的本機資料庫，在您要設定地理複寫合作夥伴伺服器上是 DBManager。
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] 建議您永遠保持同步處理與更新 Microsoft Azure SQL 資料庫使用 Management Studio 中的最新版本。 [更新 SQL Server Management Studio 中](https://msdn.microsoft.com/library/mt238290.aspx)。


## <a name="add-secondary-database"></a>新增第二個資料庫

您可以使用**ALTER DATABASE**陳述式合作夥伴在伺服器上建立地理複製的第二個資料庫。 您可以執行此陳述式包含要複製的資料庫伺服器的主要資料庫。 地理複製的資料庫 （「 主要資料庫 」） 會有資料庫複寫相同的名稱，並會根據預設，有相同的服務層級的主要資料庫。 次要資料庫可以是讀取或未讀取，而且可以是單一資料庫或彈性的 databbase。 如需詳細資訊，請參閱[變更資料庫 (SQL)](https://msdn.microsoft.com/library/mt574871.aspx)和[服務層](sql-database-service-tiers.md)。
建立並定位次要資料庫後，資料就會開始非同步複寫從主要資料庫。 下列步驟說明如何設定地理複寫使用 Management Studio 中。 若要建立無法讀取且容易閱讀次要連結，可以使用單一資料庫或彈性的資料庫，步驟會提供。

> [AZURE.NOTE] 如果資料庫有相同的主要資料庫名稱與指定的夥伴伺服器上的命令會失敗。


### <a name="add-non-readable-secondary-single-database"></a>新增非可讀的次要 （單一資料庫）

您可以使用下列步驟來建立無法讀取次要為單一資料庫。

1. 使用版本 13.0.600.65 或更新版本的 SQL Server Management Studio 中。

     > [AZURE.IMPORTANT] 下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio 中。 建議您永遠保持 Azure 入口網站更新同步使用 Management Studio 中的最新版本。


2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 您可以使用下列**ALTER DATABASE**陳述式，讓的 MySecondaryServer1 MySecondaryServer1 是您好記的伺服器名稱的位置上的非可讀取次要資料庫與主要地理複寫到本機資料庫。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. 按一下 [**執行**] 來執行查詢。


### <a name="add-readable-secondary-single-database"></a>新增可讀取次要 （單一資料庫）
您可以使用下列步驟，建立可讀取次要為單一資料庫。

1. 在 Management Studio 中連線至您的邏輯 Azure SQL 資料庫伺服器。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 您可以使用下列**ALTER DATABASE**陳述式，讓主要與次要伺服器上可讀取次要資料庫地理複寫到本機資料庫。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. 按一下 [**執行**] 來執行查詢。



### <a name="add-non-readable-secondary-elastic-database"></a>新增非可讀的次要 （彈性的資料庫）

您可以使用下列步驟來建立無法讀取次要彈性的資料庫。

1. 在 Management Studio 中連線至您的邏輯 Azure SQL 資料庫伺服器。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 您可以使用下列**ALTER DATABASE**陳述式，讓彈性的資料庫中的第二個伺服器上的非可讀取次要資料庫與主要地理複寫到本機資料庫。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. 按一下 [**執行**] 來執行查詢。



### <a name="add-readable-secondary-elastic-database"></a>新增可讀取次要 （彈性的資料庫）
您可以使用下列步驟來建立可讀取次要彈性的資料庫。

1. 在 Management Studio 中連線至您的邏輯 Azure SQL 資料庫伺服器。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 您可以使用下列**ALTER DATABASE**陳述式，讓主要使用彈性的資料庫中的第二個伺服器上可讀取次要資料庫地理複寫到本機資料庫。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. 按一下 [**執行**] 來執行查詢。



## <a name="remove-secondary-database"></a>移除次要資料庫

若要永久其主要與次要資料庫複寫合作關係，您可以使用**ALTER DATABASE**陳述式。 主要資料庫所在的主版資料庫執行此陳述式。 關聯終止之後, 第二個資料庫會變成一般讀寫資料庫。 如果第二個資料庫的連線中斷命令順利完成，但次要後恢復連線時，會變成讀寫。 如需詳細資訊，請參閱[變更資料庫 (SQL)](https://msdn.microsoft.com/library/mt574871.aspx)和[服務層](sql-database-service-tiers.md)。

若要移除地理複寫合作關係的地理複寫次要使用下列步驟。

1. 在 Management Studio 中連線至您的邏輯 Azure SQL 資料庫伺服器。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 若要移除地理複寫次要使用下列**ALTER DATABASE**陳述式。

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. 按一下 [**執行**] 來執行查詢。

## <a name="monitor-geo-replication-configuration-and-health"></a>監視地理複寫組態和健康狀況

監視工作包括地理複寫設定的監視及監視資料複寫健康狀況。  您可以使用母片的資料庫中的**sys.dm_geo_replication_links**動態管理檢視，以傳回邏輯 Azure SQL 資料庫伺服器上的所有結束複製連結，每個資料庫的相關資訊。 此檢視包含複寫連結主要和次要資料庫之間的每一個資料列。 您可以使用**sys.dm_replication_link_status**動態管理檢視，傳回目前複寫複寫連結可供每個 Azure SQL 資料庫的資料列。 這包含主要和次要資料庫。 如果指定的主要資料庫有一個以上的連續複寫連結，此表格會包含一個資料列的每個關聯性。 檢視會建立在所有的資料庫，包括邏輯母片。 不過，查詢中的邏輯母片的檢視會傳回空集合。 您可以使用**sys.dm_operation_status**動態管理檢視以顯示所有的資料庫作業，包括複製連結的狀態的狀態。 如需詳細資訊，請參閱[sys.geo_replication_links （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt575501.aspx)， [sys.dm_geo_replication_link_status （Azure SQL 資料庫）](https://msdn.microsoft.com/library/mt575504.aspx)，然後[sys.dm_operation_status （Azure SQL 資料庫）](https://msdn.microsoft.com/library/dn270022.aspx)。

您可以使用下列步驟來監視地理複寫合作關係。

1. 在 Management Studio 中連線至您的邏輯 Azure SQL 資料庫伺服器。

2. 開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主**，再按一下 [**新增查詢**。

3. 您可以使用下列陳述式來顯示所有資料庫的地理複寫連結。

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. 按一下 [**執行**] 來執行查詢。
5. 開啟 [資料庫] 資料夾，展開 [**系統資料庫**] 資料夾、 **MyDB**，以滑鼠右鍵按一下，然後按一下**新的查詢**。
6. 若要顯示複寫 lags 及最後一個試驗使用下列陳述式的 MyDB 我次要資料庫的時間。

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. 按一下 [**執行**] 來執行查詢。
8. 您可以使用下列陳述式來顯示資料庫 MyDB 相關聯的最新地理複寫作業。

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. 按一下 [**執行**] 來執行查詢。

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>將非可讀的次要升級至容易閱讀

在年 4 月 2017年無法讀取副類型會停用，無法讀取中現有的資料庫會自動升級到可讀的次要連結。 如果您使用的無法讀取次要連結今天，想要升級進行閱讀，您可以使用下列簡單的步驟，為每次要。

> [AZURE.IMPORTANT] 沒有自助的就地升級至可讀取無法讀取次要的方法。 如果您只次要放後，然後主要資料庫仍會維持不受保護，直到完全同步處理新的次要。 如果您的應用程式 SLA 需要主要一律會受到保護，您應該考慮建立不同的伺服器平行次要之前套用升級上述的步驟。 請注意，每個主要可以有最多 4 次要資料庫。


1. 請先連線到*第二個*伺服器，然後放非可讀的次要資料庫︰  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. 現在連線至*主要*的伺服器，並新增新的可讀取次要

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解使用地理複寫，請參閱-[作用中的地理複寫](sql-database-geo-replication-overview.md)
- 業務連續性概觀與分析藍本，請參閱[業務連續性概觀](sql-database-business-continuity.md)
