<properties
 pageTitle="自動調整大小 HPC 套件叢集節點 |Microsoft Azure"
 description="自動變大和縮小 Azure 中 HPC 套件叢集運算節點數目"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>自動變大和縮小 Azure 中的 HPC 套件叢集資源根據叢集工作量




如果您部署 Azure 「 突發 」 節點中 HPC 套件叢集，或在 Azure Vm 中建立 HPC 套件叢集，您可能會想自動放大或縮小 Azure 計算資源，例如節點] 或 [根據叢集上目前的工作負載的核心數目的方式。 這個選項可讓您更有效率地使用 Azure 資源，並控制其成本。
若要這麼做，請設定 HPC 套件叢集屬性**AutoGrowShrink**。 或者，執行安裝 HPC pack **AzureAutoGrowShrink.ps1** HPC PowerShell 指令碼。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]此外，目前您可以只會自動變大和縮小 HPC 套件運算節點正在執行 Windows 伺服器作業系統。

## <a name="set-the-autogrowshrink-cluster-property"></a>設定 AutoGrowShrink 叢集屬性

### <a name="prerequisites"></a>必要條件

* **HPC Pack 2012 R2 更新 2 或更新版本叢集**-叢集主節點可部署任一內部部署或 Azure VM。 請參閱瞭解如何開始使用內部部署前端節點和 Azure 「 突發 」 節點的 [[設定混合式叢集 HPC pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) 。 請參閱快速部署 Azure Vm HPC 套件叢集[HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。


* **標頭中的節點 Azure 叢集**-如果您使用的 HPC 套件 IaaS 部署指令碼建立叢集、 AutoGrowShrink] 選項設定叢集設定檔中啟用**AutoGrowShrink**叢集屬性。 如需詳細資訊，請參閱隨附的[指令碼下載](https://www.microsoft.com/download/details.aspx?id=44949)的文件。 

    使用 [下一節所述的 HPC PowerShell 命令部署叢集之後，或者，啟用**AutoGrowShrink**叢集屬性。 若要針對此準備，請先完成下列步驟︰
    1. 主節點和 Azure 訂閱，請設定 Azure 管理憑證。 測試部署，可以使用預設的 Microsoft HPC Azure 自我簽署的憑證 HPC 套件安裝在主節點，並只將該憑證上傳至 Azure 訂閱。 選項及步驟，請參閱[TechNet Library 的指引](https://technet.microsoft.com/library/gg481759.aspx)。
    2. 在執行**regedit** 、 移至 HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo，以及新增新的字串值。 設定值的名稱以 「 指紋 」，然後值資料的步驟 1 中憑證指紋]。


### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>若要設定 AutoGrowShrink 屬性 HPC PowerShell 命令

以下是範例 HPC PowerShell 命令以設定**AutoGrowShrink**及調整其與其他參數的行為。 在本文稍後的完整清單的設定，請參閱[AutoGrowShrink 參數](#AutoGrowShrink-parameters)。 

若要執行下列命令，在叢集開始 HPC PowerShell 以系統管理員。

**若要啟用 AutoGrowShrink 屬性**

    Set-HpcClusterProperty –EnableGrowShrink 1

**若要停用 AutoGrowShrink 屬性**

    Set-HpcClusterProperty –EnableGrowShrink 0

**若要以分鐘為單位變更放大間隔**

    Set-HpcClusterProperty –GrowInterval <interval>

**若要以分鐘為單位變更縮小間隔**

    Set-HpcClusterProperty –ShrinkInterval <interval>

**若要檢視目前的 AutoGrowShrink 設定**

    Get-HpcClusterProperty –AutoGrowShrink

### <a name="autogrowshrink-parameters"></a>AutoGrowShrink 參數

以下是您可以使用 [**設定 HpcClusterProperty**命令修改 AutoGrowShrink 參數。

* **EnableGrowShrink** -切換至 [啟用或停用**AutoGrowShrink**屬性。
* **ParamSweepTasksPerCore** -增加一個核心參數清除任務數量。 預設值為增加一個核心每項工作。 
 
    >[AZURE.NOTE] HPC 套件 QFE KB3134307 變更**TasksPerResourceUnit** **ParamSweepTasksPerCore** 。 它會根據工作資源類型，並可節點、 通訊端或核心。
    
* **GrowThreshold** -觸發自動成長的佇列工作的閥值。 預設值為 1，這表示的佇列狀態 1 或多個工作時自動成長節點。
* **GrowInterval** -分鐘的時間間隔自動成長觸發程序。 預設值為 5 分鐘。
* **ShrinkInterval** -分鐘的時間間隔觸發自動壓縮。 預設值為 5 分鐘。 |
* **ShrinkIdleTimes**連續的檢查，以縮小表示節點的數字是閒置。 預設為 3 次。 例如，如果**ShrinkInterval**為 5 分鐘，HPC 套件會檢查節點是否閒置每 5 分鐘。 如果節點空閒狀態之後 3 連續檢查 （15 分鐘），然後 HPC 套件壓縮的節點。
* **ExtraNodesGrowRatio** -要放大郵件傳遞介面 (MPI) 工作的節點的其他百分比。 預設值為 1，這表示 HPC 套件成長節點 MPI 工作 1%。 
* **GrowByMin** -切換至表示成長原則為基礎的工作所需的最低資源。 預設值為 false，這表示 HPC 套件成長節點根據工作所需的最大資源的工作。
* **SoaJobGrowThreshold**臨界值的內送 SOA 要求觸發自動放大程序。 預設值是 50000。  
    
    >[AZURE.NOTE] 啟動 HPC 套件 2012 R2 更新 3 支援此參數。
    
* **SoaRequestsPerCore** -增加一個核心要求的內送 SOA 的數目。 預設值是 20000。  

    >[AZURE.NOTE] 啟動 HPC 套件 2012 R2 更新 3 支援此參數。

### <a name="mpi-example"></a>MPI 範例

依預設 HPC 套件增加 1%額外的節點 MPI 工作 （**ExtraNodesGrowRatio**設為 1）。 原因是 MPI 可能需要多個節點，並在所有節點準備好時，只可執行工作。 Azure 啟動時節點，有時候一個節點可能需要更多時間來與其他人，造成其他節點為空閒等待的節點，準備好開始。 增加額外的節點，HPC 套件減少此資源等待的時間，而且可能會儲存成本。 若要增加額外的節點 MPI 工作 （例如，若要 10%) 的百分比，執行與類似的命令

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>SOA 範例

根據預設， **SoaJobGrowThreshold**設定為 [50000 並**SoaRequestsPerCore**設定為 200000。 如果您提交 70000 要求的一個 SOA 工作時，會有一個佇列的工作中且傳入的邀請 70000。 在此例 HPC 套件增加 1 核心佇列工作，以及傳入要求時，提升 (70000 50000) / 核心 20000 = 1，因此總計會成長 2 核心 SOA 作業。

## <a name="run-the-azureautogrowshrinkps1-script"></a>執行 AzureAutoGrowShrink.ps1 指令碼

### <a name="prerequisites"></a>必要條件

* **HPC 套件 2012 R2 更新 1 或更新版本叢集**- **AzureAutoGrowShrink.ps1**指令碼安裝 %ccp_home%bin] 資料夾中。 可叢集主節點部署任一內部部署或 Azure VM。 請參閱瞭解如何開始使用內部部署前端節點和 Azure 「 突發 」 節點的 [[設定混合式叢集 HPC pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) 。 請參閱[HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)快速部署 HPC 套件中的叢集 Azure Vm，或使用[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)。

* **PowerShell 的 azure 0.8.12**目前的指令碼的 PowerShell 的 Azure 這個版本而定。 如果您執行的在更新版本，您可能降級 PowerShell 的 Azure[版本 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi)執行指令碼。 

* **針對 Azure 突發節點的叢集**-主節點或 HPC 套件的安裝位置的用戶端電腦上執行指令碼。 如果在用戶端電腦上執行，請確定您設定的變數 $env: CCP_SCHEDULER 正確指向主節點。 Azure 「 突發 」 節點必須已新增至叢集，但是他們可能會在不部署狀態。


* **叢集部署 Azure Vm** -執行指令碼標頭節點 VM，因為**開始 HpcIaaSNode.ps1**而定，並**停駐點 HpcIaaSNode.ps1**指令碼的已那里安裝。 這些指令碼此外需要 Azure 管理憑證或發佈設定檔案 （請參閱[管理計算 Azure 中 HPC 套件叢集節點](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)）。 請確認所有運算節點 Vm 必須已加入至叢集。 他們可能會在 [停止] 狀態。

### <a name="syntax"></a>語法

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
### <a name="parameters"></a>參數

 * **NodeTemplates** -節點範本中的 [名稱]，以定義為節點變大和縮小範圍。 如果未指定 (預設值是@()),時**節點類型**的值為 AzureNodes，且 [ **ComputeNodes**節點] 群組中的所有節點範圍中**節點類型**具有 ComputeNodes 的值時，範圍位於 [ **AzureNodes**節點] 群組中的所有節點。

 * **JobTemplates** -工作範本的 [名稱]，以定義要放大的節點的範圍。

 * **節點類型**-變大和縮小至節點的類型。 支援的值為︰

     * **AzureNodes** – 內部部署中的 Azure PaaS （突發） 節點或 Azure IaaS 叢集。

     * **ComputeNodes** -僅在 Azure IaaS 叢集計算節點 Vm。

* **NumOfQueuedJobsPerNodeToGrow** -的佇列工作所需放大一個節點的數字。

* **NumOfQueuedJobsToGrowThreshold** -開始放大程序的佇列工作閥值數目。

* **NumOfActiveQueuedTasksPerNodeToGrow** -放大一個節點所需的佇列工作作用中的數字。 如果**NumOfQueuedJobsPerNodeToGrow**指定大於 0 的值，則會忽略此參數。

* **NumOfActiveQueuedTasksToGrowThreshold** -開始放大程序進行中的佇列工作閥值數目。

* **NumOfInitialNodesToGrow** -最小值的初始成長如果範圍中的所有節點**不部署**」 或 「**已停止 (Deallocated)**的節點數目。

* **GrowCheckIntervalMins** -間隔檢查成長之間的分鐘數。

* **ShrinkCheckIntervalMins** -分鐘即可縮小檢查之間的間隔。

* **ShrinkCheckIdleTimes**連續數指出節點是閒置縮小檢查 （隔開**ShrinkCheckIntervalMins**）。

* **UseLastConfigurations** -引數檔案中儲存先前的設定。

* **ArgFile**-用來儲存及更新執行指令碼設定的引數檔案的名稱。

* **LogFilePrefix** -首碼記錄檔的名稱。 您可以指定的路徑。 預設的目前工作目錄寫入記錄檔。

### <a name="example-1"></a>範例 1

下列範例會設定與預設 AzureNode 範本合併以變大和縮小自動部署 Azure 尖峰節點。 如果所有的節點最初**不部署**] 狀態，會啟動，至少有 3 節點。 如果佇列工作數目超過 8，指令碼會開始節點，直到其號碼超過至**NumOfQueuedJobsPerNodeToGrow**佇列工作的比率。 如果閒置 3 個連續的空閒時間中找到節點，則會將其停止。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>範例 2

下列範例會設定 Azure 運算節點 Vm 使用預設的 ComputeNode 範本以變大和縮小自動部署。
設定預設工作範本的工作定義叢集上的工作量。 如果一開始會停止所有節點，啟動至少 5 節點。 如果作用中的佇列工作數目超過 15，指令碼會開始節點，直到其號碼超過**NumOfActiveQueuedTasksPerNodeToGrow**作用中佇列工作的比率。 如果閒置 10 個連續的空閒時間中找到節點，則會將其停止。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
