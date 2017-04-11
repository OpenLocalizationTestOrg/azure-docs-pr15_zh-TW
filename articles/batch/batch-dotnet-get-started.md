<properties
    pageTitle="教學課程-快速入門 Azure 批次.NET 文件庫 |Microsoft Azure"
    description="瞭解 Azure 批次，以及如何開發範例案例批次服務的基本概念。"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="08/15/2016"
    ms.author="marsma"/>

# <a name="get-started-with-the-azure-batch-library-for-net"></a>快速入門.net 的 Azure 批次文件庫

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

瞭解基本概念[Azure]批次[azure_batch]和[批次.NET] [net_api]本文中的文件庫我們討論 C# 範例應用程式逐步解說。 我們會看看如何這個範例應用程式會使用批次服務處理工作平行負載在雲端，以及如何與互動的[Azure 儲存體](../storage/storage-introduction.md)檔案臨時與擷取。 您將學習一般批次應用程式工作流程技巧。 您也會基底瞭解批次，例如工作、 工作、 集區的主要元件，並且計算節點。

![批次解決方案工作流程 (basic)][11]<br/>

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉 C# 和 Visual Studio。 也會假設您無法滿足下列指定 Azure 和批次，儲存空間的服務帳戶建立需求。

### <a name="accounts"></a>帳戶

- **Azure 帳戶**︰ 如果您還沒有[建立的免費 Azure 帳戶]Azure 訂閱[azure_free_account]。
- **批次帳戶**︰ 當您有 Azure 訂閱，[建立 Azure 批次帳戶](batch-account-create-portal.md)。
- **儲存帳戶**︰ 請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。

> [AZURE.IMPORTANT] 批次目前支援*只***一般用途**儲存的帳戶類型，在步驟 5 中所述[的相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。

### <a name="visual-studio"></a>Visual Studio

您必須建立範例專案的**Visual Studio 2015** 。 您可以尋找免費與試用版的 Visual Studio [Visual Studio 2015 產品概觀][visual_studio]。

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial*程式碼範例

[DotNetTutorial] [github_dotnettutorial]範例是其中一個[azure-批次的範例]中的多個程式碼範例[github_samples]GitHub 上的儲存機制。 您可以下載的範例，即可**下載 ZIP** ] 按鈕，在存放庫首頁上，或按一下[azure 批次-範例 master.zip] [github_samples_zip]直接下載連結。 一旦您已擷取 ZIP 檔案的內容，您會看到下列資料夾中的方案︰

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Azure 批次檔案總管 （選用）

[Azure 批次檔案總管][github_batchexplorer]是[azure-批次的範例]中所包含的免費公用[github_samples]GitHub 上的儲存機制。 雖然非必要，完成本教學課程中，則可以很有用開發及偵錯批次方案時。

## <a name="dotnettutorial-sample-project-overview"></a>DotNetTutorial 範例專案概觀

*DotNetTutorial*程式碼範例是 Visual Studio 2015 解決方案的兩個專案所組成︰ **DotNetTutorial**和**TaskApplication**。

- **DotNetTutorial**是用戶端應用程式上執行平行的工作量批次，儲存空間的服務與互動的運算節點 （虛擬機器）。 在本機工作站上執行，DotNetTutorial。

- **TaskApplication**是執行執行的實際工時 Azure 中的運算節點的程式。 在範例中，`TaskApplication.exe`剖析從 Azure 儲存體 （輸入檔案） 下載的檔案中的文字。 然後，它會包含出現在輸入的檔案中的前三個單字的清單的文字檔案 （輸出檔案）。 建立輸出檔案之後，TaskApplication 上傳的檔案至 Azure 儲存體。 這可供下載的用戶端應用程式使用。 TaskApplication 上執行，平行批次服務中的多個運算節點。

下圖顯示執行的用戶端應用程式與*DotNetTutorial*，所執行的工作， *TaskApplication*應用程式的主索引作業。 這項基本的工作流程是一般的許多會以批次來建立的計算解決方案。 時，不會示範每個批次服務中使用的功能，幾乎批次案例包含類似的程序。

![工作流程範例批次][8]<br/>

