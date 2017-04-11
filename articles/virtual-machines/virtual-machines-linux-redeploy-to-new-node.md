<properties 
    pageTitle="重新部署 Linux 虛擬機器 |Microsoft Azure" 
    description="說明如何重新部署 Linux 虛擬機器，以減少 SSH 連線問題。" 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>部署至新的 Azure 節點的虛擬機器

如果您已對協助疑難排解 SSH 或重新部署 VM Azure 虛擬機器 (VM)，應用程式存取的問題。 當您重新部署 VM 時，它會將 VM 移至新的節點 Azure 基礎結構，然後力量其重新開啟，保留所有您的設定選項和相關聯的資源。 本文將說明如何重新部署 VM 使用 Azure CLI 或 Azure 入口網站。

> [AZURE.NOTE] 部署 VM 之後，請暫時磁碟會遺失，然後更新動態虛擬網路介面相關聯的 IP 位址。 


## <a name="using-azure-cli"></a>使用 Azure CLI

請確定您的電腦上有[安裝最新的 Azure CLI](../xplat-cli-install.md)在資源管理員模式 (`azure config mode arm`)。

若要重新部署虛擬機器中使用下列 Azure CLI 命令︰

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

通過重新部署的機會程序，您可以看到 VM 變更的狀態。 `PowerState`的 VM 進入從 'Running' '更新 」，然後 「 開始 」，並最後 '執行' 通過重新部署至新的 」 主機的程序。 檢查 Vm 與資源群組內的狀態︰

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>後續步驟
如果您無法連線到您 VM 的問題，您可以尋找特定說明[疑難排解 SSH 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)或[詳細的 SSH 疑難排解步驟](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md)。 如果您無法存取您 VM 上執行應用程式，您也可以瞭解[應用程式疑難排解問題](virtual-machines-linux-troubleshoot-app-connection.md)。