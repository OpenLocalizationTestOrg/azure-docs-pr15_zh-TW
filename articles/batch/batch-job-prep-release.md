<properties
    pageTitle="準備及批次清理工作 |Microsoft Azure"
    description="使用工作層級準備工作，最小化至 Azure 批次的資料傳輸運算節點，然後放開節點清理工作完成的工作]。"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="09/16/2016"
    ms.author="marsma" />

# <a name="run-job-preparation-and-completion-tasks-on-azure-batch-compute-nodes"></a>Azure 批次運算節點上執行作業準備及完成工作

 Azure 批次工作通常需要某些格式設定的任務會執行，並完成其工作後工作進行的維修作業之前。 您可能需要下載至您的運算節點的常見工作輸入的資料，或在工作完成後，將任務輸出資料上傳至 Azure 儲存體。 若要執行這些作業，您可以使用**準備工作**，然後**放開工作**的工作。

## <a name="what-are-job-preparation-and-release-tasks"></a>什麼是準備工作，然後放開工作嗎？

執行工作的工作之前，則會在排定執行至少有一個任務的所有運算節點上執行作業準備工作。 一旦完成工作，則會在執行至少有一個任務的集區中每個節點上執行工作釋出工作。 如同一般批次任務，您可以指定要執行作業準備或發行工作時叫用的命令列。

準備和發行的工作提供熟悉批次工作功能，例如檔案下載 ([資源檔案][net_job_prep_resourcefiles])，較高的執行、 自訂環境變數、 最大執行工期、 重試計數及檔案保留時間。

在以下各節中，您將學習如何使用[JobPreparationTask] [net_job_prep]和[JobReleaseTask] [net_job_release]類別集中[批次.NET] [api_net]文件庫。

> [AZURE.TIP] 準備及版本的工作是 「 共用資料庫 」 環境中，在其中運算節點的集區之間工作執行保存及許多工作會使用特別有用。

## <a name="when-to-use-job-preparation-and-release-tasks"></a>何時使用工作準備，然後放開工作

準備工作和工作發行任務是適合用下列情況︰

**下載一般工作的資料**

批次工作通常需要一組通用的資料，做為輸入工作的工作。 例如，在分析計算每日風險，市場資料為特定工作的還一般工作中的所有任務。 此市場資料時，通常有幾個 gb 的大小，應該一次下載至每個運算節點讓節點執行任何工作可以使用它。 若要下載此資料的每個節點，才能執行作業的其他工作使用**工作準備工作**。

**刪除工作的輸出**

