<properties
    pageTitle="工作輸出中 Azure 批次的持續性 |Microsoft Azure"
    description="瞭解如何使用長期商店批次工作和工作輸出，並啟用 Azure 入口網站中檢視此保存的輸出 Azure 儲存體。"
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
    ms.date="09/07/2016"
    ms.author="marsma" />

# <a name="persist-azure-batch-job-and-task-output"></a>固定 Azure 批次的工作和工作輸出

您通常在批次中執行的工作會產生輸出必須儲存，然後在工作中，用戶端應用程式的工作，或同時執行其他工作稍後擷取。 此輸出可能處理輸入的資料所建立的檔案或記錄檔與執行工作相關聯。 本文會介紹.NET 類別的文件庫使用慣例為基礎技術保存至 Azure Blob 儲存體，讓它刪除您的集區工作，並計算節點之後，甚至這類工作成果。

藉由使用本文中的技巧，您也可以檢視您的工作輸出中**已儲存輸出檔案**和[Azure 入口網站]中的**已儲存記錄檔**[portal]。

![已儲存輸出檔案和入口網站中的 [儲存記錄選取器][1]

>[AZURE.NOTE] [Azure 批次檔案慣例][nuget_package]預覽目前位於本文所述的.NET 類別庫。 部分此處所述的功能可能會變更之前一般的顯示狀態。

## <a name="task-output-considerations"></a>任務輸出考量

當您設計批次方案時，您必須考慮相關的工作和工作輸出的幾項因素。

* **計算節點存留時間**︰ 計算節點通常是暫時性，尤其是在已啟用自動調整大小的資料庫。 節點執行的工作的輸出在節點，且只在檔案保留時間已設定工作時，才可供使用。 若要確保工作輸出會保留，您的工作必須因此上傳其輸出檔案至長期存放區，例如 Azure 儲存體。

* **輸出儲存空間**︰ 若要保存工作輸出資料，以長期儲存空間，您可以使用[Azure 儲存體 SDK](../storage/storage-dotnet-how-to-use-blobs.md)工作程式碼中上傳工作輸出至 Blob 儲存體容器。 如果您執行的容器和檔案命名慣例，用戶端應用程式或工作中的其他工作可以然後找出並下載輸出根據慣例。

* **輸出擷取**︰ 您可以擷取工作輸出直接從您的資料庫中的計算節點或 Azure 儲存體如果您的工作保留其輸出。 若要直接從運算節點擷取工作的成果，您需要檔案名稱和節點輸出位置。 如果您仍然存在輸出至 Azure 儲存體，下游工作] 或 [用戶端應用程式必須完整路徑中 Azure 儲存體使用 Azure 儲存體 SDK 下載的檔案。

* **檢視輸出**︰ 當您以批次工作 Azure 入口網站中瀏覽並選取**節點上的檔案**時，您會看到與此任務相關的所有檔案，而不只是輸出檔案您感興趣。 同樣地，運算節點上的檔案可供使用，節點存在，而且只在檔案保留時間已設定工作時，才。 若要檢視您已保存 Azure 儲存體入口網站或應用程式，例如[Azure 儲存檔案總管]中的任務輸出[storage_explorer]，您必須知道的位置，並直接瀏覽至檔案。

## <a name="help-for-persisted-output"></a>持續性輸出的說明

批次小組具有可協助您更輕鬆地保存工作與輸出的任務，定義，並實作一組的命名慣例，以及.NET 類別文件庫， [Azure 批次檔案慣例][nuget_package]批次應用程式中，您可以使用的文件庫。 此外，Azure 入口網站並知道這些命名慣例，好讓您可以輕鬆地找到您已使用文件庫中儲存的檔案。

## <a name="using-the-file-conventions-library"></a>使用檔案慣例文件庫

[Azure 批次檔案慣例][nuget_package]是批次.NET 應用程式可用來輕鬆地儲存及擷取工作輸出 Azure 儲存體中往返.NET 類別文件庫。 它適用於工作與用戶端兩者中的程式碼，任務代碼保存檔案，和清單，並可擷取的用戶端程式碼中使用。 您的工作也可以使用文件庫來擷取輸出上游工作，例如，在[任務相依性](batch-task-dependencies.md)的情況下。

確保存放容器與任務輸出檔案命名慣例，根據和上傳到正確的地方保存至 Azure 儲存體時的處理慣例文件庫。 當您擷取輸出時，您可以輕鬆找到指定的工作或工作的輸出清單或擷取識別碼的目的，而不是由知道檔案名稱，或儲存空間中存在的輸出。

比方說，您可以使用 「 列出工作 7 中繼檔案 」 或 「 取得我縮圖預覽的工作*mymovie*，」 文件庫，並不需要知道檔案名稱或位置，在您儲存的帳戶。

### <a name="get-the-library"></a>取得文件庫

您可以取得文件庫，其包含新的類別，並將延伸[CloudJob] [net_cloudjob]和[CloudTask] [net_cloudtask]類別以新的方法，從[NuGet][nuget_package]。 您可以將其新增至 Visual Studio 專案使用[NuGet 文件庫封裝管理員][nuget_manager]。

>[AZURE.TIP] 您可以找到[原始程式碼][ github_file_conventions] GitHub Microsoft Azure SDK 的.NET 存放庫中上 Azure 批次檔案慣例文件庫。

## <a name="requirement-linked-storage-account"></a>要求︰ 連結的儲存空間帳戶

若要儲存的輸出，以便長期使用檔案慣例文件庫的儲存空間，並在 Azure 入口網站中檢視，您必須批次帳戶[連結 Azure 儲存體帳戶](batch-application-packages.md#link-a-storage-account)。 如果您尚未選取，使用 Azure 入口網站儲存客戶連結至您批次的帳戶︰

**批次帳戶**刀 >**設定** > **儲存帳戶** > **儲存帳戶**（無）] > 在您的訂閱中選取儲存的帳戶

在連結的儲存空間帳戶的詳細逐步解說，請參閱[Azure 批次應用程式套件的應用程式部署](batch-application-packages.md)]。

