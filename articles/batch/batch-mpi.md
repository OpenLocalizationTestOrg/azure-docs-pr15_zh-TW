<properties
    pageTitle="Azure 批次執行多個執行個體工作 MPI 應用程式 |Microsoft Azure"
    description="了解如何執行 Azure 批次中使用多個執行個體任務類型的郵件傳遞介面 (MPI) 應用程式。"
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
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-azure-batch"></a>Azure 批次執行郵件傳遞介面 (MPI) 應用程式中使用多個執行個體的工作

多個執行個體的工作可讓您同時在多個運算節點上執行 Azure 批次工作。 這些工作啟用高效能運算像批次中的郵件傳遞介面 (MPI) 應用程式的案例。 您可以在本文中，瞭解如何執行多個執行個體工作使用[批次.NET] [api_net]文件庫。

>[AZURE.NOTE] 雖然這份文件中的範例著重於批次.NET MS-MPI Windows 運算節點，以下討論的多個執行個體工作概念是用於其他平台和技術 （Python 和 Intel MPI Linux 節點，例如上）。

## <a name="multi-instance-task-overview"></a>多個執行個體工作概觀

批次，每項工作通常是在單一運算節點-執行提交給工作，多個任務及批次服務排程節點上執行每項工作。 不過，藉由設定工作的**多個執行個體設定**，您會告訴批次，請改為建立一個的主要任務與任務上多個節點，然後執行。

![多個執行個體工作概觀][1]

當您使用多個執行個體設定工作提交任務時，批次就會執行幾個步驟唯一多個執行個體工作︰

