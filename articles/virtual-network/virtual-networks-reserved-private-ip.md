<properties 
   pageTitle="如何設定靜態內部私人 ip 位址"
   description="了解靜態內部 IPs (DIPs)，以及如何管理其"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>瞭解如何使用 PowerShell （傳統） 的靜態內部私人 IP 位址
在大部分情況下，您不需要指定虛擬機器的靜態內部 IP 位址。 從您指定的範圍，Vm 虛擬網路中的會自動收到內部 IP 位址。 不過在某些情況下，指定的靜態 IP 位址特定 vm 意義。 例如，如果您 VM 會執行 DNS，或將會網域控制站。 即使是透過停駐點/deprovision 狀態 VM 與，保持內部的靜態 IP 位址。 

> [AZURE.IMPORTANT] Azure 有兩種不同的部署模型建立及使用的資源︰[資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。 本文涵蓋使用傳統的部署模型。 Microsoft 建議最新的部署使用[資源管理員部署模型](virtual-networks-static-private-ip-arm-ps.md)。

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>如何驗證是否有空特定 IP 位址
若要確認是否有空中名為*TestVnet*vnet IP 位址*10.0.0.7* ，請執行下列 PowerShell 命令並驗證*IsAvailable*值︰

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] 如果您想要測試以上的命令，在 [安全的環境追蹤建立 vnet[建立虛擬網路](virtual-networks-create-vnet-classic-portal.md)中的指導方針為*TestVnet* ]，確保它使用*10.0.0.0/8*位址空間。

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>如何建立 VM 時，指定靜態內部 ip 位址
下列 PowerShell 指令碼會建立新名為*TestService*雲端服務，然後從 Azure 中擷取圖像，然後建立新的雲端服務使用擷取的圖像中名為*TestVM* VM、 設定位於 [子網路*的子網路-1*，VM 再 VM *10.0.0.7*設定為靜態內部 ip 位址︰

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>如何取得 VM 內部靜態 IP 資訊
若要檢視靜態內部 IP 資訊，請使用上方的指令碼建立 vm，請執行下列 PowerShell 命令， *IpAddress*觀察值︰

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>如何從 VM 移除靜態內部 ip 位址
若要移除靜態內部 IP 新增上面的指令碼 vm，請執行下列 PowerShell 命令︰
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>如何將內部靜態 ip 位址新增至現有的 VM
若要新增內部的靜態 IP vm 會建立使用上述 runt 的指令碼他命令︰

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>後續步驟

[保留的 IP](virtual-networks-reserved-public-ip.md)

[執行個體層級的公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
