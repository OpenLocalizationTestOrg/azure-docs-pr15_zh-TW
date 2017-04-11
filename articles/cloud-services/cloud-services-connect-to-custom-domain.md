<properties
  pageTitle="連線到自訂的網域控制站的雲端服務 |Microsoft Azure"
  description="了解如何使用 PowerShell 與 AD 網域分機號碼自訂 AD 網域連接您的 web/工作者角色"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>若要自訂裝載於 Azure AD 網域控制站連線 Azure 雲端服務的角色

我們先將 Azure 中設定虛擬網路 (VNet)。 我們會再將 VNet （裝載在 Azure 虛擬機器） Active Directory 網域控制站加入。 接下來，我們會將現有的雲端服務角色加入預先建立的 VNet，並接著網域控制站連接圖形。

我們開始之前，要注意的事項的幾個︰

1.  本教學課程中使用 PowerShell，請確定您有安裝 PowerShell 的 Azure 並準備好讓。 若要取得 PowerShell 的 Azure 所設定的說明，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

2.  您 AD 網域控制站和 Web/工作者角色的執行個體必須 VNet。

請遵循此逐步指南，如果遇到任何問題，請留言下方。 其他人會回到您 （是的我們執行讀取註解）。

3. 網路參考的雲端服務<mark>必須是</mark>**傳統的虛擬網路**。

## <a name="create-a-virtual-network"></a>建立虛擬網路

您可以使用 Azure 傳統入口網站或 PowerShell 的 Azure 中建立虛擬網路。 在此教學課程中，我們將使用 PowerShell。 若要建立一個虛擬 Azure 傳統入口網站的網路，請參閱[建立虛擬網路](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

當您完成設定虛擬網路時，您必須建立 AD 網域控制站。 在此教學課程中，將會設定我們 AD 網域控制站 Azure 虛擬機器上。

若要這麼做，建立虛擬機器透過 PowerShell 使用下列命令︰

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>升級您的網域控制站的虛擬機器
若要設定的 AD 網域控制站的虛擬機器，您必須登入 VM，並將它設定。

要登入 VM，您可以透過 PowerShell 取得 RDP 檔案，請使用下方的命令。

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

當您登入 VM 時，設定您的虛擬機器的 AD 網域控制站依照如何[設定您的客戶 AD 網域控制站](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)的逐步指南。

## <a name="add-your-cloud-service-to-the-virtual-network"></a>將您的雲端服務新增至虛擬網路

接下來，您需要將您的雲端服務部署新增到您剛剛建立的 VNet。 若要這麼做，請以相關的章節，加上使用電子郵件地址] 或編輯器] 中您所選擇的 Visual Studio 您 cscfg 修改您的雲端服務 cscfg。

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

下一步建立雲端服務專案，並將其部署到 Azure。 若要取得雲端服務套件部署至 Azure 的說明，請參閱[如何建立及部署雲端服務](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>連線到網域 web/工作者的角色

一旦雲端服務專案部署上 Azure，連接角色執行個體自訂 AD 網域使用副檔名為 AD 網域。 若要將副檔名為 AD 網域新增至您現有的雲端服務部署，並加入自訂網域，請在 PowerShell 中執行下列命令︰

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

這樣就大功告成了。

您可以雲端服務現在應該加入至您的自訂網域控制站。 如果您想要進一步瞭解如何設定 AD 網域副檔名可用的其他選項，請使用 PowerShell 說明如下所示。

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
