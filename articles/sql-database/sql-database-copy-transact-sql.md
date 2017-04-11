<properties 
    pageTitle="複製 SQL Azure SQL 資料庫 |Microsoft Azure" 
    description="建立使用 SQL Azure SQL 資料庫的複本" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>複製 SQL Azure SQL 資料庫


> [AZURE.SELECTOR]
- [概觀](sql-database-copy.md)
- [Azure 入口網站](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T SQL](sql-database-copy-transact-sql.md)


此下列步驟說明如何使用 TRANSACT-SQL SQL 資料庫複製到相同或不同的伺服器。 資料庫複製操作會使用[建立資料庫](https://msdn.microsoft.com/library/ms176061.aspx)陳述式。

若要完成這份文件中的步驟，您需要下列項目︰

- Azure 的訂閱。 如果您需要的 Azure 訂閱，只要按一下 [**免費試用版**頂端的 [此頁面中，，然後回到完成這份文件。
- Azure SQL 資料庫。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟︰[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx)。 如果您沒有 SSMS，或如果本文所述的功能無法使用，[下載最新版本](https://msdn.microsoft.com/library/mt238290.aspx)。


## <a name="copy-your-sql-database"></a>複製 SQL 資料庫

登入主要資料庫使用的伺服器層級的本金登入或建立您想要複製的資料庫的登入。 登入所沒有的伺服器層級本金必須是 dbmanager 角色的成員才能複製的資料庫。 如需登入並連線到伺服器的詳細資訊，請參閱[管理登入](sql-database-manage-logins.md)。

開始複製來源資料庫[建立資料庫](https://msdn.microsoft.com/library/ms176061.aspx)陳述式。 執行此陳述式起始複製程序的資料庫。 複製資料庫為非同步處理，因為資料庫完成複製之前，會傳回建立資料庫陳述式。


### <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL 資料庫複製到相同的伺服器

登入主要資料庫使用的伺服器層級的本金登入或建立您想要複製的資料庫的登入。 登入所沒有的伺服器層級本金必須是 dbmanager 角色的成員才能複製的資料庫。

為新資料庫執行此命令複製 Database1 命名 Database2 相同的伺服器上。 根據您的資料庫大小複製作業可能需要一些時間才能完成。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>將 SQL 資料庫複製到不同的伺服器

登入主要目的伺服器，建立新的資料庫所在的 Azure SQL 資料庫伺服器的資料庫。 使用來源資料庫來源 Azure SQL 資料庫伺服器上的資料庫擁有者 (DBO) 為具有相同的名稱和密碼登入。 目的伺服器上的登入必須也是 dbmanager 角色的成員，或伺服器層級的本金登入。

這個命令會將 Database1 上 server1-複製到新的資料庫上 server2 命名為 Database2。 根據您的資料庫大小複製作業可能需要一些時間才能完成。


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>監控複製作業的進度

查詢 sys.databases] 和 [sys.dm_database_copies 檢視來監控複製程序。 複製進行時，state_desc 欄的新資料庫的 sys.databases 檢視設為複製。


- 如果複製失敗，請 state_desc 欄的新資料庫的 sys.databases 檢視設為疑問。 在此情況下，在新的資料庫執行 DROP 陳述式，再試一次。
- 如果複製順利完成，state_desc 檢視的欄位 sys.databases 新資料庫會設定為 [線上。 在此案例中，複製完成，而新資料庫會一般資料庫中，無法變更來源資料庫的獨立。

> [AZURE.NOTE] -如果您決定要取消複製在進行時，請在新資料庫執行[卸除資料庫](https://msdn.microsoft.com/library/ms178613.aspx)陳述式。 或者，在來源資料庫上執行卸除資料庫陳述式也會取消複製程序。


## <a name="resolve-logins-after-the-copy-operation-completes"></a>[複製] 作業完成後，解決登入

線上目的伺服器上新的資料庫後，請重新對應至目的伺服器上登入新的資料庫的使用者使用[變更使用者](https://msdn.microsoft.com/library/ms176060.aspx)陳述式。 若要解決孤立的使用者，請參閱[疑難排解孤立的使用者](https://msdn.microsoft.com/library/ms175475.aspx)。 另請參閱[如何管理之後損毀修復 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。

新的資料庫中的所有使用者都維護來源資料庫中的權限。 啟動資料庫複本的使用者會變成新資料庫的資料庫擁有者，並指派新的安全性識別碼信用。 複製成功之後，再重新對應其他使用者發起的租用戶複製資料庫擁有者 (DBO)，登入可以登入新的資料庫。


## <a name="next-steps"></a>後續步驟

- 如需複製 Azure SQL 資料庫的概觀，請參閱[複製 Azure SQL 資料庫](sql-database-copy.md)。
- 請參閱複製資料庫，使用 [Azure 入口網站的 [[複製使用 Azure 入口網站的 Azure SQL 資料庫](sql-database-copy-portal.md)。
- 請參閱使用 PowerShell 將資料庫複製的 [[複製 Azure SQL 資料庫使用 PowerShell](sql-database-copy-powershell.md) 。
- 瞭解[如何管理 Azure SQL 資料庫安全性損毀修復後的](sql-database-geo-replication-security-config.md)，若要深入瞭解管理使用者與登入，複製到不同的邏輯伺服器的資料庫時。



## <a name="additional-resources"></a>其他資源

- [管理登入](sql-database-manage-logins.md)
- [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)
- [匯出 BACPAC 資料庫](sql-database-export.md)
- [業務連續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫文件](https://azure.microsoft.com/documentation/services/sql-database/)