1. 批次服務會建立一個**主要**和**子任務**根據多個執行個體的設定。 任務 （主要再加上所有的子任務） 總數量比對**執行個體**（運算節點） 您在多個執行個體設定中指定。
1. 批次將運算節點的其中一個指定為**母片**，並排程母片上執行的主要工作。 它會排程執行運算節點配置給多個執行個體工作，每個節點的一個子任務的剩餘子任務。
1. 主要及所有子任務下載您在多個執行個體設定中指定的任何**一般資源檔案**。
1. 一般資源檔案已下載，主要和子任務執行您在多個執行個體設定中指定的**下] 命令**。 [下] 命令，通常是準備節點執行工作。 這可包括啟動背景服務 (例如[Microsoft MPI][msmpi_msdn]的`smpd.exe`) 和驗證節點都已準備好要處理節點間的郵件。
1. 主要工作會主節點*之後*協調命令已經順利完成的主要及所有子任務上執行**應用程式命令**。 應用程式命令本身，多個執行個體工作的命令列，而只執行主要的工作。 在 [ [MS MPI][msmpi_msdn]-基礎解決方案，這是您執行您 MPI 啟用應用程式使用`mpiexec.exe`。

> [AZURE.NOTE] 雖然功能不同，則 「 多個執行個體工作 「 不在唯一的任務類型，例如[StartTask] [net_starttask]或[JobPreparationTask][net_jobprep]。 多個執行個體任務是只要標準批次任務 ([CloudTask] [net_task]批次.net) 已設定的多個執行個體的值。 本文中，我們會將此為**多個執行個體的工作**。

## <a name="requirements-for-multi-instance-tasks"></a>多個執行個體工作的需求

多個執行個體工作要求與**啟用節點間通訊**，以及**停用一個任務執行**與集區。 如果您想要執行集區中的多個執行個體的工作，internode 通訊停用或*maxTasksPerNode*值大於 1，永遠不會排程任務，它會保留永遠在 「 作用中的 「 狀態。 此程式碼片段顯示使用批次.NET 文件庫的這類資料庫的建立。

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicated: 3
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

此外，多個執行個體可以*只*在上執行**建立 2015 年 12 月 14 日之後的集區**中的節點。

### <a name="use-a-starttask-to-install-mpi"></a>使用 StartTask 安裝 MPI

若要執行 MPI 應用程式與多個執行個體工作，您必須安裝 MPI 實作 （MS MPI 或 Intel MPI，例如） 上運算節點集區中。 這是良好的時間，使用[StartTask][net_starttask]，其中執行時節點加入集區，或重新啟動。 此程式碼片段建立為[資源檔案]指定 MS MPI 安裝套件 StartTask[net_resourcefile]。 開始工作的命令列執行之後資源檔案下載至節點。 在此情況下，命令列執行 MS MPI 自動的的安裝。

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    RunElevated = true,
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>遠端直接記憶體存取 (RDMA)

當您選擇運算節點的[RDMA 簡訊的大小](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)，例如 A9 您批次集區中時，您 MPI 應用程式可以利用的網路 Azure 的高效能、 低延遲遠端直接記憶體存取 (RDMA)。

查看下列文章中指定為 「 RDMA 簡訊 」 大小︰

* **CloudServiceConfiguration**集區

  * [雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)(僅限 Windows)

* **VirtualMachineConfiguration**集區

  * [在 Azure 虛擬機器的大小](../virtual-machines/virtual-machines-linux-sizes.md)(Linux)

  * [在 Azure 虛擬機器的大小](../virtual-machines/virtual-machines-windows-sizes.md)(Windows)

>[AZURE.NOTE] 若要利用 RDMA 上[Linux 運算節點](batch-linux-nodes.md)，您必須使用**Intel MPI**節點。 如需 CloudServiceConfiguration 和 VirtualMachineConfiguration 資料庫的詳細資訊，請參閱[資料庫](batch-api-basics.md#Pool)一批次功能概觀。

## <a name="create-a-multi-instance-task-with-batch-net"></a>以批次.NET 建立多個執行個體的工作

現在，我們已涵蓋的資料庫需求及 MPI 套件的安裝，讓我們來建立多個執行個體工作。 在此程式碼片段，我們建立標準的[CloudTask][net_task]，然後設定其[MultiInstanceSettings] [net_multiinstance_prop]屬性。 如先前所述，多個執行個體工作不在不同的任務類型，但標準批次工作設定多個執行個體項目。

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>主要任務和子任務

當您建立任務的多個執行個體設定時，您可以指定所執行的工作運算節點的數。 當您送出工作的工作時，批次服務會建立一個的**主要**任務及其一起符合您指定的節點數目足夠**子任務**。

這些工作指派給*numberOfInstances* 1 0 的範圍中的整數識別碼。 任務識別碼為 0 是主要的工作，且所有其他 id 子任務。 例如，如果您建立下列的多個執行個體設定任務時，主要工作有識別碼為 0，和子任務有識別碼 1 至 9。

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>主版節點

當您送出工作的多個執行個體時，批次及的服務將運算節點的其中一個指定為 「 主要 」 的節點，排程主節點上執行的主要工作。 子任務排程執行多個執行個體工作分派的節點的其他部分。

## <a name="coordination-command"></a>協調] 命令

**下] 命令**執行兩個主要的和子任務。

封鎖的 [下] 命令引動-批次不會執行應用程式命令，直到 [下] 命令已成功傳回的所有的子任務。 [下] 命令應該因此啟動任何必要的背景服務、 驗證其可供使用，，然後結束。 例如，此協調] 命令的方案，使用 MS MPI 版 7 啟動 SMPD 服務的節點上，然後結束︰

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

請注意使用`start`在這個協調命令。 這是因為`smpd.exe`應用程式不會執行之後立即傳回。 不使用的[開始][cmd_start]命令，這個協調命令就不會傳回，並會因此被封鎖執行應用程式命令。

## <a name="application-command"></a>應用程式] 命令

