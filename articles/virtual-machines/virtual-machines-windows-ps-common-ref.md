<properties 
   pageTitle="常見的 PowerShell 命令，如 Vm |Microsoft Azure"
   description="一般的 PowerShell 命令，可以讓您開始建立和管理您的 Windows Azure 中的 Vm"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>建立及管理 Vm 一般的 PowerShell 命令

本文涵蓋的一些您可以用來建立及管理虛擬機器中 Azure 訂閱的 PowerShell 的 Azure 命令。  如需使用特定的命令列切換參數及選項的詳細說明，您可以使用 [**取得說明** *] 命令*。

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="create-a-vm"></a>建立 VM

任務 | ] 命令
-------------- | -------------------------
建立 VM 設定 | $vm =[新增 AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName 「 vm_name 」-VMSize 「 vm_size 」<BR></BR><BR></BR>VM 設定用來定義或更新 vm 的設定。 設定初始化 VM 和其[大小](virtual-machines-windows-sizes.md)的名稱。
新增設定的設定 | $vm =[設定 AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) VM $vm-Windows 電腦名稱 「 電腦名稱 」-認證 $cred ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>作業系統的設定，包括[認證](https://technet.microsoft.com/library/hh849815.aspx)會新增至您先前建立使用新增 AzureRmVMConfig 設定物件。
新增網路介面 | $vm =[新增 AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) VM $vm-識別碼 $nic識別碼<BR></BR><BR></BR>VM 必須通訊虛擬網路中的[網路介面](virtual-machines-windows-ps-create.md)。 您也可以使用[取得 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx)擷取現有的網路介面物件。
指定的平台圖像 | $vm =[設定 AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm-PublisherName 「 publisher_name 」-提供 「 publisher_offer 」-Sku 「 product_sku 」 的 「 最新 」 版本<BR></BR><BR></BR>[圖像的資訊](virtual-machines-windows-cli-ps-findimage.md)會新增至您先前建立使用新增 AzureRmVMConfig 設定物件。 當您設定 OS 磁碟，若要使用的平台圖像時，才會使用從這個命令傳回的物件。
若要使用的平台圖像設定 OS 磁碟 | $vm =[設定 AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) VM $vm-名稱 「 disk_name 」-VhdUri 「 http://mystore1.blob.core.windows.net/vhds/disk_name.vhd 」-CreateOption FromImage<BR></BR><BR></BR>作業系統磁碟及[存放](../storage/storage-powershell-guide-full.md)位置的名稱會新增至您先前建立的 [設定] 物件。
若要使用通用的圖像設定 OS 磁碟 | $vm = 設定 AzureRmVMOSDisk VM $vm-名稱 「 disk_name 」-SourceImageUri 」 https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk。{guid}.vhd 」-VhdUri 「 https://mystore1.blob.core.windows.net/vhds/disk_name.vhd 」-CreateOption FromImage-Windows<BR></BR><BR></BR>作業系統磁碟與來源圖像的位置中[儲存空間](../storage/storage-powershell-guide-full.md)的磁碟位置的名稱會新增至 [設定] 物件。
若要使用特殊的圖像設定 OS 磁碟 | $vm = 設定 AzureRmVMOSDisk VM $vm-名稱 「 name_of_disk 」-VhdUri 「 http://mystore1.blob.core.windows.net/vhds/ 」-CreateOption 附加-Windows
建立 VM | [新增 AzureRmVM]() ResourceGroupName 「 resource_group_name 」 的位置 」 location_name 」-VM $vm<BR></BR><BR></BR>在 [[資源] 群組](../powershell-azure-resource-manager.md)中，會建立所有資源。 在資源群組相同的[位置](https://msdn.microsoft.com/library/azure/dn495177.aspx)，必須建立 VM。 執行此命令時，您之前，先執行新增 AzureRmVMConfig、 設定 AzureRmVMOperatingSystem、 設定 AzureRmVMSourceImage、 新增 AzureRmVMNetworkInterface，以及設定 AzureRmVMOSDisk。

## <a name="get-information-about-vms"></a>取得 Vm 的相關資訊

任務 | ] 命令
-------------- | -------------------------
在訂閱清單 Vm| [取得 AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
在 [資源] 群組中的清單 Vm | 取得 AzureRmVM ResourceGroupName 「 resource_group_name 」<BR></BR><BR></BR>相關的資源群組清單中您的訂閱，請使用[取得 AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx)。
取得 VM 的相關資訊 | 取得 AzureRmVM ResourceGroupName 「 resource_group_name 」-名稱 「 vm_name 」

## <a name="manage-vms"></a>管理 Vm

任務 | ] 命令
-------------- | -------------------------
啟動 VM | [開始 AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 vm_name 」
停止 VM | [停止 AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 vm_name 」
重新啟動執行 VM | [重新啟動 AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 vm_name 」
刪除 VM | [移除 AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 vm_name 」
一般化 VM | [設定 AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) ResourceGroupName YourResourceGroup-名稱 「 vm_name 」-一般化<BR></BR><BR></BR>執行儲存 AzureRmVMImage 之前，請執行此命令。
擷取 VM | [儲存 AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName 「 resource_group_name 」-VMName 「 vm_name 」-DestinationContainerName 「 image_container 」-VHDNamePrefix 「 image_name_prefix 」-路徑 」 C:\filepath\filename.json 」<BR></BR><BR></BR>虛擬機器必須[關閉並一般化](virtual-machines-windows-generalize-vhd.md)要用來建立圖像。 執行此命令時，您之前，先執行設定 AzureRmVm。
更新 VM | [更新 AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) ResourceGroupName 「 resource_group_name 」-VM $vm<BR></BR><BR></BR>取得目前使用取得 AzureRmVM VM 設定、 變更 VM 物件上的設定，然後執行此命令。
新增資料磁碟 vm | [新增 AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) VM $vm-名稱 「 disk_name 」-VhdUri 「 https://mystore1.blob.core.windows.net/vhds/disk_name.vhd 」-LUN #-快取 ReadWrite DiskSizeinGB #-CreateOption 空白<BR></BR><BR></BR>取得 AzureRmVM 可用於取得 VM 物件。 指定 LUN 數] 及 [磁碟的大小。 執行更新-AzureRmVM VM 中用於設定的變更。 未初始化您新增的磁碟。 加入初始化磁碟的相關資訊，請參閱[管理 Azure 虛擬機器使用資源管理員與 PowerShell](virtual-machines-windows-ps-manage.md)。
從 VM 移除資料磁碟 | [移除 AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) VM $vm-名稱 「 disk_name 」<BR></BR><BR></BR>取得 AzureRmVM 可用於取得 VM 物件。 執行更新-AzureRmVM VM 中用於設定的變更。
新增 VM 副檔名 | [設定 AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ResourceGroupName 「 resource_group_name 」-位置 」 azure_location 」-VMName 「 vm_name 」-名稱 「 extension_name 」-Publisher 「 publisher_name 」-類型 」 extension_type 」-TypeHandlerVersion 「 #.# 」-設定 $Settings ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>執行此命令時以您想要安裝的副檔名為適當的[設定資訊](virtual-machines-windows-extensions-configuration-samples.md)。
移除 VM 副檔名 | [移除 AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 extension_name 」-VMName 「 vm_name 」

## <a name="next-steps"></a>後續步驟

- 請參閱建立虛擬機器中[建立 Windows VM 使用資源管理員和 PowerShell](virtual-machines-windows-ps-create.md)的基本步驟。

