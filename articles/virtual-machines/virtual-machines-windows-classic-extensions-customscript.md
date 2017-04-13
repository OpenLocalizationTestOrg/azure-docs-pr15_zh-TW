<properties
   pageTitle="在 Windows VM 上的 [自訂指令碼延伸模組 |Microsoft Azure"
   description="使用自訂指令碼延伸執行遠端 Windows VM PowerShell 指令碼來自動化 Azure VM 設定工作"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Windows 虛擬機器自訂指令碼副檔名

本文可讓您概略瞭解如何在 Windows Vm 上使用副檔名為自訂指令碼，使用 Azure 服務管理 Api Azure PowerShell cmdlet。

由 Microsoft 建立完成並受信任的虛擬機器 (VM) 副檔名協力廠商發行者擴充 VM 的功能。 如需 VM 擴充功能的概觀，請參閱[Azure VM extensions 和功能](virtual-machines-windows-extensions-features.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟，使用資源管理員模型](virtual-machines-windows-extensions-customscript.md)。

## <a name="custom-script-extension-overview"></a>自訂指令碼副檔名概觀

在 Windows 版副檔名為自訂指令碼，您可以在遠端 VM 執行 PowerShell 指令碼未登入。 您可以不開啟任何其他的連接埠佈建 VM 之後，或在任何時間週期中 VM 執行指令碼。 最常見的使用情況下，執行自訂指令碼副檔名包含執行、 安裝及設定其他軟體 VM 上加以佈建後。

### <a name="prerequisites-for-running-the-custom-script-extension"></a>執行自訂指令碼副檔名的先決條件

1. 安裝<a href="http://azure.microsoft.com/downloads" target="_blank">Azure PowerShell cmdlet</a> 0.8.0 版本或更新版本。
2. 如果您想要在現有的 VM 上執行的指令碼，請確定 VM 代理程式已啟用 VM 上。 如果沒有安裝，請遵循下列[步驟](virtual-machines-windows-classic-agents-and-extensions.md)。 如果 VM 建立從 Azure 入口網站中，依預設安裝 VM 代理程式。
3. 上傳您想要至 Azure 儲存體 VM 上執行的指令碼。 指令碼可以來自單一容器或多個存放容器。
4. 使項目指令碼，其副檔名為開始啟動其他指令碼，則應該撰寫指令碼。

## <a name="custom-script-extension-scenarios"></a>自訂指令碼擴充案例

### <a name="upload-files-to-the-default-container"></a>上傳檔案到預設的容器

下列範例會示範如何來執行指令碼 VM 上如果他們是您的訂閱的預設帳戶存放容器。 上傳至 ContainerName 的指令碼。 您可以使用驗證預設儲存帳戶**取得 AzureSubscription – 預設**] 命令。

下列範例會建立 VM，但您也可以在現有的 VM 上執行相同的狀況。

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>上傳檔案到非預設存放容器

這種情況下會顯示如何使用非預設儲存容器內同一份訂閱或不同的訂閱中的上傳指令碼和檔案。 此範例顯示現有的 VM，但在建立 VM 時，就可以完成相同的作業。

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>將指令碼上傳到多個容器跨不同的儲存帳戶

  如果指令碼檔儲存到多個容器，您必須提供共用的完整存取簽章 (SA) URL 的檔案來執行指令碼。

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>從 Azure 入口網站中新增自訂指令碼副檔名

移至 [VM <a href="https://portal.azure.com/ " target="_blank">Azure 入口網站</a>中，並指定要執行指令碼檔將延伸。

  ![指定的指令碼檔][5]


### <a name="uninstall-the-custom-script-extension"></a>解除安裝自訂指令碼副檔名

您可以使用下列命令解除安裝自訂指令碼副檔名從 VM。

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>使用範本的副檔名為自訂指令碼

若要瞭解如何使用自訂指令碼副檔名與 Azure 資源管理員範本，請參閱[使用 Windows Vm 使用 Azure 資源管理員範本的自訂指令碼副檔名](virtual-machines-windows-extensions-customscript.md)。

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
