<properties 
   pageTitle="部署資源管理員] 中使用 PowerShell 固定公用 ip VM |Microsoft Azure"
   description="瞭解如何部署 Vm 固定的公用 ip 使用 PowerShell 中資源管理員"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-powershell"></a>部署固定的公用 ip 使用 PowerShell VM

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="step-1---start-your-script"></a>步驟 1-啟動您的指令碼

您可以下載完整用的 PowerShell 指令碼[以下](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1)。 請遵循下列步驟來變更搭配使用您的環境中的指令碼。

1. 變更您想要使用您的部署中的數值為根據的變數下列的值。 此文件中使用此案例地圖以下的值。

        # Set variables resource group
        $rgName                = "IaaSStory"
        $location              = "West US"
        
        # Set variables for VNet
        $vnetName              = "WTestVNet"
        $vnetPrefix            = "192.168.0.0/16"
        $subnetName            = "FrontEnd"
        $subnetPrefix          = "192.168.1.0/24"
        
        # Set variables for storage
        $stdStorageAccountName = "iaasstorystorage"
        
        # Set variables for VM
        $vmSize                = "Standard_A1"
        $diskSize              = 127
        $publisher             = "MicrosoftWindowsServer"
        $offer                 = "WindowsServer"
        $sku                   = "2012-R2-Datacenter"
        $version               = "latest"
        $vmName                = "WEB1"
        $osDiskName            = "osdisk"
        $nicName               = "NICWEB1"
        $privateIPAddress      = "192.168.1.101"
        $pipName               = "PIPWEB1"
        $dnsName               = "iaasstoryws1"

## <a name="step-2---create-the-necessary-resources-for-your-vm"></a>步驟 2-您 VM 建立所需的資源

建立 VM 之前, 必須 VM 所使用的資源群組、 VNet、 公用 IP，與 NIC。

1. 建立新的資源群組。

        New-AzureRmResourceGroup -Name $rgName -Location $location
        
2. 建立 VNet 和子網路。

        $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
            -AddressPrefix $vnetPrefix -Location $location   
        
        Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
            -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
        
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 

3. 建立公用 IP 資源。 

        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
            -AllocationMethod Static -DomainNameLabel $dnsName -Location $location

4. 建立網路介面 (NIC) 建立上述的公用 IP 子網路中 vm。 請注意擷取 VNet Azure，這必要的**設定 AzureRmVirtualNetwork**若要變更現有的 VNet 執行，因為第一個指令程式。

        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
            -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
            -PublicIpAddress $pip

5. 建立儲存帳戶裝載 VM OS 磁碟機。

        $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
            -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## <a name="step-3---create-the-vm"></a>步驟 3-建立 VM 

現在，所有必要的資源是在 [位置]，您可以建立新的 VM。

1. 建立設定物件 vm。

        $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 

1. 取得 VM 本機系統管理員帳戶認證。

        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

2. 建立 VM 設定物件。

        $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
            -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

3. 設定 VM 作業系統圖像。

        $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
            -Offer $offer -Skus $sku -Version $version

4. 設定 OS 磁碟。

        $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
        $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage

5. 在 NIC 新增 VM。

        $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary

6. 建立 VM。

        New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location

7. 儲存的指令碼檔。

## <a name="step-4---run-the-script"></a>步驟 4-執行指令碼

當您任何必要的變更，並了解指令碼之後顯示上方，執行指令碼]。 

1. 從 PowerShell 主控台或 PowerShell ise [以系統中，執行上述的指令碼。
2. 以下的輸出應該會顯示，請稍候幾分鐘。

        ResourceGroupName : IaaSStory
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
                
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {}
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "AddressPrefix": "192.168.1.0/24"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
        
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {
                              "DnsServers": []
                            }
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "ProvisioningState": "Succeeded"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
                
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        Status              : Succeeded
        StatusCode          : OK
        Output              : 
        StartTime           : 1/12/2016 12:57:56 PM -08:00
        EndTime             : 1/12/2016 12:59:13 PM -08:00
        Error               : 
        ErrorText           : 

   