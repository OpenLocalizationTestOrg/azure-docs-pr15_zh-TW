<properties
    pageTitle="從 [通用 VHD 建立 VM |Microsoft Azure"
    description="瞭解如何從一般化 VHD 映像使用 PowerShell 的 Azure 資源管理員部署模型中建立 Windows 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>建立 VM 從一般化 VHD 圖像

一般化的 VHD 映像已所有使用[Sysprep](virtual-machines-windows-generalize-vhd.md)移除您的個人帳戶資訊。 藉由內部部署 VM，然後[上傳至 Azure VHD](virtual-machines-windows-upload-image.md)，在執行 Sysprep 或現有的 Azure VM，然後[複製 VHD](virtual-machines-windows-vhd-copy.md)上執行 Sysprep，您可以建立一般化的 VHD。

如果您想要從特定 VHD 建立 VM，請參閱[建立從特殊 VHD VM](virtual-machines-windows-create-vm-specialized.md)。

若要從一般化 VHD 建立 VM 最快速的方法是使用 [快速入門範本] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)。 


## <a name="prerequisites"></a>必要條件

如果您要使用的內部部署 VM，如下使用 HYPER-V，您應該確認建立從上傳 VHD 您接在[準備 Windows VHD 上傳至 Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md)中的指示進行。 

同時上傳的 Vhd 和現有 Azure VM Vhd 需要加以一般化，您可以建立 VM 之前，先使用此方法。 如需詳細資訊，請參閱[一般化 Windows 虛擬機器使用 Sysprep](virtual-machines-windows-generalize-vhd.md)。 


## <a name="set-the-uri-of-the-vhd"></a>設定的 VHD URI

若要使用 vhd URI 是格式︰ https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd。 在此範例中命名**myVHD** VHD 位於容器**mycontainer**儲存帳戶**mystorageaccount** 。

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>建立虛擬網路

建立 vNet 和[虛擬網路](../virtual-network/virtual-networks-overview.md)的子網路。


1. 建立子網路。 下列範例會建立名為**mySubnet**資源群組**myResourceGroup** **10.0.0.0/24**的地址前置詞中的子網路。  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. 建立虛擬網路。 下列範例會建立虛擬網路具名**myVnet** **10.0.0.0/16**的地址前置詞**西美國**位置。  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>建立公用 IP 位址和網路介面

若要啟用通訊虛擬機器中的虛擬網路，您需要的[公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)，以及網路介面。

1. 建立的公用 IP 位址。 此範例會建立一個名為**myPip**的公用 IP 位址。 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. 建立在 NIC 此範例會建立名為 NIC **myNic**。 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組及 RDP 規則

若要使用 RDP 您 vm 登入，您需要讓連接埠 3389 RDP 存取權的安全性規則。 

此範例會建立名為**myNsg**包含稱為**myRdpRule**允許連接埠 3389 RDP 流量的規則 NSG。 如需有關 NSGs 的詳細資訊，請參閱[使用 PowerShell 的 Azure 中 vm 開啟連接埠](virtual-machines-windows-nsg-quickstart-powershell.md)。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>建立一個變數的虛擬網路

建立已完成的虛擬網路的變數。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>建立 VM

下列 PowerShell 指令碼說明如何設定虛擬機器設定並使用新的安裝做為來源上傳的 VM 圖像。

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>確認 VM 已建立 

完成時，您應該會看到下**瀏覽** [Azure 入口網站](https://portal.azure.com)中新建立的 VM > **虛擬機器**，或使用下列 PowerShell 命令︰

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>後續步驟

若要管理新的 PowerShell 的 Azure 虛擬機器，請參閱[使用 Azure 資源管理員與 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)。


