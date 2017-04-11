<properties
    pageTitle="彈性的資料庫工作快速入門"
    description="如何使用彈性的資料庫作業"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>彈性的資料庫工作快速入門

Azure SQL 資料庫的彈性的資料庫作業 （預覽版本） 可讓您可靠性執行橫跨多個資料庫時自動重試，並提供最終完成保證 T SQL 指令碼。 如需有關彈性的資料庫作業功能的詳細資訊，請參閱[功能概觀] 頁面](sql-database-elastic-jobs-overview.md)。

本主題將延伸[快速入門彈性的資料庫工具](sql-database-elastic-scale-get-started.md)中找到的範例。 完成時，您將會︰ 了解如何建立及管理工作的管理群組的相關的資料庫。 您不需要使用彈性的小數位數的工具，好充分利用彈性工作的優點。

## <a name="prerequisites"></a>必要條件

下載並執行的[快速入門彈性的資料庫工具範例](sql-database-elastic-scale-get-started.md)。

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>建立晶怪地圖管理員使用範例應用程式

以下您將建立晶怪地圖以及幾個擊碎，後面接著資料插入到擊碎的管理員。 如果您已經有擊碎 sharded 中的資料進行設定，您可以略過下列步驟，並移至下一節。

1. 建立並執行**快速入門彈性的資料庫工具**範例應用程式。 遵循的步驟，直到步驟 7 中 [[下載並執行範例應用程式](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)] 區段。 步驟 7 結尾，您會看到下列命令提示字元︰

    ![命令提示字元][1]

2.  在 [命令] 視窗中，輸入 「 1 」，然後按**Enter**。 這會建立晶怪地圖管理員，，並將兩個擊碎新增至伺服器。 然後輸入"3"，然後按**enter 鍵**。重複這個動作四個時間。 這會在您擊碎插入範例資料列。

