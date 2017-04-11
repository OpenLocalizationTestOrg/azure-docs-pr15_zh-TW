<properties
    pageTitle="建立網際網路圖示的負載平衡器 IPv6 使用 PowerShell 的資源管理員 |Microsoft Azure"
    description="瞭解如何建立網際網路圖示的負載平衡器 IPv6 使用 PowerShell 的資源管理員"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="ipv6 azure 負載平衡器、 雙堆疊、 公用 ip、 原生 ipv6、 行動電話、 iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>開始建立網際網路圖示的負載平衡器 IPv6 使用 PowerShell 的資源管理員

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Azure CLI](./load-balancer-ipv6-internet-cli.md)
- [範本](./load-balancer-ipv6-internet-template.md)

Azure 負載平衡器是層級 4 （TCP、 UDP） 負載平衡器。 負載平衡器提供高可用性來發送連入流量的各項在雲端服務的健康服務執行個體或虛擬機器中負載平衡器設定。 Azure 負載平衡器也可以顯示多個連接埠、 多個 IP 位址，或兩者皆這些服務。

## <a name="example-deployment-scenario"></a>部署案例

下圖說明負載平衡部署本文中的解決方案。

![負載平衡器案例](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

在這個案例中，您會建立下列 Azure 資源︰

- 具網際網路負載平衡器 IPv4 與 IPv6 公用 IP 位址
- 兩個負載平衡規則，將公用 Vip 對應到私人的端點
- 顯示狀態設定為的包含兩個 Vm
- 兩個虛擬機器 (Vm)
- 每個 VM IPv4 和 IPv6 位址指派虛擬網路介面

## <a name="deploying-the-solution-using-the-azure-powershell"></a>部署使用 PowerShell 的 Azure 解決方案

下列步驟會顯示如何建立網際網路圖示的負載平衡器使用 PowerShell 的 Azure 資源管理員。 使用 Azure 資源管理員中，每個資源會建立並設定個別，然後放置在一起，建立資源。

若要部署負載平衡器，您可以建立並設定下列物件︰

- 前端 IP 設定-包含傳入網路流量的公用 IP 位址。
- 位址後端資料庫-包含從負載平衡器接收網路流量的虛擬機器網路介面 (Nic)。
- 負載平衡規則-包含對應公用的連接埠負載平衡器位址後端資料庫中的連接埠的規則。
- 連入 NAT 規則-包含對應到特定虛擬機器位址後端資料庫中的連接埠的負載平衡器公用的連接埠的規則。
- 探查-包含用來查看虛擬機器中的執行個體的後端位址集區的顯示狀態的狀況檢查]。

如需詳細資訊，請參閱[Azure 資源管理員支援負載平衡器](load-balancer-arm.md)。

## <a name="set-up-powershell-to-use-resource-manager"></a>將 PowerShell 設定為使用資源管理員

請確定您有 PowerShell 的 Azure 資源管理員在模組的最新的產品版本。

1. 登入 Azure

        Login-AzureRmAccount

    輸入您的認證出現提示時。

2. 核取訂閱的帳戶

        Get-AzureRmSubscription

3. 選擇您要使用的 Azure 訂閱。

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. 建立資源群組 （略過此步驟如果使用現有的資源群組）

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>建立虛擬網路和前端 IP 集區的公用 IP 位址

1. 建立子網路虛擬網路。

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. 建立 Azure 公用 IP 位址 (PIP) 的前端的 IP 位址集區的資源。

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] 負載平衡器會使用公用 IP 網域的標籤作為前置詞的 FQDN。 在此範例中，Fqdn 而*lbnrpipv4.westus.cloudapp.azure.com* *lbnrpipv6.westus.cloudapp.azure.com*。

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>建立前端 IP 設定與後端位址集區

1. 建立使用您所建立的公用 IP 位址的前端地址設定。

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. 建立位址後端資料庫。

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>建立 LB 規則、 NAT 規則，檢查，以及負載平衡器

此範例會建立下列項目︰

- 若要翻譯連接埠 4443 的連接埠 443 上的所有內送流量 NAT 規則
- 要平衡到後端資料庫中的地址的連接埠 80 的連接埠 80 上的所有內送流量負載平衡器規則。
- 若要允許連接埠 3389 上 Vm RDP 連線負載平衡器規則。
- 檢查頁面上的健康狀態探查規則命名*HealthProbe.aspx*或連接埠 8080 服務
- 負載平衡器所使用的所有物件

1. 建立 NAT 規則。

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. 建立狀況檢查。 有兩種方式來設定檢查︰

    HTTP 探查

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    或 TCP 探查

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
        $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2


    例如，我們將使用 TCP 探查。

3. 建立負載平衡器規則。

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389

4. 建立負載平衡器使用先前建立的物件。

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule

## <a name="create-nics-for-the-back-end-vms"></a>建立後端 Vm Nic

1. 取得虛擬網路及虛擬子網路，Nic 需要建立的位置。

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. 建立 Vm IP 設定與 Nic。

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>建立虛擬機器及指定新建立的 Nic

如需建立 VM 的詳細資訊，請參閱[建立和預先設定 Windows 資源管理員與 PowerShell 的 Azure 虛擬機器](..\virtual-machines\virtual-machines-windows-ps-create.md)

1. 建立可用性設定和儲存的帳戶

        New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'

2. 建立每個 VM 及指定前一個建立 Nic

        $mySecureCredentials= Get-Credential -Message “Type the username and password of the local administrator account.”

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
