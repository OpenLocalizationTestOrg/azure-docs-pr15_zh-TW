<properties
    pageTitle="VM 上安裝趨勢微深層安全性 |Microsoft Azure"
    description="本文將說明如何安裝和設定上建立使用傳統的部署模型中 Azure VM 趨勢微型安全性。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>如何安裝和設定趨勢微深層安全性為 Windows VM 的服務

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

本文將示範如何安裝和設定趨勢微深層安全性做為新的或現有虛擬機器 (VM) 執行 Windows Server 的服務。 以服務的深安全性包含反惡意程式碼防護、 防火牆、 侵入防止系統和監視完整性。

為安全性延伸透過 VM 代理程式已安裝的用戶端。 在新的虛擬機器中，您必須安裝 VM 代理程式以及深的安全性代理程式。 在 [現有的虛擬機器沒有 VM 代理程式的您需要下載並安裝第一次。 本文說明這兩個情況。

如果您有趨勢微從現有的訂閱的內部部署解決方案時，您可以使用它來協助保護您 Azure 虛擬機器。 如果您不客戶尚未，您可以註冊試用訂閱。 如需此方案的詳細資訊，請參閱趨勢微型部落格文章[Microsoft Azure VM 代理程式副檔名為深層安全性](http://go.microsoft.com/fwlink/p/?LinkId=403945)。

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>在 [新 VM 上安裝深層安全性代理程式

[Azure 傳統入口網站](http://manage.windowsazure.com)可讓您安裝 VM 代理程式和趨勢微安全性副檔名，當您使用 [**從圖庫**] 選項來建立虛擬機器。 如果您要建立單一虛擬機器，使用入口網站是從趨勢微新增保護簡單的方法。

這個選項，**從圖庫**會開啟，可協助您設定虛擬機器精靈。 您可以使用精靈的最後一頁，安裝 VM 代理程式和趨勢微安全性副檔名。 如需一般指示，請參閱[建立虛擬機器執行 Windows Azure 傳統入口網站中](virtual-machines-windows-classic-tutorial.md)。 當您收到的最後一個精靈頁面時，請執行下列動作︰

1.  在 [ **VM 代理程式**] 底下，核取**安裝 VM 代理程式**。

2.  在**安全性延伸模組**，核取**趨勢微深層安全性代理程式**。

    ![安裝 VM 代理程式和深的安全性代理程式](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  按一下 [建立虛擬機器核取記號。

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>在 [現有的 VM 上安裝深層安全性代理程式

若要在現有的 VM 安裝代理程式，您需要下列項目︰

- Azure PowerShell 模組、 版本 0.8.2 或更新版本，在您的本機電腦上已安裝。 您可以檢查 PowerShell 的 Azure 使用已安裝的版本**取得模組 azure | 格式化表格版本**] 命令。 如需相關指示與最新版本的連結，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 登入您的訂閱 Azure 使用`Add-AzureAccount`。

- 在目標虛擬機器上安裝 VM 代理程式的情況下。

首先，請確認已安裝 [VM 代理程式。 填入的雲端服務名稱和虛擬機器名稱，然後執行下列命令，在系統管理員層級 PowerShell 的 Azure 命令提示字元。 報價，包括內的所有項目取代 < 及 > 字元。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

如果您不知道雲端服務，虛擬機器名稱，請執行**取得 AzureVM**顯示您目前的訂閱中的所有虛擬機器該資訊。

如果 [**寫入主機**] 命令會傳回**True**，安裝 VM 代理程式。 如果會傳回**False**，請參閱的指示，張貼[VM 代理程式及副檔名-第 2 部分](http://go.microsoft.com/fwlink/p/?LinkId=403947)中的 Azure 的部落格的下載連結。

如果 VM 代理程式已安裝，請執行這些命令。

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>後續步驟

需要幾分鐘的時間，開始執行安裝時，代理程式。 之後，您需要啟動 [深層安全性虛擬機器上，讓它可以管理 「 深層安全性管理員。 請參閱下列內容以取得其他指示︰

- 關於此方案中，[針對 Microsoft Azure Instant-On 雲端安全性](http://go.microsoft.com/fwlink/?LinkId=404101)的趨勢上的文件
- 若要設定虛擬機器[範例 Windows PowerShell 指令碼](http://go.microsoft.com/fwlink/?LinkId=404100)
- 範例的[指示](http://go.microsoft.com/fwlink/?LinkId=404099)

## <a name="additional-resources"></a>其他資源

[如何登入執行 Windows Server 的虛擬機器]

[Azure VM 副檔名與功能]


<!--Link references-->
[如何登入執行 Windows Server 的虛擬機器]: virtual-machines-windows-classic-connect-logon.md
[Azure VM 副檔名與功能]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
