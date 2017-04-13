<properties
    pageTitle="設定外部接聽永遠在可用性群組 |Microsoft Azure"
    description="本教學課程中會引導您建立永遠在可用性群組接聽是外部存取使用公用虛擬 IP 位址相關聯的雲端服務的 Azure 中的步驟。"
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
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Azure 中設定外部接聽永遠上可用群組

> [AZURE.SELECTOR]
- [內部接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [外部接聽程式](virtual-machines-windows-classic-ps-sql-ext-listener.md)

本主題說明如何設定為一律上可用性群組中的外部存取網際網路上接聽程式。 這是因為關聯接聽雲端服務的**公用虛擬 IP (VIP)**地址。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


可用群組可以包含複本的內部部署，僅 Azure 或橫跨 Azure 和內部部署混合式設定。 Azure 複本可位於相同的區域內或使用多個虛擬網路 (VNets) 的多個區域。 下列步驟假設您已經[設定可用性群組](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)，但尚未設定接聽程式。

## <a name="guidelines-and-limitations-for-external-listeners"></a>外部接聽的指南和限制

當您部署使用雲端服務 pubic VIP 位址，請注意下列方針相關 Azure 中的可用性群組接聽程式︰

- 可用群組接聽程式支援 Windows Server 2008 R2、 Windows Server 2012，以及 Windows Server 2012 R2。

- 用戶端應用程式必須位於不同的雲端服務比一個包含您的顯示狀態群組 Vm。 Azure 不支援相同的雲端服務中的用戶端與伺服器的直接伺服器 return 鍵。

- 根據預設，本文中的步驟會顯示如何設定接聽使用雲端服務虛擬 IP (VIP) 地址。 不過，就可以保留，並建立您的雲端服務的多個 VIP 位址。 這樣您就可以使用本文中的步驟，以建立多個不同的 VIP 每個相關聯的接聽程式。 如需如何建立多個 VIP 位址資訊，請參閱[多個 Vip 每雲端服務](../load-balancer/load-balancer-multivip.md)。

- 如果您正在建立混合式環境的接聽程式，內部部署網路必須具備網站-VPN 與 Azure 虛擬網路除了公用網際網路連線。 Azure 子網路中，在可用性群組接聽到達。 僅限分別雲端服務的公用 IP 位址

- 不支援相同的雲端服務，您也可以使用內部負載平衡器 (ILB) 內部接聽程式中建立外部的接聽程式。

## <a name="determine-the-accessibility-of-the-listener"></a>決定接聽程式的協助工具

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文著重於建立使用**外部負載平衡**接聽程式。 如果您想是私人至虛擬網路的接聽，請參閱本文提供步驟說明如何設定[ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)的版本

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>建立與直接伺服器的負載平衡 VM 端點傳回

外部負載平衡使用虛擬雲端服務來裝載您的 Vm 公用虛擬 IP 位址。 因此您不需要建立，或在此情況下設定負載平衡器。

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. 複製文字編輯器中的下列 PowerShell 指令碼，並設定變數的值，以符合您的環境 （某些參數的已有預設值）。 請注意，是否您的顯示狀態群組跨越 Azure 區域，您必須執行指令碼一次在每個資料中心的雲端服務，讓它們的資料中心的節點。

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. 一旦您已設定變數，複製到您的 PowerShell 的 Azure 工作階段在文字編輯器，執行指令碼。 如果出現提示仍會顯示 >>，按 enter 鍵即可確認指令碼開始執行。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>確認已安裝 KB2854082，如有必要

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>在 [可用群組節點中開啟防火牆連接埠

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>建立可用性群組接聽

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. 外部負載平衡，您必須取得雲端服務，包含您複本的公用虛擬 IP 位址。 登入 Azure 傳統入口網站。 瀏覽至包含您的顯示狀態群組 VM 雲端服務。 開啟**儀表板**檢視。

3. 請注意 [**公用虛擬 IP (VIP) 位址**] 底下所顯示的位址。 如果您的方案包含 VNets，重複此步驟的每個雲端服務包含 VM 裝載複本。

4. 在下列其中一項 Vm，複製到文字編輯器中的下列 PowerShell 指令碼並將變數先前所述的值。

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. 一次您已設定變數、 開啟提高權限的 Windows PowerShell 視窗，然後複製文字編輯器中的指令碼並貼到您的 PowerShell 的 Azure 工作階段，以執行。 如果出現提示仍會顯示 >>，按 enter 鍵即可確認指令碼開始執行。

1. 針對每個 VM 上重複此步驟。 這個指令碼會 IP 位址資源設定雲端服務的 IP 位址，並設定其他參數，例如探查連接埠。 當 IP 位址資源上線時，它可以然後回應探查連接埠投票從之前在本教學課程中建立的負載平衡端點。

## <a name="bring-the-listener-online"></a>顯示接聽線上

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>待處理的項目

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>測試可用性群組接聽 （在相同的 VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>測試可用性群組接聽 （透過網際網路）

才能存取從接聽虛擬網路以外，您必須使用外部/公用負載平衡 （如本文所述） 而不 ILB，這在同一個 VNet 只存取。 在 [連接字串您指定的雲端服務的名稱。 例如，如果您有使用名稱*mycloudservice*雲端服務，sqlcmd 陳述式應，如下所示︰

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

與上一個範例中，不同的是 SQL 驗證必須使用，因為來電者不能在網際網路上使用 windows 驗證。 如需詳細資訊，請參閱[永遠在可用性] 群組中 Azure VM︰ 用戶端連線案例](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)。 使用 SQL 驗證時，請確定您在兩個複本上建立相同的登入。 如需有關如何疑難排解登入可用性群組的詳細資訊，請參閱[如何對應登入，或使用包含 SQL 資料庫使用者連線至其他複本，對應到可用性資料庫](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)。

如果一直在複本會位於不同的子網路，請用戶端必須指定**MultisubnetFailover = True**連線字串中。 這會導致平行連線到不同的子網路中的複本。 請注意此案例包含交互地區永遠在可用性群組部署。

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
