<properties
   pageTitle="建立資源管理員] 中使用 PowerShell 內部負載平衡器 |Microsoft Azure"
   description="瞭解如何建立使用 PowerShell 中資源管理員內部負載平衡器"
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

# <a name="create-an-internal-load-balancer-using-powershell"></a>建立使用 PowerShell 內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](load-balancer-get-started-ilb-classic-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


下列步驟說明如何建立使用 PowerShell 的 Azure 資源管理員內部負載平衡器。 使用 Azure 資源管理員中，建立內部負載平衡器的項目設定個別，然後結合以建立負載平衡器。

您需要建立及設定下列物件部署負載平衡器︰

- 上層結束 IP 設定-將會設定內送的網路流量的私人 IP 位址
- 後端地址集區-將會設定將會收到來自 [前端 IP 集區的負載平衡流量的網路介面
- 負載平衡規則-來源與本機的連接埠負載平衡器設定。
- 探查-設定健康狀態探查虛擬機器執行個體。
- 連入 NAT 規則-設定的連接埠規則，以便直接存取其中一個虛擬機器執行個體。

您可以在[Azure 資源管理員支援負載平衡器](load-balancer-arm.md)Azure 資源管理員取得深入瞭解載入平衡器元件。

下列步驟說明如何設定兩個虛擬機器之間負載平衡器。


## <a name="setup-powershell-to-use-resource-manager"></a>使用資源管理員設定 PowerShell

請確定您已針對 PowerShell 的 Azure 模組的最新的產品版本，並 PowerShell 設定正確存取 Azure 訂閱。

### <a name="step-1"></a>步驟 1

        Login-AzureRmAccount

### <a name="step-2"></a>步驟 2

核取訂閱的帳戶

        Get-AzureRmSubscription

系統會提示您驗證與您的認證。<BR>

### <a name="step-3"></a>步驟 3

選擇您要使用的 Azure 訂閱。 <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>建立資源群組負載平衡器

### <a name="step-4"></a>步驟 4

建立新的資源群組 （略過此步驟如果使用現有的資源群組）

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Azure 資源管理員需要所有資源群組都指定的位置。 這用於做為預設位置的 [資源] 群組中的資源。 請確認所有命令來建立負載平衡器會都使用相同的資源群組。

在上述範例中，我們建立名為 「 NRP 路由 」 和 「 西部美國 」 的位置的資源群組。

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>建立虛擬網路和前端 IP 集區私人的 IP 位址


### <a name="step-1"></a>步驟 1

建立虛擬網路的子網路，並將指派給變數 $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

建立虛擬網路︰

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

建立虛擬網路和子網路 lb 子網路會加入虛擬網路 NRPVNet 指派給變數 $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>建立最上層 IP 集區及後端位址集區

設定前端 IP 資料庫內送負載平衡器網路流量與後端地址集區接收負載平衡流量。

### <a name="step-1"></a>步驟 1

建立私人的 IP 位址 10.0.2.5 用子網路 10.0.2.0/24 會是內送的網路流量端點的 IP 前端集區。

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>步驟 2

設定後端地址集區前端 IP 資料庫中接收內送的流量使用︰

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>建立 LB 規則 NAT 規則、 探查及負載平衡器

建立之後的前端 IP 集區和地址後端資料庫，您將需要建立的規則會將屬於負載平衡器資源︰

### <a name="step-1"></a>步驟 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


上述範例會建立下列項目︰

- 連接埠 3441 所有內送的流量會移至 [連接埠 3389 NAT 規則。
- 第二個 NAT 規則的連接埠 3442 所有內送的流量會移至 [連接埠 3389。
- 將會載入的負載平衡器規則，平衡公用的連接埠 80 到本機的連接埠 80 後端位址集區上的所有內送流量。
- 會檢查路徑 」 HealthProbe.aspx 」 的健康狀態探查規則



### <a name="step-2"></a>步驟 2

建立負載平衡器一起新增 NAT 規則、 負載平衡器規則 （這個設定） 的所有物件︰

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>建立網路介面

建立內部負載平衡器之後，您需要定義的內送的負載平衡網路流量、 NAT 規則和探查，會收到的網路介面。 在此情況下網路介面個別的設定，並可以稍後指派給虛擬機器。


### <a name="step-1"></a>步驟 1


取得資源虛擬網路和子網路建立網路介面︰

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


此步驟會建立一個網路介面屬於負載平衡器後端資料庫，並建立第一個 NAT 規則的 RDP 關聯這個網路介面︰

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>步驟 2

建立第二個網路介面一部分 LB Nic2 會︰

此步驟會建立第二個網路介面，建立 RDP 指派給相同的負載平衡器後端資料庫，並將相關聯的第二個 NAT 規則︰

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

最後的結果會顯示下列動作︰

    $backendnic1

預期的輸出︰

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>步驟 3

在 NIC 指派到的虛擬機器中使用 [新增 AzureRmVMNetworkInterface] 命令。

您可以找到的逐步指示，建立虛擬機器及指定給追蹤文件的 NIC︰[建立 Azure VM 使用 PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)。

如果您已經建立虛擬機器，您可以新增網路介面，執行下列步驟︰

#### <a name="step-1"></a>步驟 1

（如果尚未執行此步驟），則請載入變數負載平衡器資源。 使用該變數稱為 $lb，而使用相同的名稱從先前所建立的負載平衡器資源。

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>步驟 2

載入至變數的後端設定。

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>步驟 3

載入變數已建立的網路介面。 使用的變數名稱是 $nic 使用網路介面名稱會與上述範例相同。

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>步驟 4

變更後端設定的網路介面上。

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>步驟 5

儲存網路介面物件。

    Set-AzureRmNetworkInterface -NetworkInterface $nic

網路介面會新增至負載平衡器後端資料庫後，便會啟動接收根據負載平衡負載平衡器資源的規則的網路流量。

## <a name="update-an-existing-load-balancer"></a>更新現有的負載平衡器


### <a name="step-1"></a>步驟 1

使用負載平衡器上述範例中的，將負載平衡器物件指派給變數 $slb 使用取得 AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>步驟 2

在下列範例中，您會新增至現有的負載平衡器的後端資料庫使用連接埠 81 中前端和連接埠 8181 連入 NAT 規則

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>步驟 3

儲存新設定使用設定 AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>移除負載平衡器

若要刪除先前建立的負載平衡器名為 「 NRP-LB 」 資源群組中稱為 「 NRP 路由 」 中使用 [移除 AzureRmLoadBalancer] 命令

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] 您可以使用 [選擇性切換-強制以避免刪除的提示。



## <a name="next-steps"></a>後續步驟

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)