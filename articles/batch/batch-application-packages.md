<properties
    pageTitle="簡易的應用程式安裝及管理 Azure 批次 |Microsoft Azure"
    description="使用應用程式套件 」 功能，輕鬆地管理多個應用程式和版本上批次的安裝 Azure 批次來計算節點。"
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

# <a name="application-deployment-with-azure-batch-application-packages"></a>Azure 批次應用程式套件的應用程式部署

Azure 批次的 「 應用程式套件 」 功能提供輕鬆地管理工作的應用程式和其部署至您的資料庫中的計算節點。 應用程式套件，您可以上傳與管理多個版本的應用程式執行您的工作，包括其支援的檔案。 您可以自動部署一或多個運算節點這些應用程式在您的資料庫。

在本文中，您將學習如何上傳與管理 Azure 入口網站中的應用程式套件。 然後，您就會瞭解如何以[批次.NET]集區運算節點上安裝[api_net]文件庫。

> [AZURE.NOTE] 此處所述的應用程式套件 」 功能會取代 「 批次應用程式 」 功能可在舊版的服務。

## <a name="application-package-requirements"></a>應用程式套件的需求

您必須[連結 Azure 儲存體帳戶](#link-a-storage-account)批次帳戶使用應用程式套件。

本文所述的應用程式套件 」 功能相容時*僅*使用 10 年 3 月 2016 建立的批次集區。 將不會計算建立這個日期之前的集區中的節點部署應用程式套件。

這項功能已採用[批次 REST API] [api_rest]版 2015年-12-01.2.2 和對應的[批次.NET] [api_net]文件庫版本 3.1.0。 我們建議使用批次時，一律使用最新的 API 版本。

> [AZURE.IMPORTANT] 目前，只*CloudServiceConfiguration*集區支援應用程式套件。 您無法建立使用 VirtualMachineConfiguration 圖像的集區中使用應用程式套件。 請參閱[虛擬機器組態](batch-linux-nodes.md#virtual-machine-configuration)] 區段的[佈建 Linux 運算 Azure 批次集區中的節點](batch-linux-nodes.md)中的兩個不同的設定的詳細資訊。

## <a name="about-applications-and-application-packages"></a>相關應用程式與應用程式套件

Azure 該批次，在*應用程式*指的是一組版本會自動下載至您的資料庫中的計算節點的二進位檔案。 將*應用程式套件*的二進位檔案一*組特定*參照，並且代表指定的*版本*的應用程式。

![應用程式與應用程式套件的高層級圖表][1]

### <a name="applications"></a>應用程式

批次中的應用程式包含一或多個應用程式套件，並指定應用程式的設定選項。 例如，應用程式，可以指定的預設應用程式套件版本，才能安裝運算節點和其封包是否可以更新或刪除。

### <a name="application-packages"></a>應用程式套件

應用程式套件是.zip 檔案包含的應用程式的二進位檔案，支援的檔案所需執行的工作。 每個應用程式套件代表特定的應用程式版本。

您可以指定資料庫與任務層級的應用程式套件。 當您建立的資料庫或任務時，您可以指定一個或多個這些套件與 （選擇性） 版本。

* **資料庫應用程式套件**部署至資料庫中的*每個*節點。 節點加入集區，和時重新啟動，或建立映像部署應用程式。

    集區中的所有節點都執行工作的工作時，會適當資料庫應用程式套件。 當您建立資料庫，您可以新增或更新現有資料庫的套件，您可以指定一個或多個應用程式套件。 如果您要更新現有的資料庫應用程式套件，您必須重新啟動它安裝新的套件的節點。

* **任務應用程式套件**只會部署到之前執行工作的命令列執行的工作，排程運算節點。 如果指定的應用程式套件和版本上已有節點，無法重新部署，且現有的套件。

    任務應用程式套件可用於共用資料庫的環境中，在一個資料庫，來執行不同工作及工作完成時，不會刪除資料庫位置。 如果您的工作集區中有較少比節點的工作，任務應用程式套件可以最小化的資料傳輸之後，才能執行工作的節點部署應用程式。

    其他任務的應用程式套件的效益的情況下會使用特別大的應用程式中，工作的小型數字的工作。 比方說，前置處理的階段或合併任務，其中的測試處理或合併列印的應用程式是重量級。

> [AZURE.IMPORTANT] 有數應用程式和應用程式套件以批次帳戶，以及最大的應用程式套件大小的限制。 如需這些限制詳細資訊，請參閱[配額和 Azure 批次服務限制](batch-quota-limit.md)。

### <a name="benefits-of-application-packages"></a>應用程式套件的優點

應用程式套件，可以簡化批次方案中的程式碼並降低管理您的工作執行的應用程式所需的資源。

集區的 [開始] 任務沒有指定節點上安裝個別的資源檔案的完整清單。 您不必手動管理您的應用程式的檔案中 Azure 儲存空間，或您的節點的多個版本。 然後，您不必擔心產生[SA Url](../storage/storage-dotnet-shared-access-signature-part-1.md)來存取您儲存的帳戶中的檔案。 在儲存應用程式套件，並將它們運算節點部署 Azure 儲存體與背景的運作批次。

## <a name="upload-and-manage-applications"></a>上傳並管理應用程式

您可以使用[Azure 入口網站][portal]或[批次管理.NET](batch-management-dotnet.md)文件庫管理批次帳戶中的應用程式套件。 在下一個幾節中，我們第一次連結的儲存空間帳戶，然後討論新增應用程式和套件和管理這些入口網站。

### <a name="link-a-storage-account"></a>連結的儲存空間帳戶

若要使用應用程式套件，您必須先批次帳戶連結 Azure 儲存體帳戶。 如果您還未儲存帳戶批次帳戶，Azure 入口網站會顯示警告，按一下 [**應用程式**] 方塊中**批次帳戶**刀第一次。

> [AZURE.IMPORTANT] 批次目前支援*只***一般用途**儲存的帳戶類型[的相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的步驟 5，[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)，所述。 當您連結 Azure 儲存體帳戶批次帳戶，連結*只***一般用途**儲存帳戶。

![Azure 入口網站中的任何儲存帳戶設定警告][9]

批次服務使用的儲存空間及擷取的應用程式套件的相關聯的儲存空間帳戶。 您已連結兩個帳戶後，批次可以自動部署儲存在連結的儲存空間帳戶至您的運算節點的套件。 **警告**刀中，按一下**儲存空間帳戶設定**，，然後按一下 [**儲存帳戶**刀批次帳戶連結的儲存空間帳戶上的 [**儲存帳戶**。

![選擇儲存帳戶刀 Azure 入口網站中][10]

我們建議您儲存帳戶*專*用於建立您批次的帳戶，並在此處選取它。 如需有關如何建立儲存帳戶的詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)中的 「 建立儲存帳戶 」。 建立儲存帳戶之後，您可以再連結它批次帳戶使用**儲存空間帳戶**刀。

> [AZURE.WARNING] 批次使用 Azure 儲存體儲存您的應用程式套件，因為您是[為常態分配][storage_pricing]封鎖 blob 資料。 請務必考慮的大小和數字的應用程式套件，並定期移除 [最小化成本遭取代之的套件。

### <a name="view-current-applications"></a>檢視目前的應用程式

若要檢視您批次的帳戶的應用程式，請按一下左側功能表中的**應用程式**功能表項目檢視**批次帳戶**刀時。

![應用程式磚][2]

這會開啟**應用程式**刀︰

![應用程式清單][3]

**應用程式**刀 」 會顯示每個應用程式在您的帳戶和下列屬性︰

* **套件**-此應用程式相關聯的版本數目。
* **預設版本**，如果您沒有指定版本，當您設定的應用程式集區時，將會安裝的版本。 這項設定為選用步驟。
* 允許**儲存格內允許更新**，指定是否套件更新、 刪除及新增的值。 如果這設定為 [**否**，套件更新和刪除停用的應用程式。 您可以加入只有新的應用程式套件的版本。 預設值為 [**是**]。

### <a name="view-application-details"></a>檢視應用程式詳細資料

按一下 [**應用程式**刀中的應用程式，以開啟包含該應用程式的詳細資料刀。

![應用程式詳細資料][4]

在應用程式的詳細資料刀，您可以設定下列設定應用程式。

* **允許更新**，指定是否可以更新或刪除其應用程式套件。 請參閱本文稍後的 「 更新或刪除應用程式套件 」。
* **預設版本**中，指定要部署至此運算節點的預設應用程式套件。
* **顯示名稱**--指定 「 易記 」 名稱時便會顯示資訊的相關應用程式，例如提供您透過批次的客戶服務的使用者介面中，可以使用解決方案您批次。

### <a name="add-a-new-application"></a>新增新的應用程式

若要建立新的應用程式，新增應用程式套件，並指定新的唯一的應用程式識別碼。 您新增新的應用程式識別碼的第一個應用程式套件也會建立新的應用程式。

按一下 [**應用程式**刀，若要開啟**新的應用程式**刀上的 [**新增**]。

![新的應用程式刀 Azure 入口網站中][5]

**新的應用程式**刀提供下列欄位指定新的應用程式和應用程式套件的設定。

**應用程式識別碼**

此欄位指定新應用程式中，這是標準的 Azure 批次 ID 驗證規則的識別碼︰

* 可以包含英數字元，包括連字號和底線的任何組合。
* 不能包含超過 64 個字元。
* 必須是唯一批次帳戶內。
* 且大小寫保留的大小寫不區分大小寫。

**版本**

指定您上傳之應用程式套件的版本。 版本字串為下列的驗證規則︰

* 可以包含英數字元，包括連字號、 底線和期間的任何組合。
* 不能包含超過 64 個字元。
* 必須是唯一的應用程式中。
* 保留，並不區分大小寫。

**應用程式套件**

這個欄位指定包含應用程式二進位檔案，才能執行應用程式的支援檔案。 按一下 [**選取檔案**] 方塊或瀏覽並選取.zip 檔案包含您的應用程式檔案的資料夾圖示。

選取檔案後，按一下**[確定**] 以開始上的傳至 Azure 儲存體。 [上傳] 作業完成後，您會收到通知，並刀會關閉。 根據您上傳檔案的大小，以及您的網路連線的速度，這項作業可能需要一些時間。

> [AZURE.WARNING] [上傳] 作業完成之前，請勿關閉**新的應用程式**刀。 這樣會停止上傳程序。

### <a name="add-a-new-application-package"></a>新增新的應用程式套件

若要新增現有的應用程式的新應用程式套件版本，請選取應用程式中**的應用程式**刀、 按一下**套件**，然後按一下 [**新增**] 以開啟 [**新增套件**刀。

![Azure 入口網站中新增應用程式套件刀][8]

如您所見，欄位相符的**新的應用程式**刀，但已停用 [**應用程式識別碼**] 方塊。 如您所做為新的應用程式中，指定**版本**新套件，瀏覽至您的**應用程式套件**.zip 檔案，，然後按一下**[確定**] 上, 傳的套件]。

### <a name="update-or-delete-an-application-package"></a>更新或刪除應用程式套件

若要更新或刪除現有的應用程式套件，開啟應用程式的詳細資料刀，按一下 [**封包**開啟**封裝**刀，按一下您想要修改的應用程式套件的列中的**省略符號**，然後選取您想要執行的動作。

![更新或刪除 Azure 入口網站中的套件][7]

**更新**

當您按一下 [**更新**] 時，會顯示*更新套件*刀。 然而是*新的應用程式套件*防禦，以類似此刀啟用僅限於套件選取欄位，讓您指定新 ZIP 檔案上傳。

![更新套件刀 Azure 入口網站中][11]

**刪除**

當您按一下 [**刪除**] 時，會要求您確認刪除封裝版本，並批次從 Azure 儲存體刪除套件。 如果您刪除應用程式的預設版本，**預設的版本**設定將會移除應用程式。

![刪除應用程式][12]

## <a name="install-applications-on-compute-nodes"></a>運算節點上安裝應用程式

現在，您已經看到如何管理應用程式套件 Azure 入口網站，我們可以討論部署運算節點，以及執行這些工作批次的方式。

### <a name="install-pool-application-packages"></a>安裝資料庫應用程式套件

若要在資料庫中的所有運算節點上安裝應用程式套件，指定一個或多個應用程式套件*參照*的資料庫。 您指定的集區應用程式套件上各個運算節點安裝該節點加入集區和時重新啟動或建立映像的節點。

批次.NET 中指定一個或多個[CloudPool][net_cloudpool]。[ApplicationPackageReferences][net_cloudpool_pkgref]當您建立新的資料庫，或現有的資料庫。 [ApplicationPackageReference] [net_pkgref]類別指定集區上安裝的應用程式識別碼和版本運算節點。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] 如果應用程式套件部署失敗基於任何原因，請批次服務標記節點[無法使用][net_nodestate]，以及將排程的節點上執行的工作。 在此情況下，您應該**重新啟動**若要重新起始套件部署節點。 重新啟動節點也會啟用節點上一次任務的排程。

### <a name="install-task-application-packages"></a>安裝工作應用程式套件

類似的資料庫，您指定的工作的應用程式套件*參照*。 當任務排程節點上執行時，套件下載，而且建構之前執行工作的命令列。 如果節點上已安裝的指定的套件與版本，不會下載套件，且現存的封裝。

若要安裝工作應用程式套件，設定任務的[CloudTask][net_cloudtask]。[ApplicationPackageReferences][net_cloudtask_pkgref]屬性︰

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>執行安裝的應用程式

下載並解壓縮命名目錄中您指定的資料庫或任務的套件`AZ_BATCH_ROOT_DIR`的節點。 批次也會建立環境變數，包含命名目錄的路徑。 您任務的命令列參考節點上的應用程式時，使用這個環境變數。 變數是以下列格式︰

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`與`version`會對應到您指定的部署的應用程式以及封裝版本的值。 例如，如果您指定的應用程式*混合*版本 2.7 應該安裝，您的工作命令列會使用這個環境變數存取其檔案︰

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

如果您指定的應用程式的預設版本，您可以略過的版本後置字元。 例如，如果您設定 「 2.7 「 為應用程式*混合*的預設版本，任務可以參考以下環境變數和它們會執行 2.7 的版本︰

`AZ_BATCH_APP_PACKAGE_BLENDER`

下列程式碼片段顯示任務命令列範例啟動*混合*應用程式的預設版本︰

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] 請參閱[工作環境設定](batch-api-basics.md#environment-settings-for-tasks)[批次功能概觀](batch-api-basics.md)計算節點環境設定的詳細資訊。

## <a name="update-a-pools-application-packages"></a>更新資料庫的應用程式套件

如果已經應用程式套件以設定現有的資料庫，您可以指定新套件集區。 如果您指定的資料庫時，下列套用新的套件參照︰

* 加入集區的所有新節點並重新啟動或建立映像的任何現有節點將會安裝新指定的套件。
* 計算節點已使用的資料庫中更新套件參照時不會自動安裝新的應用程式套件。 這些計算節點必須重新啟動，或建立映像接收新套件。
* 部署新套件時，建立的環境變數會反映新的應用程式套件參照。

在此範例中，現有的資料庫已設為其中一個其[CloudPool]*混合*應用程式版本 2.7[net_cloudpool]。[ApplicationPackageReferences][net_cloudpool_pkgref]. 若要更新的版本 2.76b 集區的節點，請指定新的[ApplicationPackageReference] [net_pkgref]新的版本，與認可變更。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

現在已設定新的版本，加入集區的任何*新*節點將具有版本 2.76b 部署。 若要安裝 2.76b*已經*集區中的節點，請重新啟動或重新它們。 請注意，重新啟動節點會保留上一個套件部署中的檔案。

## <a name="list-the-applications-in-a-batch-account"></a>批次帳戶的應用程式] 清單

您可以使用[ApplicationOperations]列出的應用程式，以及其套件批次帳戶[net_appops]。[ListApplicationSummaries][net_appops_listappsummaries]方法。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>自動換行

使用應用程式套件，您可以協助您選取的工作的應用程式，並指定確切的版本，才能使用處理工作批次啟用服務時的客戶。 您可能也會提供您上傳並追蹤其本身的應用程式，在您的服務中的客戶的能力。

## <a name="next-steps"></a>後續步驟

* [批次 REST API] [api_rest]也會提供支援，以使用應用程式套件。 例如，請參閱[applicationPackageReferences] [ rest_add_pool_with_packages] [增益集區帳戶]中的項目[rest_add_pool]有關如何指定要使用 REST API 來安裝套件。 顯示[應用]程式[rest_applications]如需詳細資訊，瞭解如何使用批次 REST API 來取得應用程式的資訊。

* 瞭解如何以程式設計方式[管理 Azure 批次帳戶與以批次管理.NET 配額](batch-management-dotnet.md)。 [批次管理.NET] [api_net_mgmt]文件庫可以啟用的批次應用程式或服務的帳戶建立及刪除功能。

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "應用程式套件的高層級圖表"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure 入口網站中的應用程式磚"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure 入口網站中的應用程式刀"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure 入口網站中的應用程式詳細資料刀"
[5]: ./media/batch-application-packages/app_pkg_05.png "新的應用程式刀 Azure 入口網站中"
[7]: ./media/batch-application-packages/app_pkg_07.png "更新或刪除封裝 Azure 入口網站中的下拉式清單"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure 入口網站中的新應用程式套件刀"
[9]: ./media/batch-application-packages/app_pkg_09.png "沒有連結的儲存空間帳戶通知"
[10]: ./media/batch-application-packages/app_pkg_10.png "選擇儲存帳戶刀 Azure 入口網站中"
[11]: ./media/batch-application-packages/app_pkg_11.png "更新套件刀 Azure 入口網站中"
[12]: ./media/batch-application-packages/app_pkg_12.png "刪除 Azure 入口網站中的套件確認] 對話方塊"
