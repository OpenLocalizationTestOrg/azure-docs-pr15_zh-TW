<properties
   pageTitle="開始使用 PowerShell 的 Azure 批次 |Microsoft Azure"
   description="取得 PowerShell 的 Azure 指令程式可用來管理 Azure 批次服務的快速簡介"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>開始使用 Azure 批次 PowerShell cmdlet
透過 Azure 批次 PowerShell cmdlet，您可以執行，和指令碼的許多您以批次 Api、 Azure 入口網站中及 Azure 命令列介面 (CLI) 執行相同的工作。 這是指令程式可用來管理您批次的帳戶，並使用您的批次資源，例如集區、 工作和工作的快速簡介。

批次 cmdlet 和詳細的 cmdlet 語法的完整清單，請參閱[Azure 批次 cmdlet 參考](https://msdn.microsoft.com/library/azure/mt125957.aspx)。

本文根據版本 3.0.0 PowerShell 的 Azure 中的 cmdlet。 我們建議您更新您 Azure PowerShell 經常利用服務更新與增強功能。

## <a name="prerequisites"></a>必要條件

執行下列作業以使用 Azure PowerShell 來管理您批次的資源。

* [安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)

* **登入 AzureRmAccount**指令程式連線至您的訂閱 （Azure 批次 cmdlet 運送 Azure 資源管理員模組中）︰

    `Login-AzureRmAccount`

* **登錄批次者命名空間**。 這項作業只需要執行的**一次，每個訂閱**。

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>管理批次帳戶與索引鍵

### <a name="create-a-batch-account"></a>建立批次帳戶

**新增 AzureRmBatchAccount**建立批次帳戶中指定的資源群組。 如果您還沒有 [資源] 群組中，執行[新增 AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx)指令程式。 指定**位置**參數，例如 「 美國中部 」 中的其中一個 Azure 區域。 例如︰

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

在 [資源] 群組中，指定的名稱中 <*account_name*> 的帳戶的位置和資源群組的名稱，然後建立批次帳戶。 建立批次帳戶，可以採取一些時間才能完成。 例如︰

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] 批次帳戶名稱必須是唯一的資源群組的 Azure 地區、 介於 3 到 24 個字元，並使用大小寫字母和數字只。

### <a name="get-account-access-keys"></a>取得帳戶便捷鍵
**取得 AzureRmBatchAccountKeys**顯示 Azure 批次帳戶相關聯的便捷鍵。 例如，執行下列動作以取得帳戶您建立主要和次要鍵。

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>產生新的便捷鍵
**新增 AzureRmBatchAccountKey**產生新的主要或次要帳戶金鑰 Azure 批次帳戶。 例如，若要產生新的主索引鍵，為您批次的帳戶，請輸入︰

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] 若要產生新的第二個機碼，指定 「 次要 」 **KeyType**參數。 您必須分別重新產生主要和次要鍵。

### <a name="delete-a-batch-account"></a>刪除批次帳戶
**移除 AzureRmBatchAccount**刪除批次帳戶。 例如︰

    Remove-AzureRmBatchAccount -AccountName <account_name>

出現提示時，請確認您要移除的帳戶。 帳戶移除可能需要一些時間才能完成。

## <a name="create-a-batchaccountcontext-object"></a>建立 BatchAccountContext 物件

若要驗證使用批次 PowerShell cmdlet，當您建立及管理批次資料庫、 工作、 工作及其他資源，請先建立 BatchAccountContext 物件以儲存您的帳戶名稱與索引鍵︰

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

您將使用**BatchContext**參數的 cmdlet 傳遞 BatchAccountContext 物件。

> [AZURE.NOTE] 根據預設，帳戶的主索引鍵用於驗證]，，但是您可以明確選取金鑰] 以使用藉由變更 BatchAccountContext 物件的**KeyInUse**屬性︰ `$context.KeyInUse = "Secondary"`。

## <a name="create-and-modify-batch-resources"></a>建立及修改批次資源
若要建立批次的帳號] 之下的 [資源使用 cmdlet，例如**新增 AzureBatchPool**、**新增 AzureBatchJob**，以及**新增 AzureBatchTask** 。 為對應**取得-** **設定-** cmdlet，以更新現有的資源的屬性，並**移除-** cmdlet 來移除批次的帳號] 之下的資源。

使用這些 cmdlet，除了傳遞 BatchContext 物件，許多時，您需要建立或傳遞包含詳細的資源設定的物件，如下列範例所示。 請參閱其他範例的每個 cmdlet 的詳細的說明。

### <a name="create-a-batch-pool"></a>建立批次資料庫