在 [共用資料庫 」 環境中，位置集區運算節點不是退役工作之間，您可能需要刪除之間執行工作資料。 您可能需要以節省磁碟空間節點，或符合貴組織的安全性原則。 若要刪除下載作業準備工作，或執行工作期間所產生的資料使用**工作釋出工作**。

**記錄保留**

您可能會想要保留您的任務產生的記錄檔的複本，或可能損毀可能失敗的應用程式所產生的傾印檔案。 在這種情況下使用**工作釋出工作**壓縮並將此資料上傳到[Azure 儲存體][azure_storage]帳戶。

>[AZURE.TIP] 若要保存記錄其他工作和工作的另一種方法的輸出資料是使用[Azure 批次檔案慣例](batch-task-output.md)文件庫。

## <a name="job-preparation-task"></a>作業準備工作

之前執行工作的工作，批次，請在 [執行的工作排程的每個運算節點上執行作業準備工作。 根據預設，批次服務等待工作準備工作，先完成，才能執行排程節點上執行的工作。 不過，您可以設定不所要等待的服務。 如果重新啟動節點，請再次執行工作準備工作，但您也可以停用這種行為。

僅在排程要執行工作的節點執行作業準備工作。 這可防止不必要的準備工作執行，以防節點未指派的任務。 這可能發生的工作的工作數小於集區中的節點數目。 也適用於[同時工作執行](batch-parallel-node-tasks.md)啟用時，而某些節點閒置如果任務計數小於總可能同時工作。 藉由不閒置節點上執行作業準備工作，您可以花較，在資料傳輸費用。

> [AZURE.NOTE] [JobPreparationTask] [net_job_prep_cloudjob]不同於[CloudPool.StartTask] [pool_starttask]的 JobPreparationTask 開頭的每個工作，而 StartTask 執行運算節點時才會執行第一次加入集區或重新啟動。

## <a name="job-release-task"></a>工作釋出工作

一旦工作標記為已完成，執行至少有一個任務的集區中每個節點上執行作業釋出工作。 您將工作標示為已完成的發行終止要求。 批次服務然後將工作狀態設定為*終止*、 結束任何作用中或執行工作與工作，並執行工作釋出工作。 工作然後移至*已完成*的狀態。

> [AZURE.NOTE] 刪除作業也會執行工作釋出工作。 不過，如果已經結束工作，釋出工作是執行一次如果稍後刪除作業。

## <a name="job-prep-and-release-tasks-with-batch-net"></a>工作準備，然後放開批次.NET 工作

若要使用作業準備工作，指派[JobPreparationTask] [net_job_prep]物件至您的工作[CloudJob.JobPreparationTask] [net_job_prep_cloudjob]屬性。 同樣地，初始化[JobReleaseTask] [net_job_release]並將其指派給您的工作[CloudJob.JobReleaseTask] [net_job_prep_cloudjob]屬性設定工作的釋出工作。

在此程式碼片段， `myBatchClient` [BatchClient]的執行個體[net_batch_client]，及`myPool`是批次帳戶內現有的集區。

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobPreparationTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

如先前所述，當終止或刪除工作時，會執行釋出工作。 終止工作[JobOperations.TerminateJobAsync][net_job_terminate]。 刪除工作[JobOperations.DeleteJobAsync][net_job_delete]。 您通常會終止或刪除工作，當完成其工作，或已達到逾時，您所定義。

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsy("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>在 GitHub 的程式碼範例

若要查看準備工作，然後放開動作中的工作，請查看[JobPrepRelease] [job_prep_release_sample]上 GitHub 範例專案。 此主控台應用程式執行下列動作︰

1. 建立兩個 「 小 」 的節點的集區。
2. 建立工作準備、 版本與標準工作的工作。
3. 執行工作的準備工作，第一次撰寫文字目錄中的檔案節點的 「 共用 」 的節點 ID。
4. 將其任務識別碼寫入相同的文字檔案至每個節點上執行的工作。
5. 一旦完成所有任務 （或已達到逾時），列印到主控台的每個節點的文字檔案的內容。
6. 工作完成時，會執行刪除檔案從節點工作釋出工作。
6. 列印每個節點他們所執行的工作準備及發行工作的結束代碼。
7. 若要允許的工作及/或集區刪除確認暫停執行。

範例應用程式的輸出非常類似下列動作︰

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 small nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

>[AZURE.NOTE] 新的資料庫 （某些節點已可供其他人之前的工作） 中的節點的變數建立並開始時間，因為您可能會看到不同的輸出。 具體來說，快速完成工作，因為其中一個資料庫的節點可能會執行所有工作的工作。 如果這種情況，您會注意到工作準備及不執行任何工作的節點不存在發行工作。

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>檢查工作準備和 Azure 入口網站中的版本工作

當您執行的範例應用程式時，您可以使用[Azure 入口網站][portal]檢視的工作，其工作內容，或甚至下載工作的工作修改共用的文字檔案。

下面的螢幕擷取畫面會顯示在範例應用程式的執行 Azure 入口網站中的**準備工作刀**。 瀏覽至您的工作完成後*JobPrepReleaseSampleJob*屬性 （但刪除您的工作與集區之前），然後按一下 [**準備工作**或**發行工作**檢視其內容。

![Azure 入口網站中的工作準備屬性][1]

## <a name="next-steps"></a>後續步驟

### <a name="application-packages"></a>應用程式套件

除了工作準備工作，您也可以使用[應用程式套件](batch-application-packages.md)的功能批次運算節點準備執行工作。 此功能的部署不需要執行安裝程式、 應用程式包含多個 （100 +） 檔案或需要嚴格版本控制的應用程式的應用程式尤其有用。

### <a name="installing-applications-and-staging-data"></a>安裝應用程式，並執行資料

此 MSDN 論壇文章提供以下概觀準備您的節點執行的工作有數種方法︰

[安裝應用程式，並執行資料批次運算節點][forum_post]

同時撰寫 Azure 批次的小組成員之一，討論幾項技巧，您可以用來部署應用程式與資料，來計算節點。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
