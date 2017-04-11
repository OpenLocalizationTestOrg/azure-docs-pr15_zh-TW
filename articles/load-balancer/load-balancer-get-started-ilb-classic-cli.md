<properties
   pageTitle="建立使用傳統的部署模型中的 Azure CLI 內部負載平衡器 |Microsoft Azure"
   description="瞭解如何建立使用傳統的部署模型中的 Azure CLI 內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>開始建立使用 Azure CLI 內部負載平衡器 （傳統）

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](load-balancer-get-started-ilb-arm-cli.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>若要建立內部負載平衡器設定虛擬機器

若要建立內部負載平衡器設定並將其流量傳送至該伺服器，您必須執行下列動作︰

1. 建立內部負載平衡會進行負載平衡負載平衡集的伺服器上的連入流量端點的執行個體。

1. 新增對應至將接收內送的流量虛擬機器中的端點。

1. 設定將會傳送流量負載平衡將其流量傳送至內部負載平衡執行個體的虛擬 IP (VIP) 地址的伺服器。

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>逐步建立使用 CLI 內部負載平衡器

本指南會顯示如何建立內部負載平衡器根據上述情況。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。

2. 執行**azure config 模式**命令以切換到傳統模式，如下所示。

        azure config mode asm

    預期的輸出︰

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>建立結束點和負載平衡器設定

此案例假設稱為 「 mytestcloud 「 雲端服務中的虛擬機器 」 DB1 」 和 「 DB2 」。 兩個虛擬機器使用虛擬網路，稱為 「 我 」 testvnet 」 與 「 子網路 1 」 的子網路。

本指南將會建立內部負載平衡器設定使用連接埠 1433年為私人的連接埠和 1433年為本機的連接埠。

這是最常見的案例在後端使用內部負載平衡器保證不會直接使用的公用 IP 位址公開資料庫伺服器擁有 SQL 虛擬機器。


### <a name="step-1"></a>步驟 1

建立使用設定內部負載平衡器`azure network service internal-load-balancer add`。

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

使用參數︰

**-r**雲端服務名稱<BR>
**-n** -內部負載平衡器名稱<BR>
**-t**子網路名稱 （由您將會新增至內部負載平衡器虛擬機器相同子網路）<BR>
**-** -（選擇性） 新增私人的靜態 IP 位址<BR>

請查看`azure service internal-load-balancer --help`如需詳細資訊。

您可以檢查使用命令的內部負載平衡器屬性`azure service internal-load-balancer list`*雲端服務的名稱*。

以下所示的輸出的範例︰

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>步驟 2

當您新增第一個結束點時，您可以設定的內部負載平衡器設定。 結束點虛擬機器與探查連接埠至內部負載平衡器組在此步驟中，您會建立關聯。

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

使用參數︰

**-k**本機的虛擬機器連接埠<BR>
**-t**探查連接埠<BR>
**-r**探查通訊協定<BR>
**-e**探查間隔秒數<BR>
**-f**逾時間隔秒數 <BR>
**-i** -內部負載平衡器名稱 <BR>


## <a name="step-3"></a>步驟 3

確認負載平衡器設定使用`azure vm show`*虛擬機器名稱*

    azure vm show DB1

輸出會是︰

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>建立虛擬機器遠端桌面結束點

您可以建立公用連接埠的網路流量轉寄到本機的連接埠特定虛擬機器使用遠端桌面端點`azure vm endpoint create`。

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>虛擬機器移除負載平衡器

您可以移除刪除相關聯的端點來設定內部負載平衡器的虛擬機器。 端點會移除後，虛擬機器不屬於負載平衡器不再設定。

 使用上述範例中，您可以移除使用命令來建立虛擬機器 」 DB1 」 從內部負載平衡器 」 ilbset 」 的端點`azure vm endpoint delete`。

    azure vm endpoint delete DB1 tcp-1433-1433


請查看`azure vm endpoint --help`如需詳細資訊。


## <a name="next-steps"></a>後續步驟

[設定使用來源 IP 相關性負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)