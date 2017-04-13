<properties
   pageTitle="使用多個 Nic 建立 Windows VM |Microsoft Azure"
   description="瞭解如何使用多個 Nic 附加至並使用 PowerShell 的 Azure 或資源管理員的範本建立 Windows VM。"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>使用多個 Nic 建立 Windows VM
您可以建立具有附加多個虛擬網路介面 (Nic) 的 Azure 虛擬機器 (VM)。 有不同的前端與後端的連線或網路專門用來監視或備份解決方案的子網路是最常見的案例。 本文提供使用多個 Nic 附加建立 VM 的快速命令。 如詳細資訊，包括如何建立您自己的 PowerShell 指令碼，內的多個 Nic 閱讀更多有關[部署多重 NIC Vm](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)。 不同[VM 大小](virtual-machines-windows-sizes.md)支援不同的數字的 Nic，因此會相應地大小您 VM。

>[AZURE.WARNING] 當您建立 VM-您無法將 Nic 新增至現有的 VM 時，您必須附加多個 Nic。 您可以[建立 VM 根據原始的虛擬磁碟](virtual-machines-windows-vhd-copy.md)，以及視部署 VM 建立多個 Nic。

## <a name="create-core-resources"></a>建立核心資源
請確定您擁有[最新 PowerShell 的 Azure 安裝和設定](../powershell-install-configure.md)。 登入您的 Azure 帳戶︰

```powershell
Login-AzureRmAccount
```

在下列範例中，用您自己的值來取代範例參數名稱。 範例參數名稱包含`myResourceGroup`， `mystorageaccount`，及`myVM`。

首先，建立資源群組。 下列範例會建立資源群組名稱`myResourceGroup`中`WestUs`位置︰

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

建立保留您的 Vm 儲存帳戶。 下列範例會建立名為儲存帳戶`mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>建立虛擬網路和子網路
定義兩個虛擬網路的子網路-前端的交通和後端流量。 下列範例會定義具名的兩個子網路`mySubnetFrontEnd`和`mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

建立您的虛擬網路和子網路。 下列範例會建立名為虛擬網路`myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>建立多個 Nic
建立兩個 Nic，附加至前端子網路 NIC 和 NIC 到後端的子網路。 下列範例會建立兩個 Nic，名為`myNic1`和`myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

通常您也建立[網路安全性群組](../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../load-balancer/load-balancer-overview.md)可協助管理及分散您 Vm 流量。 [更詳細的多重 NIC VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)本文會引導您建立網路安全性群組及指派 Nic。


## <a name="create-the-virtual-machine"></a>建立虛擬機器
立即開始建立您 VM 設定。 每個虛擬記憶體大小的上限，您可以新增至 VM Nic 總數。 進一步瞭解[Windows VM 大小](virtual-machines-windows-sizes.md)。 

首先，設定您的 VM 認證`$cred`變數，如下所示︰

```powershell
$cred = Get-Credential
```

下列範例會定義具名 VM `myVM` ，並使用支援兩個 Nic 虛擬記憶體大小 (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

建立您 VM 設定的其餘部分。 下列範例會建立 Windows Server 2012 R2 VM:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

附加您先前建立的兩個 Nic:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

設定您的新 VM 儲存與虛擬磁碟︰

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

最後，建立 VM:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>建立多個 Nic 使用資源管理員範本
Azure 資源管理員範本會使用宣告式 JSON 檔案，以定義您的環境。 您可以瞭解[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。 資源管理員範本提供建立資源的多個執行個體部署，例如建立多個 Nic 期間的方式。 您可以使用*複製*至指定的執行個體建立數︰

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

進一步瞭解[建立多個執行個體，使用*複製*](../resource-group-create-multiple.md)。 

您也可以使用`copyIndex()`然後附加到資源的名稱，可讓您建立的 [數字`myNic1`，`MyNic2`等。以下顯示附加的索引值的範例︰

```bash
"name": "[concat('myNic', copyIndex())]", 
```

您可以瞭解[建立多個 Nic 使用資源管理員範本](../virtual-network/virtual-network-deploy-multinic-arm-template.md)的完整的範例。

## <a name="next-steps"></a>後續步驟
請確定嘗試建立多個 Nic VM 時，檢閱[Windows VM 大小](virtual-machines-windows-sizes.md)。 請注意的每個虛擬記憶體大小支援的 Nic 數上限。 

請記住，您無法將其他 Nic 新增至現有的 VM，當您部署 VM 時，您必須建立所有 Nic。 請注意規劃您的部署，請確定您已從開始的所有必要的網路連線時。