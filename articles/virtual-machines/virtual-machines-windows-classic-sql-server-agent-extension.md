<properties
    pageTitle="SQL Server Vm （傳統） 的 SQL Server 代理程式副檔名 |Microsoft Azure"
    description="本主題說明如何管理 SQL Server 代理程式延伸，會自動執行特定的 SQL Server 管理工作。 包括自動備份、 自動修正和 Azure 金鑰保存庫整合。 本主題會使用傳統的部署模式。"
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
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>SQL Server Vm （傳統） 的 SQL Server 代理程式副檔名

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-server-agent-extension.md)
- [傳統](virtual-machines-windows-classic-sql-server-agent-extension.md)

若要自動化管理工作的 Azure 虛擬機器上，執行 SQL Server IaaS 代理程式副檔名 (SQLIaaSAgent)。 本主題提供支援的分機號碼，以及指示安裝、 狀態及移除服務的概觀。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]若要檢視資源管理員版本的本文，請參閱[SQL Server 代理程式副檔名的 SQL Server Vm 資源管理員](virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="supported-services"></a>支援的服務

SQL Server IaaS 代理程式延伸支援下列管理工作︰

| 管理功能 | 描述 |
|---------------------|-------------------------------|
| **SQL 自動備份** | 自動排程備份所有資料庫的預設的執行個體中 VM 的 SQL Server。 如需詳細資訊，請參閱[自動備份的 SQL Server 中 Azure 虛擬機器 （傳統）](virtual-machines-windows-classic-sql-automated-backup.md)。|
| **SQL 自動修正** | 設定期間您 VM 更新可以進行，讓您避免發生更新您的工作負載的最大使用量時間進行的維修作業視窗。 如需詳細資訊，請參閱[自動修正的 SQL Server 中 Azure 虛擬機器 （傳統）](virtual-machines-windows-classic-sql-automated-patching.md)。|
| **Azure 鍵保存庫整合** | 可讓您自動安裝並設定您的 SQL Server VM Azure 金鑰保存庫。 如需詳細資訊，請參閱[適用於 SQL Server Azure Vm （傳統） 上設定 Azure 金鑰保存庫整合](virtual-machines-windows-classic-ps-sql-keyvault.md)。|

## <a name="prerequisites"></a>必要條件

在您 VM 使用 SQL Server IaaS 代理程式副檔名需求︰

### <a name="operating-system"></a>作業系統︰

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>SQL Server 版本︰

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:

[下載並設定的最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)。

啟動 Windows PowerShell，並將其連線至您 Azure 的訂閱，使用 [**新增 AzureAccount** ] 命令。

    Add-AzureAccount

如果您有多個訂閱，請使用**選取 AzureSubscription**選取包含您的目標訂閱傳統 VM。

    Select-AzureSubscription -SubscriptionName <subscriptionname>

此時，您可以取得傳統的虛擬機器及其相關聯的服務名稱，以**取得 AzureVM**命令的清單。

    Get-AzureVM

## <a name="installation"></a>安裝

傳統 Vm，則必須使用 PowerShell 安裝 SQL Server IaaS 代理程式副檔名與設定相關聯的服務。 您可以使用**設定 AzureVMSqlServerExtension** PowerShell cmdlet 來安裝副檔名。 例如，下列命令安裝在 Windows Server VM （傳統） 的副檔名為和名稱，「 SQLIaaSExtension 」。

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

如果您更新為最新版本的 SQL IaaS 代理副檔名，您必須重新虛擬機器後更新副檔名。

>[AZURE.NOTE] 傳統的虛擬機器沒有安裝和設定 SQL IaaS 代理程式副檔名為透過入口網站的選項。

## <a name="status"></a>狀態

若要確認已安裝副檔名為其中一個方法是 Azure 入口網站中檢視代理程式狀態。 在虛擬機器刀中，選取 [**所有設定**，然後按一下 [**延伸**]。 您應該會看到列出**SQLIaaSAgent**副檔名。

![Azure 入口網站中的 SQL Server IaaS 代理程式副檔名](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

您也可以使用**取得 AzureVMSqlServerExtension** Azure Powershell 指令程式。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>移除   

在 Azure 入口網站，您可以將副檔名為解除安裝的虛擬機器屬性**擴充**刀上的省略符號，即可。 然後按一下 [**刪除**]。

![解除安裝 SQL Server IaaS 代理程式副檔名為 Azure 入口網站中](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

您也可以使用**移除 AzureVMSqlServerExtension** Powershell 指令程式。

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>後續步驟

開始使用其中一個副檔名為支援服務。 如需詳細資訊，請參閱本文的[支援服務](#supported-services)] 區段中的參照。

如需有關如何執行 SQL Server Azure 虛擬機器上的商務連絡人的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
