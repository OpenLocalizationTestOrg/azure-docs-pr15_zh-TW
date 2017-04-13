<properties
    pageTitle="調整大小的 Windows VM |Microsoft Azure"
    description="調整大小建立資源管理員部署模型] 中，使用 Powershell 的 Azure Windows 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>調整大小的 Windows VM

本文將示範如何調整 Windows VM，使用 Powershell 的 Azure 資源管理員部署模型中建立的大小。

建立虛擬機器 (VM) 之後，您可以調整 VM 向上或向下藉由變更虛擬記憶體大小。 在某些情況下，您必須先解除配置 VM。 如果目前主控 VM 硬體叢集上沒有新的大小，也可能會發生。

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>調整大小 Windows VM 不在可用性設定

1. 列出可用的硬體叢集裝載 VM VM 大小。 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. 如果列出所要的大小，請執行下列命令以調整大小 VM。 如果沒有列出所要的大小，請執行步驟 3。

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. 如果沒有列出所要的大小，請執行下列命令解除 VM 調整，並重新啟動 VM。

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] 解除 VM 釋放指派給 VM 任何動態 IP 位址。 不受影響的作業系統與資料磁碟。 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>調整大小 Windows VM 在可用性設定

如果新的大小，在可用性設定 vm 無法使用目前主控 VM 硬體叢集上，在可用性設定的所有 Vm 都必須解除配置以調整大小 VM。 您也可能會需要設定一個 VM 已調整大小後的可用性更新其他 Vm 的大小。 若要調整大小 VM 在可用性設定，請執行下列步驟。

1. 列出可用的硬體叢集裝載 VM VM 大小。

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. 如果列出所要的大小，請執行下列命令以調整大小 VM。 如果沒有列出，請移至步驟 3。

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. 如果沒有列出所要的大小，繼續執行下列步驟，以解除所有 Vm 集的顯示狀態、 調整大小 Vm，並重新開啟。

4.  停止所有 Vm 集的可用性。

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  調整大小並重新啟動 Vm 集的可用性。

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>後續步驟

- 其他延展性執行多個 VM 執行個體，延展。 如需詳細資訊，請參閱[自動調整 Windows 電腦中設定虛擬機器小數位數](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)。



