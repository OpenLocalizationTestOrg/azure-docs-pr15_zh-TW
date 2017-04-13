<properties
   pageTitle="想要的 Azure 概觀的狀態設定 |Microsoft Azure"
   description="使用 Microsoft Azure 副檔名處理常式 PowerShell 所需的狀態設定的概觀。 包括先決條件、 架構、 cmdlet。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure 所需的狀態設定副檔名處理常式簡介 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure VM 代理程式和相關聯的副檔名是 Microsoft Azure 基礎結構服務的一部分。 VM 副檔名是擴充 VM 功能並簡化各種 VM 管理作業的軟體元件。 例如，VMAccess 副檔名可以用來重設系統管理員的密碼，或自訂指令碼副檔名可用來 VM 上執行指令碼。

本文為 Azure PowerShell 的 Azure Vm 介紹 PowerShell 所需的狀態設定 (DSC) 副檔名。 上傳並啟用 PowerShell DSC 副檔名 Azure VM 上套用 PowerShell DSC 設定，您可以使用新的 cmdlet。 PowerShell DSC 副檔名呼叫 PowerShell DSC 以確立 VM 上接收到的 DSC 設定。 此功能也可透過 Azure 入口網站。

## <a name="prerequisites"></a>必要條件 ##
**本機電腦**若要互動 Azure VM 副檔名，您需要使用 Azure 入口網站或 Azure PowerShell SDK。 

**來賓代理程式**DSC 設定來設定 Azure VM 必須是 [Windows 管理 Framework (WMF) 4.0 或 5.0 支援的作業系統。 支援的作業系統版本的完整清單位於[DSC 擴充版本歷程記錄](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/)。

## <a name="terms-and-concepts"></a>字詞和概念 ##
本指南假設熟悉以下的概念︰

設定-DSC 設定文件。 

節點-DSC 設定的目標。 在此文件中，「 節點] 一律是指 Azure VM。

設定資料-.psd1 檔案包含環境的資料設定

## <a name="architectural-overview"></a>架構概觀 ##

Azure DSC 副檔名為使用 Azure VM 代理程式架構發表、 制訂，以及報告 DSC 設定 Azure Vm 上執行。 DSC 副檔名預期.zip 檔案包含至少設定文件及一組透過 Azure PowerShell SDK 或透過 Azure 入口網站提供的參數。

第一次呼叫擴充功能時，它會執行安裝程序。 此程序會安裝的版本的 Windows 管理 Framework (WMF) 使用下列邏輯︰

1. 在 Windows Server 2016 Azure VM OS 時，會不執行任何動作。 在 Windows Server 2016 已經有 PowerShell 安裝最新版本。
2. 如果`wmfVersion`屬性指定，則除非與 VM 的作業系統不相容安裝 WMF 的版本。
3. 如果沒有`wmfVersion`指定屬性、 已安裝最新適用 WMF 版本。

安裝 WMF 需要重新啟動電腦。 後重開機，副檔名為下載.zip 所指定的檔案中`modulesUrl`屬性。 如果此位置是 Azure blob 儲存體，可以中指定的 SA 權杖`sasToken`存取該檔案的屬性。 設定函數.zip 下載並解壓縮後，在中定義`configurationFunction`執行產生要求刪除。 延伸再執行`Start-DscConfiguration -Force`產生 MOF 檔案。 副檔名為擷取輸出] 和 [寫入它回 Azure 狀態頻道。 從這裡開始，DSC LCM 控點的監控和為標準校正。 

## <a name="powershell-cmdlets"></a>PowerShell cmdlet ##

PowerShell 指令程式可用於 ARM 或 ASM 封裝、 發佈及監視 DSC 副檔名部署。 下列 cmdlet，列出 ASM 模組，但可以使用 「 AzureRm 「 使用 ARM 模型取代 「 Azure 」。 例如，`Publish-AzureVMDscConfiguration`使用 ASM，其中`Publish-AzureRmVMDscConfiguration`使用 ARM。 

`Publish-AzureVMDscConfiguration`設定檔案中所需的時間、 掃描它相依 DSC 資源，並建立包含的設定和制訂設定所需的 DSC 資源的.zip 檔案。 它也可以建立本機使用套件`-ConfigurationArchivePath`參數。 否則，它會將.zip 檔案發佈至 Azure blob 儲存體並保護其安全性與 SA 權杖。

這個指令程式所建立的.zip 檔案具有.ps1 設定指令碼在 [封存] 資料夾的根目錄。 資源必須放在 [封存] 資料夾中的 [模組] 資料夾。 

`Set-AzureVMDscExtension`插入到 VM 設定物件]，然後可套用到使用 Azure VM PowerShell DSC 副檔名所需的設定`Update-AzureVM`。

