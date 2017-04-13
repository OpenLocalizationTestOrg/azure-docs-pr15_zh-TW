<properties
   pageTitle="部署使用 PowerShell 資源管理員] 中的多重 NIC Vm |Microsoft Azure"
   description="瞭解如何使用 PowerShell 資源管理員] 中的多重 NIC Vm 的部署"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-using-powershell"></a>部署多重 NIC Vm 使用 PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](virtual-network-deploy-multinic-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

目前，您不能與單一 NIC Vm 和使用多個 Nic Vm 在相同的可用性設定。 因此，您需要實作後端伺服器位於不同的資源群組的其他所有元件。 下列步驟使用 [資源群組*IaaSStory*主要資源] 群組中，與*IaaSStory 後端*的後端伺服器。

## <a name="prerequisites"></a>必要條件

您可以佈署後端伺服器之前，必須先部署這個案例中所需的所有資源的主要資源群組。 若要部署這些資源，請遵循下列步驟。

1. 瀏覽至[[範本] 頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在 [範本] 頁面右側的 [**父項資源群組**中，按一下 [**部署至 Azure**]。
3. 如有需要變更參數值，然後遵循部署資源群組 Azure 預覽入口網站中的步驟。

> [AZURE.IMPORTANT] 請確定您儲存的帳戶名稱都是唯一的。 您不能 Azure 中有重複的儲存體帳戶名稱。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>部署後端 Vm

後端 Vm 取決於下列資源的建立。

- **儲存帳戶的資料**。 更佳的效能，請在資料庫伺服器上的資料磁碟會使用實心狀態磁碟機 (SSD) 技術，需要進階版儲存帳戶。 請確定您部署至支援進階版儲存空間的 Azure 位置。
- **Nic**。 每個 VM 會有兩個 Nic，其中一個資料庫存取另一個用於管理。
- **可用性設定**。 所有資料庫伺服器便會都新增至單一的可用性設定，以確保 Vm 中至少一個已啟動和執行期間進行的維修作業。  

### <a name="step-1---start-your-script"></a>步驟 1-啟動您的指令碼

您可以下載完整用的 PowerShell 指令碼[以下](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1)。 請遵循下列步驟來變更搭配使用您的環境中的指令碼。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. 變更根據您現有的資源群組部署上方[的先決條件](#Prerequisites)變數下列的值。

        $existingRGName        = "IaaSStory"
        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"
        $remoteAccessNSGName   = "NSG-RemoteAccess"
        $stdStorageAccountName = "wtestvnetstoragestd"

2. 變更您想要使用的後端部署的數值為根據的變數下列的值。

        $backendRGName         = "IaaSStory-Backend"
        $prmStorageAccountName = "wtestvnetstorageprm"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $publisher             = "MicrosoftSQLServer"
        $offer                 = "SQL2014SP1-WS2012R2"
        $sku                   = "Standard"
        $version               = "latest"
        $vmNamePrefix          = "DB"
        $osDiskPrefix          = "osdiskdb"
        $dataDiskPrefix        = "datadisk"
        $diskSize              = "120"  
        $nicNamePrefix         = "NICDB"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

3. 擷取您部署所需的現有的資源。

        $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
        $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
        $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
        $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### <a name="step-2---create-necessary-resources-for-your-vms"></a>步驟 2-建立必要的資源，為您的 Vm

您需要建立新的資源群組、 資料磁碟，並為所有 Vm 可用性儲存帳戶。 您 alos 需要每個 VM 本機系統管理員帳戶認證。 若要建立這些資源，請執行下列步驟。

1. 建立新的資源群組。

        New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. 建立新的進階版儲存帳戶建立上方的 [資源] 群組中。

        $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
            -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. 建立新的可用性設定。

        $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. 取得本機系統管理員使用的每個 VM 帳戶認證。

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

### <a name="step-3---create-the-nics-and-backend-vms"></a>步驟 3-建立 Nic 和後端 Vm

您需要使用迴圈來建立您想要並建立所需 Nic 和 Vm 迴圈中的多 Vm。 若要建立的 Nic 及 Vm，請執行下列步驟。

1. 開始`for`循環播放重複的命令來建立 VM 和兩個 Nic 次數以有必要，請根據的值`$numberOfVMs`變數。

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. 建立用於資料庫存取 NIC。

            $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
            $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
            $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. 建立用於遠端存取 NIC。 請注意此 NIC 有與相關聯，NSG 的方式。

            $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
            $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
            $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
                -NetworkSecurityGroupId $remoteAccessNSG.Id

4. 建立`vmConfig`物件。

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. 建立兩個資料磁碟每 VM。 請注意資料磁碟先前建立的進階版儲存帳戶中。

            $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
            $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
                -VhdUri $data1VhdUri -CreateOption empty -Lun 0

            $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
            $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
                -VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. 設定作業系統中，並用於 VM 圖像。

            $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
            $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. 新增建立上述兩個 Nic`vmConfig`物件。

            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. 建立 OS 磁碟，然後建立 VM。 請注意`}`結束`for`循環播放。

            $osDiskName = $vmName + "-" + $osDiskSuffix
            $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
            $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
            New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
        }

### <a name="step-4---run-the-script"></a>步驟 4-執行指令碼

現在，您可以下載並變更您的需求所根據的指令碼，runt 他的指令碼使用多個 Nic 建立後端資料庫 Vm。

1. 儲存您的指令碼，並從 [ **PowerShell**命令提示字元或**PowerShell ise [以系統**。 您會看到 [初始輸出] 清單，如下所示。

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. 請稍候幾分鐘填寫認證的提示時，按一下**[確定]**。 以下的輸出代表單一 VM。 請注意整個程序花了 8 分鐘才能完成。

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        EndTime             : 10/30/2015 9:30:03 AM -08:00
        Error               :
        Output              :
        StartTime           : 10/30/2015 9:22:54 AM -08:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK
