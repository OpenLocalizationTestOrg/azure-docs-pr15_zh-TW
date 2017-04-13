<properties
    pageTitle="SQL Server 虛擬機器 （傳統） 的自動備份 |Microsoft Azure"
    description="說明自動備份功能的 SQL Server 執行中 Azure 虛擬機器使用資源管理員。 "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>SQL server Azure 虛擬機器 （傳統） 中的自動的備份

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-automated-backup.md)
- [傳統](virtual-machines-windows-classic-sql-automated-backup.md)

自動的備份會自動設定[受管理的備份到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)執行 SQL Server 標準 2014年或企業版 Azure VM 的所有現有和新資料庫。 這可讓您設定資料庫備份，以便利用長期 Azure blob 儲存體。 自動的備份取決於[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]若要檢視資源管理員版本的本文，請參閱[自動備份的 SQL Server Azure 虛擬機器資源管理員] 中](virtual-machines-windows-sql-automated-backup.md)。

## <a name="prerequisites"></a>必要條件

若要使用自動備份，請考慮下列先決條件︰

**作業系統**︰

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本版本**︰

- SQL Server 2014 標準
- SQL Server 2014 企業版

>[AZURE.NOTE] SQL Server 2016 還不支援自動備份。

**資料庫設定**︰

- 目標資料庫必須使用完整復原模式。

**Azure PowerShell**:

- [安裝最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)。

**SQL Server IaaS 副檔名**︰

- [安裝 SQL Server IaaS 副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

## <a name="settings"></a>設定

下表描述可設定自動備份的選項。 傳統 Vm，則必須使用 PowerShell 來設定這些設定。

|設定|範圍 （預設值）|描述|
|---|---|---|
|**自動的備份**|啟用或停用 （已停用）|啟用或停用自動備份的執行 SQL Server 標準 2014年或企業版 Azure VM。|
|**保留期間**|1-30 天 （30 天）|若要保留備份天數。|
|**儲存帳戶**|Azure 儲存體帳戶 （建立指定 vm 儲存帳戶）|Azure 儲存以用於將自動備份檔案儲存在 blob 儲存體帳戶。 容器會建立在此儲存備份的所有檔案的位置。 備份檔案命名慣例包含日期、 時間及電腦名稱。|
|**加密**|啟用或停用 （已停用）|啟用或停用加密。 啟用加密時，若要還原的備份的憑證位於使用相同的命名慣例相同 automaticbackup 容器中的指定的儲存帳戶。 如果變更密碼，以該密碼，系統會產生新的憑證，但舊的憑證，仍還原先前的備份。|
|**密碼**|密碼文字 （無）|加密金鑰的密碼。 這只是必要如果啟用加密。 還原加密的備份，您必須正確的密碼，在備份的時所使用的相關的憑證。|

## <a name="configuration-with-powershell"></a>使用 PowerShell 設定

在下列 PowerShell 範例中，現有的 SQL Server 2014 VM 設定自動備份。 **新增 AzureVMSqlServerAutoBackupConfig**命令設定自動備份設定以備份儲存在 $storageaccount 變數所指定的 Azure 儲存體帳戶。 將保留這些備份，10 天。 **設定 AzureVMSqlServerExtension** ] 命令會更新指定的 Azure VM，利用這些設定。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

可能需要幾分鐘的時間安裝和設定 SQL Server IaaS 代理程式。

若要啟用加密，修改傳遞 EnableEncryption 參數，以及 CertificatePassword 參數的密碼 （安全字串） 的上一個指令碼。 下列指令碼自動備份設定在先前範例中，並新增加密。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

若要停用自動備份，請執行相同的指令碼，而不**-啟用****新增 AzureVMSqlServerAutoBackupConfig**參數。 如同安裝時，它可能需要幾分鐘，停用自動備份。

>[AZURE.NOTE] 停用和解除安裝 SQL Server IaaS 代理程式不會移除先前管理備份的設定。 您應該先停用或解除安裝 SQL Server IaaS 代理程式來停用自動備份。

## <a name="next-steps"></a>後續步驟

自動的備份 Azure Vm 設定受管理的備份。 因此請務必要[檢閱的文件，受管理的備份](https://msdn.microsoft.com/library/dn449496.aspx)瞭解行為，以及的含意。

您可以尋找其他的備份與還原 SQL Server Azure Vm 上指導在下列主題︰[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-backup-recovery.md)。

其他可用的自動化工作的相關資訊，請參閱[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

如需有關如何執行 SQL Server Azure Vm 的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
