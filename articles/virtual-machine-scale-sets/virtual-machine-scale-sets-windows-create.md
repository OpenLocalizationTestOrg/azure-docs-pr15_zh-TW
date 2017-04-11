<properties
    pageTitle="建立虛擬機器縮放比例設定使用 PowerShell |Microsoft Azure"
    description="建立虛擬機器縮放比例設定使用 PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>建立使用 PowerShell 的 Azure 設定 Windows 虛擬機器比例

這些步驟，請依照建立 Azure 虛擬機器縮放比例設定的填滿-的--空白方法。 請參閱若要進一步瞭解縮放比例設定的[虛擬機器縮放比例設定的概觀](virtual-machine-scale-sets-overview.md)。

應該需要大約 30 分鐘本文中的步驟。

## <a name="step-1-install-azure-powershell"></a>步驟 1︰ 安裝 Azure PowerShell

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="step-2-create-resources"></a>步驟 2︰ 建立資源

建立所需的您新的縮放設定的資源。

### <a name="resource-group"></a>資源群組

資源群組中，必須包含虛擬機器縮放比例設定。

1. 取得可用的位置，您可以在其中放置資源的清單︰

        Get-AzureLocation | Sort Name | Select Name

2. 選擇最適合您的位置，取代**$locName**的值，以該位置名稱，然後建立變數︰

        $locName = "location name from the list, such as Central US"

3. 取代您想要使用新的資源群組，然後建立變數為名稱的**$rgName**值︰ 

        $rgName = "resource group name"
        
4. 建立資源群組︰
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    您應該會看到類似此範例中︰

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>儲存帳戶

儲存帳戶虛擬機器用於儲存的作業系統磁碟和縮放比例所使用的診斷資料。 可能的話，則有每個虛擬機器中縮放比例設定建立儲存帳戶的最佳作法。 如果不是，每個儲存帳戶不超過 20 Vm 計劃。 本文中的範例顯示三個儲存帳戶所建立的三個虛擬機器。

1. 儲存帳戶的名稱來取代**$saName**的值。 測試唯一性的名稱。 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Answer （回應） **，則為 True**時，您的建議的名稱是唯一的。

3. **$SaType**的值取代儲存帳戶類型，然後建立變數︰  

        $saType = "storage account type"
        
    可能的值︰ Standard_LRS、 Standard_GRS、 Standard_RAGRS 或 Premium_LRS。
        
4. 建立帳戶︰
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    您應該會看到類似此範例中︰

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. 重複步驟 1 到 4，即可建立三個儲存帳戶，例如 myst1 myst2，與 myst3。

### <a name="virtual-network"></a>虛擬網路

在縮放比例設定虛擬機器需要虛擬網路。

1. 取代您想要使用的虛擬網路的子網路，然後建立變數為名稱的**$subnetName**值︰ 

        $subnetName = "subnet name"
        
2. 建立子網路設定︰
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    地址前置詞可能不同虛擬網路。

3. 取代您想要使用的虛擬網路，然後建立變數為名稱的**$netName**值︰ 

        $netName = "virtual network name"
        
4. 建立虛擬網路︰
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>縮放比例設定的設定

您有您需要的縮放設定，現在讓我們來建立的所有資源。  

1. 取代您想要使用的 IP 設定，然後建立變數為名稱的**$ipName**值︰ 

        $ipName = "IP configuration name"
        
2. 建立 IP 設定︰

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. 取代您想要使用的縮放比例設定設定，然後建立變數為名稱的**$vmssConfig**值︰   

        $vmssConfig = "Scale set configuration name"
        
3. 建立縮放比例設定的設定︰

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    此範例顯示比例設定與三個虛擬機器所建立。 有關容量的縮放設定的詳細資訊，請參閱[虛擬機器縮放比例設定的概觀](virtual-machine-scale-sets-overview.md)。 此步驟也會包含在設定中設定的虛擬機器 （又稱為 SkuName） 的大小。 若要尋找符合您需求的大小，請查看[虛擬機器的大小](../virtual-machines/virtual-machines-windows-sizes.md)。
    
4. 縮放比例設定組態新增網路介面設定︰
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    您應該會看到類似此範例中︰

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>作業系統的設定檔

1. 取代您想要使用，然後建立變數的電腦名稱前置詞**$computerName**的值︰ 

        $computerName = "computer name prefix"
        
2. 取代值的**$adminName**虛擬機器上的管理員帳戶的名稱，然後建立變數︰

        $adminName = "administrator account name"
        
3. **$AdminPassword**的值取代的帳戶密碼，然後建立變數︰

        $adminPassword = "password for administrator accounts"
        
4. 建立作業系統設定檔︰

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>儲存空間的設定檔

1. 取代您想要使用的儲存空間的設定檔，然後建立變數為名稱的**$storageProfile**值︰  

        $storageProfile = "storage profile name"
        
2. 建立定義使用圖像的變數︰  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    若要尋找要使用的其他圖像的相關資訊，看看[瀏覽和使用 Windows PowerShell 和 Azure CLI 選取 Azure 虛擬機器圖像](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md)。
        
3. **$VhdContainers**的值取代包含虛擬硬碟的儲存位置，例如 「 https://mystorage.blob.core.windows.net/vhds 」，路徑的清單，然後建立變數︰
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. 建立儲存設定檔︰

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>虛擬機器縮放設定

最後，您可以建立的縮放設定。

1. **$VmssName**的值取代虛擬機器比例集的名稱，然後建立變數︰

        $vmssName = "scale set name"
        
2. 建立縮放比例設定︰

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    您應該會看到類似此範例會顯示在成功部署︰

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>步驟 3︰ 探索資源

使用這些資源來瀏覽您建立虛擬機器縮放比例設定︰

- Azure 入口網站-可使用入口網站是資訊的一段有限。
- [Azure 資源檔案總管](https://resources.azure.com/)這項工具最適合探索您的縮放設定的目前狀態。
- Azure PowerShell-使用此命令，以取得資訊︰

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>後續步驟

- 管理您剛建立使用[管理集中虛擬機器小數位數的虛擬機器](virtual-machine-scale-sets-windows-manage.md)中的資訊的縮放設定
- 請考慮設定自動縮放比例中[自動縮放比例和虛擬機器縮放比例設定為](virtual-machine-scale-sets-autoscale-overview.md)使用資訊來設定您的比例
- 深入瞭解檢閱[虛擬機器比例集垂直自動調整大小](virtual-machine-scale-sets-vertical-scale-reprovision.md)的垂直縮放比例
