<properties
    pageTitle="設定使用 PowerShell 的 Azure VM 一直在可用性群組"
    description="本教學課程中使用傳統的部署模型，以建立的資源，並使用 PowerShell 永遠上顯示狀態中建立群組 Azure。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>設定使用 PowerShell 的 Azure VM 一直在可用性群組

> [AZURE.SELECTOR]
- [資源管理員︰ 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [資源管理員︰ 手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [傳統︰ UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [傳統︰ PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure 虛擬機器 (Vm) 可協助實作較低的可用性 SQL Server 系統成本資料庫系統管理員。 本教學課程教您如何實作使用 SQL Server 永遠在結束端對端的 Azure 環境內可用性群組。 教學課程結尾您 SQL Server 永遠解決方案 Azure 中的會包含下列元素︰

- 包含多個子網路，包括前端與後端子網路虛擬網路

- 網域控制站的 Active Directory (AD) 網域

- 兩個 SQL Server Vm 部署到後端的子網路及 AD 網域

- 3-節點 WSFC 叢集節點多數仲裁模型

- 使用兩個同步認可複本可用性資料庫的可用性群組

這種情況下會選擇簡單，不適用於其成本效果或其他因素 Azure 上。 例如，您可以減少的 Vm 可用性兩個複本群組才能儲存為仲裁檔案共用見證 2 節點 WSFC 集中使用的網域控制站 Azure 中的計算小時。 這個方法減少 VM 計算一個以上的設定。

本教學課程旨在說明您設定以上所述的解決方案，但不詳細說明的詳細資料的每一個步驟所需的步驟。 因此，而非顯示 GUI 設定步驟，就會使用 PowerShell 指令碼來協助您快速每一個步驟。 假設下列動作︰

- 您已經有 Azure 虛擬機器訂閱的帳戶。

- 您已安裝[Azure PowerShell cmdlet](../powershell-install-configure.md)。

- 您已經有深入瞭解永遠在可用性群組的內部部署的解決方案。 如需詳細資訊，請參閱[永遠在可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>連線至您訂閱的 Azure 並建立虛擬網路

1. 視窗中的 PowerShell 本機電腦上匯入 Azure 模組、 發佈的設定檔案下載到您的電腦，並連線至您訂閱的 Azure 您 PowerShell 工作階段匯入下載的發佈設定。

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **取得 AzurePublishgSettingsFile** ] 命令會自動產生管理 Azure 憑證下載檔案至您的電腦。 在瀏覽器將會自動開啟，系統會提示您輸入的 Microsoft 帳戶認證 Azure 訂閱。 下載的**.publishsettings**檔案會包含所有管理 Azure 訂閱所需的資訊。 之後將檔案儲存至本機目錄，匯入並使用 [**匯入 AzurePublishSettingsFile** ] 命令。

    >[AZURE.NOTE] Publishsettings 檔案包含您的認證 （解碼），用來管理您的訂閱 Azure 和服務。 此檔案的安全性最佳作法是暫時外部來源目錄 （例如在 Libraries\Documents 資料夾），將其儲存，並完成匯入後再將它刪除。 惡意使用者取得檔案的存取權 publishsettings 可以編輯、 建立及刪除 Azure 服務。

1. 定義一系列的您將用來建立您的雲端 IT 基礎結構的變數。

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    請注意下列動作，以確保您的命令會稍後成功︰

    - 變數**$storageAccountName**和**$dcServiceName**必須是唯一因為它們用來識別您雲端儲存空間帳戶和雲端伺服器，分別在網際網路上。

    - 您稍後使用的虛擬網路設定文件中設定為變數**$affinityGroupName**和**$virtualNetworkName**指定的名稱。

    - **$sqlImageName**指定 VM 圖像包含 SQL Server 2012 Service Pack 1 企業版的更新的名稱。

    - 為求， **Contoso ！ 000**是整個教學課程中使用相同的密碼。

1. 建立相關性群組。

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. 建立虛擬網路匯入的設定檔。

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    設定檔案包含下列的 XML 文件。 簡而言之，它會指定虛擬網路，稱為**ContosoAG**，相關性群組中，名為 [ **ContosoNET**並有地址空間**10.10.0.0/16**和具有兩個子網路、 **10.10.1.0/24**和**10.10.2.0/24**，它是使用正面與子網路，分別返回子網路]。 使用正面與子網路可讓您可以將用戶端應用程式，例如 Microsoft SharePoint，返回子網路是放置 SQL Server Vm 的位置。 如果您先前變更**$affinityGroupName**和**$virtualNetworkName**變數，您也必須變更相對應的名稱。

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. 建立與相關性群組您已建立並將其設為目前的儲存空間帳戶中，在您的訂閱相關聯的儲存空間帳戶。

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. 在新的雲端服務及可用性設定建立 DC 伺服器。

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    這一系列的管道的命令會執行下列動作︰

    - **新增 AzureVMConfig**建立 VM 設定。

    - **新增 AzureProvisioningConfig**提供獨立 Windows server 設定的參數。

    - **新增 AzureDataDisk**會將您將會用來將 Active Directory 資料，與快取選項設定為 [無資料磁碟。

    - **新增 AzureVM**建立新的雲端服務，並在新的雲端服務中建立新的 Azure VM。

1. 等待新 VM 完全佈建後，並將遠端桌面檔案下載到您的工作目錄。 新的 Azure VM 耗費很長時間進行佈建，因為循環播放時持續以獲得新 VM，才可供使用。

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

現在成功提供 DC 伺服器。 接下來，您就會設定 Active Directory 網域 DC 伺服器上。 在您的本機電腦上保持 PowerShell 視窗開啟。 您會使用該稍後再次建立兩個 SQL Server Vm。

## <a name="configure-the-domain-controller"></a>設定網域控制站

1. 啟動遠端桌面檔案，以連線至 DC 伺服器。 使用電腦的系統管理員的 AzureAdmin 的使用者名稱和密碼**Contoso ！ 000**，您所指定建立新的 VM 時。

1. 在 [系統管理員模式中開啟 PowerShell 視窗。

1. 執行下列動作**帶領。執行檔**設定**corp.contoso.com**的網域，在磁碟機 M 資料目錄] 命令。

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    命令完成後，VM 會自動重新啟動。

1. 連線到 DC 伺服器再次啟動遠端桌面檔案。 這次請登入為**CORP\Administrator**。

1. 以系統管理員模式開啟 PowerShell 視窗並匯入 Active Directory PowerShell 模組使用下列命令︰

        Import-Module ActiveDirectory

1. 執行下列命令，將三個使用者新增至該網域。

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install**用來設定有關 SQL Server 服務執行個體、 WSFC 叢集和 [可用性] 群組中的所有項目。 **CORP\SQLSvc1**和**CORP\SQLSvc2**會作為 SQL Server 服務帳戶的兩個 SQL Server Vm。

1. 接下來，請執行下列命令，在網域] 建立電腦物件的權限授與**CORP\Install** 。

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    上述指定 GUID 是電腦物件類型 GUID。 **CORP\Install**帳戶需要**讀取全部內容**和**建立電腦物件**權限，才能建立 WSFC 叢集直接作用中的物件。 **讀取所有內容**的權限已被 CORP\Install 根據預設，所以您不需要授予明確。 如需有關建立 WSFC 叢集時所需的權限的詳細資訊，請參閱[容錯移轉叢集逐步指南︰ 在 Active Directory 中的 [設定帳戶](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)。

    現在您完成設定 Active Directory 和使用者物件時，您會建立兩個 SQL Server Vm 並加入此網域。

## <a name="create-the-sql-server-vms"></a>建立 SQL Server Vm

1. 繼續使用 PowerShell 視窗，即會在您的本機電腦上開啟。 定義下列其他變數︰

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP 位址**10.10.0.4**通常會被指派您建立 Azure 虛擬網路的**10.10.0.0/16**子網路中的第一個 vm。 您應該確認這是執行**IPCONFIG**的 DC 伺服器的位址。

1. 執行 WSFC 叢集，名為**ContosoQuorum**中建立的第一個 VM 管道下列命令︰

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    請注意下列有關上述命令︰

    - **新增 AzureVMConfig**建立 VM 設定所要的顯示狀態設定名稱。 後續 Vm 將會建立同名的可用性設定，使它們加入相同的可用性設定。

    - **新增 AzureProvisioningConfig**連接 VM 所建立的 Active Directory 網域。

    - **設定 AzureSubnet**放置 VM 返回子網路中。

    - **新增 AzureVM**建立新的雲端服務，並在新的雲端服務中建立新的 Azure VM。 **DnsSettings**參數指定為新的雲端服務中的伺服器的 DNS 伺服器 IP 位址**10.10.0.4**，也就是 DC 伺服器的 IP 位址。 若要啟用新 Vm 雲端服務中的順利加入到 Active Directory 網域需要此參數。 沒有此參數，您必須以手動方式 IPv4 在中設定您的 VM VM 已佈建後，使用 DC 伺服器為主要的 DNS 伺服器，並加入 VM Active Directory 網域。

1. 執行下列管道的命令來建立 SQL Server Vm，「 **ContosoSQL1** **ContosoSQL2**。

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    請注意下列有關上方的命令︰

    - **新增 AzureVMConfig**使用 DC 伺服器時，使用相同的可用性設定名稱，然後使用的 SQL Server 2012 Service Pack 1 企業版圖像虛擬機器圖庫中。 它也會以閱讀快取只將作業系統磁碟 （沒有寫入快取）。 建議您將資料庫檔案移轉到另一個資料磁碟您附加的 vm 及設定未讀取或寫入快取。 不過下, 一步的最佳做法是若要移除寫入快取作業系統磁碟上，因為您無法移除閱讀作業系統磁碟上的快取。

    - **新增 AzureProvisioningConfig**連接 VM 所建立的 Active Directory 網域。

    - **設定 AzureSubnet**放置 VM 返回子網路中。

    - **新增 AzureEndpoint**新增存取端點，以便用戶端應用程式能夠存取網際網路上的下列 SQL Server 服務執行個體。 不同的連接埠是提供給 ContosoSQL1 和 ContosoSQL2。

    - **新增 AzureVM** ContosoQuorum 為相同的雲端服務中建立新的 SQL Server VM。 如果您想要在相同的可用性設定，您必須在同一個雲端服務中放置 Vm。

1. 每個 VM 完全佈建和其遠端桌面檔案下載至您的工作目錄，請等候。 循環三個新 Vm 循環播放，以及執行每個最上層的大括號內的命令。

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    SQL Server Vm 現在佈建後，執行，但它們會安裝 SQL server 使用預設選項。

## <a name="initialize-the-wsfc-cluster-vms"></a>初始化 WSFC 叢集 Vm

在此區段中，您需要修改 WSFC 叢集和 SQL Server 安裝中的三個伺服器。 特別是︰

- （所有伺服器）您需要安裝的**容錯**功能。

- （所有伺服器）您需要將**CORP\Install**新增為電腦的**系統管理員**。

- （ContosoSQL1 和 ContosoSQL2 只）您需要新增**CORP\Install**為**系統管理員**角色中的預設資料庫。

- （ContosoSQL1 和 ContosoSQL2 只）您必須將**NT AUTHORITY\System**新增具有下列權限的登入︰

    - 變更任何可用性] 群組

    - 連線 SQL

    - 檢視伺服器狀態

- （ContosoSQL1 和 ContosoSQL2 只）SQL Server VM 上已啟用**TCP**通訊協定。 不過，您仍需要開啟遠端存取的 SQL Server 的防火牆。

現在，您已經準備好開始。 開始使用**ContosoQuorum**，請遵循下列步驟︰

1. 連線到**ContosoQuorum**啟動遠端桌面檔案。 使用電腦的系統管理員的**AzureAdmin**的使用者名稱和密碼**Contoso ！ 000**，您所指定建立 Vm 時。

1. 確認電腦有至**corp.contoso.com**成功加入。

1. SQL Server 安裝完成執行自動的初始化工作之前，請等候。

1. 在 [系統管理員模式中開啟 PowerShell 視窗。

1. 安裝 Windows 容錯功能。

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. 本機系統管理員身分新增**CORP\Install** 。

        net localgroup administrators "CORP\Install" /Add

1. 登出 ContosoQuorum。 您會立即完成此伺服器。

        logoff.exe

接下來，初始化**ContosoSQL1**和**ContosoSQL2**。 請遵循下列步驟，也就是這兩個 SQL Server Vm 相同。

1. 連接到兩個 SQL Server Vm 啟動遠端桌面檔案。 使用電腦的系統管理員的**AzureAdmin**的使用者名稱和密碼**Contoso ！ 000**，您所指定建立 Vm 時。

1. 確認電腦有至**corp.contoso.com**成功加入。

1. SQL Server 安裝完成執行自動的初始化工作之前，請等候。

1. 在 [系統管理員模式中開啟 PowerShell 視窗。

1. 安裝 Windows 容錯功能。

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. 本機系統管理員身分新增**CORP\Install**

        net localgroup administrators "CORP\Install" /Add

1. 匯入 SQL Server PowerShell 提供者。

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. 新增預設的 SQL Server 執行個體的系統管理員角色**CORP\Install** 。

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. 新增具有三個的登入**NT AUTHORITY\System**上述的權限。

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. 開啟遠端存取的 SQL Server 的防火牆。

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. 登出兩個 Vm。

        logoff.exe

最後，您可以開始設定可用性群組。 您會使用 SQL Server PowerShell 提供者來執行**ContosoSQL1**的所有工作。

## <a name="configure-the-availability-group"></a>設定 [可用性] 群組

1. 連線到**ContosoSQL1**再次啟動遠端桌面檔案。 在使用電腦帳戶的記錄，而不是使用登入**CORP\Install**。

1. 在 [系統管理員模式中開啟 PowerShell 視窗。

1. 定義下列變數︰

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. 匯入 SQL Server PowerShell 提供者。

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. 變更 CORP\SQLSvc1 ContosoSQL1 的 SQL Server 服務帳戶。

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. 變更 CORP\SQLSvc2 ContosoSQL2 的 SQL Server 服務帳戶。

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. 從[建立 WSFC 叢集永遠上顯示狀態中的群組 Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)的**CreateAzureFailoverCluster.ps1**下載至本機工作目錄中。 您會使用這個指令碼來協助您建立功能 WSFC 叢集。 重要 WSFC 與 Azure 網路之間的互動方式的詳細資訊，請參閱[高可用性及損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-high-availability-dr.md)。

1. 變更您使用的目錄，並建立 WSFC 叢集下載指令碼。

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. 啟用永遠在可用性群組**ContosoSQL1**和**ContosoSQL2**上的預設 SQL Server 執行個體。

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. 建立備份的目錄，並授與權限的 SQL Server 服務帳戶。 您會使用這個目錄來準備次要複本上的可用性資料庫。

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. **ContosoSQL1**稱為**MyDB1**上建立資料庫完整備份，然後記錄備份，與還原方式上**ContosoSQL2** **與 NORECOVERY**選項。

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. 在 SQL Server Vm 上建立可用性] 群組中的端點並設定適當的權限的端點上。

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. 建立可用性複本。

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. 最後，建立 [可用性] 群組中，加入可用性] 群組中的次要複本。

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>後續步驟
您現在成功實作 SQL Server 永遠在 Azure 中建立可用性群組。 若要設定的接聽這個可用性群組，請參閱[設定一律上顯示狀態中的群組 Azure ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)。

如需使用 SQL Server Azure 中的其他資訊，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
