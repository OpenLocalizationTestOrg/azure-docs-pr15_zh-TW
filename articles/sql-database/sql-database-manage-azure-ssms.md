<properties 
    pageTitle="管理 SSMS 的 SQL 資料庫 |Microsoft Azure" 
    description="瞭解如何使用 SQL Server Management Studio 管理 SQL 資料庫伺服器和資料庫。" 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>使用 SQL Server Management Studio 管理 Azure SQL 資料庫 


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

您可以使用 SQL Server 管理 Studio (SSMS)，來管理 Azure SQL 資料庫伺服器和資料庫。 本主題會引導您 SSMS 的一般工作。 您應該已經有的伺服器及之前 Azure SQL 資料庫中建立資料庫。 如需詳細資訊，請參閱[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)] 和 [[連線] 和 [使用 SSMS 查詢](sql-database-connect-query-ssms.md)。

建議您使用的是最新版的 SSMS，每當您使用 Azure SQL 資料庫。 

> [AZURE.IMPORTANT] 一律使用最新版的 SSMS，因為它會持續改良搭配使用最新的更新與 Azure SQL 資料庫。 若要取得最新版本，請參閱[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。



## <a name="create-and-manage-azure-sql-databases"></a>建立及管理 Azure SQL 資料庫

連線至**主版**的資料庫，您可以在伺服器上建立資料庫並修改或卸除現有的資料庫。 下列步驟說明如何完成 Management Studio 中透過數種常見資料庫管理工作。 若要執行這些工作，請確定您已連線到您設定您的伺服器時所建立的伺服器層級本金登入與**主要**資料庫。

若要開啟 [查詢] 視窗中 Management Studio 中，開啟 [資料庫] 資料夾展開 [**系統資料庫**] 資料夾，以滑鼠右鍵按一下**主版**，再按一下**新的查詢**。

-   用於**建立資料庫**陳述式建立資料庫。 如需詳細資訊，請參閱[建立資料庫 （SQL 資料庫）](https://msdn.microsoft.com/library/dn268335.aspx)。 下列陳述式會建立一個名為**myTestDB**資料庫，並指定的預設大小上限為 250 GB 的標準 S0 版資料庫。

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

按一下 [**執行**] 來執行查詢。

-   使用**ALTER DATABASE**陳述式修改現有的資料庫，例如，如果您想要變更的名稱和資料庫的版本。 如需詳細資訊，請參閱[變更資料庫 （SQL 資料庫）](https://msdn.microsoft.com/library/ms174269.aspx)。 下列陳述式修改您在先前的步驟來變更您建立的資料庫至標準 S1 的版本。

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   若要刪除現有的資料庫中使用**卸除資料庫**陳述式。 如需詳細資訊，請參閱[刪除資料庫 （SQL 資料庫）](https://msdn.microsoft.com/library/ms178613.aspx)。 下列陳述式刪除**myTestDB**資料庫，但不放現在因為您將會用它來建立一個步驟中的 [登入。

        DROP DATABASE myTestBase;

-   主要資料庫有即可檢視所有資料庫的相關詳細資料，您可以使用**sys.databases**檢視。 若要檢視所有現有的資料庫，請執行下列陳述式︰

        SELECT * FROM sys.databases;

-   在 SQL 資料庫中，**使用**陳述式不支援資料庫之間切換。 不過，您需要建立直接到目標資料庫的連線。

>[AZURE.NOTE] 建立或修改資料庫的 SQL 陳述式許多必須執行自己批次中，無法使用其他的 SQL 陳述式分組。 如需詳細資訊，請參閱陳述式特定資訊。

## <a name="create-and-manage-logins"></a>建立及管理登入

**主要**資料庫包含登入，哪些登入有建立資料庫或其他登入的權限。 管理登入連線到您設定您的伺服器時所建立的伺服器層級本金登入與**主要**資料庫。 您可以對母片的整個伺服器上管理登入資料庫執行查詢，使用**建立登入**、**變更登入**或**放登入**的陳述式。 如需詳細資訊，請參閱[管理資料庫及 SQL 資料庫中的登入](http://msdn.microsoft.com/library/azure/ee336235.aspx)。 


-   您可以使用 [**建立登入**陳述式，建立伺服器層級的登入。 如需詳細資訊，請參閱[建立登入 （SQL 資料庫）](https://msdn.microsoft.com/library/ms189751.aspx)。 下列陳述式建立稱為**login1**登入。 **Password1**換成您所選擇的密碼。

        CREATE LOGIN login1 WITH password='password1';

-   您可以使用 [**建立使用者**陳述式，授與資料庫層級權限。 必須**主**資料庫中建立的所有登入。 連線到不同的資料庫登入，您必須授與它使用該資料庫上的 [**建立使用者**陳述式的資料庫層級權限。 如需詳細資訊，請參閱[建立使用者 （SQL 資料庫）](https://msdn.microsoft.com/library/ms173463.aspx)。 

-   若要提供 login1 稱為**myTestDB**資料庫的權限，完成下列步驟︰

 1.  重新整理物件總管] 中檢視您所建立的**myTestDB**資料庫，以滑鼠右鍵按一下物件總管] 中的伺服器名稱，然後再按一下 [**重新整理**。  

     如果您關閉連線，您可以在 [檔案] 功能表上選取 [**連接物件總管**重新連線。

 2. 以滑鼠右鍵按一下**myTestDB**資料庫，然後選取**新的查詢**。

    3.  執行下列 myTestDB 資料庫建立資料庫的使用者名稱對應到伺服器層級登入**login1** **login1User**陳述式。

            CREATE USER login1User FROM LOGIN login1;

-   使用**預存程序\_addrolemember**預存程序提供適當的資料庫的權限等級的使用者帳戶。 如需詳細資訊，請參閱[sp_addrolemember (SQL)](http://msdn.microsoft.com/library/ms187750.aspx)。 下列陳述式提供**login1User**資料庫的唯讀權限新增至**login1User** **db\_datareader**角色。

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   用於**變更登入**陳述式修改現有的登入，例如，如果您想要變更的密碼，登入。 如需詳細資訊，請參閱[變更登入 （SQL 資料庫）](https://msdn.microsoft.com/library/ms189828.aspx)。 **變更登入**陳述式應該執行**主要**資料庫。 切換回 [已連線至該資料庫的 [查詢] 視窗。 下列陳述式修改**login1**登入以重設密碼。 **NewPassword**取代目前的密碼，登入您的選擇及**oldPassword**的密碼。

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   使用**拖放登入**陳述式刪除現有的登入。 刪除登入伺服器層級時，也會刪除任何相關聯的資料庫的使用者帳戶。 如需詳細資訊，請參閱[刪除資料庫 （SQL 資料庫）](https://msdn.microsoft.com/library/ms178613.aspx)。 **卸除登入**陳述式應該執行**主要**資料庫。 刪除**login1**登入。

        DROP LOGIN login1;

-   主要資料庫具有**sys.sql\_登入**檢視，您可以使用檢視登入。 若要檢視所有現有的登入，請執行下列陳述式︰

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>監視使用動態管理檢視的 SQL 資料庫

SQL 資料庫支援監控個別的資料庫，您可以使用的幾個動態管理檢視。 您可以透過這些檢視來擷取的 [監視器] 資料類型的一些範例如下所述。 如需完整的詳細資料與更多的使用狀況範例，請參閱[監控 SQL 資料庫使用動態管理檢視](https://msdn.microsoft.com/library/azure/ff394114.aspx)。

-   查詢動態管理檢視需要**檢視資料庫狀態**權限。 若要**檢視資料庫狀態**權限授與特定資料庫使用者，連線至資料庫並執行下列陳述式對資料庫︰

        GRANT VIEW DATABASE STATE TO login1User;

-   計算資料庫大小使用**sys.dm\_db\_分割\_stats**檢視。 **Sys.dm\_db\_分割\_stats**檢視在資料庫中，您可以用來計算資料庫大小傳回的每個磁碟分割頁面和列數資訊。 下列查詢會傳回資料庫的大小以百萬位元組︰

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   使用**sys.dm\_接著\_連線**和**sys.dm\_接著\_工作階段**檢視來擷取目前的使用者連線及內部資料庫與相關聯的工作的相關資訊。 下列查詢會傳回目前連線的相關資訊︰

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   使用**sys.dm\_接著\_查詢\_stats**要擷取的彙總的效能統計資料檢視快取查詢計劃。 下列查詢會傳回平均 CPU 時間來上方的五個查詢的相關資訊。

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
 
 