在建立或更新批次資料庫，您可以選取雲端服務設定] 或 [運算節點 （請參閱[批次功能概觀](batch-api-basics.md#pool)） 上的作業系統的虛擬機器設定。 您的選擇會決定是否影像的其中一個[Azure 來賓 OS 版本](../cloud-services/cloud-services-guestos-update-matrix.md#releases)，或使用其中一個支援 Linux 或 Windows VM 中的圖像 Azure Marketplace 您運算節點。

當您執行**新增 AzureBatchPool**時，傳入 PSCloudServiceConfiguration 或 PSVirtualMachineConfiguration 物件的作業系統設定。 例如，下列 cmdlet 建立大小小型運算節點，在雲端服務設定建立影像系列 3 (Windows Server 2012) 的最新版本的作業系統與新批次集區。 在這裡， **CloudServiceConfiguration**參數會指定*$configuration*變數 PSCloudServiceConfiguration 物件。 **BatchContext**參數會指定先前定義的變數*$context* BatchAccountContext 物件。

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

自動縮放公式取決於新的資料庫中的計算節點的目標數目。 在此情況下，公式會直接**$TargetDedicated = 4**，指出計算資料庫中的節點數目最多為 4。

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>集區、 工作、 工作和其他詳細資料的查詢

用於建立批次的帳號] 之下的實體 cmdlet，例如**取得 AzureBatchPool**、**取得 AzureBatchJob**，以及**取得 AzureBatchTask**以查詢。

### <a name="query-for-data"></a>資料查詢

例如，使用**取得 AzureBatchPools**尋找您的資料庫。 預設的所有集區，您的帳戶，假設您已在此查詢會儲存在*$context*BatchAccountContext 物件︰

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>使用 OData 的篩選

您可以提供使用**篩選**參數尋找只顯示您感興趣的物件 OData 篩選。 例如，您可以找到所有集區識別碼 」 myPool 」 開始著手使用︰

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

這個方法不是本機的管線中使用 「 物件位置 」 彈性。 不過，查詢會直接傳送給批次服務，好讓所有篩選在伺服器端，儲存網際網路頻寬會發生的情況。

### <a name="use-the-id-parameter"></a>使用 Id 參數

OData 篩選的替代方案是使用**Id**參數。 若要查詢的識別碼 」 myPool 」 特定集區︰

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

**Id**參數支援全識別碼搜尋、 不萬用字元或 OData 樣式篩選。

### <a name="use-the-maxcount-parameter"></a>使用 MaxCount 參數

根據預設，每個指令程式會傳回 1000年物件的最大值。 超過此限制，調整以顯示回較少的物件，篩選或明確設定使用**MaxCount**參數的最大值。 例如︰

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

若要移除的上限，將**MaxCount**為 0 或更少。

### <a name="use-the-powershell-pipeline"></a>使用 PowerShell 管線

批次 cmdlet 可以使用 PowerShell 管線傳送 cmdlet 之間的資料。 這有指定參數，相同的效果，但可讓您更容易使用多個項目。

例如，尋找並顯示所有任務，在您的帳戶︰

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

重新啟動 （重新開機） 集區中的每個運算節點︰

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>應用程式套件管理

應用程式套件提供簡化部署應用程式到您的資料庫中的計算節點。 透過批次 PowerShell cmdlet，您可以上傳和管理應用程式套件中您批次的帳戶，並部署套件版本，來計算節點。

**建立**應用程式︰

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**新增**應用程式套件︰

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

設定應用程式的**預設版本**︰

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**清單**的應用程式套件

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**刪除**應用程式套件

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**刪除**應用程式

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] 您必須先刪除應用程式刪除所有應用程式的應用程式套件的版本。 如果您想要刪除目前有應用程式套件的應用程式，您會收到 「 衝突 」 錯誤。

### <a name="deploy-an-application-package"></a>部署應用程式套件

當您建立資料庫時，您可以指定一個或多個應用程式套件，以供部署。 當您在資料庫建立時指定套件時，將其部署至每個節點為節點的連接集區。 當您重新啟動或重新建立影像節點時也部署套件。

指定`-ApplicationPackageReference`當建立要將應用程式套件部署至資料庫的節點，他們加入集區資料庫] 選項。 請先建立**PSApplicationPackageReference**物件，然後設定讓它使用您想要部署至此的集區運算節點的應用程式識別碼以及封裝版本︰

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

現在，建立資料庫，然後指定套件參照物件做為引數`ApplicationPackageReferences`選項︰

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

您可以[使用 Azure 批次應用程式套件的應用程式部署](batch-application-packages.md)尋找應用程式套件的詳細資訊。

>[AZURE.IMPORTANT] 您必須[連結 Azure 儲存體帳戶](#linked-storage-account-autostorage)至您要使用應用程式套件的批次帳戶。

### <a name="update-a-pools-application-packages"></a>更新資料庫的應用程式套件

若要更新指派給現有的資料庫的應用程式，先建立具有所需的屬性 （應用程式識別碼以及封裝版本） 的 PSApplicationPackageReference 物件︰

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

接下來，從批次取得資料庫、 清除任何現有的套件，加入我們新的套件參考，及更新批次服務使用新的資料庫設定︰

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

您現在已經更新批次服務中的集區的內容。 若要實際部署新的應用程式套件，來計算資料庫中的節點，不過，您必須重新啟動或重新這些節點。 您可以使用這個命令集區中，重新啟動每個節點︰

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] 您可以將多個應用程式套件部署運算節點集區中。 如果您想要*新增*應用程式套件，而不是取代目前已部署的套件，省略`$pool.ApplicationPackageReferences.Clear()`一行。

## <a name="next-steps"></a>後續步驟

* 如需詳細的 cmdlet 語法與範例，請參閱[Azure 批次 cmdlet 參考](https://msdn.microsoft.com/library/azure/mt125957.aspx)。

* 如需有關應用程式與批次的應用程式套件的詳細資訊，請參閱[Azure 批次應用程式套件的應用程式部署](batch-application-packages.md)。
