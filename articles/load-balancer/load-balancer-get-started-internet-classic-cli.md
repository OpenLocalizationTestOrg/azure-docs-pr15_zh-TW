<properties
   pageTitle="開始建立網際網路圖示的負載平衡器在傳統的部署模型中使用 Azure CLI |Microsoft Azure"
   description="瞭解如何建立網際網路圖示在傳統的部署模型中使用 Azure CLI 負載平衡器"
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

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>開始建立網際網路圖示 Azure CLI 中的負載平衡器 （傳統）

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[瞭解如何建立網際網路圖示的負載平衡器使用 Azure 資源管理員](load-balancer-get-started-internet-arm-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>逐步建立網際網路對使用 CLI 負載平衡器

本指南會顯示如何建立根據上述情況網際網路負載平衡器。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。

2. 執行**azure config 模式**命令以切換到傳統模式，如下所示。

        azure config mode asm

    預期的輸出︰

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>建立結束點和負載平衡器設定

此案例假設虛擬機器 」 web1 」 和 「 web2 」 所建立。
本指南將會建立使用為公用連接埠的連接埠 80 和連接埠 80 和本機的連接埠負載平衡器設定。 探查連接埠也是設定連接埠 80，名為負載平衡器設定 「 lbset 」。


### <a name="step-1"></a>步驟 1

建立第一個端點及負載平衡器設定使用`azure network vm endpoint create`虛擬機器 」 web1 」。

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

使用參數︰

**-k**本機的虛擬機器連接埠<br>
**command 和 o**通訊協定<BR>
**-t**探查連接埠<BR>
**-b**負載平衡器名稱<BR>

## <a name="step-2"></a>步驟 2

將第二個虛擬機器 」 web2 」 新增至 [負載平衡器設定。

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>步驟 3

確認負載平衡器設定使用`azure vm show`。

    azure vm show web1

輸出會是︰

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>建立虛擬機器遠端桌面結束點

您可以建立公用連接埠的網路流量轉寄到本機的連接埠特定虛擬機器使用遠端桌面端點`azure vm endpoint create`。

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>虛擬機器移除負載平衡器

您需要刪除關聯的負載平衡器設定虛擬機器中的端點。 當端點會移除時，虛擬機器不屬於負載平衡器不再設定。

 使用上述範例中，您可以移除虛擬機器 」 web1 」 所建立的端點負載平衡器 」 lbset 「 使用命令`azure vm endpoint delete`。

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] 您可以瀏覽更多選項，來管理端點使用命令`azure vm endpoint --help`


## <a name="next-steps"></a>後續步驟

[開始設定內部負載平衡器](load-balancer-get-started-ilb-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

