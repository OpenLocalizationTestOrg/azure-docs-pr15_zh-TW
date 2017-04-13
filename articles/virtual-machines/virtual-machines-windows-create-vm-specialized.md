<properties
    pageTitle="建立您的 Windows VM 複本 |Microsoft Azure"
    description="瞭解如何建立您執行 Windows 中，在資源管理員部署模型中的特殊 Azure VM 的複本。"
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>從 [特殊 VHD 建立 VM

建立新的 VM 附加特殊的 VHD 與使用 Powershell OS 磁碟。 特殊的 VHD 維護的使用者帳戶與應用程式從您的原始 VM 其他狀態資料。 

如果您想要從一般化 VHD 建立 VM，請參閱[建立 VM 從一般化 VHD 圖像](virtual-machines-windows-create-vm-generalized.md)。

## <a name="create-the-subnet-and-vnet"></a>建立子網路和 vNet

建立 vNet 和[虛擬網路](../virtual-network/virtual-networks-overview.md)的子網路。

1. 建立子網路。 本範例會建立子網路名稱為**mySubNet**，在 [資源群組**myResourceGroup**，並設定**10.0.0.0/24**子網路位址首碼。

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. 建立 vNet。 此範例中設定的虛擬網路名稱**myVnetName**、 位置**西美國**，且**10.0.0.0/16**虛擬網路的地址前置字元。 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>建立的公用 IP 位址和 NIC

若要啟用通訊虛擬機器中的虛擬網路，您需要的[公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)，以及網路介面。

1. 建立的公用 IP。 在此範例中，以**myIP**設定的公用 IP 位址名稱。

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. 建立在 NIC 在此範例中，NIC 名稱是設為**myNicName**。

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>建立網路安全性群組及 RDP 規則

若要使用 RDP 您 vm 登入，您需要的安全性規則，允許連接埠 3389 RDP 存取權。 建立從現有的新 vm VHD 特殊的 VM VM 是建立您可以使用從來源虛擬機器的擁有權限使用 RDP 登入現有的帳戶。

此範例中設定 NSG 名稱**myNsg**和**myRdpRule**RDP 規則名稱。

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

如需關於結束點和 NSG 規則的詳細資訊，請參閱[使用 PowerShell 的 Azure 中 vm 開啟連接埠](virtual-machines-windows-nsg-quickstart-powershell.md)。

## <a name="create-the-vm-configuration"></a>建立 VM 設定

附加為 OS VHD 複製的 VHD 設定 VM 設定。


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


如果您有要附加的 vm 的資料磁碟，您也應該新增下列動作︰ 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

資料和作業系統磁碟 Url 看起來應該像這樣︰ `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`。 您可以在入口網站上找到這瀏覽功能至目標存放容器，按一下作業系統或 VHD 已複製的資料，然後複製 URL 的內容。


## <a name="create-the-vm"></a>建立 VM

建立 VM 使用我們剛剛建立的設定。

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

如果此命令順利完成，您會看到輸出像這樣︰

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>確認 VM 已建立 
 
您應該會在[Azure 入口網站](https://portal.azure.com)，**瀏覽]**底下看到新建立的 VM > **虛擬機器**，或使用下列 PowerShell 命令︰

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>後續步驟

若要登入新的虛擬機器，瀏覽至 VM[入口網站](https://portal.azure.com)中，按一下 [**連線**] 並開啟遠端桌面 RDP 檔案。 登入新的虛擬機器中使用原始虛擬機器帳戶的認證。 如需詳細資訊，請參閱[如何連線並登入執行 Windows Azure 虛擬機器](virtual-machines-windows-connect-logon.md)。







