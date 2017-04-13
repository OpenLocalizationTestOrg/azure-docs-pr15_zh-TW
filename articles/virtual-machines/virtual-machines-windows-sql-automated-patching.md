<properties
    pageTitle="自動修正的 SQL Server Vm （資源管理員） |Microsoft Azure"
    description="說明自動修正功能的 SQL Server 執行的虛擬機器中 Azure 使用資源管理員。"
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
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>自動化修補 Azure 虛擬機器 （資源管理員） 中的 SQL Server

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-sql-automated-patching.md)
- [傳統](virtual-machines-windows-classic-sql-automated-patching.md)

自動化的 Patching 建立與維護視窗的 Azure 虛擬機器執行 SQL Server。 自動的更新只能安裝在此進行的維修作業] 視窗。 SQL server，此 rescriction 可確保系統更新與任何相關聯的重新啟動發生時最可能的資料庫。 自動化的 Patching 取決於[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-sql-server-agent-extension.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。 若要檢視精簡版本的本文，請參閱[自動修補傳統 Azure 虛擬機器中的 SQL Server](virtual-machines-windows-classic-sql-automated-patching.md)。

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

- 如果您打算使用 PowerShell 來設定自動修正，請[安裝最新的 PowerShell 的 Azure 命令](../powershell-install-configure.md)。

>[AZURE.NOTE] 自動化的 Patching 依賴 SQL Server IaaS 代理程式副檔名。 目前的 SQL 虛擬機器庫圖像預設新增至此分機]。 如需詳細資訊，請參閱[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="settings"></a>設定

下表說明可設定為自動修正的選項。 實際的設定步驟，視您使用的是 Azure 入口網站或 Windows PowerShell 的 Azure 命令而有所不同。

|設定|可能的值|描述|
|---|---|---|
|**自動修正**|啟用或停用 （已停用）|啟用或停用自動修正 Azure 虛擬機器。|
|**進行的維修作業排程**|每天，星期一、 星期二、 星期三、 星期四、 星期五、 星期六、 星期日|下載並安裝 Windows、 SQL Server，與 Microsoft 更新您的虛擬機器排程。|
|**維護開始小時**|0 24|若要更新虛擬機器本機的開始時間。|
|**進行的維修作業] 視窗的持續時間**|30 180|允許完成下載並安裝更新的分鐘數。|
|**修補程式類別**|重要|若要下載並安裝更新的類別。|

## <a name="configuration-in-the-portal"></a>在入口網站的設定
若要設定自動修正期間佈建或現有的 Vm，您可以使用 [Azure 入口網站。

### <a name="new-vms"></a>新的 Vm
若要設定自動修正資源管理員部署模型中建立新的 SQL Server 虛擬機器時使用 Azure 入口網站。

在**SQL Server 設定**刀中，選取 [**自動修正**。 下列 Azure 入口網站的螢幕擷取畫面顯示**自動修正 SQL**刀。

![SQL 自動化修補 Azure 入口網站中](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

針對的內容，請參閱完成的主題上[佈建 Azure 中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="existing-vms"></a>現有 Vm
針對現有的 SQL Server 虛擬機器中，選取 [SQL Server 虛擬機器]。 然後選取 [ **SQL Server 組態**] 區段中的**設定**刀。

![SQL 自動修補現有 Vm](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

在**SQL Server 組態**刀中，按一下 [自動修正] 區段中的 [**編輯**] 按鈕。

![設定現有 Vm SQL 自動修正](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

完成時，按一下 [**確定**] 按鈕下方的**SQL Server 組態**刀，以儲存變更。

如果您要啟用自動修正第一次，Azure 設定 SQL Server IaaS 代理程式在背景中。 在這段時間，Azure 入口網站可能不會顯示該設定自動修正。 請等候幾分鐘的時間代理程式安裝、 設定。 之後 Azure 入口網站會反映新的設定。

>[AZURE.NOTE] 您也可以設定自動修正使用的範本。 如需詳細資訊，請參閱[Azure 快速入門範本自動修正](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update)。

## <a name="configuration-with-powershell"></a>使用 PowerShell 設定

佈建之後您 SQL VM，使用 PowerShell 來設定自動修正。

在下列範例中，PowerShell 用來設定 [現有的 SQL Server VM 中的 [自動修正。 **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**命令設定新的 [進行的維修作業] 視窗，自動更新。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

根據此範例中下, 表說明目標 Azure VM 效果︰

|參數|效果|
|---|---|
|**DayOfWeek**|每個星期四安裝修補程式。|
|**MaintenanceWindowStartingHour**|開始在 11:00 am 會更新。|
|**MaintenanceWindowsDuration**|120 分鐘內，必須安裝更新。 根據開始的時間，他們必須完成 1:00 pm。|
|**PatchCategory**|這個參數的唯一可能的設定，則**重要事項**。|

可能需要幾分鐘的時間安裝和設定 SQL Server IaaS 代理程式。

若要停用自動修補程式，執行相同的指令碼，而不**-啟用** **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**參數。 如果沒有**-啟用**參數表示要停用此功能的命令。

## <a name="next-steps"></a>後續步驟

其他可用的自動化工作的相關資訊，請參閱[SQL Server IaaS 代理程式副檔名](virtual-machines-windows-sql-server-agent-extension.md)。

如需有關如何執行 SQL Server Azure Vm 的詳細資訊，請參閱[SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)。
