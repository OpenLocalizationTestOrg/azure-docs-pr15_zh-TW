<properties 
   pageTitle="執行個體層級公用 IP (ILPIP) |Microsoft Azure"
   description="了解 ILPIP (PIP)，以及如何管理其"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>執行個體層級的公用 IP 概觀
執行個體層級的公用 IP (ILPIP) 是直接將 VM 或角色的執行個體，而非您 VM 或角色的執行個體位於雲端服務，您可以指定的公用 IP 位址。 這不需要 VIP (虛擬 IP) 指派給您的雲端服務的位置。 而是直接連接到您 VM 或角色的執行個體，您可以使用其他 IP 位址。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](virtual-network-ip-addresses-overview-arm.md)。 

請確定您瞭解[IP 位址](virtual-network-ip-addresses-overview-classic.md)Azure 中的運作方式。

>[AZURE.NOTE] 過去 ILPIP 被稱為 PIP，代表公用 IP。 

![ILPIP 與 VIP 之間的差異](./media/virtual-networks-instance-level-public-ip/Figure1.png)

圖 1 所示，雲端服務的存取個別 Vm 會以正常方式存取使用 VIP 時使用的 VIP:&lt;連接埠號碼&gt;。 透過 ILPIP 指派給特定 VM，可以直接使用的 IP 位址來存取 VM。

當您建立 Azure 中的雲端服務時，對應的 DNS 記錄會自動建立允許存取服務的完整的網域名稱 (FQDN)，而不是使用實際 VIP。 為 ILPIP，讓存取權，而不是 ILPIP FQDN VM 或角色執行個體，這時的相同程序。 例如，如果您建立名為*contosoadservice*，在雲端服務，設定網頁角色名為*contosoweb*與兩個執行個體，Azure 將登錄下列 A 記錄的執行個體︰

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] 您可以指定單一 ILPIP 每個 VM 或角色執行個體。 您可以使用最多 5 ILPIP 的每個訂閱。 此時，ILPIP 不支援多重 NIC Vm。

## <a name="why-should-i-request-an-ilpip"></a>為什麼應該要求 ILPIP？
如果您想要能夠連線到您 VM 或角色的執行個體直接指派給該 IP 位址，而不是使用雲端服務 VIP:&lt;連接埠號碼&gt;，要求您 VM 或您的角色執行個體 ILPIP。
- **被動式 FTP** -由 ILPIP 您 VM，在您接收流量幾乎任何連接埠，您不會以開啟接收流量端點。 案例像被動式 FTP 動態選擇連接埠是如此。
- **輸出 IP**源自 VM 的外寄流量的做為來源 ILPIP 而這可唯一識別 VM 外部項目。

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>如何要求 ILPIP VM 建立期間
下列 PowerShell 指令碼會建立新的雲端服務，名為*FTPService*，然後從 Azure 中擷取圖像和建立名為*FTPInstance*使用擷取的圖像 VM、 設定為使用 ILPIP，VM，新服務將 VM:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>如何以擷取 vm ILPIP 資訊
若要檢視使用上述的指令碼建立 vm ILPIP 資訊，請執行下列 PowerShell 命令， *PublicIPAddress*和*PublicIPName*觀察值︰

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>如何移除 VM ILPIP
若要移除新增至上述的指令碼 VM ILPIP，請執行下列 PowerShell 命令︰
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>如何將 ILPIP 新增至現有的 VM
若要新增使用上述的指令碼建立 VM ILPIP，執行下列命令︰

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>如何建立與 VM ILPIP 使用服務設定檔之間的關聯
您也可以使用 [服務設定 (CSCFG) 檔案關聯 vm ILPIP。 下列範例 xml 示範如何設定使用角色執行個體名稱*MyPublicIP* ILPIP 雲端服務︰ 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>後續步驟

- 了解如何[IP 位址](virtual-network-ip-addresses-overview-classic.md)傳統部署模型中的運作。

- 深入了解[保留的 IPs](virtual-networks-reserved-public-ip.md)。
 