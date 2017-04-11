<properties
    pageTitle="延伸 SQL 資料庫中的事件 |Microsoft Azure"
    description="說明 Azure SQL 資料庫中的延伸的事件 (XEvents)，以及如何事件工作階段稍有不同的 Microsoft SQL Server 事件工作階段。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""
    tags=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="genemi"/>


# <a name="extended-events-in-sql-database"></a>SQL 資料庫中的延伸的事件

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

本主題說明如何實作 Azure SQL 資料庫中的延伸事件的位置略有不同比較 Microsoft SQL Server 中的延伸事件。


- SQL 資料庫 V12 獲得延伸的事件功能中第二個下半部的行事曆 2015年。
- SQL Server 2008 自已擴充的事件。
- 擴充的事件 SQL 資料庫的功能集是 SQL Server 上的功能強大的子集。


*XEvents*會有時會用的 「 延伸事件 」 非正式暱稱位於部落格和其他非正式的位置。


> [AZURE.NOTE] 年 10 月 2015年延伸的事件工作階段功能就會啟動 Azure SQL 資料庫預覽層級。 尚未設定的一般的顯示狀態 (GA) 日期。
>
> Azure[服務更新](https://azure.microsoft.com/updates/?service=sql-database)頁面 GA 公告進行時，有文章。


Azure SQL 資料庫及 Microsoft SQL Server，擴充的事件的其他資訊位於︰

- [快速入門︰ SQL Server 中的延伸的事件](http://msdn.microsoft.com/library/mt733217.aspx)
- [擴充的事件](http://msdn.microsoft.com/library/bb630282.aspx)


## <a name="prerequisites"></a>必要條件


本主題假設您已經知道︰


- [Azure SQL 資料庫服務](https://azure.microsoft.com/services/sql-database/)。


- Microsoft SQL Server 中的[擴充事件](http://msdn.microsoft.com/library/bb630282.aspx)。
 - 我們的說明文件延伸事件的大量適用於 SQL Server 和 SQL 資料庫。


選擇做為[目標](#AzureXEventsTargets)事件檔案時，先前揭露下列項目，是很有幫助︰


- [Azure 儲存服務](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [使用 Azure PowerShell 的 Azure 儲存體](../storage/storage-powershell-guide-full.md)-提供 PowerShell 和 Azure 儲存體服務的完整資訊。


## <a name="code-samples"></a>程式碼範例


相關的主題提供兩種程式碼範例︰


- [撥打緩衝 SQL 資料庫中的延伸事件的目標程式碼](sql-database-xevent-code-ring-buffer.md)
 - 簡短的簡單 TRANSACT-SQL 指令碼。
 - 我們強調主題中的程式碼範例，當您完成撥打緩衝目標之後，您應該釋放其資源執行 alter 首`ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;`陳述式。 您可以在稍後新增另一個執行個體來撥打緩衝`ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`。


- [SQL 資料庫中的延伸事件的事件檔案目標程式碼](sql-database-xevent-code-event-file.md)
 - 階段 1 是 PowerShell 來建立 Azure 儲存體容器。
 - 階段 2 是使用 Azure 儲存體容器的 TRANSACT-SQL。


## <a name="transact-sql-differences"></a>TRANSACT-SQL 差異


- 當您在 SQL Server 執行[建立事件工作階段](http://msdn.microsoft.com/library/bb677289.aspx)] 命令時，您會使用**開啟伺服器**子句。 但 SQL 資料庫上您在**開啟資料庫**子句改為使用。


- **開啟資料庫**子句也適用於[變更的事件工作階段](http://msdn.microsoft.com/library/bb630368.aspx)，並[放事件工作階段](http://msdn.microsoft.com/library/bb630257.aspx)TRANSACT-SQL 命令。


- 最佳作法是包含事件工作階段選擇**STARTUP_STATE = 開啟**在您**建立的事件工作階段**或**修改事件工作階段**的陳述式中。
 - **= 開啟**值支援會自動重新啟動後的邏輯的資料庫，因為容錯移轉重新設定。


## <a name="new-catalog-views"></a>新的類別目錄檢視


擴充的事件功能支援多個[類別目錄檢視](http://msdn.microsoft.com/library/ms174365.aspx)。 類別目錄檢視會告訴您相關*的中繼資料或定義*的目前資料庫中的使用者所建立的事件工作階段。 檢視不會傳回作用中的事件工作階段的執行個體的相關資訊。


| 名稱<br/>類別目錄檢視 | 描述 |
| :-- | :-- |
| **sys.database_event_session_actions** | 會傳回每個動作的列上的事件階段每項事件。 |
| **sys.database_event_session_events** | 傳回每項事件的列中的事件工作階段。 |
| **sys.database_event_session_fields** | 傳回每一個已明確設定事件和目標的可自訂的資料行的資料列。 |
| **sys.database_event_session_targets** | 傳回一列的每一個事件目標的事件工作階段。 |
| **sys.database_event_sessions** | 傳回一列的 SQL 資料庫資料庫中的每個事件工作階段。 |


在 [Microsoft SQL Server，類似的類別目錄檢視擁有包含*.server\_*而不是*.database\_*。 名稱模式就像是**sys.server_event_%**。


## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>新的動態管理檢視[(Dmv)](http://msdn.microsoft.com/library/ms188754.aspx)


Azure SQL 資料庫有[動態管理檢視 (Dmv)](http://msdn.microsoft.com/library/bb677293.aspx)支援擴充的事件。 Dmv 告訴您*使用*的事件工作階段。


| DMV 的名稱 | 描述 |
| :-- | :-- |
| **sys.dm_xe_database_session_event_actions** | 傳回事件工作階段動作的相關資訊。 |
| **sys.dm_xe_database_session_events** | 傳回工作階段事件的相關資訊。 |
| **sys.dm_xe_database_session_object_columns** | 顯示物件繫結至工作階段的設定值。 |
| **sys.dm_xe_database_session_targets** | 傳回工作階段目標的相關資訊。 |
| **sys.dm_xe_database_sessions** | 傳回一列的範圍是每一個事件工作階段目前的資料庫。 |


在 [Microsoft SQL Server，類似的類別目錄檢視的命名不*\_資料庫*部分名稱，例如︰


- **sys.dm_xe_sessions**，而不是名稱<br/>**sys.dm_xe_database_sessions**。


### <a name="dmvs-common-to-both"></a>一般兩者 Dmv


擴充的事件有其他 Dmv Azure SQL 資料庫和 Microsoft SQL Server 一般的︰


- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>尋找可用延伸事件、 動作和目標


您可以執行簡單 SQL**選取**，以取得可用的事件、 動作和目標清單。


```
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>目標 SQL 資料庫事件工作階段


以下是可以擷取結果來自您 SQL 資料庫上的事件工作階段的目標︰


- [撥打緩衝目標](http://msdn.microsoft.com/library/ff878182.aspx)-簡要會保存在記憶體中的事件資料。
- [事件計數器目標](http://msdn.microsoft.com/library/ff878025.aspx)-計算延伸的事件工作階段期間發生的所有事件。
- [事件檔案的目標](http://msdn.microsoft.com/library/ff878115.aspx)-寫入到 Azure 儲存體容器的完整緩衝時間。


[追蹤的 Windows 事件 (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API 不適用於擴充 SQL 資料庫上的事件。


## <a name="restrictions"></a>限制


有幾項 befitting SQL 資料庫的雲端環境的安全性相關差異︰


- 擴充的事件會成立單一租用戶隔離模型。 在一個資料庫中的事件工作階段無法存取資料或事件，從另一個資料庫。

- 您不能發出**主要**資料庫內容中**建立的事件工作階段**陳述的式。


## <a name="permission-model"></a>權限模型


您要**建立的事件工作階段**陳述式資料庫必須**控制**」 權限。 資料庫擁有者 (dbo) 有**控制**」 權限。


### <a name="storage-container-authorizations"></a>儲存容器的授權


您為您 Azure 存放容器產生的 SA token 必須指定**rwl**的權限。 **Rwl**值會提供下列權限︰


- 已讀取
- 撰寫
- 清單


## <a name="performance-considerations"></a>考量效能


有大量使用延伸事件便會累積多記憶體比整體系統健康情況。 因此 Azure SQL 資料庫系統動態設定，並調整限制的使用中的記憶體可以累計事件工作階段的量。 許多因素進入動態計算。


如果您收到錯誤訊息，指出最大的記憶體已強制執行時，您可以採取一些修正動作是︰


- 執行較少的同時事件工作階段。


- 透過事件階段您**建立**並**變更**陳述式，減少您指定的記憶體**MAX\_記憶體**子句。


### <a name="network-latency"></a>網路延遲


網路延遲或失敗 Azure 儲存體 blob 保存資料時，可能會遇到的**事件檔案**的目標。 在他們等待完成的網路通訊時，可能會延遲 SQL 資料庫中的其他事件。 此延遲速度變慢您的工作量。

- 若要降低效能風險，避免**EVENT_RETENTION_MODE** ] 選項設定為**NO_EVENT_LOSS**事件中工作階段的定義。


## <a name="related-links"></a>相關的連結


- [使用 Azure PowerShell 的 Azure 儲存空間](../storage/storage-powershell-guide-full.md)。
- [Azure 儲存體 Cmdlet](http://msdn.microsoft.com/library/dn806401.aspx)


- [使用 Azure PowerShell 的 Azure 儲存體](../storage/storage-powershell-guide-full.md)-提供 PowerShell 和 Azure 儲存體服務的完整資訊。
- [如何使用從.NET Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)


- [建立認證 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [建立事件工作階段 (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Jonathan Kehayias 的部落格文章瞭解 Microsoft SQL Server 中的延伸事件](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


擴充的事件其他程式碼範例主題可在下列連結。 不過，您必須定期檢查任何範例，請參閱樣本是否為目標 Microsoft SQL Server，與 Azure SQL 資料庫。 然後您可以決定是否需要稍微修改一下執行範例。


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
