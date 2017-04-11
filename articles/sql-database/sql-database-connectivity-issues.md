<properties
    pageTitle="修正 SQL 連線錯誤、 暫時性錯誤 |Microsoft Azure"
    description="瞭解如何疑難排解、 診斷，並避免 SQL 連接錯誤或暫時 Azure SQL 資料庫中的錯誤。 "
    keywords="sql 連接的連接字串、 連線問題、 暫時性的錯誤、 連接錯誤"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>疑難排解、 診斷，並避免 SQL 連線錯誤和暫時性錯誤 SQL 資料庫

本文將說明如何避免、 疑難排解、 診斷，以及減輕連線錯誤與用戶端應用程式發生其互動 Azure SQL 資料庫時的暫時性錯誤。 瞭解如何設定重試邏輯，建立連接字串，以及調整其他連線設定。

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>暫時錯誤 （暫時錯誤）

暫時性錯誤-此外，暫時性錯誤-具有很快會解決本身根本原因。 Azure 系統快速將硬體資源向較佳的負載平衡各種負載時的暫時性的錯誤偶爾發生原因。 大部分的這些重新設定事件通常完成小於 60 秒數。 在此重新設定時間範圍內，您可能需要 Azure SQL 資料庫的連線問題。 建置應用程式連線至 Azure SQL 資料庫時，應該要預期這些暫時性的錯誤，而非其出現為應用程式錯誤的使用者其程式碼中實作重試邏輯方式處理。

如果您的用戶端程式使用 ADO.NET，是錯誤相關的暫時性的**SqlException**擲回另行告知您的程式。 [**數字**] 屬性可以比較的暫時性主題頂端附近的錯誤清單︰ [SQL 錯誤碼 SQL 資料庫用戶端應用程式](sql-database-develop-error-messages.md)。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>連線與] 命令

您會重試 SQL 連線，或根據下列再次建立︰

* **次嘗試連線發生暫時性的錯誤**︰ 應該重試之後延遲幾秒鐘的連線。

* **SQL 查詢命令發生暫時性的錯誤**: 命令應該不會立即重試。 不過，延遲之後，應該剛建立連線。 然後可以重試] 命令。


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>重試邏輯的暫時性錯誤


在包含重試邏輯時，有時會發生暫時性錯誤的用戶端程式會更強大的。


當您的程式進行通訊 Azure SQL 資料庫到第 3 的廠商介軟體時，查詢與廠商是否介軟體包含重試邏輯的暫時性錯誤。

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>重試原則


- 如果是暫時性的錯誤，應該會重試嘗試開啟連線。


- 不應該直接淘汰 SQL SELECT 陳述式的暫時性錯誤而失敗。
 - 不過，建立全新的連線，然後再試一次選取。


- 當暫時性錯誤而失敗 SQL UPDATE 陳述式時之前更新重試一次,，應該要建立全新的連線。
 - 重試邏輯必須確保整個資料庫交易完成之後，或的整個交易已復原。


#### <a name="other-considerations-for-retry"></a>重試其他考量


- 批次程式會自動啟動後工作時間和其會完成，才能上午，能負擔很長的時間間隔其重試之間耐心。


- 使用者介面程式應該人力就可能會太長等待之後放棄的帳戶。
 - 不過，方案不能重試每隔幾秒鐘，因為該原則可以種類系統要求。


#### <a name="interval-increase-between-retries"></a>重試之間的間隔增加



我們建議您在您的第一個重試之前延遲 5 秒。 小於 5 秒風險充斥雲端服務的延遲後重試。 延遲成長指數，每個後續重試上限為 60 秒。

