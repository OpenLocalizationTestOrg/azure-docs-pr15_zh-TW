<properties
    pageTitle="Azure Active Directory 網域服務︰ 管理指南 |Microsoft Azure"
    description="加入受管理的網域使用 PowerShell 的 Azure 與傳統部署模型中的 Windows 虛擬機器。"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>加入受管理的網域使用 PowerShell 的 Windows Server 虛擬機器

> [AZURE.SELECTOR]
- [Azure 傳統入口網站-Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell 的 Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure 有兩種不同的部署模型建立及使用的資源︰[資源管理員] 及 [傳統](../resource-manager-deployment-model.md)。 本文涵蓋使用傳統的部署模型。 Azure AD 網域服務目前不支援資源管理員模型。

這些步驟會顯示如何自訂一組 Azure PowerShell 命令建立及使用的建置組塊方法預先 windows Azure 虛擬機器。 這些步驟可協助您建立 windows Azure 虛擬機器並加入 Azure AD 網域服務受管理網域。

這些步驟，請依照建立 PowerShell 的 Azure 命令集的填滿-的--空白方法。 如果您是 PowerShell 的新手，或您想要知道值指定成功設定為何，此方法很有用。 進階的 PowerShell 使用者可以採取的命令和替代自己的變數值 （以 「 $」 開頭的線條）。

如果您尚未這麼做，使用[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)中的指示，在您的本機電腦上安裝 PowerShell 的 Azure。 然後，開啟 Windows PowerShell 命令提示字元。

## <a name="step-1-add-your-account"></a>步驟 1︰ 新增您的帳戶

1. 在 PowerShell 提示中，輸入**新增 AzureAccount** ，然後按一下 [**輸入**]。
2. 輸入與您 Azure 訂閱相關聯的電子郵件地址，然後按一下 [**繼續**]。
3. 輸入您的帳戶的密碼。
4. 按一下 [**登入**]。

## <a name="step-2-set-your-subscription-and-storage-account"></a>步驟 2︰ 設定您的訂閱和儲存的帳戶

設定您 Azure 訂閱及儲存帳戶在 Windows PowerShell 命令提示字元中執行這些命令。 報價，包括內的所有項目取代 < 與 > 字元，請使用正確的名稱。

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

您可以從**取得 AzureSubscription**命令的輸出的 SubscriptionName 屬性，以取得正確的訂閱名稱。 執行**選取 AzureSubscription**命令後，您可以從**取得 AzureStorageAccount**命令的輸出的標籤屬性取得正確的儲存體帳戶名稱。


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>步驟 3︰ 逐步解說-佈建虛擬機器並加入受管理的網域
以下是設定為使用空白的行之間的可讀性每個組塊建立此虛擬機器的相對應 Azure PowerShell 命令。

指定要佈建 Windows 虛擬機器的相關資訊。

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

D、 DS 或 G 數列虛擬機器 InstanceSize 值，請參閱[虛擬機器和 Azure 雲端服務的大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。

提供的受管理的網域的相關資訊。

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

指定雲端服務的名稱。

    $svcname="Contoso100-test"

指定應該要聯結 VM 虛擬網路的名稱。 確定用於此虛擬網路 AAD DS 受管理的網域。

    $vnetname="MyPreviewVnet"

選取要用來佈建 VM VM 圖像。

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

設定 VM-設定 VM 名稱、 執行個體大小與要使用的圖像。

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

取得本機系統管理員認證 vm。 選擇強式本機系統管理員的密碼。

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

取得屬於 VM 加入受管理的網域 」 AAD DC 管理員 」 群組的使用者帳戶認證。 未指定的網域名稱的執行個體，在此範例中，我們指定 「 鮑 」 的使用者名稱。

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

設定 VM-指定網域加入需求與必要的認證。

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

設定 VM 子網路。

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

可省略︰ 指向該網域的 IP 位址。 如果您設定 DNS 伺服器的虛擬網路 Azure AD 網域服務管理網域的 IP 位址，就不需要此步驟。

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

現在，佈建網域的 Windows VM。

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>佈建 Windows VM 並自動將它加入 AAD 網域服務管理網域的指令碼
這個 PowerShell 命令集所建立的業務伺服器的虛擬機器的︰

- 使用 Windows Server 2012 R2 資料中心影像。
- 是小型額外的虛擬機器。
- 具有名稱 contoso 測試。
- 會自動加入網域 contoso100 受管理的網域。
- 會新增至相同的虛擬網路的受管理的網域。

以下是建立 Windows 虛擬機器並自動將它加入 Azure AD 網域服務管理網域的完整的範例指令碼。

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>相關的內容
- [Azure AD 網域服務-快速入門指南](./active-directory-ds-getting-started.md)

- [管理 Azure AD 網域服務管理的網域](./active-directory-ds-admin-guide-administer-domain.md)
