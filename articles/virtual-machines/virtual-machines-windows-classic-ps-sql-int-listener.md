<properties
    pageTitle="永遠顯示狀態群組設定 ILB 接聽程式 |Microsoft Azure"
    description="本教學課程中會使用傳統的部署模型，以建立的資源，並在 Azure 使用內部負載平衡器 (ILB) 建立永遠在可用性群組接聽程式。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Azure 中設定一律上顯示狀態群組 ILB 接聽程式

> [AZURE.SELECTOR]
- [內部接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [外部接聽程式](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>概觀

本主題說明如何使用**內部負載平衡器 (ILB)**設定為一律上可用性] 群組的接聽。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]若要設定一直在可用性群組 ILB 接聽程式資源管理員模型中，請參閱[設定為永遠在可用性] 群組中 Azure 內部負載平衡器](virtual-machines-windows-portal-sql-alwayson-int-listener.md)。


可用群組可以包含複本的內部部署，僅 Azure 或橫跨 Azure 和內部部署混合式設定。 Azure 複本可位於相同的區域內或使用多個虛擬網路 (VNets) 的多個區域。 下列步驟假設您已經[設定可用性群組](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)，但尚未設定接聽程式。

## <a name="guidelines-and-limitations-for-internal-listeners"></a>內部接聽的指南和限制
請注意下列方針可用性群組接聽程式使用 ILB Azure 中︰

- 可用群組接聽程式支援 Windows Server 2008 R2、 Windows Server 2012，以及 Windows Server 2012 R2。

- 只有一個內部可用性群組接聽支援每雲端服務，因為接聽已設定為 ILB，而且沒有只有一個 ILB 每雲端服務。 不過，則可以建立多個外部接聽程式。 如需詳細資訊，請參閱[設定一律上顯示狀態群組 Azure 中外部接聽程式](virtual-machines-windows-classic-ps-sql-ext-listener.md)。

- 不支援相同的雲端服務，您也可以使用雲端服務的公用 VIP 外部接聽程式中建立內部的接聽程式。

## <a name="determine-the-accessibility-of-the-listener"></a>決定接聽程式的協助工具

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文著重於建立使用**內部負載平衡器 (ILB)**的接聽。 如果您需要公用/外部接聽，請參閱本文提供步驟說明如何設定[外部接聽](virtual-machines-windows-classic-ps-sql-ext-listener.md)的版本

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>建立與直接伺服器的負載平衡 VM 端點傳回

ILB，您必須先建立內部負載平衡器。 這是下方的指令碼。

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. 對於**ILB**，您應該指派的靜態 IP 位址。 首先，請執行下列命令以檢查目前的 VNet 設定︰

        (Get-AzureVNetConfig).XMLConfiguration

1. 請注意包含主控複本 Vm 子網路的**子網路**名稱。 這會**$SubnetName**參數指令碼中使用。

1. 然後記下**VirtualNetworkSite**名稱] 和 [起始**AddressPrefix**包含主控複本 Vm，子網路。 將兩個值傳遞給**測試 AzureStaticVNetIP** ] 命令，並檢查**AvailableAddresses**，以尋找可用的 IP 位址。 例如，如果 VNet 已命名*MyVNet* ，必須子網路位址範圍的開始時間*172.16.0.128*，下列命令會列出可用的地址︰

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. 選擇其中一個可用的地址，然後將其用於下列指令碼的**$ILBStaticIP**參數。

3. 複製文字編輯器中的下列 PowerShell 指令碼，並設定變數的值，以符合您的環境 （請注意，某些參數的已有預設值）。 請注意，使用相關性群組的現有部署無法新增 ILB。 如需有關 ILB 需求的詳細資訊，請參閱[內部負載平衡器](../load-balancer/load-balancer-internal-overview.md)。 此外，如果您的顯示狀態群組跨越 Azure 區域，您必須執行指令碼一次在每個資料中心的雲端服務，讓它們的資料中心的節點。

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. 一旦您已設定變數，複製到您的 PowerShell 的 Azure 工作階段在文字編輯器，執行指令碼。 如果出現提示仍會顯示 >>，按 enter 鍵即可確認指令碼開始執行。附註

>[AZURE.NOTE] Azure 傳統入口網站不支援內部負載平衡器次，因此 ILB 或 Azure 傳統入口網站中的端點，就不會看到您。 如果負載平衡器在其上執行，不過，**取得 AzureEndpoint**傳回內部 IP 位址。 否則，則會傳回 null。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>確認已安裝 KB2854082，如有必要

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>在 [可用群組節點中開啟防火牆連接埠

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>建立可用性群組接聽

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. ILB，則必須使用的 IP 位址的內部負載平衡器 (ILB) 建立較舊版本。 您可以使用下列指令碼，取得 PowerShell 這個 IP 位址。

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. 在下列其中一項 Vm，將您的作業系統的 PowerShell 指令碼複製到文字編輯器並將變數先前所述的值。

    Windows Server 2012 或更新版本使用下列指令碼︰

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Windows Server 2008 R2 使用下列指令碼︰

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. 一次您已設定變數、 開啟提高權限的 Windows PowerShell 視窗，然後複製文字編輯器中的指令碼並貼到您的 PowerShell 的 Azure 工作階段，以執行。 如果出現提示仍會顯示 >>，按 enter 鍵即可確認指令碼開始執行。

2. 針對每個 VM 上重複此步驟。 這個指令碼會 IP 位址資源設定雲端服務的 IP 位址，並設定其他參數，例如探查連接埠。 當 IP 位址資源上線時，它可以然後回應探查連接埠投票從之前在本教學課程中建立的負載平衡端點。

## <a name="bring-the-listener-online"></a>顯示接聽線上

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>待處理的項目

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>測試可用性群組接聽 （在相同的 VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