[**步驟 1。**](#step-1-create-storage-containers) 建立**容器**中 Azure Blob 儲存體。<br/>
[**步驟 2。**](#step-2-upload-task-application-and-data-files) 將任務應用程式檔案與輸入的檔案上傳到容器。<br/>
[**步驟 3。**](#step-3-create-batch-pool) 建立批次**資料庫**。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a。** 集區**StartTask**節點下載任務二進位檔案 (TaskApplication)，他們加入集區。<br/>
[**步驟 4。**](#step-4-create-batch-job) 建立批次**工作**。<br/>
[**步驟 5。**](#step-5-add-tasks-to-job) 將**任務**新增至工作。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a。** 若要在節點上執行任務。<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b。** 每個任務從 Azure 儲存空間，下載輸入的資料，然後開始執行。<br/>
[**步驟 6。**](#step-6-monitor-tasks) 監視工作。<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a。** 工作完成後，其上傳其輸出資料至 Azure 儲存體。<br/>
[**步驟 7。**](#step-7-download-task-output) 下載儲存工作成果。

如上所述，不是每個批次解決方案會執行這些確切的步驟，並可能包括有更多，但*DotNetTutorial*範例應用程式示範批次解決方案中找到的一般程序。

## <a name="build-the-dotnettutorial-sample-project"></a>建立*DotNetTutorial*範例專案

您可以順利執行範例之前，您必須指定批次和儲存的帳戶認證*DotNetTutorial*專案中`Program.cs`檔案。 如果您尚未執行此動作方案 Visual Studio 中按兩下開啟`DotNetTutorial.sln`方案檔案。 開啟 Visual Studio 使用或**檔案 > 開啟 > 專案/方案**功能表。

開啟`Program.cs` *DotNetTutorial*專案中。 依指定檔案的上方，然後新增您的認證︰

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] 如上所述，您必須目前 Azure 儲存體中所指定**一般用途**儲存帳戶認證。 批次應用程式使用**一般用途**儲存帳戶內 blob 儲存體。 未指定的認證儲存帳戶所建立的選取*Blob 儲存體*帳戶類型。

您可以在[Azure 入口網站]尋找您批次，儲存空間的帳戶認證中的每個服務帳戶刀[azure_portal]:

![批次入口網站中的認證][9]
![入口網站中的儲存空間認證][10]<br/>

現在您已經更新專案使用您的認證，方案總管] 中的以滑鼠右鍵按一下並按一下 [**建立的解決方案**。 如果系統提示您，請確認還原的任何 NuGet 套件]。

> [AZURE.TIP] 如果未自動還原 NuGet 套件，或發生失敗的還原套件的錯誤，請確定您有[NuGet 封裝管理員][nuget_packagemgr]安裝。 然後啟用遺失套件的下載。 請參閱[啟用套件還原建置期間][nuget_restore]啟用套件下載。

在下列區段中，我們劃分成處理工作負載批次服務中，執行步驟範例應用程式，並討論的詳細資料，這些步驟。 我們建議您工作透過其餘的本文中，因為討論不每一行樣本中的程式碼時，請參閱 Visual Studio 中開啟的方案。

瀏覽至頂端`MainAsync` *DotNetTutorial*專案的方法`Program.cs`檔案，以開始步驟 1。 每個步驟下然後大致追蹤方法中的呼叫進度`MainAsync`。

## <a name="step-1-create-storage-containers"></a>步驟 1︰ 建立存放容器

![Azure 儲存體中建立容器][1]
<br/>

批次包含內建支援互動 Azure 儲存體。 在您儲存帳戶的容器會提供批次帳戶中執行的工作所需的檔案。 容器也會提供儲存工作產生的輸出資料的位置。 *DotNetTutorial*用戶端應用程式的第一個項目是[Azure Blob 儲存體](../storage/storage-introduction.md)中建立三個容器︰

- **應用程式**︰ 此容器會儲存應用程式執行工作，以及任何相依性，例如 Dll。
- **輸入**︰ 工作會下載至安裝程序*輸入*容器的資料檔案。
- **成果**︰ 輸入的檔案處理工作完成，他們會將結果上載至*輸出*容器。

讓我們互動存放區帳戶建立容器，才能使用[Azure 儲存用戶端文件庫的.NET][net_api_storage]。 我們建立帳戶的參照與[CloudStorageAccount][net_cloudstorageaccount]，並從建立[CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

我們使用`blobClient`參照整個應用程式，並將它做為參數傳遞有數種方法。 例如，位於立即何處撥遵循上述的程式碼區塊`CreateContainerIfNotExistAsync`實際建立容器。

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

只要容器建立之後，應用程式現在可以上傳的工作會使用檔案。

> [AZURE.TIP] [如何使用從.NET Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)提供使用 Azure 儲存體容器和二進位大型物件的概觀。 當您以批次開始使用應該會讀取清單頂端附近。

## <a name="step-2-upload-task-application-and-data-files"></a>步驟 2︰ 將任務應用程式和資料檔案上傳

![上傳至容器的任務應用程式及輸入 （資料） 檔案][2]
<br/>

在檔案上傳作業， *DotNetTutorial*先定義**應用程式**，並**輸入**檔案路徑的集合存在於本機電腦上。 然後將其上傳這些檔案的容器的上一個步驟所建立。

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

有兩種方法在`Program.cs`所涉及的上傳程序︰

- `UploadFilesToContainerAsync`︰ 這個方法會傳回一系列[ResourceFile] [ net_resourcefile] （如下所示） 的物件和內部通話`UploadFileToContainerAsync`上傳*filePaths*參數傳遞給每個檔案。
- `UploadFileToContainerAsync`︰ 這是實際執行檔案上傳並建立[ResourceFile]的方法[net_resourcefile]物件。 之後上傳檔案，它會取得共用的 access 簽名 (SA) 檔案，並傳回代表該 ResourceFile 物件。 共用的 access 簽章也會如下所示。

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles

[ResourceFile] [net_resourcefile]提供批次中之前執行工作時，會下載至運算節點的 Azure 儲存體檔案 url 中的工作。 [ResourceFile.BlobSource] [net_resourcefile_blobsource]屬性存在於 Azure 儲存體中，指定檔案的完整 URL。 URL 也可能會包含共用的 access 簽章 (SA)，提供安全存取檔案。 大部分的工作類型批次.NET 內包含*ResourceFiles*屬性時，包括︰

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

DotNetTutorial 範例應用程式未使用 JobPreparationTask 或 JobReleaseTask 任務類型，但您可以閱讀更多關於中[執行作業準備及完成工作 Azure 批次運算節點](batch-job-prep-release.md)。

### <a name="shared-access-signature-sas"></a>共用的 access 簽章 (SA)

共用的 access 簽章字串的 — 時包含 URL 的一部分，提供容器和 Azure 儲存體中的二進位大型物件的安全存取。 應用程式使用 blob 和共用的容器 DotNetTutorial 存取簽章 Url，並示範如何取得這些共用的 access 簽章字串從儲存服務。

- **Blob 共用 access 簽章**︰ 集區 StartTask DotNetTutorial 中的會從儲存下載的應用程式的二進位檔案及輸入的資料檔案時，使用 blob 共用 access 簽章 （請參閱以下的步驟 #3）。 `UploadFileToContainerAsync`中 DotNetTutorial 的方法`Program.cs`包含取得每個 blob 共用的 access 簽章的程式碼。 它是藉由撥[CloudBlob.GetSharedAccessSignature][net_sas_blob]。

- **容器共用 access 簽章**︰ 為每個任務的完成運算節點的工作，將其上傳的輸出檔案*輸出*中容器 Azure 儲存體。 若要這麼做，TaskApplication 會使用容器共用的 access 簽名時，會提供寫入存取權的容器路徑的一部分上, 傳的檔案。 取得容器共用的 access 簽章是以類似的方式，以取得 blob 共用 access 簽章。 在 DotNetTutorial，您會發現的`GetContainerSasUrl`協助程式方法通話[CloudBlobContainer.GetSharedAccessSignature] [net_sas_container]執行此作業。 您可以閱讀深入瞭解 TaskApplication 如何使用容器共用 access 中的簽章 」 步驟 6︰ 監控工作。 」

> [AZURE.TIP] 核取 [共用的 access 簽章，兩個部分系列[第 1 部分︰ 了解共用的 access 簽章 (SA) 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)和[第 2 部分︰ 建立及使用共用的 access 簽章 (SA) 與 Blob 儲存體](../storage/storage-dotnet-shared-access-signature-part-2.md)，若要進一步瞭解提供安全存取您儲存的帳戶中的資料。

## <a name="step-3-create-batch-pool"></a>步驟 3︰ 建立批次資料庫

![建立批次資料庫][3]
<br/>

批次**集區**是在其批次執行工作的工作的運算節點 （虛擬機器） 的集合。

它上傳至儲存帳戶的應用程式和資料檔案之後， *DotNetTutorial*與批次服務會使用啟動批次.NET 文件庫。 若要這麼做，請[BatchClient] [net_batchclient]第一次建立︰

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

接下來，請在通話批次帳戶中建立運算節點的集區`CreatePoolAsync`。 `CreatePoolAsync`使用[BatchClient.PoolOperations.CreatePool] [net_pool_create]實際批次服務中建立資料庫的方法。

```csharp
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

當您建立資料庫[CreatePool][net_pool_create]，指定多個參數，例如運算節點、[節點的大小](../cloud-services/cloud-services-sizes-specs.md)，以及節點的作業系統。 在 [ *DotNetTutorial*，我們使用[CloudServiceConfiguration] [net_cloudserviceconfiguration]指定[雲端服務](../cloud-services/cloud-services-guestos-update-matrix.md)的 Windows Server 2012 R2。 不過，藉由指定[VirtualMachineConfiguration] [net_virtualmachineconfiguration]相反地，您可以建立集區建立服務商場圖像中的節點包含 Windows 和 Linux 圖像，如需詳細資訊請參閱[佈建 Linux 運算 Azure 批次集區中的節點](batch-linux-nodes.md)。

> [AZURE.IMPORTANT] 您會計算資源批次。 您可以最小化成本，來降低`targetDedicated`1 之前執行範例。

這些實體節點的屬性，以及您也可以指定[StartTask] [net_pool_starttask]集區。 StartTask 執行每個節點的節點加入集區，以及每次重新啟動節點。 StartTask 是運算節點之前執行的工作上安裝應用程式的特別有用。 例如，如果您的工作使用 Python 指令碼處理資料，您可以使用 StartTask 運算節點上安裝 Python。

在此範例應用程式，StartTask 複製它會從儲存下載的檔案 (這指定使用[StartTask][net_starttask]。[ResourceFiles] [net_starttask_resourcefiles]屬性) 從 StartTask 工作目錄存取*所有*的節點上執行的工作，可以共用目錄。 將複製基本上， `TaskApplication.exe` ，而且每個節點上共用的目錄相依性節點加入集區，使的節點上執行任何工作時可以存取。

> [AZURE.TIP] Azure 批次的**應用程式套件**功能提供使運算節點到應用程式集區中的另一種方法。 如需詳細資訊，請參閱[Azure 批次應用程式套件的應用程式部署](batch-application-packages.md)。

上述的程式碼片段顯著也是使用兩種環境中的變數 StartTask 的*命令列*] 屬性︰`%AZ_BATCH_TASK_WORKING_DIR%`和`%AZ_BATCH_NODE_SHARED_DIR%`。 批次集區中的每個運算節點會自動設定的特定批次的幾個環境變數。 任何處理程序所執行的工作有權存取這些環境變數。

> [AZURE.TIP] 瞭解更多有關環境變數的運算節點批次資料庫及中工作的工作目錄的詳細資訊，還有請參閱[適用於開發人員批次功能概觀](batch-api-basics.md)中的 [[工作環境設定](batch-api-basics.md#environment-settings-for-tasks)及[檔案及目錄](batch-api-basics.md#files-and-directories)] 區段。

## <a name="step-4-create-batch-job"></a>步驟 4︰ 建立批次工作

![建立批次工作][4]<br/>

批次**工作**的工作，集合，而相關聯的運算節點集區。 在 [相關聯的集區運算節點上，執行中工作的工作。

您可以使用兩端的組織及追蹤工作的相關的工作量，也可以限制特定的限制式，例如 [最大的執行階段工作 （以及由副檔名，其工作），以及工作優先順序相對於批次帳戶中其他工作的工作。 在此範例中，不過，工作是只與步驟 3 中所建立的集區相關聯。 不設定任何其他屬性。

批次的所有工作都會與特定的資料庫相關聯。 這個關聯指示工作的工作會在執行的節點。 您可以使用[CloudJob.PoolInformation]指定此[net_job_poolinfo]屬性時，程式碼片段之以下所示。

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

現在，已建立工作，工作會新增至執行工作。

## <a name="step-5-add-tasks-to-job"></a>步驟 5︰ 將任務新增至工作

![將任務新增至工作][5]<br/>
*（1） 的工作會新增至工作、 （2) 任務的排程節點上執行及 （3） 工作下載處理的資料檔案*

批次**工作**是在運算節點執行工作的個別單位。 任務具有命令列，並執行指令碼或您指定的命令列中的可執行檔。

實際執行的工作，都必須工作加入工作。 每個[CloudTask] [net_task]已使用的命令列的屬性和[ResourceFiles] [ net_task_resourcefiles] （如同使用集區 StartTask） 的節點之前自動執行的命令列下載的工作。 在*DotNetTutorial*範例專案中，每項工作程序只能有一個檔案。 因此，其 ResourceFiles 集合包含單一項目。

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] 當他們存取環境變數例如`%AZ_BATCH_NODE_SHARED_DIR%`或執行應用程式中的節點找不到`PATH`，必須與前置任務命令列`cmd /c`。 明確地將執行命令解譯，並指示終止後執行您的命令。 這項需求不需要，如果您的工作執行應用程式中的節點`PATH`（例如*robocopy.exe*或*powershell.exe*） 並沒有環境變數用。

內`foreach`上述的程式碼片段循環播放，您可以看到建構工作的命令列時，例如以*TaskApplication.exe*傳遞三個命令列引數︰

1. **第一個引數**是要處理之檔案的路徑。 為存在於節點，這是本機檔案路徑。 ResourceFile 中的物件時`UploadFileToContainerAsync`第一次建立上方的檔案名稱的這個屬性 （做為參數使用 ResourceFile 建構函式）。 這表示檔案可以集中*TaskApplication.exe*相同的目錄。

2. **第二個引數**指定上方*N*文字應該才能寫入輸出檔案。 在範例中，這是硬式編碼，好讓輸出檔案會寫入頂端的三個字。

3. **第三個引數**是提供寫入存取權，**輸出**容器 Azure 儲存體中共用的 access 簽章 (SA)。 *TaskApplication.exe*會在其上傳至 Azure 儲存體的輸出檔案時，使用此共用的 access 簽章的 URL。 您可以在此找到程式碼`UploadFileToContainer`TaskApplication 專案的方法`Program.cs`檔案︰

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>步驟 6︰ 監控工作

![監視工作][6]<br/>
*用戶端應用程式 (1) 監視工作的完成和成功的狀態，並 （2） 工作將結果資料上傳至 Azure 儲存體*

當工作會新增至工作時，他們會自動佇列與排程與工作相關聯的資料庫中的計算節點上執行。 根據您指定的設定，批次所有任務佇列、 排程、 重試，與其他任務管理責任會為您處理。 有許多種監控執行工作。 DotNetTutorial 顯示報表僅在完成和工作失敗或成功狀態的簡單範例。

內`MonitorTasks`中 DotNetTutorial 的方法`Program.cs`，有三個批次.NET 概念保證討論。 清單中出現的順序︰

1. **ODATADetailLevel**︰ 指定[ODATADetailLevel] [net_odatadetaillevel]在清單中作業 （例如取得工作的工作清單） 是不可或缺確保批次應用程式的效能。 新增[查詢 Azure 批次服務有效率地](batch-efficient-list-queries.md)讀取清單如果您打算執行任何類型的監控批次應用程式中的狀態。

2. **TaskStateMonitor**: [TaskStateMonitor] [net_taskstatemonitor]提供批次.NET 應用程式和協助工具程式監控任務狀態。 在 [ `MonitorTasks`， *DotNetTutorial*等待所有工作達到[TaskState.Completed] [net_taskstate]時間限制。 然後終止工作。

3. **TerminateJobAsync**︰ 終止工作[JobOperations.TerminateJobAsync] [ net_joboperations_terminatejob] （或封鎖 JobOperations.TerminateJob） 標記為已完成的工作。 一定要這麼做，如果您批次的解決方案使用[JobReleaseTask][net_jobreltask]。 這是任務的一種特殊類型，此述[準備及完成的工作](batch-job-prep-release.md)。

`MonitorTasks`的*DotNetTutorial*的方法`Program.cs`下方會出現︰

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a scheduling error
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the
        // Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.SchedulingError != null)
        {
            // A scheduling error indicates a problem starting the task on the node.
            // It is important to note that the task's state can be "Completed," yet
            // still have encountered a scheduling error.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a scheduling error: {1}",
                task.Id,
                task.ExecutionInformation.SchedulingError.Message);
        }
        else if (task.ExecutionInformation.ExitCode != 0)
        {
            // A non-zero exit code may indicate that the application executed by
            // the task encountered an error during execution. As not every
            // application returns non-zero on failure by default (e.g. robocopy),
            // your implementation of error checking may differ from this example.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>步驟 7︰ 下載工作輸出

![從儲存下載工作成果][7]<br/>

現在，完成工作時，可以從 Azure 儲存體下載工作的輸出。 這是呼叫`DownloadBlobsFromContainerAsync`中*DotNetTutorial*的`Program.cs`:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] 通話`DownloadBlobsFromContainerAsync` *DotNetTutorial*在應用程式指定檔案應該會下載至您`%TEMP%`資料夾。 可以自由修改此輸出位置。

## <a name="step-8-delete-containers"></a>步驟 8︰ 刪除容器

因為您會位於 Azure 儲存體中的資料，則一律建議您先移除不再需要您批次工作的任何二進位大型物件。 在 [DotNetTutorial 的`Program.cs`，這是以協助方法的三個呼叫`DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

方法本身只會取得容器的參照，接著再呼叫 [ [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>步驟 9︰ 刪除的工作及資料庫

在最後一個步驟中，若要刪除的工作及 DotNetTutorial 應用程式所建立的資料庫會提示使用者。 雖然您不會工作和工作，您**要為運算節點。 因此，建議您只會依需要配置節點。 刪除未使用的資料庫可以進行的維修作業程序的一部分。

BatchClient [JobOperations] [net_joboperations]和[PoolOperations] [net_pooloperations]兩者都有相對應的刪除方法，稱為如果使用者向您確認刪除動作︰

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] 請記住，您會計算資源的 — 刪除未使用的資料庫會最小化的成本。 此外，請注意，刪除資料庫刪除該資料庫，內的所有運算節點節點上的任何資料集區刪除之後將無法復原。

## <a name="run-the-dotnettutorial-sample"></a>執行*DotNetTutorial*範例

當您執行的範例應用程式時，則會類似下列主控台輸出。 在執行時，就會發生在暫停`Awaiting task completion, timeout in 00:30:00...`時所啟動的資料庫的運算節點。 使用[Azure 入口網站][azure_portal]監控您的資料庫，計算節點、 工作和工作期間和後執行。 使用[Azure 入口網站][azure_portal]或[Azure 存放裝置總管][storage_explorers]若要檢視應用程式所建立的儲存空間資源 （容器和二進位大型物件）。

一般的執行時間是**約 5 分鐘**，當您執行應用程式的預設設定。

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>後續步驟

可以自由變更*DotNetTutorial*和*TaskApplication* ，若要嘗試使用不同的計算案例。 例如，請嘗試加入執行延遲*TaskApplication*，例如使用[Thread.Sleep][net_thread_sleep]，以模擬長時間執行工作，並查看其在入口網站。 請嘗試新增更多的工作或調整運算節點的數字。 新增邏輯檢查，並允許使用速度的執行時間現有資料庫 (*提示*︰ 取出`ArticleHelpers.cs`中[Microsoft.Azure.Batch.Samples.Common] [ github_samples_common] [azure-批次的範例]中的專案[github_samples])。

現在，您已經熟悉批次解決方案的基本工作流程，就深入批次服務的其他功能。

- 請閱讀[適用於開發人員批次功能概觀](batch-api-basics.md)，我們建議所有新批次使用者。
- 在**您採取進階的開發**[批次學習路徑]的其他批次開發文章上開始[batch_learning_path]。
- 請參閱使用批次中[TopNWords]處理 「 前 n 個字 「 工作負載的不同實作[github_topnwords]範例。

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Azure 儲存體中建立容器"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "上傳至容器的任務應用程式及輸入 （資料） 檔案"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "建立批次資料庫"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "建立批次工作"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "將任務新增至工作"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "監視工作"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "從儲存下載工作成果"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "批次解決方案工作流程 （完整的圖表）"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "批次認證，在入口網站"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "在入口網站中的儲存空間認證"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "批次解決方案工作流程 （最小圖表）"
