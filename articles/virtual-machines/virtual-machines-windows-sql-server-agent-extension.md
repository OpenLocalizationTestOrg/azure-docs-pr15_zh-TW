<properties
    pageTitle="SQL Server Vm （資源管理員） 的 SQL Server 代理程式副檔名 |Microsoft Azure"
    description="本主題說明如何管理 SQL Server 代理程式延伸，會自動執行特定的 SQL Server 管理工作。 包括自動備份、 自動修正和 Azure 金鑰保存庫整合。 本主題會使用資源管理員部署模式。"
    services="virtual-machines-windows"
    documentationCenter=""
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
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>SQL Server Vm （資源管理員） 的 SQL Server 代理程式副檔名

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-server-agent-extension.md)
- [傳統](virtual-machines-windows-classic-sql-server-agent-extension.md)

若要自動化管理工作的 Azure 虛擬機器上，執行 SQL Server IaaS 代理程式副檔名 (SQLIaaSExtension)。 本主題提供支援的分機號碼，以及指示安裝、 狀態及移除服務的概觀。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。 若要檢視精簡版本的本文，請參閱[SQL Server 代理程式的副檔名 SQL Server 傳統 Vm](virtual-machines-windows-classic-sql-server-agent-extension.md)。

## <a name="supported-services"></a>支援的服務

SQL Server IaaS 代理程式延伸支援下列管理工作︰

| 管理功能 | 描述 |
|---------------------|-------------------------------|
| **SQL 自動備份** | 自動排程備份所有資料庫的預設的執行個體中 VM 的 SQL Server。 如需詳細資訊，請參閱[自動備份的 SQL Server 中 Azure 虛擬機器 （資源管理員）](virtual-machines-windows-sql-automated-backup.md)。|
| **SQL 自動修正** | 設定進行的維修作業] 視窗的更新您 VM 可能需要的位置，因此可避免在您工作負載的尖峰期間的更新。 如需詳細資訊，請參閱[自動修正的 SQL Server 中 Azure 虛擬機器 （資源管理員）](virtual-machines-windows-sql-automated-patching.md)。|
| **Azure 鍵保存庫整合** | 可讓您自動安裝並設定您的 SQL Server VM Azure 金鑰保存庫。 如需詳細資訊，請參閱[適用於 SQL Server Azure Vm （資源管理員） 上設定 Azure 金鑰保存庫整合](virtual-machines-windows-ps-sql-keyvault.md)。|

## <a name="prerequisites"></a>必要條件

在您 VM 使用 SQL Server IaaS 代理程式副檔名需求︰

**作業系統**︰

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本**︰

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [下載並設定的最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)

## <a name="installation"></a>安裝

當您提供的其中一個 SQL Server 虛擬機器庫圖像時，會自動安裝 SQL Server IaaS 代理程式副檔名。

如果您建立 OS 專用的 Windows Server 的虛擬機器時，您可以使用**設定 AzureVMSqlServerExtension** PowerShell 指令程式來手動安裝副檔名。 例如，下列命令 OS 專用 Windows Server VM 上安裝副檔名為和其名稱為 「 SQLIaaSExtension 」。

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

如果您更新為最新版本的 SQL IaaS 代理副檔名，您必須重新虛擬機器後更新副檔名。

>[AZURE.NOTE] 如果您以手動方式在 Windows Server VM 安裝 SQL Server IaaS 代理副檔名，您必須使用及管理其使用 PowerShell 命令的功能。 入口網站的介面是僅適用於 SQL Server] 圖庫的圖像。

## <a name="status"></a>狀態

若要確認已安裝副檔名為其中一個方法是 Azure 入口網站中檢視代理程式狀態。 在虛擬機器刀中，選取 [**所有設定**，然後按一下 [**延伸**]。 您應該會看到列出**SQLIaaSExtension**副檔名。

![Azure 入口網站中的 SQL Server IaaS 代理程式副檔名](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

您也可以使用**取得 AzureVMSqlServerExtension** Azure Powershell 指令程式。

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

代理程式已安裝，並提供一般狀態資訊，向您確認前一個命令。 您也可以取得自動備份和 Patching 相關的特定的狀態資訊，並輸入下列命令。

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>移除   

在 Azure 入口網站，您可以解除副檔名為安裝的虛擬機器屬性**擴充**刀上的省略符號，即可。 然後按一下 [**刪除**]。

![解除安裝 SQL Server IaaS 代理程式副檔名為 Azure 入口網站中](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

您也可以使用**移除 AzureRmVMSqlServerExtension** Powershell 指令程式。

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>後續步驟

開始使用其中一個副檔名為支援服務。 如需詳細資訊，請參閱本文的[支援服務](#supported-services)] 區段中的參照。

如需有關如何執行 SQL Server Azure 虛擬機器上的商務連絡人的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