主要工作及所有子任務已完成執行 [下] 命令，以 [主要工作*只*執行多個執行個體任務的命令列。 我們稱為 「 此**應用程式命令**，以區別 [下] 命令。

MS MPI 應用程式，使用應用程式命令來執行 MPI 啟用應用程式與`mpiexec.exe`。 例如，以下是使用 MS MPI 版本 7 解決方案的應用程式命令︰

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

>[AZURE.NOTE] 因為 MS MPI 的`mpiexec.exe`使用`CCP_NODES`變數預設 （請參閱[環境變數](#environment-variables)） 範例應用程式命令列上方排除它。

## <a name="environment-variables"></a>環境變數

批次建立多個[環境變數][msdn_env_var]特定運算節點配置給多個執行個體任務上的多個執行個體工作。 您協調和應用程式的命令列參考這些環境變數，如指令碼和他們所執行的程式。

使用批次服務會建立下列環境變數多個執行個體工作︰

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

完整這些及其他批次計算節點環境變數的詳細資訊，包括其內容和可見性，請參閱[計算節點環境變數][msdn_env_var]。

>[AZURE.TIP] 批次 Linux MPI 程式碼範例包含如何使用數個這些環境變數的範例。 [協調 cmd] [coord_cmd_example]艦隊指令碼下載常見的應用程式，並輸入，將檔案從 Azure 儲存體、 啟用 [母片] 節點的網路檔案系統 (NFS) 共用，並設定其他配置給多個執行個體工作 NFS 用戶端的節點。

## <a name="resource-files"></a>資源檔案

有兩組時應考量的多個執行個體任務的資源檔案︰ 下載*所有*任務的**一般資源檔案**(兩個主要和子任務)，及**資源檔案**指定多個執行個體工作本身的*只有主要*工作下載。

在多個執行個體設定任務中，您可以指定一個或多個**常見的資源檔案**。 [Azure 儲存體](./../storage/storage-introduction.md)下載這些常見的資源檔案至主要的每個節點的**任務共用的目錄**及所有子任務。 您也可以使用從應用程式] 及 [下] 命令行存取工作共用的目錄`AZ_BATCH_TASK_SHARED_DIR`環境變數。 `AZ_BATCH_TASK_SHARED_DIR`路徑是相同的配置給多個執行個體任務每個節點，因此您可以共用單一協調命令之間的主要及所有子任務。 批次不會 「 共用 」 其實遠端存取的目錄，但您可以將其作為連接或共用點上環境變數的秘訣稍早所述。

您指定的多個執行個體工作本身的資源檔案下載到該任務的工作目錄， `AZ_BATCH_TASK_WORKING_DIR`，依預設。 如上所述，相較於一般資源檔案，主要的工作會下載指定多個執行個體工作本身的資源檔案。

> [AZURE.IMPORTANT] 使用環境變數`AZ_BATCH_TASK_SHARED_DIR`和`AZ_BATCH_TASK_WORKING_DIR`參考這些在您的命令列中的目錄。 請勿嘗試手動建構路徑。

## <a name="task-lifetime"></a>任務為單位的週期

主要工作的存留時間控制整個多個執行個體工作的存留時間。 主要當結束時，所有的子任務會終止。 結束程式碼的主要工作，結束代碼，而因此用來決定的成功或失敗重試進行的工作。

如果任何子任務失敗，使用非零傳回程式碼，結束，例如整個多個執行個體的工作失敗。 多個執行個體工作然後終止，而重試，其重試限制。

當您刪除工作的多個執行個體時，主要及所有子任務會一併刪除批次服務。 所有子任務的目錄，其檔案從刪除運算節點，如同在標準的工作。

[TaskConstraints] [net_taskconstraints]針對多個執行個體工作，例如[MaxTaskRetryCount][net_taskconstraint_maxretry]， [MaxWallClockTime][net_taskconstraint_maxwallclock]，及[RetentionTime] [net_taskconstraint_retention]屬性]，在是標準的工作，並套用至主要及所有子任務，會生效。 不過，如果您要變更[RetentionTime] [net_taskconstraint_retention]新增多個執行個體工作的工作，這項變更後的屬性只會套用至主要的工作。 所有的子任務繼續使用原始[RetentionTime][net_taskconstraint_retention]。

如果最近工作的時間部份的多個執行個體任務，則運算節點的最近使用的工作清單會反映出子任務的識別碼。

## <a name="obtain-information-about-subtasks"></a>取得子任務的相關資訊

