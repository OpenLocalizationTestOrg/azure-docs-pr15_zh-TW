<properties
    pageTitle="調整大小傳統的 Windows VM |Microsoft Azure"
    description="調整大小建立傳統的部署模型] 中，使用 Powershell 的 Azure Windows 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>調整大小傳統部署模型中建立 Windows VM

本文將示範如何調整 Windows VM，使用 Powershell 的 Azure 傳統部署模型中建立的大小。

考慮調整大小 VM 的能力，有兩個概念會控制可用來調整大小虛擬機器大小的範圍。 第一個概念是部署您 VM 時的區域。 Azure 區域網頁上的 [服務] 索引標籤下，是 VM 區域中可用的大小的清單。 第二個概念是目前裝載您 VM 的實體硬體。 裝載 Vm 的實體伺服器是常見的實體硬體叢集群組在一起。 如果您要新增的虛擬記憶體大小為支援硬體叢集目前主控 VM 根據與不同變更虛擬記憶體大小的方法。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]您也可以[調整大小 VM 資源管理員部署模型中建立](virtual-machines-windows-resize-vm.md)。


## <a name="add-your-account"></a>新增您的帳戶

您必須設定以使用傳統 Azure 資源 PowerShell 的 Azure。 請遵循下列步驟來設定 Azure PowerShell 來管理傳統的資源。

1. 在 PowerShell 提示中，輸入`Add-AzureAccount`按一下**enter 鍵**。 
2. 輸入與您 Azure 訂閱相關聯的電子郵件地址，然後按一下 [**繼續**]。 
3. 輸入您的帳戶的密碼。 
4. 按一下 [**登入**]。 


## <a name="resize-in-the-same-hardware-cluster"></a>在相同的硬體叢集調整大小

若要調整大小 VM 硬體叢集裝載 VM 中可用的大小，請執行下列步驟。

1. 執行下列 PowerShell 命令清單用於裝載雲端服務，其中包含 VM 硬體叢集 VM 大小。

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. 執行下列命令以調整大小 VM。

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>在新的硬體叢集上調整大小

若要調整大小 VM 大小硬體叢集裝載雲端 VM 中無法使用的服務與雲端服務中的所有 Vm 必須重新建立。 每個雲端服務被裝載在單一硬體叢集中，因此在雲端服務中的所有 Vm 必須都是支援在硬體叢集的大小。 下列步驟會說明如何調整大小 VM，刪除，然後再重新建立雲端服務。

1. 執行下列 PowerShell 命令清單可用 VM 大小區域中。 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. 記下包含調整 VM 雲端服務中的每個 VM 所有設定的設定。 
3. 刪除選取選項，以保留磁碟的每個 VM 雲端服務中的所有 Vm。
4. 重新建立所需的虛擬記憶體大小調整 VM。
5. 重新建立使用硬體叢集現在裝載雲端服務中可用的虛擬記憶體大小雲端服務中的所有其他 Vm。

您可以找到刪除並重新建立新的虛擬記憶體大小雲端服務的範例指令碼[以下](https://github.com/Azure/azure-vm-scripts)。 


## <a name="next-steps"></a>後續步驟

- [調整大小 VM 資源管理員部署模型中建立](virtual-machines-windows-resize-vm.md)。