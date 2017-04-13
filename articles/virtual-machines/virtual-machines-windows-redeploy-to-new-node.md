<properties 
    pageTitle="重新部署 Windows 虛擬機器 |Microsoft Azure" 
    description="說明如何重新部署 Windows 虛擬機器，以減少 RDP 連線問題。" 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>部署至新的 Azure 節點的虛擬機器

如果您有已遇到問題疑難排解遠端桌面 (RDP) 連線或應用程式存取 windows Azure 虛擬機器 (VM)，重新部署 VM 協助。 當您重新部署 VM 時，它會將 VM 移至新的節點 Azure 基礎結構，然後力量其重新開啟，保留所有您的設定選項和相關聯的資源。 本文將說明如何重新部署 VM 使用 PowerShell 的 Azure 或 Azure 入口網站。

> [AZURE.NOTE] 部署 VM 之後，請暫時磁碟會遺失，然後更新動態虛擬網路介面與相關聯的 IP 位址。 

## <a name="using-azure-powershell"></a>使用 PowerShell 的 Azure

請確定您有最新 PowerShell 的 Azure 1.x 安裝在您的電腦上。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

若要重新部署虛擬機器中使用這個 PowerShell 的 Azure 命令︰

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>後續步驟
如果您無法連線到您 VM 的問題，您可以尋找特定說明[疑難排解 RDP 連線](virtual-machines-windows-troubleshoot-rdp-connection.md)或[詳細的 RDP 疑難排解步驟](virtual-machines-windows-detailed-troubleshoot-rdp.md)。 如果您無法存取您 VM 上執行應用程式，您也可以瞭解[應用程式疑難排解問題](virtual-machines-windows-troubleshoot-app-connection.md)。