使用[SQL Server 連線共用 (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)中的*封鎖期間*使用 ADO.NET 用戶端的討論。

您也可以設定程式自動結束前的次數上限。


#### <a name="code-samples-with-retry-logic"></a>重試邏輯的程式碼範例


使用各種不同的語言，重試邏輯的程式碼範例可在︰

- [SQL 資料庫與 SQL Server 的連線庫](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>測試您重試邏輯


若要測試您重試邏輯，您必須模擬或大於可以修正您的程式仍在執行時，會造成錯誤。


##### <a name="test-by-disconnecting-from-the-network"></a>從網路中斷連線測試


您可以測試您重試邏輯的其中一個方法是用戶端電腦中斷的網路連線時執行的程式。 會使用錯誤︰

- **SqlException.Number** = 11001
- 訊息: 「 沒有這種主機 」


第一次重試嘗試的一部分，您的程式，可以修正拼字錯誤，並嘗試連線。


若要將此實務您拔除您從網路的電腦之前您啟動程式。 然後您的程式會辨識會造成程式的執行的時間參數︰

1. 暫時 11001 將錯誤時，將暫時性的清單。
2. 如往常般嘗試其第一個連線。
3. 會攔截錯誤之後，請從清單中移除 11001。
4. 顯示一則訊息，告訴使用者插入網路的電腦。
 - 暫停**Console.ReadLine**方法或] 對話方塊中使用 [確定] 按鈕來執行。 使用者插入網路的電腦之後，按 Enter 鍵。
5. 再次嘗試連線，必須是成功。


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>測試連線時，資料庫名稱拼字錯誤


您的程式可以故意拼錯第一次連線嘗試之前的使用者名稱。 會錯誤︰

- **SqlException.Number** = 18456
- 訊息: 「 登入失敗使用者 'WRONG_MyUserName'。 」


第一次重試嘗試的一部分，您的程式，可以修正拼字錯誤，並嘗試連線。


若要將此實務程式無法辨識的內容會造成程式的執行的時間參數︰

1. 暫時 18456 加入錯誤時，將暫時性的清單。
2. 刻意 'WRONG_' 加入的使用者名稱。
3. 錯誤會攔截之後，請從清單中移除 18456。
4. 移除的使用者名稱 」 WRONG_ 」。
5. 再次嘗試連線，必須是成功。

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>連線重試.NET 典型參數


如果您用戶端程式連線至 Azure SQL 資料庫使用.NET Framework 課程**System.Data.SqlClient.SqlConnection**，您應該使用.NET 4.6.1 或更新版本，因此您可以利用其連線重試] 功能。 此功能的詳細資料的[此處](http://go.microsoft.com/fwlink/?linkid=393996)。


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


當您[連線字串](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx)的**典型**物件時，您應該進行下列參數之間的值︰

- ConnectRetryCount &nbsp; &nbsp; *（預設值為 1。範圍是 0 到 255。）*
- ConnectRetryInterval &nbsp; &nbsp; *（預設值為 1 秒。範圍是 1 到 60。）*
- 連線逾時&nbsp; &nbsp; *（預設為 15 秒。範圍是 0 到 2147483647）*


具體來說，您所選的值，應該都讓下列 」 等相等性 true:

- 連線逾時 = ConnectRetryCount * ConnectionRetryInterval

例如，如果計數 = 3，與間隔 = 10 秒內，逾時為只有 29 秒會不想讓系統足夠的時間供其第 3 與最終重試] 在連線︰ 29 < 3 * 10。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>連線與] 命令


**ConnectRetryCount**和**ConnectRetryInterval**參數可讓您試一次連線沒有告知或終究您的程式，例如返回您的程式中的控制項的**典型**物件。 重試可能會發生以下情況︰

- mySqlConnection.Open 方法通話
- mySqlConnection.Execute 方法通話

有微妙的地方。 如果正在執行*查詢*時，就會發生暫時性的錯誤，**典型**物件不會重新連線作業，然後它肯定不會重新查詢]。 不過，**典型**快速檢查您執行的查詢在傳送前的連線。 如果快速查看偵測到連線問題，**典型**重試連線作業。 如果重試成功，您的查詢傳送執行。


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>應該要結合應用程式重試邏輯 ConnectRetryCount 嗎？

假設您的應用程式有強大的自訂重試邏輯。 它可能試一次連線 4 的時間。 如果您新增**ConnectRetryInterval**及**ConnectRetryCount** = 3 至您的連線字串，您會增加到 4 * 3 = 12 重試計數重試。 您可能不想這類高次數。

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Azure SQL 資料庫的連線

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>連接︰ 連接字串


