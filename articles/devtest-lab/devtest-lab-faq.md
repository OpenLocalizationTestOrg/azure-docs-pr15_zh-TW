<properties
    pageTitle="Azure DevTest 實驗室常見問題集 |Microsoft Azure"
    description="找出 Azure DevTest 實驗室常見問題的解答"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Azure DevTest 實驗室常見問題集

本文將回答一些 Azure DevTest 實驗室最常見的疑問。

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>一般
- [如果我沒有問題解答以下嗎？](#what-if-my-question-isnt-answered-here)
- [為什麼應該使用 Azure DevTest 實驗室？](#why-should-i-use-azure-devtest-labs) 
- [「 擔心-空閒、 自助 」 是指什麼？](#what-does-quotworry-free-self-servicequot-mean)
- [我要如何使用 Azure DevTest 實驗室？](#how-can-i-use-azure-devtest-labs) 
- [我要如何正在計費 Azure DevTest 實驗室的？](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>安全性 
- [什麼是 Azure DevTest 實驗室中不同的安全性層級？](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [如何建立角色，讓使用者執行特定任務？](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>SONT/CD 整合與自動化 
 
- [Azure DevTest 實驗室將與我 SONT/CD toolchain 整合嗎？](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>虛擬機器 
 
- [為什麼看不到特定 Vm 中看到內 Azure DevTest 實驗室 Azure 虛擬機器刀？](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [自訂圖像與公式之間的差異為何？](#what-is-the-difference-between-custom-images-and-formulas) 
- [如何建立多個 Vm 從相同的範本，一次？](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [如何將我現有的 Azure Vm 移到 [我的 Azure DevTest 實驗室實驗？](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [我是否可以將多個磁碟附加到我 Vm？](#can-i-attach-multiple-disks-to-my-vms) 
- [我要如何自動化上傳 VHD 檔案，以建立自訂圖像程的序？](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [如何刪除我的實驗中的所有 Vm 的程序自動化？](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>成品 
 
- [什麼是成品？](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>實驗室設定 
 
- [如何從 Azure 資源管理員範本建立實驗室？](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [為什麼我 Vm 中任意名稱不同的資源群組建立？我可以重新命名或修改這些資源群組？](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [在同一份訂閱可以建立多少實驗室？](#how-many-labs-can-i-create-under-the-same-subscription)
- [每個實驗室可以建立多少 Vm？](#how-many-vms-can-i-create-per-lab)
- [如何共用的直接連結至我的實驗中？](#how-do-i-share-a-direct-link-to-my-lab)
- [什麼是 Microsoft 帳戶？](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>疑難排解 
 
- [我的成品無法 VM 建立期間。如何疑難排解？](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [為何不是我現有的虛擬網路正確地儲存？](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>如果我沒有問題解答以下嗎？
如果此處未列出您的問題，請讓我們知道，我們將協助您尋找解答。

- 將問題張貼在[Disqus 執行緒](#comments)此常見問題集的結尾，然後加入 Azure 快取小組與本文相關的其他社群成員]。
- 若要達到更多的對象， [Azure DevTest 實驗室 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)上張貼問題，加入 Azure DevTest 實驗室小組與其他社群的成員。
- 若要讓功能要求，提交您的要求和[Azure DevTest 實驗室使用者語音](https://feedback.azure.com/forums/320373-azure-devtest-labs)想法。

### <a name="why-should-i-use-azure-devtest-labs"></a>為什麼應該使用 Azure DevTest 實驗室？ 
Azure DevTest 實驗室可以儲存您的小組時間和金錢。 開發人員可以建立自己的環境使用數種不同的基底，並使用成品快速部署，並設定應用程式。 使用自訂的圖像和公式，虛擬機器可以儲存為範本，然後輕鬆地再次出現。 此外，實驗室提供多個可設定原則，讓實驗室管理員減少浪費及管理小組的環境。 這些原則會包含自動關機，成本閥值，每個使用者和最大 VM 大小上限 Vm。 如 Azure DevTest 實驗室更深入的說明，請閱讀[概觀](devtest-lab-overview.md)或查看[簡介影片](/documentation/videos/videos/what-is-azure-devtest-labs)。 

### <a name="what-does-worry-free-self-service-mean"></a>「 擔心-空閒、 自助 」 是指什麼？
「 擔心空閒自助 」 表示開發人員及其人員視需要建立自己的環境，而且系統管理員必須知道 Azure DevTest 實驗室協助最小化的安全性，還費時，以及控制成本。 系統管理員可以指定哪些 VM 大小、 允許 Vm 數目上限，以及當 Vm 會啟動和關閉。 Azure DevTest 實驗室也可讓您輕鬆地監控成本，設定通知，以保持知道實驗室中的資源的使用方式。 

### <a name="how-can-i-use-azure-devtest-labs"></a>我要如何使用 Azure DevTest 實驗室？ 
每當您需要開發或測試環境中，而且您想要重新產生這些快速及/或管理成本儲存原則時，azure DevTest 實驗室相當實用。 

以下是我們的客戶使用 Azure DevTest 實驗室某些案例︰ 

- 管理開發和測試環境在同一個位置，請使用原則，以減少成本及共用自訂圖像建置小組之間。
- 開發應用程式儲存整個開發階段的磁碟狀態使用自訂的圖像。
- 追蹤進度與成本。 
- 建立大量測試環境品質保證測試。
- 輕鬆設定，並重現上各種環境的應用程式中使用的成品和公式。 
- 發送 Vm 的 hackathons （共同作業開發或測試工作），然後輕鬆地不支援的這些事件結束時。 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>我要如何正在計費 Azure DevTest 實驗室的？ 
Azure DevTest 實驗室是免費的服務，表示免費建立實驗室及設定原則、 範本及成品。 您是付款僅適用於 Azure 實驗室，例如虛擬機器、 儲存帳戶和虛擬網路中使用的資源。 如需成本實驗室資源的詳細資訊，瞭解[Azure DevTest 實驗室價格](https://azure.microsoft.com/pricing/details/devtest-lab/)。 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>什麼是 Azure DevTest 實驗室中不同的安全性層級？  
安全性存取取決於[Azure Role-Based 存取控制 (RBAC)](../active-directory/role-based-access-built-in-roles.md)。 若要瞭解 access 的運作方式，有助於了解權限、 角色，以及範圍所定義的 RBAC 之間的差異。

- **權限**的權限是定義的存取特定的動作。 例如，權限可能是唯讀存取所有虛擬機器。 
- **角色**角色是一組可進行分組及指派給使用者的權限。 例如，「 訂閱擁有者 」 有訂閱中的所有資源的存取權。 
- **範圍**範圍是 Azure 資源的階層中的層級。 例如，範圍可能是資源群組或單一實驗室或整個訂閱。 
 
範圍內的 Azure DevTest 實驗室，有兩種類型的定義使用者權限的角色︰ 實驗室擁有者及實驗室使用者。

- **實驗室擁有者**實驗室擁有者可存取實驗室內任何資源。 因此，他們可以修改原則、 讀取和寫入任何 Vm、 變更虛擬網路，以及等。 
- **實驗室使用者**-實驗室使用者可以檢視所有實驗室資源，例如 Vm、 原則，以及虛擬網路，但無法修改原則或其他使用者所建立的任何 Vm。 您也可在 Azure DevTest 實驗室，建立自訂的角色，您可以瞭解如何在文章中，[以特定實驗室原則授與使用者權限](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。 
 
由於範圍階層，使用者在特定範圍內有權限時，他們會自動授與這些包含每個較低層級範圍的權限。 比方說，如果使用者指派給角色的訂閱擁有者，然後他們能存取所有資源在訂閱。 所有的虛擬機器、 所有的虛擬網路及所有實驗室這類資源包括。 因此，訂閱的擁有者就會自動繼承實驗室擁有者的角色。 不過，相反不正確。 實驗室擁有者有權存取實驗室，也就是較低的領域，比訂閱層級。 因此，不能看到虛擬機器虛擬網路或外部實驗室任何資源實驗室擁有者。 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>如何建立角色，讓使用者執行特定任務？
可以在這裡找到有關如何建立自訂的角色和權限指派給該角色的完整的文章。 建立 「 DevTest 實驗室進階使用者 」，所具權限來啟動和停止所有 Vm 實驗室中的角色的指令碼範例如下︰
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest 實驗室將與我 SONT/CD toolchain 整合嗎？ 
如果您使用的 VSTS，有可讓您可以自動化中 Azure DevTest 實驗室您發行的管線[Azure DevTest 實驗室工作延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。 此擴充功能的用途包括︰

- 建立並部署 VM 自動及設定其與使用 Azure 檔案複製或 PowerShell VSTS 任務的最新版本。 
- 自動擷取 VM 的狀態之後測試重現相同 VM 進一步調查的上一個錯誤。 
- 不再需要時，請刪除 VM 發行管線的結尾。 

下列部落格文章提供指引與使用副檔名為 VSTS 的相關資訊︰
 
- [Azure DevTest 實驗室 – VSTS 副檔名](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [部署新 VM VSTS 從現有 AzureDevTestLab 中](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [使用連續部署 AzureDevTestLabs VSTS 版本管理](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
其他 SONT/CD toolchains，可以透過 VSTS 工作延伸達成的所有先前所述的案例，可以透過部署[Azure 資源管理員範本](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)使用[PowerShell 的 Azure cmdlet](../resource-group-template-deploy.md)和[.NET Sdk](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/)同樣達成。 您也可以使用[REST Api DevTest 實驗室的](http://aka.ms/dtlrestapis)整合與您 toolchain。  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>為什麼看不到特定 Vm 中看到內 Azure DevTest 實驗室 Azure 虛擬機器刀？
Azure DevTest 實驗室中建立 VM 時，權限給存取該 VM。 您可同時在實驗室刀與**虛擬機器**中檢視。 DevTest 實驗室角色的使用者可以看到所有的虛擬機器實驗室的**所有虛擬機器**刀透過建立實驗室中。 不過，DevTest 實驗室角色的使用者不會自動取得 VM 資源的其他人所建立的讀取權限。 因此，這些 Vm 不會顯示在**虛擬機器**刀。 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>自訂圖像與公式之間的差異為何？ 
自訂圖像是 VHD （虛擬硬碟），，而公式是您可以設定的其他設定，您可以儲存並重新產生的圖像。 自訂圖像可能會比如果您想要快速建立 [種環境使用相同的基本、 不變的圖像。 公式是如果您想要重現您 VM 的最新的位元、 虛擬/子網路，或的特定大小的設定。 如需深入的說明，請參閱，[比較自訂圖像和 DevTest 實驗室中的公式](devtest-lab-comparing-vm-base-image-types.md)。 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>如何建立多個 Vm 從相同的範本，一次？ 
建立 VM 及[部署從 Windows PowerShell 的 Azure 資源管理員範本](../resource-group-template-deploy.md)時，您可以使用[VSTS 工作延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)或[產生 Azure 資源管理員範本](devtest-lab-add-vm-with-artifacts.md#save-arm-template)。 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>如何將我現有的 Azure Vm 移到 [我的 Azure DevTest 實驗室實驗？ 
我們正在設計解決方案，直接將 Vm 移至 Azure DevTest 實驗室，但目前您可以將複製您現有的 Vm Azure DevTest 實驗室，如下所示︰ 

1. 複製您使用這個[Windows PowerShell 指令碼](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)的現有 VM VHD 檔案 
1. Azure DevTest 實驗室測試環境內，[建立自訂的圖像](devtest-lab-create-template.md)。 
1. 建立 VM 中從您的自訂圖像 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>我是否可以將多個磁碟附加到我 Vm？ 
將多個磁碟附加至 Vm 功能受支援。  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>我要如何自動化上傳 VHD 檔案，以建立自訂圖像程的序？ 
有兩個選項︰

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload)可用來複製或 VHD 檔案上傳至實驗室相關聯的儲存空間帳戶。
- [Microsoft Azure 儲存檔案總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)是執行 Windows、 OSX 及 Linux 的獨立應用程式。   
 
若要尋找您實驗室相關聯的目的地儲存帳戶，請遵循下列步驟︰

1. [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)登入。 
1. 從 [左] 面板中，選取**資源群組**。 
1. 找出並選取您實驗室相關聯的資源群組。 
1. 在**概觀**刀中，選取其中一個儲存帳戶。 
1. 選取 [**二進位大型物件**]。
1. 尋找清單中的上傳。 如果沒有，返回步驟 #4，然後嘗試其他儲存的帳戶。
1. 使用**URL**做為您在 AzCopy 命令的目的地。


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>如何刪除我的實驗中的所有 Vm 的程序自動化？

除了 Vm 刪除 Azure 入口網站中測試環境中，您可以使用 PowerShell 指令碼測試環境中刪除所有 Vm。 在下列範例中，只要修改**值，以變更**註解下的參數值。 您可以擷取`subscriptionId`， `labResourceGroup`，及`labName`實驗室刀 Azure 入口網站中的值。 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>什麼是成品？ 
成品是可供您最新的位元] 或 [到 VM 您開發工具部署的可自訂項目。 建立簡單的按下滑鼠，過程附加您 vm，一旦 VM 佈建後，成品部署及設定，您 VM。 我們[公用 Github 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)中，在各種已存在的成品，但您可以輕鬆[製作您自己的成品](devtest-lab-artifact-author.md)。 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>如何從 Azure 資源管理員範本建立實驗室？ 
我們提供您可以為部署[實驗室 Azure 資源管理員範本 Github 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)-或修改，以建立您實驗室的自訂範本。 每個這些範本都有一個連結，您可以按一下 [部署為實驗室-下您自己的 Azure 訂閱，或者您可以自訂的範本及[部署使用 PowerShell 或 Azure CLI](../resource-group-template-deploy.md)。
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>為什麼我 Vm 中任意名稱不同的資源群組建立？ 我可以重新命名或修改這些資源群組？ 
資源群組會建立以下列方式來管理使用者權限和存取權的虛擬機器 Azure DevTest 實驗室的順序。 時以您想要的名稱，您可以將 VM 移到另一個資源群組中，如此一來，因此不建議。 我們正在處理改善此體驗，允許更大的彈性。   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>在同一份訂閱可以建立多少實驗室？ 
沒有任何特定的限制，您可以建立每個訂閱的實驗室數目。 不過，使用的資源限於每個訂閱。 您可以瞭解[限制和配額依賴 Azure 訂閱](../azure-subscription-service-limits.md)，並[瞭解如何增加這些限制](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)。 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>每個實驗室可以建立多少 Vm？ 
沒有任何特定的限制，可以建立每次實驗的 Vm 數目。 不過，目前實驗室支援在標準的儲存空間，同時執行只關於 40 Vm 和 25 Vm 同時執行進階版儲存區中。 我們目前正在增加這些限制。 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>如何共用的直接連結至我的實驗中？

若要共用的直接連結給實驗室使用者，您可以執行下列程序。

1. 瀏覽至實驗室 Azure 入口網站中。
2. 從您的瀏覽器複製實驗室 URL 並實驗室使用者與共用檔案。 

>[AZURE.NOTE] 如果實驗室使用者是以[MSA 帳戶](#what-is-a-microsoft-account)的外部使用者，且不屬於您公司的 Active directory，它們可能會收到錯誤時瀏覽至提供的連結。 如果他們收到錯誤訊息，指導按一下右上角的 [Azure 入口網站其名稱，然後選取 [從功能表的 [**目錄**] 區段中的實驗室存在的目錄。

### <a name="what-is-a-microsoft-account"></a>什麼是 Microsoft 帳戶？

Microsoft 帳戶是您使用的幾乎所有項目所做的 Microsoft 裝置和服務。 電子郵件地址和密碼登入 Skype、 Outlook.com、 OneDrive、 Windows Phone 和 Xbox LIVE – 您使用的是，這表示您的檔案、 相片、 連絡人和設定可以依照您任何裝置。 

>[AZURE.NOTE] Microsoft 帳戶用來被稱為 「 Windows Live ID 」。
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>我的成品無法 VM 建立期間。 如何疑難排解？ 
若要瞭解如何取得有關您失敗的成品的記錄，請參閱部落格文章-撰寫的其中一個我們 Mvp-[如何疑難排解失敗 AzureDevTestLabs 的成品](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)。 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>為何不是我現有的虛擬網路正確地儲存？  
一個可能原因是您的虛擬網路名稱包含週期。 如果是這樣，請嘗試移除期間或並取代成連字號，然後嘗試再次儲存虛擬網路。
