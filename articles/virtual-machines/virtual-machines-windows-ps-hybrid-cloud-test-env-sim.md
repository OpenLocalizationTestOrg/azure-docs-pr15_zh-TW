<properties 
    pageTitle="模擬的混合式雲端測試環境 |Microsoft Azure" 
    description="建立模擬的混合式雲端環境的 IT 專業人員或開發測試，使用兩個 Azure 虛擬網路和 VNet-VNet 連線。" 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>設定測試模擬的混合式雲端環境

本文會引導您完成建立使用兩個 Azure 虛擬網路的 Microsoft Azure 模擬的混合式雲端環境。 以下是結果的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

這會模擬混合式雲端生產環境，所組成︰

- 模擬與簡化內部部署網路裝載於 Azure 虛擬網路 （測試實驗室虛擬網路）。
- 模擬的跨內部部署虛擬網路裝載於 Azure (TestVNET)。
- 兩個虛擬網路之間的 VNet-VNet 連線。
- 第二個網域控制站 TestVNET 虛擬網路中。

這會提供基礎] 和 [一般開始點從您可以︰

- 開發和模擬的混合雲端環境中測試應用程式。
- 建立電腦，一些測試實驗室虛擬網路中，一些 TestVNET 虛擬網路中，以模擬混合式雲端的 IT 工作負載的測試的設定。

有四個設定此混合雲端測試環境的主要階段︰

1.  設定測試實驗室虛擬網路。
2.  建立跨內部部署虛擬網路。
3.  建立 VNet-VNet VPN 連線。
4.  設定 DC2。 

此設定會需要 Azure 的訂閱。 如果您有 MSDN 或 Visual Studio 訂閱，請參閱[針對 Visual Studio 版訂閱的每月 Azure 信用卡](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

>[AZURE.NOTE] 虛擬機器和 Azure 中的虛擬網路閘道器會在執行時都會造成進行中的貨幣成本。 此成本是針對您的 MSDN 計費或付費訂閱。 以一組的兩個 Azure 虛擬機器實作 Azure VPN 閘道器。 若要最小化的成本，建立測試環境，執行您需要測試和示範盡快。

## <a name="phase-1-configure-the-testlab-virtual-network"></a>階段 1︰ 設定測試實驗室虛擬網路

使用[底數設定測試環境](https://technet.microsoft.com/library/mt771177.aspx)主題中的指示，名為測試實驗室 Azure 虛擬網路設定 DC1、 APP1 及 CLIENT1 電腦。 

接下來，請啟動 Azure PowerShell 提示。

> [AZURE.NOTE] 下列命令會將使用 PowerShell 的 Azure 1.0 及更新版本。

登入您的帳戶。

    Login-AzureRMAccount

取得您使用下列命令的訂閱名稱。

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定您的 Azure 訂閱。 使用同一份訂閱您用來建立基本階段 1 組態設定。 報價，包括內的所有項目取代 < 與 > 字元，請以正確的名稱。

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

接下來，新增至您基本的設定，將用來主控 Azure 閘道器的測試實驗室虛擬網路的閘道器子網路。

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

接下來，要求將指派給測試實驗室虛擬網路的閘道器的公用 IP 位址。

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

接下來，建立您的閘道器。

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

請記住花費 20 分鐘或更多] 以建立新的閘道器。

從 Azure 入口網站上您的本機電腦，連線到 DC1 使用 CORP\User1 的認證。 若要設定 CORP 網域，讓電腦和使用者使用其本機網域控制站進行驗證，請執行下列命令系統管理員層級的 Windows PowerShell 命令提示字元 DC1 上。

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

這是您目前的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>階段 2︰ 建立 TestVNET 虛擬網路

首先，建立 TestVNET 虛擬網路，並加以保護與網路安全性群組。

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

接下來，要求配置給 TestVNET 虛擬網路的閘道器，並建立您的閘道器的公用 IP 位址。

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

這是您目前的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>階段 3︰ 建立 VNet-VNet 的連線

首先，請從您的網路或安全性的管理員，取得隨機、 強式密碼編譯 32 個字元的預先共用的金鑰。 或者，使用在[建立 IPsec 預先共用金鑰的隨機字串](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx)的資訊，取得預先共用的金鑰。

接下來，請使用這些命令來建立 VNet-VNet VPN 連線，可能要花一些時間才能完成。

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

請稍候幾分鐘，您應該建立連線。

這是您目前的設定。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>階段 4︰ 設定 DC2

首先，建立 DC2 虛擬機器。 在您的本機電腦上，在 PowerShell 的 Azure 命令提示字元執行這些命令。

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接下來，從 Azure 入口網站連線至新的 DC2 虛擬機器。

接下來，設定 Windows 防火牆規則，允許流量基本連線測試。 系統管理員層級 Windows PowerShell 命令提示字元 DC2 上，執行下列命令。

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

偵測 （ping） 命令應該會導致 IP 位址 10.0.0.4 從四個成功的回覆。 這是流量的透過 VNet-VNet 連線的測試。

接下來，新增額外的資料磁碟 DC2 上為新的磁碟區磁碟機字母 f:。

1.  在左窗格的 [伺服器管理員，按一下 [**檔案及儲存服務**]，然後按一下**磁碟**。
2.  在 [內容] 窗格中，在 [**磁碟**] 群組中按一下**磁碟 2** （設定為 [**未知****的磁碟分割**)。
3.  按一下 [**工作**]，然後按一下 [**新的區**。
4.  在與前段距離您開始新的大量精靈頁面，按一下 [**下一步**]。
5.  在 [選取 [伺服器和磁碟] 頁面上，按一下**磁碟 2**]，然後按 [**下一步**。 出現提示時，按一下**[確定]**。
6.  在 [指定 [音量] 頁面的大小，請按一下 [**下一步**]。
7.  在 [指派至磁碟機字母或資料夾頁面中，按一下 [**下一步**]。
8.  在 [選取檔案系統設定] 頁面中，按一下 [**下一步**]。
9.  在 [確認] 選項頁面中，按一下 [**建立**]。
10. 完成時，請按一下 [**關閉**]。

接下來，設定 DC2 複本網域控制站 corp.contoso.com 網域。 在 [DC2 上，從 Windows PowerShell 命令提示字元中執行這些命令。

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

請注意，系統會提示您提供 CORP\User1 密碼] 和 [目錄服務還原模式 (DSRM) 密碼，並重新啟動 DC2。

現在，TestVNET 虛擬網路會有自己的 DNS 伺服器 (DC2)，您必須設定 TestVNET 虛擬網路，若要使用這個 DNS 伺服器。

1.  在 Azure 入口網站的左窗格中，按一下 [虛擬網路] 圖示，然後按一下**TestVNET**。
2.  按一下 [**設定**] 索引標籤的 [ **DNS 伺服器**。
3.  在**主要的 DNS 伺服器**中，輸入取代 10.0.0.4 **192.168.0.4** 。
4.  按一下 [**儲存**]。

這是您目前的設定。 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
模擬的混合雲端環境已準備好進行測試。

## <a name="next-step"></a>下一步

- 設定此環境中[的商務應用程式的 web 式行](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md)。
