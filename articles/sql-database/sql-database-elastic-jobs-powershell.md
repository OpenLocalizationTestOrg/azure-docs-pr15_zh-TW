<properties 
    pageTitle="建立及管理彈性的資料庫作業使用 PowerShell" 
    description="PowerShell 用來管理 Azure SQL 資料庫集區" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>建立及管理 SQL 資料庫彈性的資料庫工作使用 PowerShell （預覽版本）

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



PowerShell Api 的**彈性的資料庫作業**（預覽版本），可讓您定義一組的指令碼會執行的資料庫。 本文將示範如何建立和管理**彈性的資料庫作業**使用 PowerShell cmdlet。 請參閱[彈性的工作概觀](sql-database-elastic-jobs-overview.md)。 

## <a name="prerequisites"></a>必要條件
* Azure 的訂閱。 免費的試用版，請參閱[一個月免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。
* 彈性的資料庫工具建立資料庫的一組。 請參閱[開始使用彈性的資料庫工具](sql-database-elastic-scale-get-started.md)。
* Azure PowerShell。 詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。
* **彈性的資料庫作業**PowerShell 套件︰ 請參閱[安裝彈性的資料庫作業](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>選取您 Azure 的訂閱

若要選取訂閱，您需要您的訂閱 Id (**-SubscriptionId**) 或訂閱名稱 (**-SubscriptionName**)。 如果您有多個訂閱，您可以執行**取得 AzureRmSubscription**指令程式，並想要的訂閱資訊複製結果集。 一旦您有訂閱資訊，請執行下列 commandlet 若要將此訂閱設定預設值，也就是建立及管理工作的目標︰

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[PowerShell ise [以系統](https://technet.microsoft.com/library/dd315244.aspx)建議開發及執行針對彈性的資料庫作業的 PowerShell 指令碼的使用方式。

## <a name="elastic-database-jobs-objects"></a>彈性的資料庫作業物件

下表列出所有的物件類型的**彈性的資料庫工作**以及其描述和相關的 PowerShell Api。

<table style="width:100%">
  <tr>
    <th>物件類型</th>
    <th>描述</th>
    <th>相關的 PowerShell Api</th>
  </tr>
  <tr>
    <td>認證</td>
    <td>使用者名稱和密碼連線至資料庫執行指令碼或應用程式的 DACPACs 時使用。 <p>密碼會傳送給和彈性的資料庫作業資料庫中儲存之前加密。  彈性的資料庫作業服務透過建立並安裝指令碼從上傳的認證解密的密碼。</td>
    <td><p>取得 AzureSqlJobCredential</p>
    <p>新 AzureSqlJobCredential</p><p>設定 AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>指令碼</td>
    <td>用於跨資料庫執行 TRANSACT-SQL 指令碼。  在限定指令碼時，應該是冪，因為服務會再試一次執行時失敗的指令碼。
    </td>
    <td>
    <p>取得 AzureSqlJobContent</p>
    <p>取得 AzureSqlJobContentDefinition</p>
    <p>新 AzureSqlJobContent</p>
    <p>設定 AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">資料層應用程式</a>套用到資料庫的套件。

    </td>
    <td>
    <p>取得 AzureSqlJobContent</p>
    <p>新 AzureSqlJobContent</p>
    <p>設定 AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>資料庫目標</td>
    <td>指向 [Azure SQL 資料庫的資料庫與伺服器名稱。

    </td>
    <td>
    <p>取得 AzureSqlJobTarget</p>
    <p>新 AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>晶怪地圖目標</td>
    <td>資料庫目標與要用來決定資訊儲存在將彈性的資料庫晶怪對應認證的組合。
    </td>
    <td>
    <p>取得 AzureSqlJobTarget</p>
    <p>新 AzureSqlJobTarget</p>
    <p>設定 AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>自訂集合目標</td>
    <td>已定義之的資料庫執行共同使用的群組。</td>
    <td>
    <p>取得 AzureSqlJobTarget</p>
    <p>新 AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>自訂集合子目標</td>
    <td>從自訂集合參照的目標資料庫。</td>
    <td>
    <p>新增 AzureSqlJobChildTarget</p>
    <p>移除 AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>工作</td>
    <td>
    <p>定義的參數，可用來觸發執行或滿足排程工作。</p>
    </td>
    <td>
    <p>取得 AzureSqlJob</p>
    <p>新 AzureSqlJob</p>
    <p>設定 AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>作業執行</td>
    <td>
    <p>根據執行原則處理的任務完成 [執行指令碼] 或 [套用至目標使用認證的資料庫連線，而失敗的 DACPAC 所需的容器。</p>
    </td>
    <td>
    <p>取得 AzureSqlJobExecution</p>
    <p>開始 AzureSqlJobExecution</p>
    <p>停止 AzureSqlJobExecution</p>
    <p>等待 AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>執行工作的工作</td>
    <td>
    <p>單一完成工作的工時單位。</p>
    <p>如果工作任務不能順利執行，將記錄產生的例外狀況訊息，而且會建立和執行根據指定的執行原則相符工作工作。</p></p>
    </td>
    <td>
    <p>取得 AzureSqlJobExecution</p>
    <p>開始 AzureSqlJobExecution</p>
    <p>停止 AzureSqlJobExecution</p>
    <p>等待 AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>工作執行原則</td>
    <td>
    <p>控制項工作執行逾時與重試限制重試之間的間隔。</p>
    <p>彈性的資料庫作業包含與每個重試之間的間隔的指數輪詢導致基本上無限重試工作的工作失敗的預設工作執行原則。</p>
    </td>
    <td>
    <p>取得 AzureSqlJobExecutionPolicy</p>
    <p>新 AzureSqlJobExecutionPolicy</p>
    <p>設定 AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>排程</td>
    <td>
    <p>時間基礎的位置上重複發生的間隔或一次執行的規格。</p>
    </td>
    <td>
    <p>取得 AzureSqlJobSchedule</p>
    <p>新 AzureSqlJobSchedule</p>
    <p>設定 AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>工作引動程序</td>
    <td>
    <p>工作和排程，將在排程的觸發程序作業執行之間的對應。</p>
    </td>
    <td>
    <p>新 AzureSqlJobTrigger</p>
    <p>移除 AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>支援的彈性的資料庫工作群組類型
工作會執行 TRANSACT-SQL (T SQL) 指令碼或應用程式的 DACPACs 群組中的資料庫。 當工作提交群組資料庫中執行時，工作] 以展開 」 將每個會針對單一資料庫要求的執行執行 [] 群組中的子工作。 
 
有兩種類型的群組，您可以建立︰ 

* [晶怪地圖](sql-database-elastic-scale-shard-map-management.md)] 群組︰ 當工作提交目標晶怪地圖時，工作查詢晶怪地圖，以判斷其目前擊碎，這組，然後建立工作的每個晶怪子晶怪對應中。
* 集合的自訂群組︰ 自訂定義一組資料庫。 當工作的目標自訂的集合時，它會建立子工作的每個資料庫目前自訂集合中。

## <a name="to-set-the-elastic-database-jobs-connection"></a>若要設定彈性的資料庫工作連線

連線必須將它設定為工作*控制資料庫*之前使用 Api 的工作。 執行這個指令程式會觸發認證視窗出現要求的使用者名稱和安裝彈性的資料庫作業時所建立的密碼。 本主題中所提供的所有範例都假設已執行第一個步驟。

開啟彈性的資料庫作業的連線︰

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>加密的認證內彈性的資料庫作業

資料庫認證可以插入工作*控制資料庫*加密其密碼。 就必須儲存的認證以啟用稍後，要執行的工作 （使用作業排程）。
 
加密是透過安裝指令碼的一部分建立的憑證運作。 安裝指令碼會建立並上傳到解密密碼加密 Azure 雲端服務的憑證。 Azure 雲端服務稍後再將其儲存到公用工作*控制資料庫*可讓 PowerShell API 或 Azure 傳統入口網站的介面，來加密提供的密碼，而不需要本機安裝的憑證。
 
認證密碼會加密及彈性的資料庫作業物件安全的唯讀存取權的使用者。 但可能惡意使用者讀取寫入存取彈性的資料庫作業物件擷取密碼。 認證的設計作業執行重複使用。 認證傳遞至的目標資料庫時建立的連線。 目前沒有使用每個認證的目標資料庫的限制，惡意使用者無法新增惡意的使用者掌控資料庫的資料庫目標。 使用者就可以開始工作指定目標此資料庫，以取得認證的密碼。

安全性的彈性的資料庫工作的最佳作法包括︰

* 限制使用信任的個人 Api。
* 認證應具備執行作業工作所需的最低權限。  此[授權和權限](https://msdn.microsoft.com/library/bb669084.aspx)的 SQL Server MSDN 文章中，可以看到詳細的資訊。

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>若要建立資料庫之間的工作執行加密的認證

若要建立新的加密的認證，[**取得認證指令程式**](https://technet.microsoft.com/library/hh849815.aspx)會提示使用者名稱和密碼，可以傳遞給[**新增 AzureSqlJobCredential 指令程式**](https://msdn.microsoft.com/library/mt346063.aspx)。

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>若要更新的認證

變更密碼，請使用[**設定 AzureSqlJobCredential 指令程式**](https://msdn.microsoft.com/library/mt346062.aspx)，並設定**CredentialName**參數。

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>若要定義彈性的資料庫晶怪地圖目標

若要執行工作針對所有資料庫晶怪集合 （使用[彈性的資料庫用戶端文件庫](sql-database-elastic-database-client-library.md)建立） 中，用來做資料庫目標晶怪地圖。 此範例需要建立使用彈性的用戶端資料庫的 sharded 應用程式。 請參閱[快速入門彈性的資料庫工具範例](sql-database-elastic-scale-get-started.md)。

必須為資料庫目標設定晶怪對應 manager 資料庫，然後特定晶怪對應必須指定為目標。

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>在資料庫中建立執行 T SQL 指令碼

建立時執行 T SQL 指令碼，則強烈建議來建立這些[冪](https://en.wikipedia.org/wiki/Idempotence)和彈性失敗。 彈性的資料庫作業會再試一次執行指令碼時執行遇到錯誤，無論失敗的分類。

您可以使用 [[**新增 AzureSqlJobContent 指令程式**](https://msdn.microsoft.com/library/mt346085.aspx)來建立並儲存執行指令碼並設定**-ContentName**及**-CommandText**參數。

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>從檔案建立新的指令碼
如果檔案中定義 T SQL 指令碼，請使用此選項匯入指令碼︰

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>若要在資料庫更新 T SQL 指令碼執行  

這個 PowerShell 指令碼更新現有的指令碼的 T SQL 命令文字。

設定以反映所需的指令碼定義設定下列變數︰

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>若要更新現有的指令碼定義

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>若要建立晶怪地圖上執行指令碼作業

這個 PowerShell 指令碼會啟動跨彈性的縮放比例晶怪對應中的每個晶怪執行指令碼的工作。

設定以反映所需的指令碼和目標下列變數︰

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>若要執行的作業 

這個 PowerShell 指令碼會執行現有的工作︰

更新以反映所要的工作名稱來執行下列變數︰

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>擷取單一工作執行的狀態

使用[**取得 AzureSqlJobExecution 指令程式**](https://msdn.microsoft.com/library/mt346058.aspx)，並設定**JobExecutionId**參數，若要檢視的工作的執行狀態。

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

若要檢視的狀態子作業執行，也就是針對每個資料庫工作之目標每個作業執行的特定狀態使用**IncludeChildren**參數相同**取得 AzureSqlJobExecution**指令程式。

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>若要跨多個作業執行中檢視狀態

[**取得 AzureSqlJobExecution cmdlet**](https://msdn.microsoft.com/library/mt346058.aspx)有多個可用於顯示多個作業執行，透過提供的參數篩選的選擇性參數。 以下示範一些使用取得 AzureSqlJobExecution 可能的方式︰

擷取所有使用中的最上層工作執行︰

    Get-AzureSqlJobExecution

擷取所有上方的層級工作執行，包括非使用中工作的執行︰

    Get-AzureSqlJobExecution -IncludeInactive

擷取提供的工作的執行識別碼，包括非使用中工作的執行的所有子作業執行︰

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

擷取使用排程建立所有作業執行/工作組合，包括非作用中的工作︰

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

擷取所有目標指定晶怪地圖，包括非使用中工作的工作︰

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

擷取所有目標指定自訂集合，包括非使用中工作的工作︰

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
擷取內特定工作執行的作業任務執行的清單︰

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

擷取工作任務執行詳細資料︰

下列 PowerShell 指令碼可用來檢視詳細資料的作業的任務執行，當偵錯執行失敗時特別有用。

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>若要擷取內作業任務執行失敗

**JobTaskExecution 物件**包含該任務的訊息屬性的生命週期的屬性。 如果工作執行工作失敗，將 [週期] 屬性設定為 [*失敗*，會產生的例外狀況訊息和其堆疊設定 [訊息] 屬性。 如果工作失敗，請務必若要檢視的工作失敗指定工作的詳細資料。

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>等到完成作業執行

下列 PowerShell 指令碼可用於工作的工作完成時，請等候︰

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>建立自訂的執行原則

彈性的資料庫作業支援建立可啟動工作時套用自訂的執行原則。
  
定義目前允許執行原則︰

* 執行原則名稱︰ 識別項。
* 工作逾時︰ 的總時間之前彈性的資料庫工作，就會取消工作。
* 第一個重試之前等待的初始重試間隔︰ 間隔。
* 若要使用 [最大重試間隔︰ 重試間隔的端點。
* 用來計算的下一個間隔重試之間的間隔輪詢係數再試一次︰ 係數。  使用下列公式: （初始再試一次的間隔） * Math.pow (（間隔輪詢係數） （次數）-2)。 
* 最大嘗試︰ 執行工作內嘗試重試的最大數目。

預設執行原則會使用下列的值︰

* 名稱︰ 預設執行原則
* 工作逾時︰ 1 週
* 初始重試間隔︰ 100 毫秒
* 最大重試間隔︰ 30 分鐘
* 重試間隔係數︰ 2
* 最大嘗試︰ 2147483647

建立所需的執行原則︰

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>更新自訂執行原則

更新來更新所需的執行原則︰

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>取消工作

彈性的資料庫作業支援取消邀請的工作。  如果彈性的資料庫作業偵測到目前正在執行工作的取消邀請時，它會停止工作。

有兩種不同方式彈性的資料庫作業可執行取消通知︰

1. 目前正在執行工作的 [取消]: 取消偵測到目前執行工作時，如果取消通知將會嘗試內目前正在執行工作的外觀。  例如︰ 目前正在執行時嘗試取消長時間執行查詢時，將會嘗試取消查詢。
2. 取消任務重試︰ 控制執行緒如果取消偵測來控制執行緒之前執行啟動任務，則會避免啟動工作，並為取消宣告要求。

如果上層工作要求工作取消時，就會生效取消邀請的父項工作，以及所有的子工作。
 
以提交取消要求，請使用[**停駐點 AzureSqlJobExecution cmdlet**](https://msdn.microsoft.com/library/mt346053.aspx)設定**JobExecutionId**參數。

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>若要刪除的工作，以及非同步工作歷程記錄

彈性的資料庫作業支援非同步刪除的工作。 工作可以標示為刪除，系統會工作，其工作歷程記錄後刪除所有作業執行已都完成的工作。 系統不會自動取消使用中工作的執行。  

若要取消作用中的作業執行[**停駐點 AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx)來叫用。

若要觸發工作刪除時，使用[**移除 AzureSqlJob cmdlet**](https://msdn.microsoft.com/library/mt346083.aspx)並設定**工作名稱**參數。

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>若要建立自訂的資料庫目標

您可以定義自訂的資料庫直接執行或包含自訂資料庫群組內的目標。 例如，因為**彈性的資料庫集區**不尚未直接支援使用 PowerShell Api，您可以建立的自訂資料庫目標和自訂資料庫集合目標包含集區中的所有資料庫。

設定下列變數以反映所要的資料庫資訊︰

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>若要建立自訂的資料庫集合目標

您可以使用 [[**新增 AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) cmdlet 來定義自訂的資料庫在集合目標啟用執行跨多個已定義之的資料庫的目標。 建立資料庫群組之後, 資料庫可自訂集合目標與相關聯。

設定以反映所需的自訂集合目標設定下列變數︰

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>若要新增至自訂資料庫集合目標的資料庫

若要新增至特定的自訂集合資料庫，請使用[**新增 AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx) cmdlet。

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>檢閱資料庫中的自訂資料庫集合目標

您可以使用 [[**取得 AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) cmdlet 來擷取自訂資料庫集合目標內的子資料庫]。 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>建立自訂的資料庫集合目標上執行指令碼工作

您可以使用 [[**新增 AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) cmdlet 來建立工作對資料庫所定義的自訂資料庫集合目標的群組。 彈性的資料庫作業會展開將每個對應至資料庫相關聯的自訂資料庫集合目標的多個子工作的工作，並確保執行指令碼時，會針對每個資料庫。 同樣地，請務必指令碼有冪靈活重試。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>在資料庫的資料收集

您可以使用 [工作群組資料庫中執行查詢，並將結果傳送給特定的表格。 表格可以查詢後，查看每個資料庫查詢的結果。 這提供非同步的方法在許多資料庫執行查詢。 自動處理失敗的嘗試透過重試。

如果尚不存在，就會自動建立指定的目的資料表。 新的資料表比對結果集中的結構描述。 如果指令碼會傳回多個結果集，彈性的資料庫作業只會傳送第一個目的資料表。

下列 PowerShell 指令碼執行指令碼，並會其結果收集到指定的資料表。 這個指令碼假設已經建立的輸出單一結果集的 T SQL 指令碼，並已經建立的自訂資料庫集合目標。

此指令碼使用[**取得 AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx)指令程式。 設定指令碼、 認證，並執行目標的參數︰

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>建立並開始資料集合案例的工作

這個指令碼所用的[**開始 AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx)指令程式。
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>若要安排工作執行觸發程序

可以使用下列 PowerShell 指令碼，以建立週期性排程。 此指令碼使用分鐘的時間間隔，但[**新增 AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx)也支援-DayInterval 與-HourInterval、-MonthInterval，-WeekInterval 參數。 您可以建立一次執行的排程，傳遞-次。

建立新的排程︰

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>若要觸發在時間排程上執行的工作

可以定義工作觸發程序，有時間的排程執行作業。 可以使用下列 PowerShell 指令碼，建立工作的觸發程序。

使用[新增 AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) ，並設定下列變數對應到您要的工作和排程︰

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>若要移除的排程的關聯，若要停止執行排程工作

若要停止重複發生的作業執行透過工作觸發程序，您可以移除工作觸發程序。 移除工作觸發程序，若要停止使用[**移除 AzureSqlJobTrigger cmdlet**](https://msdn.microsoft.com/library/mt346070.aspx)的排程執行工作。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>擷取工作時間排程繫結的觸發程序

下列 PowerShell 指令碼可用來取得並顯示為特定時間排程註冊工作觸發程序。

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>若要擷取工作引動程序繫結至工作 

[取得 AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx)可用於取得並顯示包含已註冊的工作排程。

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>若要在資料庫中建立資料層應用程式 (DACPAC) 執行

若要建立 DACPAC，請參閱[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)。 若要部署 DACPAC，使用 [[新增 AzureSqlJobContent 指令程式](https://msdn.microsoft.com/library/mt346085.aspx)。 DACPAC 必須存取服務。 建議您將建立的 DACPAC 上傳至 Azure 儲存體和 DACPAC 版建立[共用的 Access 簽章](../storage/storage-dotnet-shared-access-signature-part-1.md)。

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>若要在資料庫間更新執行的資料層應用程式 (DACPAC)

註冊彈性的資料庫工作中的現有 DACPACs 都可以更新為指向新 Uri。 若要更新現有的註冊 DACPAC 上 DACPAC URI 使用[**設定 AzureSqlJobContentDefinition 指令程式**](https://msdn.microsoft.com/library/mt346074.aspx)︰

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>若要建立要在資料庫中套用的資料層應用程式 (DACPAC) 的工作

彈性的資料庫作業內建立 DACPAC 之後，您就可以建立工作套用 DACPAC 群組中的資料庫。 下列 PowerShell 指令碼可用於整個資料庫的自訂集合建立 DACPAC 工作︰

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
