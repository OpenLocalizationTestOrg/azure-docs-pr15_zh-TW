<properties
   pageTitle="將認證傳送至 Azure 使用 DSC |Microsoft Azure"
   description="安全地使用所需的狀態設定 PowerShell 的 Azure 虛擬機器傳遞認證的概觀"
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

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>傳遞至 Azure DSC 副檔名處理的認證 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

本文涵蓋副檔名為所需的狀態設定為 Azure。 DSC 副檔名處理常式的概觀位於[Azure 所需的狀態設定副檔名處理常式的簡介](virtual-machines-windows-extensions-dsc-overview.md)。 


## <a name="passing-in-credentials"></a>在 [認證傳遞
設定程序的一部分您可能需要設定使用者帳戶，存取服務，或安裝程式在使用者內容中。 若要執行這些項目，您需要提供的認證。 

DSC 可讓參數化設定認證會傳送到設定與安全地儲存在 MOF 檔案。 Azure 副檔名處理常式提供的憑證自動管理，以簡化認證管理。 

請考慮下列 DSC 設定指令碼會建立本機的使用者帳戶以指定的密碼︰

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

請務必包含*節點本機*組態的一部分。 如果此陳述式，下列步驟不如副檔名處理常式特別看起來的節點本機陳述式。 務必也包含 typecast 的*[PsCredential]*中，為此特定類型的觸發程序來加密認證副檔名。 

發佈至 blob 儲存體的這個指令碼︰

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

設定 Azure DSC 分機號碼，並提供認證︰

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>如何保護認證
執行這個程式碼會提示輸入認證。 一旦提供，儲存在記憶體簡要。 何時使用發佈`Set-AzureVmDscExtension`cmdlet，會透過 HTTPS 的 vm 傳輸，、 Azure 位置將其儲存在磁碟上，使用本機 VM 憑證加密。 然後簡要解密記憶體中，並將它傳遞給 DSC 重新加密。

此行為是不同於[使用副檔名處理常式不安全的設定](https://msdn.microsoft.com/powershell/dsc/securemof)。 Azure 環境提供傳輸透過憑證安全地設定資料的方式。 在使用 DSC 副檔名處理常式時，就不需要提供 $CertificatePath 或 $CertificateID / $Thumbprint ConfigurationData] 中的項目。


## <a name="next-steps"></a>後續步驟 ##

如需有關 Azure DSC 副檔名處理常式的詳細資訊，請參閱[Azure 所需的狀態設定副檔名處理常式的簡介](virtual-machines-windows-extensions-dsc-overview.md)。 

檢查[DSC 副檔名 Azure 資源管理員範本](virtual-machines-windows-extensions-dsc-template.md)。

如需有關 PowerShell DSC，[請造訪 PowerShell 文件中心](https://msdn.microsoft.com/powershell/dsc/overview)的詳細資訊。 

若要尋找的其他功能，您可以管理使用 PowerShell DSC，[更多 DSC 資源的 [[瀏覽 PowerShell 圖庫](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)。
