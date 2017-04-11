<properties
    pageTitle="Azure 批次的 visual Studio 範本 |Microsoft Azure"
    description="進一步瞭解這些 Visual Studio 專案範本如何協助您可實作與執行您運算密集負載 Azure 批次"
    services="batch"
    documentationCenter=".net"
    authors="fayora"
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

# <a name="visual-studio-project-templates-for-azure-batch"></a>Azure 批次的 visual Studio 專案範本

**工作管理員**和批次的**任務處理器 Visual Studio 範本**提供可協助您可實作與執行工作的最小批次您運算密集工作負載的程式碼。 這份文件說明這些範本，並提供如何使用這些指南。

>[AZURE.IMPORTANT] 本文討論資訊只有適用於下列兩個範本，並假設您已熟悉批次服務及相關的重要概念︰ 集區，計算節點、 工作和工作、 工作管理員工作、 環境變數，以及其他相關資訊。 您可以找到詳細的資訊中[的基本概念的 Azure 批次](batch-technical-overview.md)、[適用於開發人員批次功能概觀](batch-api-basics.md)」 和 「[開始使用.net Azure 批次文件庫](batch-dotnet-get-started.md)。

## <a name="high-level-overview"></a>整體概觀

[工作管理員] 和 [工作處理器範本可用於建立兩個很有用的元件︰

* 實作可以劃分工作成多個任務平行獨立，可以執行的工作分割工作管理員工作。

* 任務處理器，可以用來執行預先處理和後續處理周圍的應用程式命令列。

例如，在影片轉譯案例中，工作分割會變成單一影片工作數百或數千會分別處理個別的圖文框的個別工作。 因此，工作處理器想叫用轉譯應用程式，所有從屬參照的程序來呈現每個所需的外框，以及執行其他動作 （例如，將呈現的圖文框複製到儲存位置）。

>[AZURE.NOTE] [工作管理員] 和 [工作處理器範本無關彼此，因此您可以選擇要使用兩個或其中一個，根據您計算的工作，在您的喜好設定的需求。

下圖所示，使用這些範本的計算工作會移到三個階段︰

1. 用戶端程式碼 （例如，應用程式、 web 服務等） 送出到 Azure，指定做為其工作管理員工作的工作管理員程式的批次服務工作。

2. 批次服務運算節點上執行作業管理員工作，並工作分割會啟動上的任務處理器工作，指定的數字為許多計算節點，視需要根據參數和工作分割程式碼中的規格。

3. 任務處理器工作大小，同時執行，處理輸入的資料，並產生的輸出資料。

![圖表顯示用戶端程式碼批次服務的互動方式][diagram01]

## <a name="prerequisites"></a>必要條件

若要使用的批次範本，您將需要下列項目︰

* 在電腦與 Visual Studio 2015，或更新版本，已安裝。

* 批次範本中，可從[Visual Studio 庫][ vs_gallery] Visual Studio 副檔名為。 有兩種方式取得範本︰

  * 安裝使用 Visual Studio 中的 [ **Extensions 和更新**] 對話方塊的範本 (如需詳細資訊，請參閱[尋找及使用 Visual Studio 副檔名][vs_find_use_ext])。 在**Extensions 和更新**] 對話方塊中，搜尋並下載下列兩個擴充功能︰

    * 使用工作分割 azure 批次工作管理員
    * Azure 批次工作處理器

  * 從 [線上] 庫中下載範本，Visual studio: [Microsoft Azure 批次專案範本][vs_gallery_templates]

* 如果您打算使用[應用程式套件](batch-application-packages.md)功能部署工作管理員，以批次的任務處理器運算節點，必須先儲存客戶連結至您批次的帳戶。

## <a name="preparation"></a>準備

我們建議您建立您的工作管理員，以及您任務的處理器，最多可以包含的方案，因為這可以輕鬆地共用您的工作管理員和工作處理器程式之間的程式碼。 若要建立此方案，請遵循下列步驟︰

1. 開啟 Visual Studio 2015，然後選取**檔案** > **新增** > **專案**。

2. [**範本**] 下展開**其他專案類型**、 按一下**Visual Studio 方案**，然後選取**空白的解決方案**。

3. 輸入描述您的應用程式，以及此解決方案 (例如，「 LitwareBatchTaskPrograms 」) 的用途的名稱。

4. 若要建立新的方案，請按一下**[確定]**。

## <a name="job-manager-template"></a>工作管理員範本

[工作管理員] 範本能協助您實作工作管理員工作，可以執行下列動作︰

* 分割成多個任務的工作。
* 送出以批次執行這些工作。

>[AZURE.NOTE] 如需工作管理員工作的詳細資訊，請參閱[適用於開發人員批次功能概觀](batch-api-basics.md#job-manager-task)。

### <a name="create-a-job-manager-using-the-template"></a>建立工作管理員使用的範本

若要新增您先前建立的解決方案工作管理員，請遵循下列步驟︰

1. 在 Visual Studio 2015 中開啟現有的方案。

2. 在方案總管中，以滑鼠右鍵按一下方案，請按一下 [**新增** > **新的專案**。

3. 在**Visual C#**，按一下**雲端**，然後再按一下**與工作分割 Azure 批次工作管理員**。

4. 輸入描述您的應用程式，並會顯示工作管理員 (例如 「 LitwareJobManager 」) 為此專案的名稱。

5. 若要建立專案，請按一下**[確定]**。

6. 最後，建立強制載入所有參照的 NuGet 套件，並確認的專案是有效的開始進行修改之前的 Visual Studio 專案。

### <a name="job-manager-template-files-and-their-purpose"></a>工作管理員範本檔案和其用途

當您建立專案使用工作管理員範本時，則會產生程式碼檔案的三個的群組︰

* 主要程式檔案 (Program.cs)。 此頁面包含的程式進入點及最上層的例外狀況處理。 您通常不需要修改此。

* 架構目錄]。 此頁面包含負責完成工作管理員程式 – 解壓縮參數，將任務新增至批次工作等的 「 使用 」 工時的檔案。您通常不需要修改這些檔案。

* 工作分隔檔案 (JobSplitter.cs)。 這是您將會放在您的應用程式特定邏輯分割任務的工作。

當然，您可以視需要以支援您工作分割的程式碼，根據複雜的工作分割邏輯新增額外的檔案。

範本也會產生標準的.NET 專案檔案，例如.csproj 檔案、 app.config、 packages.config 等等。

本節的其他說明的不同的檔案，以及它們的程式碼的結構，，並說明每個類別的功能。

![Visual Studio 方案總管] 中顯示 [工作管理員範本解決方案][solution_explorer01]

**架構檔案**

* `Configuration.cs`︰ 封裝工作設定資料，例如批次帳戶詳細資料、 連結的儲存空間帳戶認證、 工作和工作資訊及工作參數的載入。 它也會提供存取批次定義環境變數 （請參閱環境設定為批次文件中的任務，） 透過 Configuration.EnvironmentVariable 類別。

* `IConfiguration.cs`︰ 擷取類別實作的設定，以便讓您可以使用偽造或模擬設定物件您工作分割單位測試。

* `JobManager.cs`︰ 協調工作管理員程式的元件。 它會負責初始化工作分隔器叫用工作分隔器和分派任務要是傳回工作分割的工作。

* `JobManagerException.cs`︰ 代表需要工作管理員結束的錯誤。 JobManagerException 用來自動換行 '預期 」 錯誤位置提供特定的診斷資訊，做為終止的一部分。

* `TaskSubmitter.cs`︰ 若要新增工作分割返回批次工作的工作此課程負責。 JobManager 類別彙總的工作的順序成批次的有效但及時元件工作，然後呼叫 TaskSubmitter.SubmitTasks 在背景執行緒每個批次。

**工作分隔器**

`JobSplitter.cs`︰ 這個類別包含特定應用程式的邏輯分割任務的工作。 架構叫用 JobSplitter.Split 方法，以取得系列為方法會傳回其新增至工作的工作。 這是工作的您會在此插入您的邏輯的類別。 實作分割方法傳回一系列 CloudTask 執行個體，代表您要分割工作的工作。

**標準.NET 命令列專案檔案**

* `App.config`︰ 標準的.NET 應用程式的設定檔。

* `Packages.config`︰ 標準 NuGet 套件相依性檔案。

* `Program.cs`︰ 包含程式進入點和最上層的例外狀況處理。

### <a name="implementing-the-job-splitter"></a>實作工作分隔器

當您開啟工作管理員範本專案時，專案必須開啟預設 JobSplitter.cs 檔案。 您可以在您的工作量執行工作的分割邏輯使用 Split() 方法顯示如下︰

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

>[AZURE.NOTE] [註解] 區段中的`Split()`方法是工作管理員範本的程式碼，以藉由新增邏輯修改分割到不同的工作的工作的唯一一節。 如果您想要修改的範本的不同分節，請確定您 familiarized 以批次的運作方式，並嘗試幾個[批次的程式碼範例][github_samples]。

您 Split() 實作擁有的存取權︰

* 工作參數，透過`_parameters`欄位。
* 代表工作，透過 CloudJob 物件`_job`欄位。
* 在代表該工作管理員工作，透過 CloudTask 物件`_jobManagerTask`欄位。

您`Split()`實作不需要直接新增到工作的工作。 而您的程式碼應該會傳回一系列 CloudTask 物件，這些將會新增至工作自動叫用工作分割架構類別。 經常使用 C# 的 iterator (`yield return`) 實作工作分隔器，因為這可讓開始越快越執行，而不是等待所有的任務，若要計算的工作的功能。

**工作分割失敗。**

如果您的工作分割發生錯誤時，它應該︰

* 終止使用 C# 的順序`yield break`陳述式的大小寫工作管理員都會被視為成功;或

* 例外狀況，工作管理員會被視為的大小寫失敗，可能會根據用戶端已設定方式，將其重試）。

在這兩種情況下，已經所傳回的工作分隔並加入至批次工作的工作會執行有資格。 如果您不想使用此選項，然後您可以︰

* 從工作分割傳回前終止工作

* 公式傳回之前的整個任務集合 (也就是傳回`ICollection<CloudTask>`或`IList<CloudTask>`，而不是實作使用 C# iterator 您工作分隔)

* 使用任務相依性，讓所有的工作，取決於成功完成的工作管理員

**工作管理員重試**

如果工作管理員失敗時，可能會重試批次服務根據用戶端重試] 設定。 一般而言，這是安全，因為當架構加入工作的工作時，它會忽略任何已存在的工作。 不過，如果計算任務很花時間，您可能不想耗費的重新計算工作; 已加入的任務成本相反地，如果重新執行不一定會產生相同的任務識別碼然後 「 忽略重複項目 」 的行為會不開始運作。 在這種情況下，您應該設計您的工作分割偵測，已完成且重複，例如執行 CloudJob.ListTasks 之前開始 yield 工作的工作。

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>結束代碼，以及 [工作管理員] 範本中的例外狀況

結束代碼和例外狀況提供機制，以決定執行的程式時的結果，他們可以協助來識別程式的執行任何問題。 [工作管理員] 範本實作結束代碼與本節所述的例外狀況。

使用 [工作管理員] 範本實作工作管理員工作可以返回三個可能的結束代碼。

| 程式碼 | 描述 |
|------|-------------|
| 0    | 已順利完成工作管理員。 完成前，執行的工作分割程式碼，所有任務已都新增至工作。 |
| 1    | 工作管理員工作失敗 」 預期 」 的部分程式中的例外狀況。 例外狀況的翻譯目標 JobManagerException 診斷資訊，請盡可能解決錯誤的建議。 |
| 2    | 工作管理員工作失敗，「 意外 」 的例外狀況。 例外狀況已登入標準輸出，但無法新增任何其他的診斷或補救資訊工作管理員。 |

在工作管理員工作失敗，某些工作可能仍已新增至服務發生錯誤之前。 才能執行這些工作正常。 請參閱 「 工作分割失敗 」 上方的這個程式碼路徑的討論。

例外狀況所傳回的所有資訊會都寫入到 stdout.txt 和 stderr.txt 檔案。 如需詳細資訊，請參閱[錯誤處理](batch-api-basics.md#error-handling)。

### <a name="client-considerations"></a>用戶端考量

本節說明一些用戶端實作需求，叫用工作管理員根據此範本時。 傳遞的參數和環境設定的詳細資訊，請參閱[如何傳遞的參數和環境變數，用戶端程式碼](#pass-environment-settings)。

**必要的認證**

若要將任務新增至 Azure 批次工作中，工作管理員工作會需要您 Azure 批次帳戶 URL 與索引鍵。 您必須將其傳送 「 YOUR_BATCH_URL YOUR_BATCH_KEY 環境變數中。 您可以設定這些工作管理員工作環境設定。 例如，在 C# 客戶︰

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**儲存的認證**

一般而言，用戶端並不需要提供工作管理員工作的連結的儲存空間帳戶認證，因為不需要明確存取連結的儲存空間帳戶 （a） 最工作管理員，而且 （b） 的連結的儲存空間帳戶通常提供給所有的任務成一般環境設定為您的工作。 如果您不提供常見的環境設定，透過連結的儲存空間帳戶，而且工作管理員需要存取連結的儲存空間，應該提供連結的儲存空間的認證，如下所示︰

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**工作管理員工作設定**

用戶端應該將工作管理員*killJobOnCompletion*旗標設定為**false**。

通常是安全的用戶端*runExclusive*設為**false**。

用戶端應該使用*resourceFiles*或*applicationPackageReferences*集合有工作管理員可執行檔 （和其所需的 Dll） 部署至運算節點。

根據預設，工作管理員將不會失敗時的重試。 根據您的工作管理員邏輯，用戶端可能會想要啟用透過*限制式*重試/*maxTaskRetryCount*。

**工作設定**

如果工作分割發出任務相依性，用戶端必須設定工作的 usesTaskDependencies 為 true。

在工作分割模型中，很少用戶端至想要將任務新增至重要性工作分割所建立的工作。 用戶端應該因此以正常方式設定工作的*onAllTasksComplete*至**terminatejob**。

## <a name="task-processor-template"></a>任務處理器範本

任務處理器範本能協助您實作工作處理器，可以執行下列動作︰

* 設定執行每個批次工作所需的資訊。
* 執行每個批次工作所需的所有巨集指令。
* 將工作輸出儲存到 [常設儲存空間。

雖然任務處理器不需要執行工作批次，使用任務處理器的主要優點是，它提供包裝紙實作在一個位置的所有工作執行的動作。 例如，如果您需要執行幾個應用程式中的每個任務，或如果您要複製資料來完成後常設儲存每個任務。

任務處理器所執行的動作可為簡單或複雜，和許多或少，所需的工作量。 此外，透過實作所有工作動作，將其中一個任務處理器，您可以隨時更新或新增動作根據應用程式或工作量要求的變更。 不過，在某些情況下工作處理器可能無法實作的最佳解決方案時，可以新增不必要的複雜，例如投影片時，可以快速啟動從簡單的命令列的工作。

### <a name="create-a-task-processor-using-the-template"></a>建立工作處理器使用範本

若要新增您先前建立的解決方案工作處理器，請遵循下列步驟︰

1. 在 Visual Studio 2015 中開啟現有的方案。

2. 在方案總管中，以滑鼠右鍵按一下方案，按一下 [**新增**]，然後按一下**新的專案**。

3. 在**Visual C#**，按一下 [**雲端**，然後再按一下**Azure 批次工作處理器**。

4. 輸入描述您的應用程式，並會顯示任務處理器 (例如 「 LitwareTaskProcessor 」) 為此專案的名稱。

5. 若要建立專案，請按一下**[確定]**。

6. 最後，建立強制載入所有參照的 NuGet 套件，並確認的專案是有效的開始進行修改之前的 Visual Studio 專案。

### <a name="task-processor-template-files-and-their-purpose"></a>任務處理器範本檔案和其用途

當您建立專案使用的任務處理器範本時，則會產生程式碼檔案的三個的群組︰

* 主要程式檔案 (Program.cs)。 此頁面包含的程式進入點及最上層的例外狀況處理。 您通常不需要修改此。

* 架構目錄]。 此頁面包含負責完成工作管理員程式 – 解壓縮參數，將任務新增至批次工作等的 「 使用 」 工時的檔案。您通常不需要修改這些檔案。

* 任務處理器檔案 (TaskProcessor.cs)。 這是您會放在您的特定應用程式的邏輯，來執行任務 （通常是藉由撥現有的可執行檔）。 前與後置處理的代碼，例如下載其他資料或上傳結果檔案，也此處。

當然，您可以視需要以支援您任務的處理器程式碼，根據複雜的工作分割邏輯新增額外的檔案。

範本也會產生標準的.NET 專案檔案，例如.csproj 檔案、 app.config、 packages.config 等等。

本節的其他說明的不同的檔案，以及它們的程式碼的結構，，並說明每個類別的功能。

![Visual Studio 方案總管] 中顯示任務處理器範本解決方案][solution_explorer02]

**架構檔案**

* `Configuration.cs`︰ 封裝工作設定資料，例如批次帳戶詳細資料、 連結的儲存空間帳戶認證、 工作和工作資訊及工作參數的載入。 它也會提供存取批次定義環境變數 （請參閱環境設定為批次文件中的任務，） 透過 Configuration.EnvironmentVariable 類別。

* `IConfiguration.cs`︰ 擷取類別實作的設定，以便讓您可以使用偽造或模擬設定物件您工作分割單位測試。

* `TaskProcessorException.cs`︰ 代表需要工作管理員結束的錯誤。 TaskProcessorException 用來自動換行 '預期 」 錯誤位置提供特定的診斷資訊，做為終止的一部分。

**任務處理器**

* `TaskProcessor.cs`︰ 執行工作。 架構叫用 TaskProcessor.Run 的方法。 這是工作的您會在此插入您的特定應用程式的邏輯的類別。 實作的執行方式︰
  * 剖析及驗證的任何任務參數
  * 撰寫您要叫用任何外部程式命令列
  * 登入任何您可能需要偵錯的診斷資訊
  * 啟動程序使用的命令列
  * 若要結束程序，請等候
  * 擷取結束的程式碼的程序來決定如果成功或失敗
  * 儲存您想要保留常設儲存至任何輸出檔案

**標準.NET 命令列專案檔案**

* `App.config`︰ 標準的.NET 應用程式的設定檔。
* `Packages.config`︰ 標準 NuGet 套件相依性檔案。
* `Program.cs`︰ 包含程式進入點和最上層的例外狀況處理。

## <a name="implementing-the-task-processor"></a>實作工作處理器

當您開啟工作處理器範本專案時，專案必須開啟預設 TaskProcessor.cs 檔案。 您可以在您的工作量執行工作的執行的邏輯使用 Run() 方法所示︰

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
>[AZURE.NOTE] [註解] 區段中的 Run() 方法是只是為您修改您的工作量中新增工作的執行的邏輯的任務處理器範本程式碼區段。 如果您想要修改的範本的不同分節，請先瞭解批次檢閱批次文件，並嘗試幾個批次程式碼範例的運作方式。

Run() 方法負責啟動命令列中，開始一或多個處理程序，等待所有的程序完成，儲存結果中，最後傳回以結束代碼。 Run() 方法是您在您任務的實作處理邏輯的位置。 任務處理器架構叫用 Run() 的方法。您不需要自行來電。

您 Run() 實作擁有的存取權︰

* 工作參數，透過`_parameters`欄位。
* 工作與任務的識別碼，透過`_jobId`和`_taskId`欄位。
* 工作設定，透過`_configuration`欄位。

**工作失敗**

若失敗，您可以藉由擲回例外狀況，結束 Run() 方法，但這保留最上層的例外處理常式在控制項中的任務結束程式碼。 如果您需要讓您可以進行診斷，例如區分不同類型的錯誤，或因為某些失敗模式應該終止工作與其他人不應該控制結束程式碼，您應傳回非零的結束代碼結束 Run() 方法。 這會變成任務結束程式碼。

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>結束代碼和工作處理器範本中的例外狀況

結束代碼和例外狀況提供機制，以決定執行的程式時的結果，其可協助您識別執行程式的問題。 任務處理器範本實作結束代碼與本節所述的例外狀況。

工作處理器任務與任務處理器範本實作可以返回三個可能的結束代碼。

| 程式碼 | 描述 |
|------|-------------|
|  [Process.ExitCode][process_exitcode] | 任務處理器已完成。 請注意，這不表示您叫用的程式成功的 – 只工作處理器成功叫用它，並執行不例外狀況的任何後續處理。 結束程式碼的意義叫用程式而定，通常結束錯誤碼 0 表示程式成功和任何其他結束程式碼表示無法使用的程式。 |
| 1    | 任務處理器無法預期 」 的部分程式中的例外狀況。 例外狀況的翻譯目標`TaskProcessorException`診斷資訊，請盡可能解決錯誤的建議。 |
| 2    | 任務處理器失敗 」 意外 」 的例外狀況。 例外狀況已登入標準輸出，但無法新增任何其他的診斷或補救資訊工作處理器。 |

>[AZURE.NOTE] 如果您所啟動的程式會使用結束代碼 1 和 2，以表示特定失敗模式，則不明確的任務處理器錯誤使用結束代碼 1 和 2。 您可以變更這些任務的處理器錯誤碼特殊結束代碼編輯 Program.cs 檔案中的例外狀況。

例外狀況所傳回的所有資訊會都寫入到 stdout.txt 和 stderr.txt 檔案。 如需詳細資訊，請參閱批次文件中的錯誤處理]、。

### <a name="client-considerations"></a>用戶端考量

**儲存的認證**

如果您的任務處理器使用 Azure blob 儲存體保存輸出，例如使用檔案慣例協助程式庫，然後需要存取*其中一個*雲端儲存帳戶認證*或*blob 容器 URL 包含共用的 access 簽章 (SA)。 範本包含支援提供常見的環境變數透過認證。 您的用戶端可以傳遞儲存認證，如下所示︰

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

儲存帳戶是然後透過 TaskProcessor 類別中可用`_configuration.StorageAccount`屬性。

如果您偏好使用 SA 容器 URL，您也可以將傳遞這透過工作常見環境設定，但任務處理器範本目前也不會包含內建支援。

**儲存設定**

我們建議用戶端] 或 [工作管理員工作建立任何容器之前將任務新增至工作所需的工作。 這是強制如果您使用 SA 容器 URL，因此 URL 不包含建立容器的權限。 建議即使您傳遞儲存帳戶認證，因為它會將儲存不必在容器上呼叫 CloudBlobContainer.CreateIfNotExistsAsync 每項工作。

## <a name="pass-parameters-and-environment-variables"></a>傳遞的參數和環境變數

### <a name="pass-environment-settings"></a>傳遞環境設定

用戶端可以傳遞到工作管理員工作環境設定的表單中的資訊。 這項資訊然後使用工作管理員工作，產生會執行計算工作的工作處理器工作時。 您可以將傳遞環境設定為資訊的範例如下︰

* 儲存帳戶名稱和帳戶金鑰
* 批次帳戶 URL
* 批次的 [帳戶金鑰

批次服務已傳遞至 [工作管理員工作的環境設定使用簡單的機制`EnvironmentSettings`中[Microsoft.Azure.Batch.JobManagerTask]屬性[net_jobmanagertask]。

例如，若要取得`BatchClient`執行個體批次帳戶，您可以將傳遞為從用戶端的環境變數程式碼的 URL 及共用索引鍵批次帳戶認證。 同樣地，若要存取連結至批次客戶儲存帳戶，您可以將傳遞儲存體帳戶名稱和儲存帳戶金鑰為環境變數。

### <a name="pass-parameters-to-the-job-manager-template"></a>參數傳遞給工作管理員範本

在許多情況下，是很有用將每個工作參數傳遞給工作管理員工作，若要控制分割程序的工作，或是設定工作的工作。 您可以上傳 JSON 檔案命名 parameters.json 為工作管理員任務的資源檔案。 參數會提供`JobSplitter._parameters`工作管理員範本中的欄位。

>[AZURE.NOTE] 內建的參數處理常式支援只字串-字串字典。 如果您想要做為參數值傳遞複雜 JSON 值時，您必須通過這些字串和剖析進行中工作分隔器或修改架構的`Configuration.GetJobParameters`方法。

### <a name="pass-parameters-to-the-task-processor-template"></a>參數傳遞給任務的處理器範本

您也可以個別工作使用工作處理器範本實作傳遞的參數。 就使用 [工作管理員] 範本中，任務處理器範本會尋找一個名為資源檔案

parameters.json，則會發現載入為參數字典。 有幾個選項的參數傳遞給任務的處理器工作的方式︰

* 重複使用工作參數 JSON。 如果只有的參數是整個工作的文件 （適用於範例、 轉譯高度和寬度），運作。 若要這麼做，建立 CloudTask 工作分割中時，新增 [工作管理員工作 ResourceFiles parameters.json 資源檔案物件的參考 (`JobSplitter._jobManagerTask.ResourceFiles`) CloudTask ResourceFiles 集合。

* 產生和上傳特定工作的 parameters.json 文件屬於作業分割執行，並參照該 blob 任務的資源檔案集合中。 這是需要不同的工作有不同的參數。 範例可能 3D 呈現案例的圖文框索引傳遞至做為參數工作的位置。

>[AZURE.NOTE] 內建的參數處理常式支援只字串-字串字典。 如果您想要做為參數值傳遞複雜 JSON 值時，您必須通過這些字串和剖析進行中工作的處理器，或修改架構的`Configuration.GetTaskParameters`方法。

## <a name="next-steps"></a>後續步驟

### <a name="persist-job-and-task-output-to-azure-storage"></a>固定的工作和工作的輸出至 Azure 儲存體

批次解決方案開發中的另一個很有幫助工具是[Azure 批次檔案慣例][nuget_package]。 使用此.NET 類別文件庫 （目前在預覽版本） 批次.NET 應用程式中的輕鬆地儲存及擷取工作輸出與 Azure 儲存體。 [固定 Azure 批次工作輸出](batch-task-output.md)包含完整的討論的文件庫及使用方式。

### <a name="batch-forum"></a>批次論壇

[Azure 批次論壇][forum]是 MSDN 上的 [討論批次並提出問題有關服務的絕佳起點。 標題上透過實用的 「 自黏 」 文章，並建立您的批次解決方案時，所出現張貼您的問題。

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
