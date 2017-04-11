<properties 
    pageTitle="SQL 資料庫 XEvent 事件檔案的程式碼 |Microsoft Azure" 
    description="PowerShell 和 TRANSACT-SQL 提供兩階段的程式碼範例示範事件檔案中的目標擴充的事件 Azure SQL 資料庫]。 Azure 儲存體必要屬於這種情況。" 
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


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>SQL 資料庫中的延伸事件的事件檔案目標程式碼

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

您要完整的程式碼範例強大的擴充的事件擷取及報表的資訊。


在 [Microsoft SQL Server，[事件檔案的目標](http://msdn.microsoft.com/library/ff878115.aspx)用來儲存到本機硬碟檔案的事件輸出。 但這類檔案不是使用 Azure SQL 資料庫。 我們改用 Azure 儲存體服務支援的事件檔案的目標。


本主題會顯示兩階段的程式碼範例︰


- 若要在雲端建立 Azure 儲存體容器的 PowerShell。

- TRANSACT-SQL:
 - 若要指定 Azure 存放容器至事件檔案的目標。
 - 若要建立並啟動事件工作階段等等。


## <a name="prerequisites"></a>必要條件


- Azure 帳戶與訂閱。 您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。


- 您可以建立表格中的任何資料庫。
 - 或者您可以[建立**AdventureWorksLT**示範資料庫](sql-database-get-started.md)以分鐘為單位。


- SQL Server Management Studio (ssms.exe)，最好是最新每月更新的版本。 您可以下載最新的 ssms.exe 從︰
 - 標題為[下載 SQL Server Management Studio 中](http://msdn.microsoft.com/library/mt238290.aspx)的主題。
 - [若要下載的直接連結。](http://go.microsoft.com/fwlink/?linkid=616025)


- 您必須已安裝的[PowerShell 的 Azure 模組](http://go.microsoft.com/?linkid=9811175)。
 - 模組提供命令，如-**新增 AzureStorageAccount**。


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Azure 儲存容器的階段 1: PowerShell 程式碼


這個 PowerShell 是階段 1 兩階段的程式碼範例。

指令碼的第一句是可能的上一個執行，以及 [rerunnable 清理] 命令。



1. 貼入 Notepad.exe，例如簡易文字編輯器中的 PowerShell 指令碼，並將指令碼儲存為副檔名**.ps1**使用的檔案。

2. 啟動 PowerShell ise [以系統管理員。

3. 出現提示時，輸入<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>然後按 enter 鍵。

4. 在 PowerShell ise [以系統，開啟您**.ps1**檔案。 執行指令碼。

5. 指令碼第一次啟動新視窗中您登入 Azure。
 - 如果您重新指令碼執行而不干擾您的工作階段時，您可以方便的註解**新增 AzureAccount**命令。


![PowerShell ise [以系統，與 Azure 已安裝，即可執行指令碼的模組。][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


請注意幾個結束時，列印的 PowerShell 指令碼的名稱值。 您必須將以下為階段 2 TRANSACT-SQL 指令碼編輯這些值。


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>階段 2︰ 使用 Azure 存放容器的 TRANSACT-SQL 程式碼


- 在此程式碼範例階段 1，您可以執行的 PowerShell 指令碼，來建立 Azure 儲存體容器。
- 下一步階段 2，下列 TRANSACT-SQL 指令碼必須使用容器。


指令碼的第一句是可能的上一個執行，以及 [rerunnable 清理] 命令。


PowerShell 指令碼列印幾個命名的值，則結束工作流程。 您必須編輯 TRANSACT-SQL 指令碼，使用這些值。 若要找出編輯端點的 TRANSACT-SQL 指令碼中尋找**TODO** 。


1. 開啟 [SQL Server Management Studio (ssms.exe)。

2. 連線至您 Azure SQL 資料庫的資料庫。

3. 按一下以開啟新的 [查詢] 窗格。

4. 將下列 SQL 指令碼貼到 [查詢] 窗格中。

5. 尋找指令碼中的每個**TODO**以適當的編輯內容。

6. 儲存及執行指令碼。


&nbsp;


> [AZURE.WARNING] 上述的 PowerShell 指令碼所產生的 SA 值可能會以開頭 「？ 」（問號）。 當您使用下列 T SQL 指令碼 SA 鍵時，您必須*移除的前置 '?'*。 否則您的努力，可能會封鎖安全性。


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


如果目標無法附加執行時，您必須停止並重新啟動事件工作階段︰


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>輸出


TRANSACT-SQL 指令碼完成後，請按一下 [ **event_data_XML**欄標題底下的 [儲存格]。 一個**<event>**項目會顯示會顯示一個 UPDATE 陳述式。

以下是一個**<event>**於測試期間所產生的項目︰


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


上述 TRANSACT-SQL 指令碼會用來讀取 event_file 下列系統函數︰

- [sys.fn_xe_file_target_read_file (SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


從延伸事件的資料檢視的進階選項的說明位於︰

- [進階的延伸事件的目標資料檢視](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>轉換為 SQL Server 上執行的程式碼範例


假設您想要在 Microsoft SQL Server 執行上述 TRANSACT-SQL 範例。


- 為求，想要完全取代 Azure 儲存體容器的使用簡單的檔案，例如**C:\myeventdata.xel**。 檔案會寫入到本機硬碟主控 SQL Server 的電腦中。


- 您不需要任何類型的 SQL 陳述式**建立主索引鍵**和**建立的認證**。


- **建立事件工作階段**中陳述式，其**新增目標**子句，您想要取代指派的 Http 值對**filename =** **C:\myfile.xel**等的完整路徑字串。
 - 需要涉及沒有 Azure 儲存體帳戶。


## <a name="more-information"></a>詳細資訊


帳戶和 Azure 儲存體服務中的容器的詳細資訊，請參閱︰

- [如何使用從.NET Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)
- [命名及參照容器、 Blob 和中繼資料](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [使用 [根容器](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [課程 1︰ 建立 Azure 容器上儲存的存取原則和共用的 access 簽章](http://msdn.microsoft.com/library/dn466430.aspx)
    - [課程 2︰ 建立 SQL Server 認證使用共用的 access 簽章](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

