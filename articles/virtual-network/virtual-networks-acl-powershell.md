<properties
   pageTitle="如何管理存取控制清單 (Acl) 端點使用 PowerShell"
   description="瞭解如何管理 Acl 使用 PowerShell"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>如何管理存取控制清單 (Acl) 端點使用 PowerShell

您可以建立和管理網路存取控制清單 (Acl) 的結束點使用 PowerShell 的 Azure 或管理入口網站中。 本主題中，您會發現 ACL 一般工作，您可以使用 PowerShell 來完成的程序。 Azure PowerShell 指令程式的清單，請參閱[Azure 管理 Cmdlet](http://go.microsoft.com/fwlink/?LinkId=317721)。 如需有關 Acl 的詳細資訊，請參閱[什麼是網路存取控制清單 (ACL)？](virtual-networks-acl.md)。 如果您想要使用 [管理] 入口網站管理您的 Acl，請參閱[如何設定設定結束點到虛擬機器](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)。

## <a name="manage-network-acls-by-using-azure-powershell"></a>使用 PowerShell 的 Azure 管理網路 Acl

您可以使用 Azure PowerShell cmdlet 來建立、 移除及設定 （設定） 網路存取控制清單 (Acl)。 包含了一些您可以設定使用 PowerShell ACL 的方式的一些範例。

若要擷取的 ACL PowerShell 指令程式的完整清單，您可以使用下列其中一項︰

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>建立規則，允許從遠端子網路的存取網路 ACL

下列範例所示的方式來建立新的 ACL 包含規則。 此 ACL 適用於虛擬機器結束點。 在下面的範例 ACL 規則會允許從遠端子網路存取]。 若要建立新的網路 ACL 遠端子網路允許規則，請開啟 Azure PowerShell ise [以系統。 複製並貼上的指令碼下方，為您自己的值，設定指令碼，然後執行指令碼。

1. 建立新的網路 ACL 物件。

        $acl1 = New-AzureAclConfig

1. 設定規則，允許從遠端子網路的存取。 在下面的範例中，您可以設定規則*100* （其優先順序透過規則 200 及更新版本） 若要讓遠端子網路*10.0.0.0/8*存取虛擬機器結束點。 取代您自己的設定需求的值。 名稱 「 SharePoint ACL 設定] 應取代您要撥打此規則的易記名稱。

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. 針對其他規則，請重複指令程式，使用您自己的設定需求取代的值。 請務必變更規則的數字順序，以反映您想要套用規則的順序。 低的規則數目優先於較高的數字。

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. 接下來，您可以建立新的結束點 （新增），或設定現有的端點 （設定） 的 ACL。 在此範例中，我們會加入新的虛擬機器端點，稱為 [網站]，並使用 ACL 設定更新虛擬機器結束點。

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. 接下來，請將 cmdlet 合併，並執行指令碼。 例如，合併的 cmdlet 如下所示︰

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>移除網路 ACL 規則，允許從遠端子網路的存取權

下列範例會示範移除網路 ACL 規則的方法。  若要移除的遠端子網路的允許規則的網路 ACL 規則，請開啟 Azure PowerShell ise [以系統。 複製並貼上的指令碼下方，為您自己的值，設定指令碼，然後執行指令碼。

1. 第一個步驟是將網路 ACL 物件取得虛擬機器結束點。 您將會移除 ACL 規則。 在此案例中，我們會加以移除的規則識別碼。 這只會移除的規則識別碼 0 acl。 它不會移除 ACL 物件虛擬機器結束點。

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. 接下來，您必須將網路 ACL 物件套用至虛擬機器結束點] 並更新虛擬機器。

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>網路 ACL 移除虛擬機器端點

在某些情況下，您可能要移除的虛擬機器端點網路 ACL 物件。 若要執行這項作業，開啟 Azure PowerShell ise [以系統。 複製並貼上的指令碼下方，為您自己的值，設定指令碼，然後執行指令碼。

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>後續步驟

[什麼是網路存取控制清單 (ACL)？](virtual-networks-acl.md)
