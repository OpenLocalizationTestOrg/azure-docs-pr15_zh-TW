<properties
   pageTitle="監視使用動態管理檢視 Azure SQL 資料庫 |Microsoft Azure"
   description="瞭解如何偵測並使用動態管理檢視監控 Microsoft Azure SQL 資料庫診斷一般效能問題。"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>使用動態管理檢視監控 Azure SQL 資料庫

Microsoft Azure SQL 資料庫可讓子集動態管理檢視診斷效能問題，可能因為封鎖或較長時間執行查詢，資源瓶頸、 不佳的查詢計劃，以及等等。 本主題提供如何使用動態管理檢視偵測一般效能問題的資訊。

SQL 資料庫部分支援三個類別的動態管理檢視︰

- 資料庫相關動態管理檢視]。
- 執行相關的動態管理檢視]。
- 交易相關動態管理檢視]。

動態管理檢視的詳細資訊，請參閱[動態管理檢視和函數 (SQL)](https://msdn.microsoft.com/library/ms188754.aspx) SQL Server 線上活頁簿中。

## <a name="permissions"></a>權限

在 SQL 資料庫查詢動態管理檢視需要**檢視資料庫狀態**權限。 **檢視資料庫狀態**權限會傳回目前的資料庫中的所有物件的相關資訊。
若要**檢視資料庫狀態**權限授與特定資料庫使用者，執行下列查詢︰

```GRANT VIEW DATABASE STATE TO database_user; ```

在內部部署的 SQL Server 執行個體，動態管理檢視會傳回伺服器陳述式資訊。 在 SQL 資料庫中，傳回目前邏輯資料庫只的相關資訊。

## <a name="calculating-database-size"></a>計算資料庫大小

下列查詢會傳回您的資料庫 （以 mb 計） 的大小︰

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

下列查詢會傳回您資料庫中的個別中的物件大小 （mb):

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>監控連線

您可以使用[sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx)檢視來擷取資訊到特定的 Azure SQL 資料庫伺服器，以及每個連線的詳細資料所建立的連線。 此外， [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx)檢視時，很有幫助擷取所有作用中的使用者連線及內部工作的相關資訊。
下列查詢會擷取目前連線的詳細資訊︰

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] 執行時的**sys.dm_exec_requests**及**sys.dm_exec_sessions 檢視**，如果您有資料庫的**檢視資料庫狀態**權限，您會看到所有執行的工作階段的資料庫。否則，您會看到目前工作階段。

## <a name="monitoring-query-performance"></a>監視查詢效能

緩慢或長執行查詢，可以使用大量系統資源。 本節會示範如何使用動態管理檢視偵測一些常見的查詢效能問題。 如需疑難排解，較舊版本，但仍有幫助的參照目標是 Microsoft TechNet 上的[效能問題的疑難排解 SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx)文章。

### <a name="finding-top-n-queries"></a>尋找前 n 個查詢

下列範例會傳回平均 CPU 時間來上方的五個查詢的相關資訊。 此範例彙總查詢雜湊，根據查詢，使其累計資源消耗的分組，以邏輯方式相同的查詢。

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
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
```

### <a name="monitoring-blocked-queries"></a>監控封鎖的查詢

變得很慢或較長時間執行查詢可以參與到消耗過多的資源，並會封鎖查詢的結果。 封鎖的原因可以是不佳的應用程式的設計、 錯誤的查詢計劃、 缺乏有用的索引，依此類推。 您可以使用 sys.dm_tran_locks 檢視，以取得 Azure SQL 資料庫中的 [鎖定目前活動的相關資訊。 例如程式碼，請參閱[sys.dm_tran_locks (SQL)](https://msdn.microsoft.com/library/ms190345.aspx) SQL Server 線上活頁簿中。

### <a name="monitoring-query-plans"></a>監控查詢計劃

計劃的效率查詢也可能會增加 CPU 消耗。 下列範例會使用[sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx)檢視，來判斷哪一個查詢使用最累計 CPU。

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>另請參閱

[SQL 資料庫的簡介](sql-database-technical-overview.md)
