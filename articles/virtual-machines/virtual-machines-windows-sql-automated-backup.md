<properties
    pageTitle="SQL Server 虛擬機器 （資源管理員） 的自動備份 |Microsoft Azure"
    description="說明自動備份功能的 SQL Server 執行中 Azure 虛擬機器使用資源管理員。 "
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>SQL Server Azure 虛擬機器 （資源管理員） 中的自動備份

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-automated-backup.md)
- [傳統](virtual-machines-windows-classic-sql-automated-backup.md)

自動的備份會自動設定[受管理的備份到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)執行 SQL Server 標準 2014年或企業版 Azure VM 的所有現有和新資料庫。 這可讓您設定資料庫備份，以便利用長期 Azure blob 儲存體。 自動的備份取決於[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-sql-server-agent-extension.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。 若要檢視精簡版本的本文，請參閱[自動備份的傳統 Azure 虛擬機器中的 SQL Server](virtual-machines-windows-classic-sql-automated-backup.md)。

## <a name="prerequisites"></a>必要條件

若要使用自動備份，請考慮下列先決條件︰

**作業系統**︰

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本版本**︰

- SQL Server 2014 標準
- SQL Server 2014 企業版

**資料庫設定**︰

- 目標資料庫必須使用完整復原模式

**Azure PowerShell**:

- 如果您打算使用 PowerShell 設定自動備份，請[安裝最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)。

>[AZURE.NOTE] 自動的備份依賴 SQL Server IaaS 代理程式副檔名。 目前的 SQL 虛擬機器庫圖像預設新增至此分機]。 如需詳細資訊，請參閱[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="settings"></a>設定

下表描述可設定自動備份的選項。 實際的設定步驟，視您使用的是 Azure 入口網站或 Windows PowerShell 的 Azure 命令而有所不同。

|設定|範圍 （預設值）|描述|
|---|---|---|
|**自動的備份**|啟用或停用 （已停用）|啟用或停用自動備份的執行 SQL Server 標準 2014年或企業版 Azure VM。|
|**保留期間**|1-30 天 （30 天）|若要保留備份天數。|
|**儲存帳戶**|Azure 儲存體帳戶 （建立指定 vm 儲存帳戶）|Azure 儲存以用於將自動備份檔案儲存在 blob 儲存體帳戶。 容器會建立在此儲存備份的所有檔案的位置。 備份檔案命名慣例包含日期、 時間及電腦名稱。|
|**加密**|啟用或停用 （已停用）|啟用或停用加密。 啟用加密時，若要還原的備份的憑證位於使用相同的命名慣例相同 automaticbackup 容器中的指定的儲存帳戶。 如果變更密碼，以該密碼，系統會產生新的憑證，但舊的憑證，仍還原先前的備份。|
|**密碼**|密碼文字 （無）|加密金鑰的密碼。 這只是必要如果啟用加密。 還原加密的備份，您必須正確的密碼，在備份的時所使用的相關的憑證。|

## <a name="configuration-in-the-portal"></a>在入口網站的設定
若要設定自動備份期間佈建或現有的 Vm，您可以使用 [Azure 入口網站。

### <a name="new-vms"></a>新的 Vm
若要設定自動備份，當您在資源管理員部署模型中建立新的 SQL Server 2014 虛擬機器中使用 Azure 入口網站。

在 [ **SQL Server 設定**刀中，選取 [**自動備份**]。 下列 Azure 入口網站的螢幕擷取畫面顯示**SQL 自動備份**刀。

![Azure 入口網站中的 SQL 自動備份設定](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

針對的內容，請參閱完成的主題上[佈建 Azure 中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="existing-vms"></a>現有 Vm
針對現有的 SQL Server 虛擬機器中，選取 [SQL Server 虛擬機器]。 然後選取 [ **SQL Server 組態**] 區段中的**設定**刀。

![針對現有 Vm SQL 自動備份](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

在**SQL Server 組態**刀中，按一下 [自動備份] 區段中的 [**編輯**] 按鈕。

![針對現有 Vm 設定 SQL 自動備份](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

完成時，按一下 [**確定**] 按鈕下方的**SQL Server 組態**刀，以儲存變更。

如果您第一次啟用自動備份，Azure 設定 SQL Server IaaS 代理程式在背景中。 在此期間，Azure 入口網站可能不會顯示該設定自動備份。 請等候幾分鐘的時間代理程式安裝、 設定。 之後 Azure 入口網站會反映新的設定。

>[AZURE.NOTE] 您也可以設定自動備份使用的範本。 如需詳細資訊，請參閱[Azure 快速入門範本自動備份](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)。

## <a name="configuration-with-powershell"></a>使用 PowerShell 設定

佈建之後您 SQL VM，使用 PowerShell 來設定自動備份。

在下列 PowerShell 範例中，現有的 SQL Server 2014 VM 設定自動備份。 **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig**命令設定自動備份設定以備份儲存在虛擬機器相關聯的 Azure 儲存體帳戶。 將保留這些備份，10 天。 **設定 AzureRmVMSqlServerExtension** ] 命令會更新指定的 Azure VM，利用這些設定。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

可能需要幾分鐘的時間安裝和設定 SQL Server IaaS 代理程式。

若要啟用加密，修改傳遞**EnableEncryption**參數，以及**CertificatePassword**參數的密碼 （安全字串） 的上一個指令碼。 下列指令碼自動備份設定在先前範例中，並新增加密。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

若要停用自動備份，請執行相同的指令碼，而不**-啟用** **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig**命令參數。 如果沒有**-啟用**參數表示要停用此功能的命令。 如同安裝時，它可能需要幾分鐘，停用自動備份。

>[AZURE.NOTE] 移除 SQL Server IaaS 代理程式並不會移除先前已設定自動備份設定。 您應該先停用或解除安裝 SQL Server IaaS 代理程式來停用自動備份。

## <a name="next-steps"></a>後續步驟

自動的備份 Azure Vm 設定受管理的備份。 因此請務必要[檢閱的文件，受管理的備份](https://msdn.microsoft.com/library/dn449496.aspx)瞭解行為，以及的含意。

您可以尋找其他的備份與還原 SQL Server Azure Vm 上指導在下列主題︰[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-backup-recovery.md)。

其他可用的自動化工作的相關資訊，請參閱[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-sql-server-agent-extension.md)。

如需有關如何執行 SQL Server Azure Vm 的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
