<properties
    pageTitle="任務相依性類型 Azure 批次 |Microsoft Azure"
    description="建立取決於其他工作，以處理 MapReduce 樣式和類似的大型資料成功完成的工作負載 Azure 批次。"
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
    ms.date="09/28/2016"
    ms.author="marsma" />

# <a name="task-dependencies-in-azure-batch"></a>Azure 批次中的任務相依性

Azure 批次的任務相依性功能是適合，如果您想要處理程序︰

- 在雲端的 MapReduce 樣式負載。
- 可以為導向非循環圖 (DAG) 表示其資料處理工作的工作。
- 任何其他工作的下游工作，取決於之上游工作的成果。

批次任務相依性，讓您建立一或多個其他工作的成功完成後，只計算節點上執行的排程的任務。 例如，您可以建立呈現的 3D 影片個別的平行工作的每個畫面的工作。 最終任務-「 合併工作 」，會將呈現的圖文框完成影片只有在成功呈現所有框架後。

您可以建立一對一或一對多關聯中的其他工作的工作。 您甚至可以建立範圍相依性工作取決於成功完成的一組任務的任務識別碼特定範圍內的位置。 您可以結合下列三個基本案例，建立多對多關聯。

## <a name="task-dependencies-with-batch-net"></a>以批次.NET 的任務相依性

本文中，我們將討論如何使用[批次.NET]設定任務相依性[net_msdn]文件庫。 我們先告訴您如何在您的工作上的 [[啟用任務相依性](#enable-task-dependencies)，然後將示範如何[設定任務相依性](#create-dependent-tasks)。 最後，我們討論批次支援[相依性案例](#dependency-scenarios)。

## <a name="enable-task-dependencies"></a>啟用任務相依性

若要使用批次應用程式中的任務相依性，您必須先告訴批次服務工作使用任務相依性。 批次.NET 中啟用它在您[CloudJob] [net_cloudjob]設定其[UsesTaskDependencies] [net_usestaskdependencies]屬性設定為`true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

在前面的程式碼片段，「 batchClient 」 是[BatchClient]的執行個體[net_batchclient]類別。

## <a name="create-dependent-tasks"></a>建立相依的任務

若要建立工作成功完成的一或多個其他任務而定，您告訴批次的工作 「 取決於 「 其他工作。 批次.net 設定[CloudTask][net_cloudtask]。[DependsOn][net_dependson] [TaskDependencies]的執行個體的屬性[net_taskdependencies]類別︰

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

此程式碼片段建立的識別碼的 「 花朵 「 排程識別碼 」 雨量 」 和 「 日 」 的工作已經完成後，只計算節點上執行的工作。

 > [AZURE.NOTE] 任務會被視為它是在**已完成**的狀態，然後**結束程式碼**其是完成`0`。 批次.NET，這表示[CloudTask][net_cloudtask]。[狀態][net_taskstate]的屬性值`Completed`和 CloudTask [TaskExecutionInformation][net_taskexecutioninformation]。[ExitCode][net_exitcode]屬性值是`0`。

## <a name="dependency-scenarios"></a>相依性案例

有三種基本的任務相依性情況，您可以使用 Azure 批次中︰ 一對一，一對多和任務識別碼範圍相依性。 可以結合下列提供的第四個案例中，多對多。

 案例&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 範例 | |
 :-------------------: | ------------------- | -------------------
 [一對一](#one-to-one) | *taskB*取決於*taskA* <p/> *taskB*會未執行的排程，直到*taskA*已順利完成 | ![圖表︰ 一對一的任務相依性][1]
 [若要從一對多](#one-to-many) | *taskC*取決於*taskA*和*taskB* <p/> *taskC*會未執行的排程，直到*taskA*和*taskB*都已順利完成 | ![圖表︰ 一對多任務相依性][2]
 [任務識別碼範圍](#task-id-range) | *taskD*取決於範圍的工作 <p/> *taskD*會未執行的排程，直到識別碼*1*到*10*的工作已順利完成 | ![圖表︰ 任務識別碼範圍相依性][3]

>[AZURE.TIP] 您可以建立**多對多**關聯，例如位置 C、 D、 E 和 F 每個的工作，取決於任務 A 和 b。這是非常實用，例如平行化前置處理下游工作取決於的多個上游工作輸出的位置。

### <a name="one-to-one"></a>一對一

若要建立具有相依性一個其他任務的完成的工作，您會提供單一任務識別碼來[TaskDependencies][net_taskdependencies]。[OnId][net_onid]靜態方法，當您填入[DependsOn] [net_dependson]屬性[CloudTask][net_cloudtask]。

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>若要從一對多

若要建立具有相依性的多個工作完成的工作，您提供的任務識別碼[TaskDependencies]集合[net_taskdependencies]。[OnIds][net_onids]靜態方法，當您填入[DependsOn] [net_dependson]屬性[CloudTask][net_cloudtask]。

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>任務識別碼範圍

若要建立具有相依性順利完成的工作群組其識別碼必須落範圍的工作，提供第一個和最後一個工作[TaskDependencies]範圍中的 [識別碼[net_taskdependencies]。[OnIdRange][net_onidrange]靜態方法，當您填入[DependsOn] [net_dependson]屬性[CloudTask][net_cloudtask]。

>[AZURE.IMPORTANT] 當您使用的相依性的任務識別碼範圍時，*必須*範圍中的任務識別碼是整數值的字串表示。 此外，範圍中的每項工作必須順利完成，可以排程為執行相依的任務。

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="code-sample"></a>程式碼範例

[TaskDependencies] [github_taskdependencies]範例的專案是其中一個[Azure 批次的程式碼範例][ github_samples] GitHub 上。 這個 Visual Studio 2015 方案會示範如何啟用工作上的任務相依性、 建立任務相依於其他工作，並執行這些工作上的 [運算節點集區。

## <a name="next-steps"></a>後續步驟

### <a name="application-deployment"></a>應用程式部署

批次的[應用程式套件](batch-application-packages.md)」 功能讓您輕鬆兩者部署的應用程式，您在上執行計算節點的版本。

### <a name="installing-applications-and-staging-data"></a>安裝應用程式，並執行資料

請查看[安裝應用程式和批次的暫存資料計算節點][forum_post]的各種方法來準備您的節點，若要執行的工作概觀 Azure 批次論壇中張貼的文章。 此文章撰寫 Azure 批次的小組成員之一，到您的運算節點是很好入門不同的方式 （包括應用程式與工作輸入的資料） 的檔案。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "圖表︰ 一對一相依性"
[2]: ./media/batch-task-dependency/02_one_to_many.png "圖表︰ 一對多相依性"
[3]: ./media/batch-task-dependency/03_task_id_range.png "圖表︰ 任務識別碼範圍相依性"
