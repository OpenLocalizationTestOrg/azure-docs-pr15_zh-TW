<properties
    pageTitle="在 [VM 上安裝 Symantec 端點保護 |Microsoft Azure"
    description="瞭解如何安裝和設定 Symantec 端點保護安全性副檔名為使用傳統的部署模型建立新的或現有 Azure VM 上。"
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

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>如何安裝和設定 Windows VM Symantec 端點保護

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

本文將示範如何安裝和設定 Symantec 端點保護用戶端上現有的虛擬機器 (VM) 執行 Windows Server。 這是完整的用戶端，包括病毒及間諜軟體保護、 防火牆和防止干擾等服務。 用戶端安裝安全性副檔名為使用 VM 代理程式。

如果您有 Symantec 從內部部署解決方案現有的訂閱，您可以使用它來保護您 Azure 虛擬機器。 如果您不客戶尚未，您可以註冊試用訂閱。 如需此方案的詳細資訊，請參閱[Microsoft Azure 平台上的 Symantec 端點保護][Symantec]。 此頁面也有授權的資訊和指示安裝於用戶端，如果您已經 Symantec 客戶的連結。

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>在 [現有的 VM 上安裝 Symantec 端點保護

在開始之前，您需要︰

- Azure PowerShell 模組、 版本 0.8.2 或更新版本，在工作電腦上。 您可以檢查 PowerShell 的 Azure 與您有安裝的版本**取得模組 azure | 格式化表格版本**] 命令。 如需相關指示與最新版本的連結，請參閱[如何安裝和設定 Azure PowerShell][PS]。 登入您的訂閱 Azure 使用`Add-AzureAccount`。

- 執行 Azure 虛擬機器上 VM 代理程式的情況下。

首先，請確認虛擬機器上已安裝 VM 代理程式。 填入的雲端服務名稱和虛擬機器名稱，然後執行下列命令，在系統管理員層級 PowerShell 的 Azure 命令提示字元。 報價，包括內的所有項目取代 < 及 > 字元。

> [AZURE.TIP] 如果您不知道雲端服務，虛擬機器名稱，請執行**取得 AzureVM**列出所有的虛擬機器中您目前的訂閱名稱。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

如果 [**寫入主機**] 命令顯示**為 True**，請安裝 VM 代理程式。 如果會顯示**為 False**，請參閱的指示，張貼[VM 代理程式及副檔名-第 2 部分]中的 Azure 的部落格的下載連結[Agent]。

如果 VM 代理程式已安裝，請執行這些安裝 Symantec 端點保護代理程式的命令。

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

若要確認 Symantec 安全性擴充功能已經安裝，保持最新狀態︰

1.  登入虛擬機器。 如需相關指示，請參閱[如何登入虛擬機器執行 Windows Server][Logon]。
2.  針對 Windows Server 2008 R2，按一下 [**開始 > Symantec 端點保護**。 Windows Server 2012 或 Windows Server 2012 R2，從 [開始] 畫面中，輸入**Symantec**，，然後按一下**Symantec 端點保護**。
3.  從 [**狀態**] 索引標籤的 [**狀態 Symantec 端點保護**視窗套用更新或重新啟動如有需要。

## <a name="additional-resources"></a>其他資源

[如何登入執行 Windows Server 的虛擬機器][Logon]

[Azure VM Extensions 和功能][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493