<properties
   pageTitle="建立使用傳統的部署模型中的 PowerShell 內部負載平衡器 |Microsoft Azure"
   description="瞭解如何建立使用傳統的部署模型中的 PowerShell 內部負載平衡器"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>開始建立使用 PowerShell 內部負載平衡器 （傳統）

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](load-balancer-get-started-ilb-arm-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>建立設定的虛擬機器內部負載平衡器

若要建立內部負載平衡器設定並將其流量傳送至該伺服器，您必須執行下列動作︰

1. 建立內部負載平衡會進行負載平衡負載平衡集的伺服器上的連入流量端點的執行個體。

1. 新增對應至將接收內送的流量虛擬機器中的端點。

1. 設定將會傳送流量負載平衡將其流量傳送至內部負載平衡執行個體的虛擬 IP (VIP) 地址的伺服器。


### <a name="step-1-create-an-internal-load-balancing-instance"></a>步驟 1︰ 建立內部負載平衡執行個體

為現有的雲端服務或地區的虛擬網路下部署雲端服務，您可以使用下列 Windows PowerShell 命令建立內部負載平衡執行個體︰

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


請注意，這種[新增 AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell 指令程式會使用 DefaultProbe 參數設定。 如需其他參數集的詳細資訊，請參閱[新增 AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx)。

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>步驟 2︰ 新增至內部負載平衡執行個體的端點

以下是範例︰

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>步驟 3︰ 設定您的伺服器，將其流量傳送至新的內部負載平衡端點

您必須設定的伺服器的流量即將成為負載平衡使用新的 IP 位址 (VIP) 的內部負載平衡執行個體。 這是在其接聽內部負載平衡執行個體的地址。 在大部分情況下，您需要剛剛新增或修改 VIP 內部負載平衡執行個體的 DNS 記錄。

如果您指定的 IP 位址的內部負載平衡執行個體建立時，您已經有 VIP。 否則，您可以看到 VIP 從下列命令︰

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



若要使用這些命令，填入值並移除 < 和 >。 以下是範例︰

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


從顯示的 [取得 AzureInternalLoadBalancer] 命令，請注意的 IP 位址，然後進行必要的變更至您的伺服器或 DNS 記錄，以確保會傳送到 VIP 傳輸。

>[AZURE.NOTE]Microsoft Azure 平台使用各種不同的系統管理案例的靜態、 公開舉例來說 IPv4 位址。 IP 位址是 168.63.129.16。 這個 IP 位址應該不會封鎖任何防火牆，因為它可能會導致非預期的行為。
>提供解答 Azure 內部負載平衡時，此 IP 位址是由監控探查從負載平衡器來決定負載平衡集中的虛擬機器的狀況。 如果網路安全性群組用來限制 Azure 虛擬機器集內部的負載平衡流量，或套用至虛擬子網路，請確定已新增至允許從 168.63.129.16 流量的網路安全性規則。


## <a name="example-of-internal-load-balancing"></a>內部負載平衡的範例

若要逐步解說端對端的程序建立兩個範例設定負載平衡設定，請參閱下列各節。

### <a name="an-internet-facing-multi-tier-application"></a>網際網路圖示，多層應用程式

您想要提供一組網際網路的網頁伺服器的負載平衡資料庫服務。 伺服器兩組都裝載於單一 Azure 雲端服務。 TCP 連接埠 1433年的網頁伺服器流量必須散佈資料庫層中的兩個虛擬機器。 圖 1 顯示設定。

![在資料庫層內部負載平衡設定](./media/load-balancer-internal-getstarted/IC736321.png)


設定包含下列各項︰

- 現有的雲端服務裝載虛擬機器稱為 mytestcloud。

- 兩個現有的資料庫伺服器的命名 DB1 DB2。

- 在網頁層的網頁伺服器連線到資料庫伺服器的資料庫層使用私用的 IP 位址。 另一個選項是使用自己的 DNS 的虛擬網路，並手動登錄的內部負載平衡器設定 A 記錄。

設定新內部負載平衡執行個體名稱為**ILBset**下列命令，並新增端點，對應到兩個資料庫伺服器的虛擬機器︰

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>移除內部負載平衡的設定

若要為端點虛擬機器移除內部負載平衡器執行個體，請使用下列命令︰

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

若要使用這些命令，填入 [值] 中，移除 < 和 >。

以下是範例︰

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

若要移除內部負載平衡器執行個體來自雲端服務，請使用下列命令︰

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

若要使用這些命令，填入值並移除 < 和 >。

以下是範例︰

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>內部負載平衡器 cmdlet 的其他資訊


若要取得其他內部負載平衡 cmdlet 的詳細資訊，請在 Windows PowerShell 提示中執行下列命令︰

- 取得說明新 AzureInternalLoadBalancerConfig-完整

- 取得說明新增 AzureInternalLoadBalancer-完整

- 取得說明取得 AzureInternalLoadbalancer-完整

- 取得說明移除 AzureInternalLoadBalancer-完整

## <a name="next-steps"></a>後續步驟

[設定使用來源 IP 相關性負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)