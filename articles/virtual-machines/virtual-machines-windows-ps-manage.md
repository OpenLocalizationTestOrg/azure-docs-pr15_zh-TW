<properties
    pageTitle="管理 Vm 使用資源管理員和 PowerShell |Microsoft Azure"
    description="管理虛擬機器使用 Azure 資源管理員與 PowerShell。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>管理資源管理員和 PowerShell 的 Azure 虛擬機器

## <a name="install-azure-powershell"></a>安裝 Azure PowerShell
 
如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="set-variables"></a>設定變數

文件中的所有命令都需要虛擬機器所在的 [資源] 群組的名稱和管理虛擬機器的名稱。 **$RgName**的值取代包含虛擬機器的資源群組的名稱。 **$VmName**的值取代 VM 的名稱。 建立變數。

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>顯示虛擬機器中的資訊

取得的虛擬機器資訊。
  
    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

會傳回類似此範例中︰

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>停止虛擬機器

停止執行中虛擬機器。

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

要求您確認︰

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
輸入**Y**若要停止虛擬機器。

請稍候幾分鐘，它會傳回類似此範例中︰

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>啟動虛擬機器

已停止，請啟動虛擬機器。

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

請稍候幾分鐘，它會傳回類似此範例中︰

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

如果您想要重新啟動虛擬機器已在執行時，使用**重新啟動 AzureRmVM**描述下一步]。

## <a name="restart-a-virtual-machine"></a>重新啟動虛擬機器

重新啟動執行的虛擬機器。

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

會傳回類似此範例中︰

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>刪除虛擬機器

刪除虛擬機器。  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] 您可以使用**-強制**略過出現確認提示時的參數。

如果您沒有使用-強制參數，要求您確認︰

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

會傳回類似此範例中︰

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>更新虛擬機器

此範例顯示如何更新虛擬機器的大小。
        
    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
會傳回類似此範例中︰

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK
                              
虛擬機器可用大小的清單，請參閱[在 Azure 虛擬機器的大小](virtual-machines-windows-sizes.md)。

## <a name="add-a-data-disk-to-a-virtual-machine"></a>加入虛擬機器中的資料磁碟

此範例顯示如何將資料磁碟新增至現有的虛擬機器。

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

未初始化您新增的磁碟。 若要初始化磁碟，您可以登入，然後使用 [磁碟管理。 如果您在安裝 WinRM 與憑證，您可建立，您可以使用遠端 PowerShell 初始化磁碟。 您也可以使用副檔名為自訂指令碼︰ 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

指令碼檔可以包含類似初始化磁碟此程式碼︰

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>後續步驟

如果有問題的部署，您可能會看看[Azure 入口網站與疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)
