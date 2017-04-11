<properties
   pageTitle="在 SQL Data Warehouse 並行和工作量管理 |Microsoft Azure"
   description="瞭解在 Azure SQL Data Warehouse 並行和工作量管理開發解決方案。"
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>在 SQL Data Warehouse 並行和工作負載的管理

在 [縮放]，[Microsoft Azure SQL Data Warehouse 可協助進行如預期呈現效能您控制並行存取層級和記憶體和 CPU 優先順序等資源配置。 本文會向您介紹並行和工作負載的管理，說明實作這兩種功能，以及您如何控制其在您的資料倉庫的概念。 SQL Data Warehouse 工作量管理被為了幫助您支援多使用者環境。 不適合多租用戶的工作量。

## <a name="concurrency-limits"></a>並行限制

SQL Data Warehouse 可讓您最多 1024 個同時連線。 同時送出所有 1024 連線查詢。 不過，若要最佳化處理量，SQL Data Warehouse 可能佇列確保每個查詢收到最小的記憶體授與某些查詢。 在執行查詢時佇列會發生。 佇列查詢時並行限制到達時，SQL Data Warehouse 可以增加總處理量確保作用中的查詢取得極所需的記憶體資源的存取權。  

並行限制由兩個概念︰*同時查詢*和*並行位置*。 若要執行查詢，必須執行中的查詢並行限制和並行位置配置。

- 並行查詢是一次執行查詢。 SQL Data Warehouse 支援更大的 DWU 大小最多 32 同步的查詢。
- 根據 DWU 配置並行位置。 每個 100 DWU 提供 4 並行位置。 例如，DW100 會配置 4 並行位置，並 DW1000 配置 40。 每個查詢會耗費一或多個並行位置，取決於[資源類別](#resource-classes)的查詢。 在 smallrc 資源類別中執行的查詢取用一個並行位置。 查詢執行較高的資源類別中使用多個並行位置。

下表描述同步查詢和並行位置，以不同的 DWU 大小限制。

### <a name="concurrency-limits"></a>並行限制

|  DWU   | 最大值同步查詢  | 並行位置配置 |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

當這些臨界值的其中一個符合時，新的查詢佇列與執行第一個增益集的先進基準。  完成查詢的查詢和位置數目低於限制，被釋放佇列的查詢。 

> [AZURE.NOTE]  在動態管理檢視 (Dmv)] 或 [類別目錄檢視上以獨佔模式執行*選取*查詢都不受到任何並行限制。 您可以監視無論在其上執行的查詢數系統。

## <a name="resource-classes"></a>資源類別

資源類別的說明您可以控制記憶體配置及指定查詢的 CPU 週期。 您可以指定四個資源類別中的表單的*資料庫角色*的使用者。 四個資源類別是**smallrc**、 **mediumrc**、 **largerc**及**xlargerc**。 Smallrc 使用者會取得記憶體較小的數量，以及可以利用更高的並行。 相反地，指派給 xlargerc 的使用者會取得大量的記憶體，，因此較少其查詢可以同時執行。

根據預設，每個使用者是小型資源類別，smallrc 的成員。 程序`sp_addrolemember`用來增加資源類別，並`sp_droprolemember`用來減少資源類別。 例如，此命令會增加至 largerc loaduser 的資源類別︰

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

好的做法是永久指派資源類別，而不變更其資源類別的 [使用者。 例如載入到直的 columnstore 資料表建立高品質的成品索引時更多記憶體配置。 若要確保載入有較高的記憶體的存取權，建立使用者專為載入資料，並將會永久指派給較高的資源類別的 [此使用者。

有幾種不受益較大的記憶體配置的查詢。 系統會忽略其資源類別配置，一律改為小型資源類別中執行這些查詢。 如果這些查詢一律執行小型資源類別中，可以執行時並行位置是壓力下，與他們無法使用不時所需的其他位置。 如需詳細資訊，請參閱[資源類別的例外狀況](#query-exceptions-to-concurrency-limits)。

在 [資源類別幾個更多詳細資料︰

- *變更角色*的權限，才能變更資源類別的使用者。  
- 雖然您可以新增使用者至一或多個較高的資源類別，使用者會指派給的最高資源類別的屬性。 如果使用者指派 mediumrc 和 largerc，較高的資源類別 (largerc) 也就是說，將會套用資源類別。  
- 您無法變更的系統管理使用者的資源類別。

如需詳細範例，請參閱[變更使用者資源類別範例](#changing-user-resource-class-example)。

## <a name="memory-allocation"></a>記憶體配置

有優缺點增加使用者的資源類別。 增加資源類別的使用者，可讓更多記憶體，可能會導致查詢執行速度更快其查詢的存取權。  然而，較高的資源類別也會減少同步可以執行的查詢數。 這是記憶體的取捨配置大量至單一查詢或允許其他的查詢，也需要同時執行的記憶體配置。 如果一位使用者指定的查詢的記憶體高配置，其他使用者將不會有存取權的相同的記憶體執行查詢。

下表會對應到每個分配配置 DWU 和資源類別的記憶體。

### <a name="memory-allocations-per-distribution-mb"></a>記憶體配置每分配 (MB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1,600   |
| DW500  |   100   |    400   |    800  |  1,600   |
| DW600  |   100   |    400   |    800  |  1,600   |
| DW1000 |   100   |    800   |  1,600  |  3,200   |
| DW1200 |   100   |    800   |  1,600  |  3,200   |
| DW1500 |   100   |    800   |  1,600  |  3,200   |
| DW2000 |   100   |  1,600   |  3,200  |  6400   |
| DW3000 |   100   |  1,600   |  3,200  |  6400   |
| DW6000 |   100   |  3,200   |  6400  |  12,800  |

從上述資料表中，您可以看到 xlargerc 資源類別中 DW2000 上執行的查詢有的存取權的每個 60 分散式資料庫中的記憶體 6,400 MB。  在 SQL Data Warehouse，有 60 散佈。 因此，若要計算的查詢中指定的資源類別的總記憶體配置，上述值應該乘以 60。

### <a name="memory-allocations-system-wide-gb"></a>記憶體配置系統 (GB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

從整個系統的記憶體配置此目錄，您可以看到 xlargerc 資源類別中 DW2000 上執行的查詢會配置總計 375 gb 的記憶體 (6,400 MB * 60 散佈 / 1024 將轉換 GB) 移至您的 SQL Data Warehouse 的全部內容。

## <a name="concurrency-slot-consumption"></a>並行位置消耗

SQL Data Warehouse 授與查詢執行較高的資源類別中的更多記憶體。 記憶體是固定的資源。  因此，每個查詢配置更多記憶體，較少的同時查詢可以執行。 下表 reiterates 所有數並行位置即可 DWU 和由資源的每個類別的位置會顯示在單一檢視中的上一個概念。

### <a name="allocation-and-consumption-of-concurrency-slots"></a>配置及消耗並行槽

|  DWU   | 最大同步的查詢  | 並行位置配置 | 使用 smallrc 的位置 |  使用 mediumrc 的位置 |  使用 largerc 的位置 |  使用 xlargerc 的位置 |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


從這個資料表中，您可以看到的 SQL Data Warehouse 執行，為 DW1000 配置 32 一個查詢的最大值和 40 並行位置總計。 如果 smallrc 中執行的所有使用者，因為每個查詢會使用 1 並行位置會允許 32 同步的查詢。 如果 DW1000 上的所有使用者正在都執行中 mediumrc，每個查詢會配置 800 MB 的每個查詢，47 GB 的總記憶體配置分配並行會受限於 5 個使用者 (40 並行位置 / 8 位置每個 mediumrc 使用者)。

## <a name="query-importance"></a>查詢重要性

SQL Data Warehouse 實作資源類別的方式，使用工作負載的群組。 共有八個工作量群組的各種 DWU 大小跨控制資源類別的行為。 任何 DWU，SQL Data Warehouse 會使用只有四個八個工作量群組。 因為每個工作量群組指定至其中一個四個資源類別可以理解︰ smallrc mediumrc，largerc，或 xlargerc。 了解工作量群組的重要性設為確保有些工作量群組設定為 [高*重要性*。 重要性用於 CPU 排程。 使用 [高重要性執行的查詢會收到三次更多的 CPU 循環的中度的重要性。 因此，並行位置對應也會決定 CPU 優先順序。 查詢會耗費 16 或多個位置，它會執行為高重要性。

下表顯示每個工作量群組的重要性對應。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>並行位置和重要性的工作量群組對應

| 工作負載的群組 | 並行位置對應 | MB / 通訊群組 | 重要性對應 |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       媒體       |
| SloDWGroupC01   |            2             |         200       |       媒體       |
| SloDWGroupC02   |            4             |         400       |       媒體       |
| SloDWGroupC03   |            8             |         800       |       媒體       |
| SloDWGroupC04   |           16             |       1,600       |       高         |
| SloDWGroupC05   |           32             |       3,200       |       高         |
| SloDWGroupC06   |           64             |       6400       |       高         |
| SloDWGroupC07   |          128             |      12,800       |       高         |

從 [**配置和並行位置的**圖表，您可以看到的 DW500 用於 1、 4、 8 或 16 並行位置 smallrc、 mediumrc、 largerc 及 xlargerc，分別。 您可以在前面的圖表，若要尋找的每個資源類別的重要性查詢這些值。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>重要性資源類別的 DW500 對應

| 資源類別 | 工作負載的群組 | 使用並行槽 | MB / 通訊群組 | 重要性 |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   媒體   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   媒體   |
| largerc        | SloDWGroupC03  |           8            |         800       |   媒體   |
| xlargerc       | SloDWGroupC04  |          16            |        1,600      |   高     |


若要查看的詳細資料的記憶體資源分派資源管理員 」 的觀點的差異，或分析工作量群組的作用中] 與 [歷程記錄的使用狀況，疑難排解時，您可以使用下列 DMV 查詢。

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>受限於並行限制的查詢

大部分的查詢都受到資源類別。 這些查詢必須符合兩個同時查詢和並行位置臨界值。 使用者無法選擇排除並行位置模型中的查詢。

若要再次提醒您，下列陳述式接受資源類別︰

- 插入選取
- 更新
- 刪除
- 選取 （時查詢使用者資料表）
- ALTER 重建索引
- ALTER 索引重新組織
- ALTER 表格重建
- 建立索引
- 建立直的 COLUMNSTORE 索引
- 建立表格另存新檔選取 (CTAS)
- 載入資料
- 進行資料移動服務 (DMS) 的資料移動作業

## <a name="query-exceptions-to-concurrency-limits"></a>查詢並行限制的例外狀況

某些查詢不受影響指派給使用者的資源類別。 當記憶體所需的資源特定的命令低，通常是因為命令中繼資料作業時，對這些並行限制的例外狀況。 這些例外狀況的目標是為了避免會永遠不需要的查詢的較大的記憶體配置。 在下列情況下，無論指派給使用者的實際資源類別皆使用預設的小型資源類別 (smallrc)。 例如，`CREATE LOGIN`一律會以 smallrc 執行。 若要執行這項作業所需的資源是很低，，因此不會讓應包含並行位置模型中的查詢。  這些查詢也不受限制的 32 的使用者並行限制，這些查詢數量 1024 工作階段的工作階段限制，可以執行。

下列陳述式不會受影響資源類別︰

- 建立或卸除表格
- 變更資料表...切換、 分割或合併的磁碟分割
- ALTER 索引停用
- 卸除索引
- 建立、 更新或卸除統計資料
- 截斷資料表
- ALTER 授權
- 建立登入
- 建立、 變更或卸除使用者
- 建立、 變更或卸除程序
- 建立或卸除檢視
- 插入的值
- 選取 [從系統檢視和 Dmv
- 說明
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>變更使用者資源類別範例

1. **建立登入︰**開啟 [**母片**上主控您的 SQL Data Warehouse 資料庫的 SQL server 資料庫連線，然後執行下列命令。

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] 最好 Azure SQL Data Warehouse 使用者的主版資料庫中建立使用者。 在 [母片中建立使用者可讓使用者登入使用 SSMS 等工具，但未指定的資料庫名稱。  您也可以使用物件總管檢視 [SQL server 上的 [所有資料庫。  如需詳細瞭解如何建立及管理使用者的詳細資訊，請參閱[安全 SQL Data Warehouse 中的資料庫][]。

2. **建立 SQL Data Warehouse 使用者︰**開啟與**SQL Data Warehouse**資料庫的連線，然後執行下列命令。

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **授與權限︰**下列範例會授與`CONTROL` **SQL Data Warehouse**資料庫。 `CONTROL`在資料庫層級會是相當於 db_owner 在 SQL Server。

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **增加資源類別︰**您可以使用下列查詢，將使用者新增至較高的工作量管理角色。

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **減少資源類別︰**若要移除的工作量管理角色的使用者使用下列查詢。

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] 無法從 smallrc 中移除使用者。

## <a name="queued-query-detection-and-other-dmvs"></a>佇列的查詢偵測和其他 Dmv

您可以使用`sys.dm_pdw_exec_requests`DMV 來識別等待並行佇列中的查詢。 查詢等待並行位置必須**已擱置**狀態。

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

可以使用檢視工作量管理角色`sys.database_principals`。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

下列查詢會顯示每位使用者指派給角色。

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse 具有類型，請等候下列動作︰

- **LocalQueriesConcurrencyResourceType**︰ 坐並行位置架構以外的查詢。 DMV 查詢及系統函數例如`SELECT @@VERSION`是本機查詢的範例。
- **UserConcurrencyResourceType**︰ 坐內並行位置架構的查詢。 對使用者資料表查詢代表想要使用此資源類型的範例。
- **DmsConcurrencyResourceType**︰ 會產生的資料移動作業。
- **BackupConcurrencyResourceType**︰ 這個等待表示的資料庫正在備份。 此資源類型的最大值為 1。 如果有多個備份要求同時，其他人會佇列。

`sys.dm_pdw_waits` DMV 可查看正在等候要求的資源。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV 顯示僅由指定查詢資源等待。 資源等待的時間只測量的資源將會提供，而不是訊號等待時間為基礎的 SQL server 排程 CPU 到查詢所需的時間等待的時間。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV 可用於等待的歷程記錄的趨勢分析。

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>後續步驟

如需有關管理資料庫使用者與安全性的詳細資訊，請參閱[安全 SQL Data Warehouse 中的資料庫][]。 如需有關如何較大的資源類別可以改善直的 columnstore 索引品質，，請參閱[Rebuilding 索引，以改善區段品質]。

<!--Image references-->

<!--Article references-->
[保護資料庫安全之 SQL Data Warehouse 中]: ./sql-data-warehouse-overview-manage-security.md
[若要改善區段品質重建索引]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[保護資料庫安全之 SQL Data Warehouse 中]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
