<properties
   pageTitle="設定一律可用性群組接聽 – Microsoft Azure"
   description="設定可用性群組接聽 Azure 資源管理員模型，內部負載平衡器使用一或多個 IP 位址。"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>設定一或多個永遠在可用性群組接聽-資源管理員 

本主題說明如何執行兩個動作︰

- 建立 SQL Server 可用性群組使用 PowerShell cmdlet 內部負載平衡器。

- 負載平衡器支援一個以上的 SQL Server 可用性群組以新增額外的 IP 位址。 

在 SQL Server 可用性群組接聽會是用戶端連線以存取資料庫中的主要或次要複本的虛擬網路名稱。 Azure 虛擬機器上負載平衡器會保留接聽程式的 IP 位址。 負載平衡器路由流量至傾聽探查連接埠的 SQL server 執行個體。 在大多數情況下，可用性群組會使用內部負載平衡器。 Azure 內部的負載平衡器可以裝載一或多個 IP 位址。 每個 IP 位址使用特定探查連接埠。 這份文件會顯示如何使用 PowerShell 來建立新的負載平衡器，或新增現有的負載平衡器 SQL Server 可用性群組的 IP 位址。 

可將多個 IP 位址指派給內部負載平衡器 Azure 的新手，而只適用於資源管理員模型。 若要完成此工作，您需要有資源管理員模型中的 Azure 虛擬機器上部署的 SQL Server 可用性群組。 兩個 SQL Server 虛擬機器必須屬於相同的可用性設定。 若要自動建立 Azure 資源管理員] 中 [可用性] 群組中，您可以使用[Microsoft 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。 此範本會自動建立可用性] 群組中，包括您的內部負載平衡器。 如果您想要的話，您可以[手動設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

本主題需要已設定可用性群組。  

相關的主題包括︰

- [Azure VM (GUI) 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [使用 Azure 資源管理員與 PowerShell 設定 VNet-VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>設定 Windows 防火牆

設定 [允許 SQL Server 存取 Windows 防火牆]。 您必須設定為允許 TCP 連接的 SQL Server 執行個體，以及接聽探查所使用的連接埠連接埠使用防火牆。 如需詳細說明，請參閱[設定 Windows 防火牆存取資料庫引擎](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1)。 建立輸入的規則的 SQL Server 連接埠和探查連接埠。

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>範例指令碼︰ 使用 PowerShell 建立內部負載平衡器

> [AZURE.NOTE] 如果您要建立可用群組與[Microsoft 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)載入您不需要自行完成此步驟。 

下列 PowerShell 指令碼建立內部負載平衡器、 設定負載平衡規則]，並設定負載平衡器 IP 位址。 若要執行指令碼，開啟 Windows PowerShell ise [以系統，並貼上的指令碼指令碼] 窗格。 使用`Login-AzureRMAccount`powershell 登入。 如果您有多個 Azure 訂閱，請使用`Select-AzureRmSubscription `若要設定訂閱。 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>範例指令碼︰ IP 位址新增到現有的負載平衡器使用 PowerShell

若要使用一個以上的可用性群組，請使用 PowerShell 將額外的 IP 位址新增到現有的負載平衡器。 每個 IP 位址需要自己負載平衡規則與探查連接埠] 前的連接埠。

前端連接埠是應用程式使用連線至 SQL Server 執行個體的連接埠。 不同的可用群組的 IP 位址可以使用相同的前端連接埠。

>[AZURE.NOTE] SQL Server 可用性群組，每個 IP 位址需要特定探查連接埠。 例如，如果 IP 位址負載平衡器上的使用探查連接埠 59999，該負載平衡器上的沒有其他 IP 位址可以使用探查連接埠 59999。 

- 如需負載平衡器的資訊限制請參閱在[網路的限制-Azure 資源管理員](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)] 下的 [**私人的最上層結束 IP 每負載平衡器**。

- 群組限制可用性的相關資訊請參閱[限制 （可用性群組）](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)。

下列指令碼新增至現有的負載平衡器的新的 IP 位址。 更新您的環境的變數。 ILB 使用接聽連接埠的負載平衡前端連接埠。 此連接埠可以是 SQL Server 會接聽的連接埠。 SQL Server 預設執行個體，這是連接埠 1433年。 負載平衡可用性群組規則需要浮動 IP (傳回直接 server)，因此的後端連接埠是相同的前端連接埠。

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>設定叢集使用負載平衡器 IP 位址 

下一步是在叢集上，設定接聽程式，並開啟接聽線上。 若要這麼做，請執行下列動作︰ 

1. 建立可用性群組接聽容錯移轉叢集上  

2. 顯示接聽線上

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1.在容錯移轉叢集上建立可用性群組接聽

在此步驟中，您可以新增容錯移轉叢集與容錯移轉叢集管理員中，用戶端存取點並再使用 PowerShell 設定叢集資源接聽探查的連接埠。 

1. 使用 RDP 連線至 Azure 虛擬機器裝載的主要複本。 

2. 開啟容錯移轉叢集管理員。

3. 選取 [**網路**] 節點，並記下叢集網路名稱。 使用此名稱`$ClusterNetworkName`變數中的 PowerShell 指令碼。

4. 展開叢集名稱，然後按一下 [**角色**。

5. 在 [**角色**] 窗格中，以滑鼠右鍵按一下 [可用的群組名稱，然後選取**新增資源** > **用戶端存取點**。

6. 在 [**名稱**] 方塊中，建立這個新的接聽程式的名稱，然後**按兩次，** ，然後按一下**完成**。 請不要攜帶接聽或線上資源為止。
 
    新接聽的名稱為網路名稱的應用程式會使用連線至 SQL Server 可用性群組中的資料庫。

7. 按一下 [**資源**] 索引標籤，然後展開您剛剛建立的用戶端存取點。 以滑鼠右鍵按一下 IP 資源，然後按一下 [屬性]。 請注意的 IP 位址的名稱。 您會使用此名稱`$IPResourceName`變數中的 PowerShell 指令碼。

8. [ **IP 位址**] 底下按一下 [**靜態 IP 位址**並設定的靜態 IP 位址時您設定的負載平衡器 IP 位址 Azure 入口網站上使用相同地址。 

9. 停用 NetBIOS 這個地址，然後按一下**[確定**]。 如果您的方案包含多個 Azure VNets，請針對每個 IP 資源重複此步驟。 

10. 讓 SQL Server 可用性群組資源相依於的 IP 位址。 以滑鼠右鍵按一下叢集管理員中的資源，這是在**其他的資源**之下的 [**資源**] 索引標籤。 

11. 在目前裝載的主要複本叢集節點，開啟提高權限的 PowerShell ise [以系統並貼上下列命令將新的指令碼。 [**相依性**] 索引標籤中，按一下接聽程式的名稱。
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. 更新變數，並執行的 PowerShell 指令碼來設定新接聽的 IP 位址和連接埠。

 >[AZURE.NOTE] 如果您的 SQL Server 是在另一個區域中，您需要執行的 PowerShell 指令碼兩次。 第一次使用叢集網路名稱，叢集 IP 資源名稱，然後從第一個資源群組負載平衡器 IP 位址。 第一次使用叢集網路名稱，叢集 IP 資源名稱，並從第二個資源群組負載平衡器 IP 位址。

現在有可用性群組接聽資源。

## <a name="2-bring-the-listener-online"></a>2.顯示接聽線上

與可用性群組接聽資源設定，您就可以將接聽線上，讓應用程式可以連線至接聽程式的可用性] 群組中的資料庫。

1. 瀏覽回至容錯移轉叢集管理員。 展開 [**角色**]，並醒目提示您可用的群組。 在 [**資源**] 索引標籤上以滑鼠右鍵按一下接聽程式名稱，然後按一下 [**內容**。

1. 按一下 [**相依性**] 索引標籤。 如果有多個列的資源，請確認 [IP 位址已或不 AND、 相依性。 按一下**[確定]**。

1. 以滑鼠右鍵按一下接聽程式名稱，然後按一下**[連線**]。

1. 接聽 web app 中，從 [**資源**] 索引標籤後，以滑鼠右鍵按一下 [可用性] 群組中，按一下 [**內容**。

1. 建立相依性接聽名稱資源 （非 IP 位址資源名稱）。 按一下**[確定]**。

1. 啟動 SQL Server Management Studio 中並連接至主要的複本。

1. 瀏覽至**AlwaysOn 高可用性** | **可用性群組** | **可用性群組接聽程式**。 

1. 您現在應該會看到您所建立的容錯移轉叢集管理員接聽程式名稱。 以滑鼠右鍵按一下接聽程式名稱，然後按一下 [**屬性**]。

1. **連接埠**] 方塊中，指定連接埠號碼可用性群組接聽程式使用先前所用 $EndpointPort （1433年是預設值），然後按一下**[確定]**。

您現在會有在資源管理員模式下執行 Azure 虛擬機器中的 SQL Server 可用性群組。 

## <a name="test-the-connection-to-the-listener"></a>測試接聽的連線

若要測試連線︰

1. SQL server，在相同的虛擬網路中，但不是屬於複本 RDP。 這可能是在叢集其他的 SQL Server。

2. 若要測試連線使用**sqlcmd**公用程式。 例如，下列指令碼建立透過 Windows 驗證的接聽程式的主要複本的**sqlcmd**連線︰

    ```
    sqlmd -S <listenerName> -E
    ```

    如果接聽程式使用的不是預設連接埠連接埠 (1433)，指定連線字串中的連接埠。 例如，下列 sqlcmd 命令會連線到連接埠 1435年接聽程式︰ 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD 連線自動連線至何種 SQL Server 執行個體主控的主要複本。 

>[AZURE.NOTE] 請確定您指定的連接埠是兩個 SQL 伺服器的防火牆上開啟。 兩個伺服器需要輸入的規則，您使用的 TCP 連接埠。 如需詳細資訊，請參閱[新增或編輯防火牆規則](http://technet.microsoft.com/library/cc753558.aspx)。 

## <a name="guidelines-and-limitations"></a>指導方針和限制

請注意下列方針，使用內部 Azure 中的可用性群組接聽程式負載平衡器︰

- 使用內部負載平衡器只能存取從接聽，在相同的虛擬網路。

## <a name="for-more-information"></a>如需詳細資訊

如需詳細資訊請參閱[設定一直在可用欄位的群組 Azure VM 手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

### <a name="powershell-cmdlets"></a>PowerShell cmdlet

您可以使用下列 PowerShell cmdlet 來建立 Azure 虛擬機器內部負載平衡器。

- `New-AzureRmLoadBalancer`建立負載平衡器。 如需詳細資訊，請參閱[新增 AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) 。 

- `New-AzureRMLoadBalancerFrontendIpConfig`建立負載平衡器前端 IP 的設定。 如需詳細資訊，請參閱[新增 AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) 。

- `New-AzureRmLoadBalancerRuleConfig`建立規則的設定負載平衡器。 如需詳細資訊，請參閱[新增 AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) 。 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`建立後端資料庫的位址設定負載平衡器。 如需詳細資訊，請參閱[新增 AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) 。 

- `New-AzureRmLoadBalancerProbeConfig`建立負載平衡器探查的設定。 如需詳細資訊，請參閱[新增 AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) 。

如果您要從 Azure 資源群組移除負載平衡器，請使用`Remove-AzureRmLoadBalancer`。 如需詳細資訊，請參閱[移除 AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx)。
