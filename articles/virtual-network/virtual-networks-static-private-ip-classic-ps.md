<properties 
   pageTitle="如何在傳統模式下使用 PowerShell 設定靜態私人 IP |Microsoft Azure"
   description="了解靜態私人 IPs (DIPs)，以及如何管理其在傳統模式和 PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-set-a-static-private-ip-address-classic-in-powershell"></a>如何設定私人的靜態 IP 位址 （傳統） 的 PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[管理資源管理員部署模型的靜態私用的 IP 位址](virtual-networks-static-private-ip-arm-ps.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

下列範例 PowerShell 命令預期已經建立一個簡單環境。 如果您想要執行這份文件中所顯示的命令，先建立測試環境中[建立 VNet](virtual-networks-create-vnet-classic-netcfg-ps.md)所述。

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>如何驗證是否有空特定 IP 位址
若要確認是否有空中名為*TestVNet*VNet IP 位址*192.168.1.101* ，請執行下列 PowerShell 命令並驗證*IsAvailable*值︰

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

預期的輸出︰

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何建立 VM 時，指定私人的靜態 IP 位址
下列 PowerShell 指令碼會建立新的雲端服務，名為*TestService*，然後從 Azure 中擷取圖像、 建立 VM 中名為 「 *DNS01*使用擷取的圖像的新雲端服務，設定要在名為*FrontEnd*，子網路 VM 並設定為私人的靜態 IP 位址的*從 192.168.1.7* vm:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

預期的輸出︰

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何擷取 vm 靜態私人 IP 位址資訊
若要檢視使用上述的指令碼建立 vm 靜態私用的 IP 位址資訊，請執行下列 PowerShell 命令， *IpAddress*觀察值︰

    Get-AzureVM -Name DNS01 -ServiceName TestService

預期的輸出︰

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何從 VM 移除私人的靜態 IP 位址
若要移除私人的靜態 IP 位址新增 VM 中的指令碼，請執行下列 PowerShell 命令︰
    
    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

預期的輸出︰

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何將私人的靜態 IP 位址新增至現有的 VM
若要新增的靜態私人 IP 位址是使用上述 runt 的指令碼所建立的 vm 他下列命令︰

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

預期的輸出︰

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="next-steps"></a>後續步驟

- 深入了解[保留的公用 IP](virtual-networks-reserved-public-ip.md)位址。
- 瞭解[執行個體層級的公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)地址。
- [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)，請參閱。