所需的連線至 Azure SQL 資料庫中的連接字串是連線到 Microsoft SQL Server 的字串與稍有不同。 從[Azure 入口網站](https://portal.azure.com/)，您可以為您的資料庫複製連線字串。


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>連線︰ IP 位址


您必須設定要接受主控您的用戶端程式的電腦的 IP 位址通訊的 SQL 資料庫伺服器。 編輯 [透過[Azure 入口網站](https://portal.azure.com/)的防火牆設定執行此動作。


如果您忘了設定的 IP 位址，您的程式將會失敗，方便的錯誤訊息，指出必要的 IP 位址。


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


如需詳細資訊，請參閱︰[如何︰ 在 SQL 資料庫設定防火牆](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>連接︰ 連接埠


通常您只需要確保開啟主控您的用戶端程式的電腦上的連外通訊的連接埠 1433年。


例如，當您的用戶端程式裝載在 Windows 電腦上，主機上的 [Windows 防火牆可讓您開啟連接埠 1433年:


1. 開啟 [控制台
2. &gt;所有控制台項目
3. &gt;在 Windows 防火牆
4. &gt;進階的設定
5. &gt;輸出規則
6. &gt;動作
7. &gt;新增規則


如果您的用戶端程式會裝載於 Azure 虛擬機器 (VM) 上，您應該如下︰<br/>[除了 ADO.NET 4.5 1433年的連接埠和 SQL 資料庫 V12](sql-database-develop-direct-route-ports-adonet-v12.md)。


有關背景 cofiguration 的連接埠與 IP 位址，請參閱︰ [Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>連線︰ ADO.NET 4.6.1


如果您的程式使用 ADO.NET 類別，例如**System.Data.SqlClient.SqlConnection**到 Azure SQL 資料庫連線，我們建議您使用.NET Framework 4.6.1 版本或更新版本。


ADO.NET 4.6.1:

- Azure SQL 資料庫，有可靠性使用**SqlConnection.Open**方法開啟連線時。 **Open**方法現在併入暫時錯誤的回應，針對特定錯誤連線逾時期間內的最佳投入重試機制。
- 支援連接共用。 這包含的功能，可讓您的程式連線物件有效驗證。



當您連線資料庫中使用連接物件時，我們建議您的程式暫時關閉連線時不會立即使用它。 成本，建立新的連線的方法是不是重新開啟連接。


如果您使用的 ADO.NET 4.0 或較舊版本，我們建議您升級為最新的 ADO.NET。

- 年 11 月 2015 中，您可以[下載 ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx)。


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>診斷

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>診斷︰ 測試是否可以連線公用程式


如果您的程式連線至 Azure SQL 資料庫失敗，診斷的其中一個選項是嘗試與公用程式連線。 理想公用程式會使用您的程式使用的相同文件庫連線。


在任何 Windows 電腦上，您可以嘗試這些公用程式︰

- SQL Server Management Studio (ssms.exe)，使用 ADO.NET 連線。
- sqlcmd.exe，透過[ODBC](http://msdn.microsoft.com/library/jj730308.aspx)連線。


連線之後，測試是否簡短 SQL 選取查詢運作。


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>診斷︰ 核取 [開啟連接埠


假設您懷疑，由於連接埠問題，因此失敗嘗試連線。 在您的電腦上，您可以執行的公用程式上設定的連接埠的報表。


在 Linux 下列公用程式可能會很有幫助︰

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - （範例值變更為您的 IP 位址）。


在 Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148)公用程式可能會很有幫助。 以下是範例執行的查詢於 Azure SQL 資料庫伺服器上的連接埠情況和其在膝上型電腦上執行︰


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>診斷︰ 登入錯誤


透過天數或週數的一般的圖樣偵測有時最適合是來診斷間歇性發生的問題。


診斷可協助您的用戶端來記錄遇到的所有錯誤。 您可以建立的記錄項目關聯的 Azure SQL 資料庫記錄本身內部錯誤資料。


企業文件庫 6 (EntLib60) 提供協助您記錄的受管理的.NET 類別︰

- [5-，只要關閉記錄落︰ 使用記錄應用程式區塊](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>診斷︰ 檢查有錯誤的系統記錄檔


以下是一些選取 SQL 陳述式，錯誤的該查詢記錄及其他資訊。


| 記錄檔的查詢 | 描述 |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | [Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx)檢視提供個別的事件，包括一些可能會導致暫時性錯誤或連線失敗的相關資訊。<br/><br/>最好是您可以關聯**start_time**或**end_time**值與您的用戶端程式時遇到問題的相關資訊。<br/><br/>**提示︰**您必須連線至要執行此程序的**主版**資料庫。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | [Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx)檢視提供事件類型，其他診斷的彙總的的數量。<br/><br/>**提示︰**您必須連線至要執行此程序的**主版**資料庫。 |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>在 [SQL 資料庫登入問題事件診斷︰ 搜尋


您可以搜尋的相關問題的事件 Azure SQL 資料庫的記錄檔中的項目。 請嘗試下列 TRANSACT-SQL 中的 SELECT 陳述式**主要**資料庫︰


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>從 sys.fn_xe_telemetry_blob_target_read_file 幾個傳回的資料列


下一步是傳回的資料列的外觀。 所顯示的 null 值通常不是空值，其他列中的。


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>企業文件庫 6


企業文件庫 6 (EntLib60) 是的架構的.NET 類別可協助您執行強大的用戶端的雲端服務，一個是 Azure SQL 資料庫服務。 您可以找出第一份造訪專用 EntLib60 可幫助每個區域的主題︰

- [6-年 4 月 2013 Enterprise 文件庫](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


重試邏輯處理暫時性錯誤的是一個區域 EntLib60 可幫助︰

- [4-堅持不懈、 私人的所有勝利︰ 使用暫時性錯誤處理應用程式區塊](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] 使用公用[下載](http://go.microsoft.com/fwlink/p/?LinkID=290898)EntLib60 的程式碼。 Microsoft 有不打算進行進一步的電子郵件地址] 或進行的維修作業更新功能更新至 EntLib。

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>暫時性的錯誤和重試 EntLib60 類別


下列 EntLib60 類別是重試邏輯特別有用。 全部這些是，或進一步的命名空間**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**下︰

*在命名空間**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

- **RetryPolicy**類別
 - **ExecuteAction**方法


- **ExponentialBackoff**類別


- **SqlDatabaseTransientErrorDetectionStrategy**類別


- **ReliableSqlConnection**類別
 - **ExecuteCommand**方法


在命名空間**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy**類別

- **NeverTransientErrorDetectionStrategy**類別


以下是 EntLib60 的相關資訊的連結︰

- 免費[書籍下載︰ Microsoft Enterprise 文件庫，第 2 Edition 開發人員指南](http://www.microsoft.com/download/details.aspx?id=41145)

- 最佳作法︰[再試一次 [一般指示](../best-practices-retry-general.md)具有重試邏輯的絕佳深入討論。

- [Enterprise 文件庫的暫時性錯誤處理應用程式區塊 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)的 NuGet 下載

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60︰ 記錄區塊


- 記錄區塊是強烈彈性，可以設定的解決方案，可讓您︰
 - 建立並儲存記錄檔的郵件中各種不同的位置。
 - 分類和篩選的郵件。
 - 收集內容是用來偵錯及追蹤，以及稽核和一般記錄需求的資訊。


- 記錄區塊抽取從記錄目的地的記錄功能，讓應用程式碼是一致，不管資料的位置與目標記錄存放區類型。


如需詳細資訊，請參閱︰ [5-為輕鬆地為落關閉記錄︰ 使用該記錄的應用程式區塊](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient 方法原始程式碼


接下來，從**SqlDatabaseTransientErrorDetectionStrategy**類別是 C# 原始程式碼**IsTransient**方法。 原始程式碼釐清哪些錯誤被視為暫時性和重試] 下，從 2013 年 4 月的。

已移除許多**//comment**線從這個複本以強調可讀性。


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>後續步驟

- 其他常見的 Azure SQL 資料庫連線問題的疑難排解，請瀏覽[到 Azure SQL 資料庫的疑難排解連線問題](sql-database-troubleshoot-common-connection-issues.md)。

- [SQL Server 連線共用 (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying*是 Apache 2.0 授權用途正在重試文件庫，撰寫**Python**，以簡化到幾乎任何項目新增重試行為的工作。](https://pypi.python.org/pypi/retrying)
