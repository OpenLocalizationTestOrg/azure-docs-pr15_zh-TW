<properties
    pageTitle="建立使用 PowerShell 的 Azure VM |Microsoft Azure"
    description="使用 PowerShell 的 Azure 和 Azure 資源管理員輕鬆建立新的 VM 執行 Windows Server。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>建立使用資源管理員和 PowerShell 的 Windows VM

本文將示範如何快速建立執行 Windows Server 和資源需要使用[資源管理員](../azure-resource-manager/resource-group-overview.md)和 PowerShell 的 Azure 虛擬機器。 

本文中的所有步驟，都才能建立虛擬機器，它應該執行步驟需要花 30 分鐘。 在命令] 範例參數值取代環境的有意義的名稱。

## <a name="step-1-install-azure-powershell"></a>步驟 1︰ 安裝 Azure PowerShell

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。
        
## <a name="step-2-create-a-resource-group"></a>步驟 2︰ 建立資源群組

所有資源必須包含在資源群組，因此就讓我們先建立。  

1. 取得可用的位置，可以在其中建立資源的清單。

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. 設定資源的位置。 這個命令來**centralus**設定的位置。

    ```powershell
    $location = "centralus"
    ```
    
3. 建立資源群組。 這個命令會建立名為您設定的位置**myResourceGroup**資源群組。

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>步驟 3︰ 建立儲存帳戶

[儲存帳戶](../storage/storage-introduction.md)需要儲存虛擬所使用的虛擬機器您建立的硬碟。 儲存帳戶名稱必須介於 3 和 24 個字元的長度，而且可能包含數字和只有大小寫字母。

1. 測試儲存體帳戶名稱的唯一性。 這個命令測試名稱**myStorageAccount**。

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    如果這個命令會傳回**True**，您建議的名稱是 Azure 中唯一的。 
    
2. 現在，建立儲存帳戶。
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>步驟 4︰ 建立虛擬網路

所有的虛擬機器是[虛擬網路](../virtual-network/virtual-networks-overview.md)的一部分。

1. 建立網路的子虛擬。 這個命令建立子網路名稱與地址的前置詞 10.0.0.0/24 **mySubnet** 。
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. 現在，建立虛擬網路。 這個命令會建立名為使用您所建立的子網路和地址的前置詞**10.0.0.0/16** **myVnet**虛擬網路。

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>步驟 5︰ 建立公用 IP 位址和網路介面

若要啟用通訊虛擬機器中的虛擬網路，您需要的[公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)，以及網路介面。

1. 建立的公用 IP 位址。 這個命令所建立的**動態**配置方法命名**myPublicIp**的公用 IP 位址。
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. 建立網路介面。 這個命令會建立一個名為的網路介面**myNIC**。

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>步驟 6︰ 建立虛擬機器

現在您有所有片段的位置時，就建立虛擬機器的時間。

1. 執行此命令以設定的系統管理員帳戶名稱和密碼虛擬機器。

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    密碼在 12 123 個字元，並有至少有一個小寫字元、 大寫的一個字元、 一個號碼，以及一個特殊字元。 
        
2. 建立虛擬機器設定物件。 這個命令建立名為**myVmConfig**定義 VM 的名稱及大小的 VM 設定物件。

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    虛擬機器可用大小的清單，請參閱[在 Azure 虛擬機器的大小](virtual-machines-windows-sizes.md)。
    
3. 設定 vm 作業系統設定。 這個命令設定 VM 電腦名稱、 作業系統類型及帳戶認證。

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. 定義使用佈建 VM 的影像。 這個命令定義要用於 VM Windows Server 圖像。 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    如需有關選取要使用的圖像的詳細資訊，請參閱[瀏覽並選取 CLI PowerShell 的 Azure 中的 Windows 虛擬機器圖像](virtual-machines-windows-cli-ps-findimage.md)。
        
5. 將您建立的網路介面加入設定。

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. 定義的名稱和 VM 硬碟的位置。 虛擬硬碟檔案會儲存於容器。 這個命令中名為您建立的儲存空間帳戶中**vhds/WindowsVMosDisk.vhd**容器建立磁碟。

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. 作業系統磁碟資訊加入 VM 設定。 **$DiskName**的值取代作業系統磁碟的名稱。 建立變數並設定新增磁碟資訊。
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. 最後，建立虛擬機器。

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>後續步驟

- 如果有問題的部署，下一步就會看[Azure 入口網站與疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)
- 瞭解如何管理您檢閱[使用 Azure 資源管理員與 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)建立虛擬機器。
- 利用範本來建立虛擬機器使用[建立資源管理員範本 Windows 虛擬機器](virtual-machines-windows-ps-template.md)中的資訊
