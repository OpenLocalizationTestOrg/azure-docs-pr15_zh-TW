<properties
    pageTitle="在 [Azure Vm （傳統） 上設定 SQL Server Azure 金鑰保存庫的整合"
    description="瞭解如何將自動化的 SQL Server 加密用於 Azure 金鑰保存庫設定。 本主題說明如何使用 SQL Server 傳統部署模型中建立虛擬機器中的 Azure 金鑰保存庫整合。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-classic"></a>在 [Azure Vm （傳統） 上設定 SQL Server Azure 金鑰保存庫的整合

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-ps-sql-keyvault.md)
- [傳統](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>概觀
有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級的加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)和[備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 這些表單的加密要求您管理並儲存您使用加密加密金鑰。 Azure 鍵保存庫 (AKV) 服務被為了改善的安全性與管理這些按鍵安全和高度可用的位置。 [SQL Server 連接器](http://www.microsoft.com/download/details.aspx?id=45344)可讓 SQL Server 使用從 Azure 金鑰保存庫這些按鍵。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

如果您正在執行 SQL Server 與內部部署電腦，有[存取您內部部署的 SQL Server 電腦從 Azure 金鑰保存庫時，可遵循的步驟](https://msdn.microsoft.com/library/dn198405.aspx)。 但的 SQL Server Azure Vm 中，您可以使用*Azure 金鑰保存庫整合*功能來節省時間。 與一些 Azure PowerShell cmdlet，以啟用此功能，您可以自動化所需的 SQL VM 來存取您的主要保存庫設定。

啟用此功能時，它會自動安裝 SQL Server 連接器、 設定 EKM 提供者，若要存取 Azure 金鑰保存庫，並建立可讓您存取您保存庫認證。 如果您看先前所述的內部部署文件中的步驟，就可以看見此功能會自動執行步驟 2 和 3。 您仍需要手動執行的唯一項目是，建立關鍵保存庫與索引鍵。 從這裡被自動您 SQL VM 整個安裝。 這項功能已完成此設定，您可以執行 T SQL 陳述式以開始加密您的資料庫或備份，亦即。

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>設定 AKV 整合
使用 PowerShell 來設定 Azure 金鑰保存庫整合。 下列各節提供必要的參數，然後範例 PowerShell 指令碼的概觀。

### <a name="install-the-sql-server-iaas-extension"></a>安裝 SQL Server IaaS 副檔名

首先，[安裝 SQL Server IaaS 副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

### <a name="understand-the-input-parameters"></a>了解輸入的參數
下表列出所需執行的 PowerShell 指令碼在下一節中的參數。

|參數|描述|範例|
|---|---|---|
|**$akvURL**|**索引鍵保存庫 URL**|「 https://contosokeyvault.vault.azure.net/ 」|
|**$spName**|**服務主要名稱**|「 fde2b411-33 d 5-4e11-af04eb07b669ccf2 」|
|**$spSecret**|**服務主要密碼**|「 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM = 」|
|**$credName**|**認證名稱**︰ AKV 整合建立 SQL Server，讓存取金鑰保存庫 VM 內認證。 選擇此認證的名稱。|「 mycred1 」|
|**$vmName**|**虛擬機器名稱**︰ 先前建立的 SQL VM 的名稱。|「 myvmname 」|
|**$serviceName**|**服務名稱**︰ SQL VM 與相關聯的雲端服務名稱。|「 mycloudservicename 」|

### <a name="enable-akv-integration-with-powershell"></a>使用 PowerShell AKV 整合
**新增 AzureVMSqlServerKeyVaultCredentialConfig** cmdlet 設定為建立物件 Azure 金鑰保存庫整合功能。 **設定 AzureVMSqlServerExtension**設定此整合和**KeyVaultCredentialSettings**參數。 下列步驟說明如何使用這些命令。

1. PowerShell 的 Azure 中先輸入的參數以設定特定值這個主題的各節所述。 下列指令碼是範例。

        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2.  設定，並啟用 AKV 整合，然後使用下列指令碼。

        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 代理程式副檔名就會更新 SQL VM 以這個新的設定。

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