## <a name="persist-output"></a>固定輸出

有兩個主要的動作執行時與檔案慣例文件庫中儲存工作成果︰ 建立存放容器，並將輸出儲存至容器。

>[AZURE.WARNING] 所有的工作和工作輸出儲存在相同的容器，因為[儲存節流限制](../storage/storage-performance-checklist.md#blobs)可能會強制執行如果嘗試同時保存檔案大量的任務。

### <a name="create-storage-container"></a>建立存放容器

您的工作保存輸出儲存之前，您必須建立他們會要上傳其輸出 blob 儲存體容器。 請執行下列動作，則可電話[CloudJob][net_cloudjob]。[PrepareOutputStorageAsync][net_prepareoutputasync]. 這個副檔名方法採用[CloudStorageAccount] [net_cloudstorageaccount]物件做為參數，並建立名為其內容的可以找到這種方式 Azure 入口網站，本文稍後所述的擷取方法容器。

您通常在用戶端應用程式，建立您的資料庫、 工作和工作的應用程式中將此程式碼。

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>儲存工作輸出

既然您已準備好 blob 儲存體中的容器，任務可以儲存輸出至容器使用[TaskOutputStorage] [net_taskoutputstorage]檔案慣例文件庫中找到的類別。

在您任務的代碼，先建立[TaskOutputStorage] [net_taskoutputstorage]物件，然後當任務已完成其工作，呼叫[TaskOutputStorage][net_taskoutputstorage]。[SaveAsync][net_saveasync]方法，將其輸出儲存至 Azure 儲存體。

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

「 輸出類型 」 參數分類保存的檔案。 有四個預先定義的[TaskOutputKind] [net_taskoutputkind]類型: 「 TaskOutput 」、 「 TaskPreview 」、 「 TaskLog 」 及 「 TaskIntermediate 」。 如果他們是用於您的工作流程，您也可以定義自訂的類型。

這些輸出類型可讓您指定哪種類型的清單，當您稍後查詢持續指定工作的輸出批次的輸出。 也就是說，當您] 清單中的工作成果，您可以篩選上的其中一個輸出類型的清單。 例如，「 讓我*預覽*輸出的任務*109*。 」 其他清單，以及擷取輸出上會出現在[擷取輸出](#retrieve-output)在本文稍後。

>[AZURE.TIP] 輸出類型也可指定 Azure 入口網站中的特定檔案出現的位置︰ *TaskOutput*-分類的檔案會出現在 「 工作輸出檔案 」，並*TaskLog*檔案會出現在 「 工作記錄 」。

### <a name="store-job-outputs"></a>儲存工作輸出

除了儲存工作輸出，您可以儲存整個工作相關聯的輸出。 例如，在合併列印工作的影片轉譯工作中，您可能會保存完整呈現的影片為工作輸出。 您的工作完成時，您的用戶端應用程式可以直接清單，然後擷取的輸出工作，因此不需要個別任務的查詢。

儲存工作成果，則可電話[JobOutputStorage][net_joboutputstorage]。[SaveAsync][net_joboutputstorage_saveasync]方法，並指定[JobOutputKind] [net_joboutputkind]和檔案名稱︰

```
CloudJob job = await batchClient.JobOperations.GetJobAsync(jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

當您要使用的工作輸出 TaskOutputKind，使用[JobOutputKind] [net_joboutputkind]參數分類工作的保存檔案。 這個參數可讓您更新查詢的特定類型的輸出 （清單）。 JobOutputKind 包含輸出] 和 [預覽類型，以及支援建立自訂的類型。

### <a name="store-task-logs"></a>儲存工作記錄

除了保存長期儲存的檔案，任務或工作完成時，您可能會發現有必要保存在執行工作--記錄檔的更新的檔案或`stdout.txt`和`stderr.txt`，例如。 Azure 批次檔案慣例文件庫達到這個目的，提供[TaskOutputStorage][net_taskoutputstorage]。[SaveTrackedAsync][net_savetrackedasync]方法。 使用[SaveTrackedAsync][net_savetrackedasync]，您可以追蹤節點 （位於您指定的時間間隔） 檔案的更新，並保存 Azure 儲存體這些更新。

在下列程式碼片段中，我們使用[SaveTrackedAsync] [net_savetrackedasync]更新`stdout.txt`Azure 儲存體工作的執行期間每 15 秒中︰

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
    await Task.Delay(stdoutFlushDelay);
}
```

`Code to process data and produce output file(s)`是只針對您的工作會以正常方式執行的程式碼的版面配置區。 例如，您可能會從 Azure 儲存體下載資料，並在其上執行轉換或計算的程式碼。 此程式碼片段的重要部分會示範如何，您可以將在這種程式碼`using` [SaveTrackedAsync]定期更新檔案封鎖[net_savetrackedasync]。

`Task.Delay`結尾的這需要`using`區塊，以確保節點代理程式清除 stdout.txt 檔案的節點 （節點代理程式集區中每個節點上執行，並提供命令控制項之間的介面節點和批次服務） 上的內容的標準出的時間。 沒有此延遲，就可能錯過電話輸出的最後一個幾秒。 此延遲可能不需要的所有檔案。

>[AZURE.NOTE] 當您啟用追蹤] SaveTrackedAsync 的檔案時，只*會將*追蹤檔案會保存 Azure 儲存體。 使用這個方法僅適用於追蹤非旋轉記錄檔，或其他檔案附加至]，也就是的資料會只新增至檔案的結尾時就會更新。

