<properties
   pageTitle="監控您的工作量使用 Dmv |Microsoft Azure"
   description="瞭解如何監視使用 Dmv 工作量。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>監控您使用 Dmv 的工作量

本文將說明如何使用動態管理檢視 (Dmv) 來監控您的工作量和調查 Azure SQL Data Warehouse 中的執行查詢。

## <a name="permissions"></a>權限

若要查詢 Dmv 本文中的，您會需要檢視資料庫狀態或控制項的權限。 通常授與檢視資料庫的狀態是慣用的權限，因此限制更多。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>監視器連線

所有的登入 SQL Data Warehouse [sys.dm_pdw_exec_sessions][]記錄。  這個 DMV 包含的最後一個 10000 登入。  Session_id 的主索引鍵，並依序指派的每個新的登入。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>監視器執行查詢

[Sys.dm_pdw_exec_requests][]都會記錄在 SQL Data Warehouse 上執行的所有查詢。  這個 DMV 包含執行的最後一個 10000 查詢。  Request_id 唯一識別每個查詢，而是這個 DMV 的主索引鍵。  Request_id 會依序指派為每個新的查詢，並加上 QID，代表查詢的識別碼。  查詢的指定 session_id 這個 DMV 顯示指定登入的所有查詢。

>[AZURE.NOTE] 預存程序使用多個要求的識別碼。  要求識別碼指派連續順序。 

以下是調查查詢執行計劃和特定查詢的時間遵循的步驟。

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>步驟 1︰ 找出您想要調查的查詢

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

從先前的查詢結果，您想要檢查查詢的**記事要求的識別碼**。

由於並行限制正在佇列中的**已擱置**狀態的查詢。 這些查詢也會出現在 sys.dm_pdw_waits 等待查詢與 UserConcurrencyResourceType 的類型。 如需並行限制的詳細資訊，請參閱[並行和工作負載的管理][]。 查詢也可以等待物件鎖定例如其他原因。  如果您的查詢正在等候的資源，請參閱[Investigating 查詢等待資源][]往本文中。

若要簡化查詢 sys.dm_pdw_exec_requests 資料表中的查詢，使用 [[標籤][]]，將註解指派給您可以查詢 [sys.dm_pdw_exec_requests] 檢視中的查詢。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>步驟 2︰ 調查查詢計劃

若要查詢的 SQL (DSQL) 計劃，擷取[sys.dm_pdw_request_steps][]使用要求的識別碼。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

當 DSQL 計劃花費比預期更長時，原因可能複雜的計劃與許多 DSQL 步驟，或是只有一個步驟，而且花費很長的時間。  如果計劃與多個移動作業的多個步驟，請考慮最佳化您的表格分配，以減少移動資料。 [表格分配][]文章說明為何必須移到解決查詢資料，並說明一些最小化資料移動的通訊群組策略。

若要進一步調查單一步驟，長查詢步驟的*operation_type*資料行的詳細資料，並記下**步驟索引**︰

- 繼續步驟 3a **SQL**作業︰ OnOperation，RemoteOperation，ReturnOperation。
- **移動資料**作業繼續步驟 3b: ShuffleMoveOperation BroadcastMoveOperation、 TrimMoveOperation、 PartitionMoveOperation、 MoveOperation、 CopyOperation。

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>步驟 3a︰ 調查分散式資料庫的 SQL

使用 [要求識別碼] 和 [步驟索引擷取[sys.dm_pdw_sql_requests][]，其中包含執行的查詢步驟中的資訊上的所有分散式資料庫的詳細資料。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

執行查詢步驟時， [DBCC PDW_SHOWEXECUTIONPLAN][]可用來擷取的 SQL Server 估計計劃從 SQL Server 計劃快取的特定的通訊群組上執行的步驟。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>步驟 3b︰ 調查分散式資料庫的資料移動

使用 [要求識別碼] 和 [步驟索引來擷取資料移動步驟從[sys.dm_pdw_dms_workers][]執行每個通訊群組的相關資訊。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- 核取*total_elapsed_time*欄，請參閱是否花費遠超過其他人移動資料的特定的通訊群組。
- 長分配，查看*rows_processed*欄是否為大幅大於其他人從該分配移動的資料列數目。 如果是這樣，這可能表示 skew 的基礎資料。

如果正在執行查詢， [DBCC PDW_SHOWEXECUTIONPLAN][]可用來擷取的 SQL Server 估計計劃從 SQL Server 計劃快取目前正在執行 SQL 步驟內特定的通訊群組。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>監控等候查詢

如果您發現查詢未進行進度因為正在等候的資源，以下是查詢來顯示查詢正在等候的所有資源。

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

如果從另一個查詢的資源正在等候查詢，然後狀態會變成**AcquireResources**。  如果查詢所需的資源，然後狀態會**允許**。

## <a name="next-steps"></a>後續步驟
如需有關 Dmv 查看[系統的檢視][]。
瞭解最佳作法的詳細資訊，請參閱[SQL Data Warehouse 最佳作法][]

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse 最佳作法]: ./sql-data-warehouse-best-practices.md
[系統檢視]: ./sql-data-warehouse-reference-tsql-system-views.md
[表格通訊群組]: ./sql-data-warehouse-tables-distribute.md
[並行和工作負載的管理]: ./sql-data-warehouse-develop-concurrency.md
[調查等待資源的查詢]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[標籤]: https://msdn.microsoft.com/library/ms190322.aspx