`Get-AzureVMDscExtension`擷取特定 VM DSC 副檔名的狀態。 

`Get-AzureVMDscExtensionStatus`擷取准許 DSC 副檔名處理常式 DSC 設定的狀態。 在單一 VM 或 Vm 的群組，可以執行此動作。

`Remove-AzureVMDscExtension`從指定的虛擬機器中移除副檔名處理常式。 這個指令程式不會**移除設定，**解除安裝 WMF，或變更虛擬機器上套用的設定。 只會移除副檔名處理常式。 

**在 ASM 和 ARM cmdlet 的主要差異**

- ARM cmdlet 都是同步。 ASM cmdlet 是非同步。
- ResourceGroupName、 VMName、 ArchiveStorageAccountName、 版本及位置是所有新必要的參數。
- ArchiveResourceGroupName 是手臂新增選擇性參數。 當您儲存的帳戶屬於不同的資源群組的項目建立虛擬機器的位置，您可以指定此參數。
- ConfigurationArchive 稱為 ArchiveBlobName 於
- ContainerName 稱為 ArchiveContainerName 手臂中
- StorageEndpointSuffix 稱為 ArchiveStorageEndpointSuffix 手臂中
- AutoUpdate 切換已新增至 [啟用自動更新為最新版本，以及其時的副檔名處理常式 ARM。 放開 WMF 的新版本時，此參數有可能會導致綱要重新啟動 VM 上。 


## <a name="azure-portal-functionality"></a>Azure 入口網站的功能 ##
瀏覽至傳統 VM。 在 [設定]-> [一般按一下 「 延伸模組 」。 建立新的窗格。 按一下 [新增]，然後選取 PowerShell DSC。

入口網站需要輸入。
**設定模組或指令碼**︰ 此欄位是必要。 需要.ps1 包含指令的檔案設定或使用.ps1 設定指令碼根目錄，與.zip 內的模組資料夾中的所有相關資源.zip 檔案。 您可以使用建立`Publish-AzureVMDscConfiguration -ConfigurationArchivePath`Azure PowerShell SDK 中所包含的指令程式。 .Zip 檔案上傳到受保護的 SA 權杖您使用者 blob 儲存體。 

**設定資料 PSD1 檔案**︰ 此欄位是選擇性的。 如果您的設定需要.psd1 中的設定資料檔案，請使用此欄位來選取它，然後上傳至您的使用者 blob 儲存空間，其受到保護，SA 權杖。 
 
**Module-Qualified 名稱的設定**︰.ps1 檔案可以有多個設定函數。 輸入的名稱，後面再加上設定.ps1 指令碼的 「\'和設定函數的名稱。 例如，如果.ps1 指令碼有名稱 「 configuration.ps1 」，且設定為 「 IisInstall 」，您可以輸入︰`configuration.ps1\IisInstall`

**設定引數**︰ 如果設定函數的引數，輸入以下格式`argumentName1=value1,argumentName2=value2`。 請注意此格式會不同於透過 PowerShell cmdlet 或資源管理員範本接受設定引數的格式。 

## <a name="getting-started"></a>快速入門 ##

Azure DSC 副檔名 DSC 設定文件中所需的時間和實行這些潛在 Azure Vm 上。 設定簡單範例如下。 將其儲存至本機為 「 IisInstall.ps1 」:

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

下列步驟 IisInstall.ps1 指令碼置於指定 VM、 執行設定，並回報狀態。
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>記錄 ##

記錄會放置在︰

C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[版本號碼]

## <a name="next-steps"></a>後續步驟 ##

如需有關 PowerShell DSC，[請造訪 PowerShell 文件中心](https://msdn.microsoft.com/powershell/dsc/overview)的詳細資訊。 

檢查[DSC 副檔名 Azure 資源管理員範本](virtual-machines-windows-extensions-dsc-template.md
)。 

若要尋找的其他功能，您可以管理使用 PowerShell DSC，[更多 DSC 資源的 [[瀏覽 PowerShell 圖庫](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)。

將機密的參數傳遞到設定的詳細資訊，請參閱[安全地使用 DSC 副檔名處理常式的管理認證](virtual-machines-windows-extensions-dsc-credentials.md)。
