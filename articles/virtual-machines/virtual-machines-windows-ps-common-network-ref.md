<properties
    pageTitle="常見網路 PowerShell 命令的 Vm |Microsoft Azure"
    description="常見的 PowerShell 命令，以取得您開始建立 Vm 虛擬網路和其相關聯的資源。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Vm 的常見網路 Azure PowerShell 命令

如果您想要建立虛擬機器，您需要建立[虛擬網路](../virtual-network/virtual-networks-overview.md)或不知道可以在其中新增 VM 現有虛擬網路。 一般而言，當您建立 VM，您也需要考慮建立本文所述的資源。

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="create-network-resources"></a>建立網路資源

任務 | ] 命令 
-------------- | -------------------------
建立子網路設定 | $subnet1 =[新增 AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -名稱 「 subnet_name 」-AddressPrefix XX。X.X.X/XX<BR>$subnet2 = 新增 AzureRmVirtualNetworkSubnetConfig-名稱 「 subnet_name 」-AddressPrefix XX。X.X.X/XX<BR><BR>一般網路可能[網際網路對負載平衡器](../load-balancer/load-balancer-internet-overview.md)子網路和不同的子網路的[內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)。 |
建立虛擬網路 | $vnet =[新增 AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -名稱 「 virtual_network_name 」-ResourceGroupName 「 resource_group_name 」 的位置 」 location_name 」-AddressPrefix XX。X.X.X/XX-子網路 $subnet1，$subnet2
檢定的結果是唯一的網域名稱 | [測試 AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) DomainQualifiedName 「 網域名稱 」 的 「 location_name 」 的位置<BR><BR>您可以指定的[公用 IP 資源](../virtual-network/virtual-network-ip-addresses-overview-arm.md)，建立的公用 IP 位址 domainname.location.cloudapp.azure.com 對應中的 Azure 受管理的 DNS 伺服器將 DNS 網域名稱。 名稱可以包含字母、 數字和連字號。 第一個和最後一個字元必須以字母或數字與網域名稱中必須是唯一的 Azure 的位置。 會傳回**True** ，如果您的建議的名稱是全域唯一的。
建立的公用 IP 位址 | $pip =[新增 AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -名稱 「 ip_address_name 」-ResourceGroupName 「 resource_group_name 」-DomainNameLabel 「 網域名稱 」 的位置 」 location_name 」-AllocationMethod 動態<BR><BR>公用 IP 位址使用您先前測試並 frontend 設定負載平衡器所使用的網域名稱。
建立一個主選單 IP 設定 | $frontendIP =[新增 AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -名稱 「 frontend_ip_name 」-PublicIpAddress $pip<BR><BR>Frontend 設定包含您先前建立的連入網路流量的公用 IP 位址。
建立地址後端資料庫 | $beAddressPool =[新增 AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -名稱 「 backend_pool_name 」<BR><BR>提供負載平衡器，可以透過網路介面存取後端內部的地址。
建立檢查 | $healthProbe =[新增 AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -名稱 「 probe_name 」-RequestPath 'HealthProbe.aspx'-通訊協定 http-連接埠 80 IntervalInSeconds 15-ProbeCount 2<BR><BR>包含狀況探查用來查看虛擬機器中的執行個體的後端位址集區的顯示狀態。
建立負載平衡規則 | $lbRule =[新增 AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -名稱 HTTP FrontendIpConfiguration $frontendIP-BackendAddressPool $beAddressPool-探查 $healthProbe-通訊協定 Tcp FrontendPort 80-BackendPort 80<BR><BR>包含指定負載平衡器公用的連接埠後端位址集區中的連接埠的規則。
建立連入的 NAT 規則 | $inboundNATRule =[新增 AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -名稱 「 rule_name 」-FrontendIpConfiguration $frontendIP-通訊協定 TCP FrontendPort 3441-BackendPort 3389<BR><BR>包含對應到特定虛擬機器中的後端位址集區的連接埠的負載平衡器公用的連接埠的規則。
建立負載平衡器 | $loadBalancer =[新增 AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 load_balancer_name 」 的位置 」 location_name 」-FrontendIpConfiguration $frontendIP-InboundNatRule $inboundNATRule-LoadBalancingRule $lbRule-BackendAddressPool $beAddressPool-探查 $healthProbe
建立網路介面 | $nic1 =[新增 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) ResourceGroupName 「 resource_group_name 」-名稱 「 network_interface_name 」 的位置 」 location_name 」-PrivateIpAddress XX。X.X.X-子網路 subnet2-LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0]-LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>建立使用的公用 IP 位址和虛擬網路的子網路您先前建立的網路介面。
    
## <a name="get-information-about-network-resources"></a>取得網路資源的相關資訊

任務 | ] 命令 
-------------- | -------------------------
清單虛擬網路 | [取得 AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) ResourceGroupName 「 resource_group_name 」<BR><BR>列出 [資源] 群組中的所有虛擬網路。
取得虛擬網路的相關資訊 | 取得 AzureRmVirtualNetwork-名稱 「 virtual_network_name 」-ResourceGroupName 「 resource_group_name 」
虛擬網路中的清單子網路 | 取得 AzureRmVirtualNetwork-名稱 「 virtual_network_name 」-ResourceGroupName 」 resource_group_name 「 & #124;選取子網路
取得子網路的相關資訊 | [取得 AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -名稱 「 subnet_name 」-VirtualNetwork $vnet<BR><BR>取得中指定的虛擬網路的子網路的相關資訊。 $Vnet 值表示取得 AzureRmVirtualNetwork 所傳回的物件。
清單的 IP 位址 | [取得 AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) ResourceGroupName 「 resource_group_name 」<BR><BR>列出 [資源] 群組中的公用 IP 位址。
清單負載平衡器 | [取得 AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) ResourceGroupName 「 resource_group_name 」<BR><BR>列出 [資源] 群組中的所有負載平衡器。
清單網路介面 | [取得 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) ResourceGroupName 「 resource_group_name 」<BR><BR>列出 [資源] 群組中的所有網路介面。
取得網路介面的相關資訊 | 取得 AzureRmNetworkInterface-名稱 「 network_interface_name 」-ResourceGroupName 「 resource_group_name 」<BR><BR>取得特定的網路介面的相關資訊。
取得 IP 設定的網路介面 | [取得 AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -名稱 「 ipconfiguration_name 」-NetworkInterface $nic<BR><BR>取得 IP 設定指定的網路介面的相關資訊。 $Nic 值表示取得 AzureRmNetworkInterface 所傳回的物件。

## <a name="manage-network-resources"></a>管理網路資源

任務 | ] 命令 
-------------- | -------------------------
加入虛擬網路的子網路 | [新增 AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) AddressPrefix XX。X.X.X/XX-名稱 「 subnet_name 」-VirtualNetwork $vnet<BR><BR>新增到現有的虛擬網路的子網路。 $Vnet 值表示取得 AzureRmVirtualNetwork 所傳回的物件。
刪除虛擬網路 | [移除 AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -名稱 「 virtual_network_name 」-ResourceGroupName 「 resource_group_name 」<BR><BR>移除 [資源] 群組中指定的虛擬網路。
刪除網路介面 | [移除 AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -名稱 「 network_interface_name 」-ResourceGroupName 「 resource_group_name 」<BR><BR>移除 [資源] 群組中指定的網路介面。
刪除負載平衡器 | [移除 AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -名稱 「 load_balancer_name 」-ResourceGroupName 「 resource_group_name 」<BR><BR>移除 [資源] 群組中的指定的負載平衡器。
刪除的公用 IP 位址 | [移除 AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-名稱 「 ip_address_name 」-ResourceGroupName 「 resource_group_name 」<BR><BR>移除 [資源] 群組中指定的公用 IP 位址。

## <a name="next-steps"></a>後續步驟

- 使用網路介面，您剛剛建立的時機您[建立 VM](virtual-machines-windows-ps-create.md)。
- 瞭解如何[建立具有多個網路介面 VM](../virtual-network/virtual-networks-multiple-nics.md)。
