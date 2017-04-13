<properties
    pageTitle="重設密碼或遠端桌面設定 Windows VM |Microsoft Azure"
    description="瞭解如何重設帳戶密碼或 Windows VM 使用 Azure PowerShell 的 Azure 入口網站上的遠端桌面服務。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>如何重設的遠端桌面服務或在 Windows VM 其登入密碼

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果您無法連線到 Windows 虛擬機器 (VM)，您可以重設本機系統管理員的密碼，或重設遠端桌面服務設定。 您可以使用 [在 PowerShell 的 Azure Azure 入口網站或 VM 存取副檔名，重設密碼。 如果您使用 PowerShell，請確定您有工作電腦上安裝最新的 PowerShell 模組和 Azure 訂閱登入。 如需詳細步驟，瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

> [AZURE.TIP] 您可以檢查 PowerShell 使用已安裝的版本`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>資源管理員部署模型中的 Windows Vm

### <a name="azure-portal"></a>Azure 入口網站
按一下 [**瀏覽]**，選取您 VM > **虛擬機器** > *Windows 虛擬機器* > **所有設定** > **重設密碼**。 密碼重設刀會顯示︰

![密碼重設頁面](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

輸入使用者名稱和新密碼，然後按一下 [**儲存**]。 再次嘗試連線到您 VM。

### <a name="vmaccess-extension-and-powershell"></a>VMAccess 副檔名與 PowerShell

請確定您有 Azure PowerShell 1.0 安裝或更新版本，且您有登入您的帳戶使用`Login-AzureRmAccount`指令程式。

#### <a name="reset-the-local-administrator-account-password"></a>**重設本機系統管理員帳戶密碼**

您可以使用的[設定 AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell 命令，以重設管理員密碼或使用者名稱。

使用下列命令來建立您的本機管理員帳戶認證︰

    $cred=Get-Credential

如果您輸入不同的名稱與目前的帳戶時，下列 VMAccess 副檔名命令重新命名本機系統管理員帳戶會指派給該帳戶的密碼與遠端桌面登出事件中發生的問題。 如果本機系統管理員帳戶已停用，VMAccess 擴充功能可讓它。

使用 VM access 副檔名為新的認證，如下所示︰

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


取代`myRG`， `myVM`， `myVMAccess`，和您設定的相關值的位置。


#### <a name="reset-the-remote-desktop-service-configuration"></a>**重設遠端桌面服務設定**

您可以重設遠端存取您 VM 藉由[設定 AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx)或[設定 AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx)，，如下所示。 (取代`myRG`， `myVM`，`myVMAccess`和位置，使用您自己的值。)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

或者︰<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] 兩個命令會將虛擬機器中的新命名的 VM 存取代理程式。 在任何點，VM 可能只單一 VM 存取代理程式。 若要順利設定 VM 存取代理程式屬性，移除使用先前設定的存取代理程式`Remove-AzureRmVMAccessExtension`或`Remove-AzureRmVMExtension`。 從 Azure PowerShell 版本 1.2.2 開始，避免發生此步驟中使用時`Set-AzureRmVMExtension`與`-ForceRerun`選項。 使用時`-ForceRerun`，請務必使用所設定的 VM 存取代理程式的相同名稱的前一個命令。

如果您仍然無法連線遠端到您的虛擬機器，請參閱其他步驟來嘗試[疑難排解遠端桌面](virtual-machines-windows-troubleshoot-rdp-connection.md)連線到 windows Azure 虛擬機器。


## <a name="windows-vms-in-the-classic-deployment-model"></a>在傳統的部署模型中的 Windows Vm

### <a name="azure-portal"></a>Azure 入口網站

建立使用傳統的部署模型的虛擬機器，您可以重設遠端桌面服務使用[Azure 入口網站](https://portal.azure.com)。 按一下 [: 可讓您**瀏覽** > **（傳統） 的虛擬機器** > *Windows 虛擬機器* > **重設遠端...**。 下列頁面隨即出現。

![重設 RDP 設定] 頁面](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

您也可以嘗試的名稱與本機系統管理員帳戶的密碼重設。 按一下 [: 可讓您**瀏覽** > **（傳統） 的虛擬機器** > *Windows 虛擬機器* > **所有設定** > **重設密碼**。 下列頁面隨即出現。

![密碼重設頁面](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

輸入新的使用者名稱和密碼後，按一下 [**儲存**]。

### <a name="vmaccess-extension-and-powershell"></a>VMAccess 副檔名與 PowerShell

請確定 VM 代理程式已安裝虛擬機器上。 VMAccess 副檔名不需要先安裝您可以使用，只要有 VM 代理程式。 確認使用下列命令已安裝 VM 代理程式。 （取代 「 myCloudService 」 和 「 myVM 」，請您雲端服務，您的 VM 名稱分別。 您可以瞭解這些名稱執行`Get-AzureVM`不具任何參數。)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

如果 [**寫入主機**] 命令顯示**為 True**，請安裝 VM 代理程式。 如果會顯示**為 False**，請參閱的指示， [VM 代理程式 」 和 「 延伸模組-第 2 部分](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)Azure 部落格文章中的下載連結。

如果您使用入口網站建立虛擬機器，檢查是否`$vm.GetInstance().ProvisionGuestAgent`，則傳回**True**。 如果沒有出現，您可以使用這個命令來設定︰

    $vm.GetInstance().ProvisionGuestAgent = $true

當您接下來的步驟執行**設定 AzureVMExtension**命令時，此命令可防止下列錯誤: 「 佈建來賓代理人必須啟用 VM 物件上設定 IaaS VM 存取延伸之前，先。 」

#### <a name="reset-the-local-administrator-account-password"></a>**重設本機系統管理員帳戶密碼**

建立登入認證使用目前的本機系統管理員帳戶名稱和新密碼，然後再執行`Set-AzureVMAccessExtension`，如下所示。

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

如果您輸入不同的名稱與目前的帳戶，VMAccess 副檔名重新命名本機系統管理員帳戶會指派給該帳戶的密碼與遠端桌面教具借出中發生的問題。 如果本機系統管理員帳戶已停用，VMAccess 擴充功能可讓它。

這些命令也重設遠端桌面服務設定。

#### <a name="reset-the-remote-desktop-service-configuration"></a>**重設遠端桌面服務設定**

若要重設遠端桌面服務設定，請執行下列命令︰

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

副檔名為 VMAccess 虛擬機器上執行兩個命令︰

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

這個命令可讓允許傳入的遠端桌面流量，使用 TCP 連接埠 3389 [內建 [Windows 防火牆] 群組。

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

這個命令 fDenyTSConnections 登錄值設為 0，啟用遠端桌面連線。


## <a name="next-steps"></a>後續步驟

如果沒有回應 Azure VM access 副檔名，您無法重設密碼，您可以[重設本機 Windows 密碼離線](virtual-machines-windows-reset-local-password-without-agent.md)。 這個方法是更進階的程序，並要求您的問題 VM 虛擬硬碟連接至另一個 VM。 請遵循本文中，記錄的步驟，只嘗試離線的密碼重設方式的最後一個步驟。

[Azure VM 副檔名與功能](virtual-machines-windows-extensions-features.md)

[連線至 RDP 或 SSH Azure 虛擬機器](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[疑難排解遠端桌面連線到 windows Azure 虛擬機器](virtual-machines-windows-troubleshoot-rdp-connection.md)
