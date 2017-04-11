<properties 
    pageTitle="SQL 資料庫 XEvent 撥打緩衝程式碼 |Microsoft Azure" 
    description="提供由輕鬆及快速撥打緩衝目標，Azure SQL 資料庫中使用的 SQL 程式碼範例。" 
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


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>撥打緩衝 SQL 資料庫中的延伸事件的目標程式碼

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

要完整的程式碼範例的最簡單的快速擷取及報表的資訊延伸事件測試期間方法。 最簡單的方法延伸的事件資料的目標是[撥打緩衝目標](http://msdn.microsoft.com/library/ff878182.aspx)。


本主題會提供的 SQL 程式碼範例︰


1. 建立一個表格，示範使用的資料。

2. 建立現有延伸事件，也就是**sqlserver.sql_statement_starting**的工作階段。
    - 事件僅限於包含特定的更新字串的 SQL 陳述式︰**陳述式，例如 「 %更新 tabEmployee %'**。
    - 選擇傳送事件的輸出類型撥打緩衝，也就是**package0.ring_buffer**的目標。

3. 啟動事件工作階段。

4. 問題幾個簡單的 SQL UPDATE 陳述式。

5. 從撥打緩衝擷取事件輸出 SQL 選取的問題。
    - 連接**sys.dm_xe_database_session_targets**和其他動態管理檢視 (Dmv)。

6. 停止事件工作階段。

7. 卸除撥打緩衝目標，釋放其資源。

8. 卸除事件工作階段的示範資料表。


## <a name="prerequisites"></a>必要條件


- Azure 帳戶與訂閱。 您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。


- 您可以建立表格中的任何資料庫。
 - 或者您可以[建立**AdventureWorksLT**示範資料庫](sql-database-get-started.md)以分鐘為單位。


- SQL Server Management Studio (ssms.exe)，最好是最新每月更新的版本。 您可以下載最新的 ssms.exe 從︰
 - 標題為[下載 SQL Server Management Studio 中](http://msdn.microsoft.com/library/mt238290.aspx)的主題。
 - [若要下載的直接連結。](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>程式碼範例


嚴重的修改，您可以在 Azure SQL 資料庫或 Microsoft SQL Server，執行下列撥打緩衝程式碼範例。 不同的是節點 '_database' 在 [名稱] 部分動態管理檢視 (Dmv)，在步驟 5 中 FROM 子句中使用的目前狀態。 例如︰

- sys.dm_xe**_database**_session_targets
- sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>撥打緩衝內容


我們會使用 ssms.exe 來執行程式碼範例。


若要檢視結果，在欄標題**target_data_XML**底下，按一下儲存格。

然後在 [結果] 窗格中按一下 [儲存格下欄標題**target_data_XML**。 按一下 [建立其他檔案] 索引標籤中的結果儲存格的內容顯示，為 XML ssms.exe。


輸出會顯示在下列區塊。 看起來，但只要有兩個**<event>**項目。


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>您撥打緩衝所發行資源


當您完成您撥打緩衝之後時，您可以移除選項，並釋放發行**改變**如下所示︰


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


更新，但尚未卸除事件工作階段的定義。 稍後您可以新增另一個撥打緩衝執行個體事件工作階段︰


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>詳細資訊


針對擴充 Azure SQL 資料庫上的事件的主要主題是︰


- [SQL 資料庫中的擴充事件考量](sql-database-xevent-db-diff-from-svr.md)的對比一些擴充與 Microsoft SQL Server Azure SQL 資料庫之間的不同的事件。


擴充的事件其他程式碼範例主題可在下列連結。 不過，您必須定期檢查任何範例，請參閱樣本是否為目標 Microsoft SQL Server，與 Azure SQL 資料庫。 然後您可以決定是否需要次要變更執行範例。


- Azure SQL 資料庫的程式碼範例︰ [SQL 資料庫中的延伸事件的事件檔案目標程式碼](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