若要取得使用批次.NET 文件庫的子任務的詳細資訊，請連絡 [ [CloudTask.ListSubtasks] [net_task_listsubtasks]方法。 這個方法會傳回所有的子任務的詳細資訊，以及運算節點執行工作的相關資訊。 您可以從這項資訊，來判斷每一個子任務的根目錄、 集區識別碼，目前狀態、 結束代碼等。 您可以使用這項資訊搭配[PoolOperations.GetNodeFile] [poolops_getnodefile]方法，以取得子任務的檔案。 請注意，這種方式不會傳回的主要的工作 (id 0) 的資訊。

> [AZURE.NOTE] 否則所述，除非批次.NET 方法的操作上多個執行個體[CloudTask] [net_task]本身*只*套用至主要的工作。 例如，當您呼叫[CloudTask.ListNodeFiles] [net_task_listnodefiles]在多個執行個體任務上的方法，傳回只有主要工作的檔案。

下列程式碼片段示範如何取得子任務的詳細資訊，以及從他們所執行的節點要求檔案內容。

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == TaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>程式碼範例

[MultiInstanceTasks] [ github_mpi] GitHub 上的程式碼範例示範如何使用多個執行個體任務執行[MS MPI] [msmpi_msdn]批次的應用程式運算節點。 請遵循[準備](#preparation)及[執行](#execution)，若要執行此範例中的步驟進行。

### <a name="preparation"></a>準備

1. 遵循[如何編譯並執行簡單的 MS MPI 程式]中的前兩個步驟[msmpi_howto]。 此滿足下列步驟 prerequesites。
1. 建立[MPIHelloWorld]的*發行*版本[helloworld_proj]範例 MPI 程式。 這是將多個執行個體任務的 [在運算節點執行的程式。
1. 建立 zip 檔案包含`MPIHelloWorld.exe`（即您建置步驟 2） 和`MSMpiSetup.exe`(您下載的步驟 1)。 您會將此 zip 檔案上傳為應用程式套件中下一個步驟。
1. 使用[Azure 入口網站][portal]建立批次[應用程式](batch-application-packages.md)稱為 「 MPIHelloWorld 」，並指定 zip 檔案您在上一個步驟為 「 1.0 版 」 的應用程式套件。 如需詳細資訊，請參閱[上傳和管理應用程式](batch-application-packages.md#upload-and-manage-applications)。

>[AZURE.TIP] 建立的*發行*版本`MPIHelloWorld.exe`，讓您不必包含任何其他的相依性 (例如，`msvcp140d.dll`或`vcruntime140d.dll`) 在您的應用程式套件。

### <a name="execution"></a>執行

1. 下載[azure-批次-範例][github_samples_zip]從 GitHub。
1. 在 Visual Studio 2015 中開啟 MultiInstanceTasks**解決方案**。 `MultiInstanceTasks.sln`解決方案檔案位於︰

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`

1. 輸入您的批次，儲存空間帳戶認證中`AccountSettings.settings` **Microsoft.Azure.Batch.Samples.Common**專案中。
1. **建立並執行**執行 MPI MultiInstanceTasks 解決方案範例應用程式上會計算批次資料庫中的節點。
1. *可省略*︰ 使用[Azure 入口網站][portal]或[批次檔案總管][batch_explorer]若要檢查的範例資料庫、 工作及之前的工作 （「 MultiInstanceSamplePool 」、 「 MultiInstanceSampleJob 」、 「 MultiInstanceSampleTask 」） 刪除資源。

>[AZURE.TIP] 您可以下載[Visual Studio 社群][visual_studio]免費如果您沒有 Visual Studio。

從輸出`MultiInstanceTasks.exe`類似下列動作︰

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>後續步驟

- Microsoft HPC 與 Azure 批次小組部落格討論[MPI 支援 Azure 批次 Linux][blog_mpi_linux]，並包含有關使用[OpenFOAM] [openfoam]批次。 您可以找到 Python 程式碼範例， [GitHub OpenFOAM 範例][github_mpi]。

- 瞭解如何使用 Azure 批次 MPI 方案中的 [[建立資料庫的 Linux 運算節點](batch-linux-nodes.md)。

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: http://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: http://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "多個執行個體概觀"
