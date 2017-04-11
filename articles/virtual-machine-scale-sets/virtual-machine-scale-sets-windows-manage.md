<properties
    pageTitle="管理虛擬機器比例集中 Vm |Microsoft Azure"
    description="管理虛擬機器中設定使用 PowerShell 的 Azure 虛擬機器小數位數。"
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
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>管理虛擬機器中的虛擬機器縮放比例設定

使用本文中的工作，來管理虛擬機器比例集中的虛擬機器。

大部分的涉及管理虛擬機器中縮放比例設定的工作要求您知道您想要管理的電腦執行個體識別碼。 若要尋找的執行個體識別碼的虛擬機器中縮放設定，您可以使用[Azure 資源檔案總管](https://resources.azure.com)。 您也可以使用資源檔案總管來驗證您完成任務的狀態。

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="display-information-about-a-scale-set"></a>顯示比例設定的相關資訊

您可以取得縮放設定，也稱為執行個體檢視的一般資訊。 或者，您可以取得更具體的資訊，例如在縮放比例設定資源的相關資訊。

使用名稱或資源群組和縮放比例設定，然後再執行命令取代引號括住的值︰

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

會傳回像這樣︰

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
資源群組和小數位數集的名稱取代引號括住的值。 取代*#*與您想要取得相關資訊，然後執行虛擬機器的執行個體識別碼︰

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
會傳回類似此範例中︰

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>啟動虛擬機器中縮放設定

資源群組和小數位數集的名稱取代引號括住的值。 取代*#*具有您想要開始，然後執行虛擬機器的識別項︰

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

在 [資源總管] 中，我們可以看到的執行個體的狀態是 [**執行**︰

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

您可以開始所有虛擬機器中不使用的執行個體識別碼參數設定小數位數。
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>停止虛擬機器中縮放設定

資源群組和小數位數集的名稱取代引號括住的值。 取代*#*具有您想要停止，然後執行虛擬機器的識別項︰

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

在 [資源總管] 中，我們可以看到的執行個體的狀態是 [**解除配置**︰

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
若要停止虛擬機器並不將它解除配置，請使用-StayProvisioned 參數。 您可以在不使用的執行個體識別碼參數停止在設定的所有虛擬機器。
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>在 [縮放比例設定中重新啟動虛擬機器

引號括住的值取代資源群組及縮放比例設定的名稱。 取代*#*具有您想要重新啟動，然後執行虛擬機器的識別項︰

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
重新啟動所有虛擬機器中的設定不使用的執行個體識別碼參數。

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>虛擬機器移除縮放設定

引號括住的值取代資源群組及縮放比例設定的名稱。 取代*#*具有您想要移除，然後執行虛擬機器的識別項︰  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

您可以移除虛擬機器縮放設定，一次不利用-執行個體識別碼參數。

## <a name="change-the-capacity-of-a-scale-set"></a>變更縮放比例設定的容量

您可以新增或移除虛擬機器藉由變更集的容量。 取得您想要變更、 設定容量至您想要設為，然後更新設定新的容量比例縮放比例設定。 在下列命令，以取代的引號括住資源群組及縮放比例設定的名稱。

  $vmss = 取得 AzureRmVmss-ResourceGroupName 」 資源群組名稱 「-VMScaleSetName 「 縮放比例設定名稱 「 $vmss.sku.capacity = 5 更新 AzureRmVmss ResourceGroupName 」 資源群組名稱 「-名稱 「 不按比例縮放設定名稱 「-VirtualMachineScaleSet $vmss 

如果您要移除的虛擬機器從縮放比例設定，以最高的識別碼虛擬機器將會移除第一次。
