<properties
   pageTitle="使用 PowerShell 來建立具網際網路負載平衡器資源管理員中 |Microsoft Azure"
   description="瞭解如何使用 PowerShell 資源管理員] 中建立具網際網路負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
   ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="get-started"></a>使用 PowerShell 來建立具網際網路負載平衡器在資源管理員

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋資源管理員部署模型。 您也可以[瞭解如何建立使用傳統的部署模型具網際網路負載平衡器](load-balancer-get-started-internet-classic-cli.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>使用 PowerShell 的 Azure 部署解決方案

下列程序說明如何使用 PowerShell 的 Azure 資源管理員] 來建立具網際網路負載平衡器。 使用 Azure 資源管理員中，每個資源是建立與個別，設定，然後將放在一起，建立負載平衡器。

您必須建立並設定下列物件部署負載平衡器︰

- 前端 IP 設定︰ 包含傳入網路流量的公用 IP (PIP) 地址。
- 位址後端資料庫︰ 從負載平衡器接收網路流量的虛擬機器中包含網路介面 (Nic)。
- 負載平衡規則︰ 包含對應公用的連接埠負載平衡器位址後端資料庫中的連接埠的規則。
- 連入 NAT 規則︰ 包含對應到特定虛擬機器位址後端資料庫中的連接埠的負載平衡器公用的連接埠的規則。
- 探查︰ 包含狀況探查用來查看虛擬機器中的執行個體的後端位址集區的顯示狀態。

如需詳細資訊，請參閱[Azure 資源管理員支援負載平衡器](load-balancer-arm.md)。

## <a name="set-up-powershell-to-use-resource-manager"></a>將 PowerShell 設定為使用資源管理員

請確定您有 PowerShell 的 Azure 資源管理員在模組的最新的產品版本︰

1. 登入 Azure。

        Login-AzureRmAccount

    輸入您的認證出現提示時。

2. 核取訂閱的帳戶。

        Get-AzureRmSubscription

3. 選擇您要使用的 Azure 訂閱。

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. 建立資源群組。 （略過此步驟如果您使用現有的資源群組。）

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>建立虛擬網路和前端 IP 集區的公用 IP 位址

1. 建立子網路和虛擬網路。

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. 建立 Azure 公用 IP 位址資源，名為**PublicIP**，若要使用的 DNS 名稱**loadbalancernrp.westus.cloudapp.azure.com**前端 IP 集區。 下列命令會使用靜態配置類型。

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]負載平衡器使用 FQDN 作為前置字元的公用 IP 網域標籤。 這是不同的傳統部署模型，即為負載平衡器 FQDN 使用雲端服務。
    >在此範例中，FQDN 是**loadbalancernrp.westus.cloudapp.azure.com**。

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>建立 IP 集區前端與後端地址資料庫

1. 建立名為**LB Frontend** **PublicIp**資源所使用的前端 IP 集區。

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. 建立名為**LB 後端**位址後端資料庫。

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>建立 NAT 規則、 負載平衡器規則，檢查，以及負載平衡器

此範例會建立下列項目︰

- 若要翻譯至連接埠 3389 的連接埠 3441 上的所有內送流量 NAT 規則
- 若要翻譯至連接埠 3389 的連接埠 3442 上的所有內送流量 NAT 規則
- 若要檢查的頁面上的健康狀態這個規則命名**HealthProbe.aspx**
- 要平衡到後端資料庫中的地址的連接埠 80 的連接埠 80 上的所有內送流量負載平衡器規則
- 負載平衡器所使用的所有物件

使用下列步驟︰

1. 建立 NAT 規則。

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. 建立狀況檢查。 有兩種方式來設定檢查︰

    HTTP 探查

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    TCP 探查

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. 建立負載平衡器規則。

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. 使用先前建立的物件，建立負載平衡器。

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>建立 Nic

建立網路介面 （或修改現有的），然後將其 NAT 規則，負載平衡器規則]，然後探查建立關聯︰

1. 取得虛擬網路和子虛擬網路，Nic 需要建立的位置。

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. 建立具名的 NIC **lb nic1 會**並將其第一個 NAT 規則與第一個 （和只） 後端地址資料庫建立關聯。

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. 建立具名的 NIC **lb nic2 會**並將其第二個 NAT 規則與第一個 （和只） 後端地址資料庫建立關聯。

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. 核取 Nic。

        $backendnic1

    預期的輸出︰

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. 使用`Add-AzureRmVMNetworkInterface`cmdlet 來指定不同的 Vm Nic。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

如需建立虛擬機器及指派 NIC 指導，請參閱[建立 Azure VM 使用 PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)。

## <a name="add-the-network-interface-to-the-load-balancer"></a>新增網路介面的負載平衡器

1. 從 Azure 擷取負載平衡器。

    （如果尚未執行此步驟），則請載入變數負載平衡器資源。 變數稱為**$lb**。使用相同的名稱從您先前建立的負載平衡器資源。

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. 載入至變數的後端設定。

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. 載入變數已建立的網路介面。 在變數名稱是**$nic**。 網路介面名稱是從先前的範例相同。

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. 變更後端設定的網路介面上。

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. 儲存網路介面物件。

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    網路介面會新增至負載平衡器後端資料庫後，便會啟動接收根據負載平衡器資源的負載平衡規則的網路流量。

## <a name="update-an-existing-load-balancer"></a>更新現有的負載平衡器

1. 藉由使用負載平衡器從先前的範例，負載平衡器物件變數**$slb**使用指派`Get-AzureLoadBalancer`。

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. 在下列範例中，您可以新增輸入的 NAT 規則-使用連接埠 81 前端資料庫中並連接埠 8181 的後端資料庫，現有的負載平衡器。

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. 使用中儲存新設定`Set-AzureLoadBalancer`。

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>移除負載平衡器

使用命令`Remove-AzureLoadBalancer`刪除先前建立的負載平衡器中名為 「 **NRP LB**資源群組稱為**NRP 路由**。

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] 您可以使用選擇性切換**-強制**若要避免刪除的提示。

## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)