3.  [Azure 入口網站](https://portal.azure.com)應該會顯示在 v12 伺服器中的三個新的資料庫︰

    ![Visual Studio 確認][2]

    此時，我們將會建立自訂的資料庫集合，以反映晶怪對應中的所有資料庫。 這可讓我們來建立和執行工作的跨擊碎新增新的資料表。

以下我們想通常建立晶怪地圖目標，使用 [**新增 AzureSqlJobTarget**指令程式。 必須為資料庫目標設定晶怪地圖 manager 資料庫，然後特定晶怪地圖已指定為目標。 不過，我們列舉伺服器中的所有資料庫，並將資料庫新增至新的自訂集合，但主要資料庫。

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>建立自訂的集合，並會將所有資料庫伺服器中自訂集合目標但母片。


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>在資料庫中建立執行 T SQL 指令碼

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>建立資料庫的 [自訂] 群組上執行指令碼工作

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>執行作業 

下列 PowerShell 指令碼可用於執行現有的工作︰

更新以反映所要的工作名稱來執行下列變數︰

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>擷取單一工作執行的狀態

若要檢視的狀態子作業執行，也就是針對每個資料庫工作之目標每個作業執行的特定狀態使用**IncludeChildren**參數相同**取得 AzureSqlJobExecution**指令程式。

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>跨多個作業執行中檢視狀態

**取得 AzureSqlJobExecution** cmdlet 有多個可用於顯示多個作業執行，透過提供的參數篩選的選擇性參數。 以下示範一些使用取得 AzureSqlJobExecution 可能的方式︰

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

## <a name="retrieve-failures-within-job-task-executions"></a>擷取內作業任務執行失敗

JobTaskExecution 物件會包含該任務的訊息屬性的生命週期的屬性。 如果工作執行工作失敗，將 [週期] 屬性設定為 [*失敗*，會產生的例外狀況訊息和其堆疊設定 [訊息] 屬性。 如果工作失敗，請務必檢視失敗指定工作的工作的詳細資料。

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>等待完成作業執行

下列 PowerShell 指令碼可用於工作的工作完成時，請等候︰

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>建立自訂的執行原則

彈性的資料庫作業支援建立可啟動工作時套用自訂的執行原則。
  
執行原則目前允許定義︰

* 執行原則名稱︰ 識別項。
* 工作逾時︰ 的總時間之前彈性的資料庫工作，就會取消工作。
* 第一個重試之前等待的初始重試間隔︰ 間隔。
* 若要使用 [最大重試間隔︰ 重試間隔的端點。
* 用來計算的下一個間隔重試之間的間隔輪詢係數再試一次︰ 係數。  使用下列公式: （初始再試一次的間隔） * Math.pow (（間隔輪詢係數） （次數）-2)。 
* 最大嘗試︰ 執行工作內嘗試重試數目上限。

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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

彈性的資料庫作業支援工作取消邀請。  如果彈性的資料庫作業偵測到目前正在執行工作的取消邀請時，它會停止工作。

有兩種不同方式彈性的資料庫作業可執行取消通知︰

1. 取消目前正在執行任務︰ 取消偵測到目前執行工作時，如果取消通知將會嘗試內目前正在執行工作的外觀。  例如︰ 目前正在執行時嘗試取消長時間執行查詢時，將會嘗試取消查詢。
2. 取消任務重試︰ 如果取消偵測來控制執行緒之前執行啟動任務，則控制執行緒會避免啟動工作與取消宣告要求。

如果上層工作要求工作取消時，就會生效取消邀請的父項工作，以及所有的子工作。
 
以提交取消要求，請使用**停駐點 AzureSqlJobExecution** cmdlet 設定**JobExecutionId**參數。

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>刪除工作的名稱及工作的歷程記錄

彈性的資料庫作業支援非同步刪除的工作。 工作可以標示為刪除，系統會工作，其工作歷程記錄後刪除所有作業執行已都完成的工作。 系統不會自動取消使用中工作的執行。  

不過，停止 AzureSqlJobExecution 必須取消作用中的作業執行叫用。

若要觸發工作刪除時，使用**移除 AzureSqlJob** cmdlet 並設定**工作名稱**參數。

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>建立自訂的資料庫目標
自訂資料庫目標可以在彈性的資料庫作業可用於執行直接或要包含自訂資料庫群組內的定義。 **彈性的資料庫集區**不尚未直接支援透過 PowerShell Api，因為您只要建立自訂的資料庫目標和自訂資料庫集合目標包含集區中的所有資料庫。

設定下列變數以反映所要的資料庫資訊︰

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>建立自訂的資料庫集合目標
自訂資料庫集合目標可以定義為啟用執行跨多個已定義之的資料庫的目標。 建立資料庫群組後，資料庫可自訂的集合目標相關聯。

設定以反映所需的自訂集合目標設定下列變數︰

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>將資料庫新增至自訂資料庫集合目標

資料庫目標可自訂的資料庫集合目標建立資料庫的群組與相關聯。 每次目標自訂資料庫集合目標建立工作時，它將會展開目標的執行時間群組相關聯的資料庫。

將想要的資料庫新增至特定的自訂集合︰

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>檢閱資料庫中的自訂資料庫集合目標

您可以使用 [**取得 AzureSqlJobTarget** cmdlet 來擷取自訂資料庫集合目標內的子資料庫]。 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>建立自訂的資料庫集合目標上執行指令碼工作

您可以使用 [**新增 AzureSqlJob** cmdlet 來建立工作對資料庫所定義的自訂資料庫集合目標的群組。 彈性的資料庫作業會展開將每個對應至資料庫相關聯的自訂資料庫集合目標的多個子工作的工作，並確保執行指令碼時，會針對每個資料庫。 同樣地，請務必指令碼有冪靈活重試。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>在資料庫的資料收集

**彈性的資料庫作業**支援群組資料庫中執行查詢，並將結果傳送至指定的資料庫資料表。 表格可以查詢後，查看每個資料庫查詢的結果。 這會提供非同步機制跨多個資料庫執行查詢。 透過重試自動處理錯誤情況下，例如一個資料庫的暫時無法使用。

如果它尚不存在，相符的結果集中的結構描述，就會自動建立指定的目的資料表。 如果執行指令碼會傳回多個結果集，彈性的資料庫作業只會傳送到提供的目的資料表的第一個。

下列 PowerShell 指令碼可用於執行指令碼收集到指定的資料表的結果。 這個指令碼假設已建立 T SQL 指令碼的輸出單一結果集，並且建立自訂的資料庫集合目標。

設定以反映所需的指令碼、 憑證和目標執行下列動作︰

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

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>建立並開始資料集合案例的工作
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>建立作業執行使用工作的觸發程序的排程

下列 PowerShell 指令碼可以用於建立重複發生的排程。 此指令碼使用一分鐘內，但新增 AzureSqlJobSchedule 也支援-DayInterval 與-HourInterval、-MonthInterval，-WeekInterval 參數。 您可以建立一次執行的排程，傳遞-次。

建立新的排程︰

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>建立工作觸發程序有時間排程上執行的工作

可以定義工作觸發程序，有時間的排程執行作業。 可以使用下列 PowerShell 指令碼，建立工作的觸發程序。

設定下列變數對應到您要的工作和排程︰

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>移除排程的關聯，若要停止執行排程工作

若要停止重複發生的作業執行透過工作觸發程序，您可以移除工作觸發程序。 移除工作觸發程序，若要停止使用**移除 AzureSqlJobTrigger**指令程式的排程執行工作。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>匯入至 Excel 的彈性的資料庫查詢結果

 您可以匯入的結果的查詢至 Excel 檔案。

1. 啟動 Excel 2013。
2.  瀏覽至 [**資料**] 功能區。
3.  按一下 [**從其他來源**，然後按一下 [**從 SQL Server]**。

    ![從其他來源 Excel 匯入][5]
4.  在**資料連線精靈**中，輸入伺服器名稱與登入認證。 然後按一下 [**下一步**。
5.  在 [**選取含有您要的資料的資料庫**] 對話方塊中選取的**ElasticDBQuery**資料庫。
6.  在 [清單] 檢視中選取 [**客戶**] 資料表，然後按一下 [**下一步**]。 然後按一下 [**完成**]。
7.  在**匯入資料]**表單中，**選取您想要檢視此活頁簿中的資料的方式**] 底下選取**資料表**，然後按一下**[確定]**。

從 [**客戶**] 資料表，儲存在不同的擊碎中的所有資料列中填入 Excel 工作表。

## <a name="next-steps"></a>後續步驟
您現在可以使用 Excel 的資料函數。 連線至彈性的查詢資料庫的 BI 和資料整合工具中使用與您的伺服器名稱、 資料庫名稱認證的連接字串。 請確定 SQL Server 支援作為資料來源的工具。 請參閱彈性的查詢資料庫以及外部的資料表，就像其他任何 SQL Server 資料庫以及您想要使用您的工具連線至 SQL Server 資料表。

### <a name="cost"></a>成本
有使用彈性的資料庫查詢功能不另外收費。 不過，這次這項功能只能在進階資料庫上以結束點，但擊碎可以是任何服務層級。

價格資訊請參閱[SQL 資料庫定價詳細資料](https://azure.microsoft.com/pricing/details/sql-database/)。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
