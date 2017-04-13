<properties
    pageTitle="自動修正的 SQL Server Vm （傳統） |Microsoft Azure"
    description="說明自動修正功能的 SQL Server 執行的虛擬機器中 Azure 使用傳統的部署模式。"
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

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>自動化修補 Azure 虛擬機器 （傳統） 中的 SQL Server

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-automated-patching.md)
- [傳統](virtual-machines-windows-classic-sql-automated-patching.md)

自動化的 Patching 建立與維護視窗的 Azure 虛擬機器執行 SQL Server。 自動的更新只能安裝在此進行的維修作業] 視窗。 SQL server，這可確保系統更新與任何相關聯的重新啟動發生時最可能的資料庫。 自動化的 Patching 取決於[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]若要檢視資源管理員版本的本文，請參閱[自動修正的 SQL Server Azure 虛擬機器資源管理員] 中](virtual-machines-windows-sql-automated-patching.md)。

## <a name="prerequisites"></a>必要條件

若要使用自動化修補程式，請考慮下列先決條件︰

**作業系統**︰

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本**︰

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [安裝最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)。

**SQL Server IaaS 副檔名**︰

- [安裝 SQL Server IaaS 副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

## <a name="settings"></a>設定

下表說明可設定為自動修正的選項。 傳統 Vm，則必須使用 PowerShell 來設定這些設定。

|設定|可能的值|描述|
|---|---|---|
|**自動修正**|啟用或停用 （已停用）|啟用或停用自動修正 Azure 虛擬機器。|
|**進行的維修作業排程**|每天，星期一、 星期二、 星期三、 星期四、 星期五、 星期六、 星期日|下載並安裝 Windows、 SQL Server，與 Microsoft 更新您的虛擬機器排程。|
|**維護開始小時**|0 24|若要更新虛擬機器本機的開始時間。|
|**進行的維修作業] 視窗的持續時間**|30 180|允許完成下載並安裝更新的分鐘數。|
|**修補程式類別**|重要|若要下載並安裝更新的類別。|

## <a name="configuration-with-powershell"></a>使用 PowerShell 設定

在下列範例中，PowerShell 用來設定 [現有的 SQL Server VM 中的 [自動修正。 **新增 AzureVMSqlServerAutoPatchingConfig**命令設定新的 [進行的維修作業] 視窗，自動更新。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

根據此範例中下, 表說明目標 Azure VM 效果︰

|參數|效果|
|---|---|
|**DayOfWeek**|每個星期四安裝修補程式。|
|**MaintenanceWindowStartingHour**|開始在 11:00 am 會更新。|
|**MaintenanceWindowsDuration**|120 分鐘內，必須安裝更新。 根據開始的時間，他們必須完成 1:00 pm。|
|**PatchCategory**|這個參數的唯一可能的設定，則 「 重要 」。|

可能需要幾分鐘的時間安裝和設定 SQL Server IaaS 代理程式。

若要停用自動修補程式，請執行相同指令碼不新增 AzureVMSqlServerAutoPatchingConfig-啟用參數。 如同安裝時，它可能需要幾分鐘，停用自動修補程式。

## <a name="next-steps"></a>後續步驟

其他可用的自動化工作的相關資訊，請參閱[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-classic-sql-server-agent-extension.md)。

如需有關如何執行 SQL Server Azure Vm 的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