## <a name="retrieve-output"></a>擷取輸出

當您擷取保存的輸出使用 Azure 批次檔案慣例文件庫時，您進行的工作和工作中心的方式。 您可以要求輸出，指定任務或工作，並不需要知道它 blob 儲存空間或甚至檔案名稱中的路徑。 您可以直接說 「 讓我的工作*109*輸出檔案。 」

下列程式碼片段逐一查看所有的工作的工作列印部分工作，輸出檔案的相關資訊，然後從儲存下載的檔案。

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (TaskOutputStorage output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="task-outputs-and-the-azure-portal"></a>工作輸出和 Azure 入口網站

Azure 入口網站會向您顯示任務輸出及保存的記錄連結的 Azure 儲存體帳戶使用[Azure 批次檔案慣例讀我檔案]中的命名慣例[github_file_conventions_readme]。 您可以實作這些慣例自己您所選擇的語言，或您可以在.NET 應用程式中使用檔案慣例文件庫。

### <a name="enable-portal-display"></a>啟用入口網站的顯示

若要啟用您輸出入口網站中的顯示方式，您必須符合下列需求︰

 1. 批次帳戶[連結 Azure 儲存體帳戶](#requirement-linked-storage-account)。
 2. 保存輸出時，請依循儲存容器和檔案的預先定義的命名慣例。 您可以找到這些慣例的定義檔案慣例文件庫[讀我檔案][github_file_conventions_readme]。 如果您使用[Azure 批次檔案慣例][nuget_package]滿足如下的文件庫，將您的輸出，這項需求。

### <a name="view-outputs-in-the-portal"></a>在入口網站檢視輸出

若要檢視工作輸出和記錄，Azure 入口網站中，瀏覽至任務其輸出您感興趣，然後按一下 [**儲存輸出檔案**] 或 [**儲存記錄檔**。 此圖像顯示**已儲存輸出檔案**任務識別碼 」 007 」:

![Azure 入口網站中的任務輸出刀][2]

## <a name="code-sample"></a>程式碼範例

[PersistOutputs] [github_persistoutputs]範例的專案是其中一個[Azure 批次的程式碼範例][ github_samples] GitHub 上。 此 Visual Studio 2015 解決方案示範如何使用 Azure 批次檔案慣例文件庫保存工作輸出長期儲存空間。 若要執行的範例，請遵循下列步驟︰

1. 在**Visual Studio 2015**中開啟的專案。
2. Microsoft.Azure.Batch.Samples.Common 專案中加入**AccountSettings.settings**批次，儲存空間**帳戶認證**。
3. **建立**（但不是會執行） 解決方案。 如果出現提示，請還原任何 NuGet 套件。
4. 若要將[應用程式套件](batch-application-packages.md)上傳的**PersistOutputsTask**使用 Azure 入口網站。 包含`PersistOutputsTask.exe`.zip 套件中，其相依組件設定 「 PersistOutputsTask 」，到應用程式識別碼] 和 [應用程式套件版本為 「 1.0 」。
5. **開始**（執行） **PersistOutputs**專案。

## <a name="next-steps"></a>後續步驟

### <a name="application-deployment"></a>應用程式部署

批次的[應用程式套件](batch-application-packages.md)」 功能讓您輕鬆兩者部署的應用程式，您在上執行計算節點的版本。

### <a name="installing-applications-and-staging-data"></a>安裝應用程式，並執行資料

請查看[安裝應用程式和批次的暫存資料計算節點][forum_post]張貼 Azure 批次論壇，如需執行的工作時，準備您的節點的各種方式的概觀。 此文章撰寫 Azure 批次的小組成員之一，到您的運算節點，並針對每一種方法的某些特殊考量是很好入門不同的方式 （包括應用程式與工作輸入的資料） 的檔案。

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "已儲存輸出檔案和入口網站中的 [儲存記錄選取器"
[2]: ./media/batch-task-output/task-output-02.png "Azure 入口網站中的任務輸出刀"