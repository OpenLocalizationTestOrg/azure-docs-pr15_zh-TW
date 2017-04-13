<properties
   pageTitle="部署多重 NIC Vm 傳統部署模型中使用 PowerShell |Microsoft Azure"
   description="瞭解如何部署多重 NIC Vm 傳統部署模型中使用 PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>部署多重 NIC Vm （傳統） 使用 PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

您可以建立 Azure 虛擬機器 (Vm)，並將多個網路介面 (Nic) 附加至每個您 Vm。 多個 Nic 上 Nic 啟用流量類型的分隔。 例如，NIC 可能會與網際網路通訊，另一個通訊只能搭配內部資源未連線到網際網路時。 跨多個 Nic 分隔網路流量的功能時需要多個網路虛擬應用裝置，例如應用程式後傳遞，但 WAN 最佳解決方案。

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](virtual-network-deploy-multinic-arm-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

目前，您不能使用單一 NIC Vm 和使用多個 Nic Vm 相同的雲端服務中。 因此，您需要在不同的雲端服務比和在此案例中的其他所有元件實作後端伺服器。 下列步驟使用的後端伺服器命名*IaaSStory*的主要的資源及*IaaSStory 後端*雲端服務。

## <a name="prerequisites"></a>必要條件

您可以佈署後端伺服器之前，必須先部署這種情況下的所有必要的資源主雲端服務。 最小值，在您要使用的後端子網路建立虛擬網路。 請造訪[建立虛擬網路使用 PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)以瞭解如何部署虛擬網路。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>部署後端 Vm

後端 Vm 取決於下列資源的建立。

- **後端子網路**。 資料庫伺服器會是不同的子網路，若要將流量的一部分。 以下的指令碼需要存在於中名為*WTestVnet*vnet 子網路。
- **儲存帳戶的資料**。 更佳的效能，請在資料庫伺服器上的資料磁碟會使用實心狀態磁碟機 (SSD) 技術，需要進階版儲存帳戶。 請確定您部署至支援進階版儲存空間的 Azure 位置。
- **可用性設定**。 所有資料庫伺服器便會都新增至單一的可用性設定，以確保 Vm 中至少一個已啟動和執行期間進行的維修作業。

### <a name="step-1---start-your-script"></a>步驟 1-啟動您的指令碼

您可以下載完整用的 PowerShell 指令碼[以下](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)。 請遵循下列步驟來變更搭配使用您的環境中的指令碼。

1. 變更根據您現有的資源群組部署上方[的先決條件](#Prerequisites)變數下列的值。

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. 變更您想要使用的後端部署的數值為根據的變數下列的值。

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>步驟 2-建立必要的資源，為您的 Vm

您要為所有 Vm 建立新的雲端服務及資料磁碟的儲存空間帳戶。 您也需要指定 Vm 圖像，以及本機系統管理員帳戶。 若要建立這些資源，請執行下列步驟。

1. 建立新的雲端服務。

        New-AzureService -ServiceName $backendCSName -Location $location

2. 建立新的進階版儲存帳戶。

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. 設定為您的訂閱的目前儲存帳戶建立上方的儲存空間帳戶。

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. 選取 [vm 的 [圖像]。

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. 設定本機系統管理員帳戶認證。

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>步驟 3-建立 Vm

您需要使用迴圈來建立您想要並建立所需 Nic 和 Vm 迴圈中的多 Vm。 若要建立的 Nic 及 Vm，請執行下列步驟。

1. 開始`for`循環播放重複的命令來建立 VM 和兩個 Nic 次數以有必要，請根據的值`$numberOfVMs`變數。

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. 建立`VMConfig`圖像、 大小和可用 vm 設定所指定的物件。

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. 在 Windows VM 為佈建 VM。

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. 設定預設 NIC，並將它指派的靜態 IP 位址。

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. 新增每個 VM 的第二個 NIC。

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. 建立的每個 VM 資料磁碟。

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. 建立每個 VM，及結束循環播放。

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>步驟 4-執行指令碼

現在，您可以下載並變更您的需求所根據的指令碼，runt 他的指令碼使用多個 Nic 建立後端資料庫 Vm。

1. 儲存您的指令碼，並從 [ **PowerShell**命令提示字元或**PowerShell ise [以系統**。 您會看到 [初始輸出] 清單，如下所示。

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. 填寫 [認證提示中所需的資訊，然後按一下**[確定**]。 將會顯示以下的輸出。

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
